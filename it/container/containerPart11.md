---
layout: default
title: Docker、k8sまとめ[第11回] ノード、PODの停止
description: Docker、k8sまとめ。k8sでスケジューリング対象から除外やノード、Podを停止する方法を記載します。
category: IT
---

[第10回](/it/container/containerPart10.html)ではヘルスチェックについて記載ました。

今回はスケジューリング対象から除外やノード、Podを停止する方法を記載します。  
Docker Desktopで単一ノードで動かしているので、実現はできないのですが、
別のノードに安全にPodを移す方法を記載します。

## 目次

- [スケジューリング対象の除外](#anchor1)  
- [ノードからの退避](#anchor2)
- [ノードからの安全な退避](#anchor3)
- [まとめ](#anchor4)

<a id="anchor1"></a>

## スケジューリング対象の除外

まずは以下のコマンドで、ノードをチェックします。
ここではDocker Desktopが前提なのでノードは一つです。足したりできるのか？？

```Shell
$ kubectl get node
NAME             STATUS   ROLES    AGE     VERSION
docker-desktop   Ready    master   5d17h   v1.19.7
```

名前は`docker-desktop`でステータスは`Ready`となっています。

これを、このコマンドを実行することで、コマンドを実行した後に、Podを追加できなくなります。

```Shell
$ kubectl cordon docker-desktop
node/docker-desktop cordoned
```

cordon コルドンと読むらしく、 
紐とか非常線とか警戒線いう意味らしい。

入れなくするということですかね？

ここでノードを確認すると、
```Shell
$ kubectl get nodes
NAME             STATUS                     ROLES    AGE     VERSION
docker-desktop   Ready,SchedulingDisabled   master   5d17h   v1.19.7
```
SchedulingDisabledとなっています。

ですので、たとえば、Podを一つ消すと`Pending`から変わらなくなります。
新しくは作れないのですね。他にノードもないから、作れず止まっている状態ですかね。

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-648fb5bbfd-8blct   1/1     Running   0          7m46s
nginx-deployment-648fb5bbfd-lgd45   1/1     Running   0          5m42s
↓
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-648fb5bbfd-8blct   0/1     Pending   0          22s
nginx-deployment-648fb5bbfd-lgd45   1/1     Running   0          5m42s
```

状態を戻すのは以下のコマンドです。

```Shell
$ kubectl uncordon docker-desktop
node/docker-desktop uncordoned
$ kubectl get nodes
NAME             STATUS   ROLES    AGE     VERSION
docker-desktop   Ready    master   5d17h   v1.19.7
```

<a id="anchor2"></a>

## ノードからの退避

先ほどの`cordon`は新たにスケジュールしないコマンドです。

すでに動いているのを削除して別のノードに移すコマンドが以下です。

```Shell
kubectl drain docker-desktop --force --ignore-daemonsets
```

--ignore-daemonsetsがないと、エラーになりました。
1つでマスターノードとかも動いてるからですかね？

```Shell
$ kubectl drain docker-desktop --force
 cannot delete DaemonSet-managed Pods (use --ignore-daemonsets to ignore): kube-system/kube-proxy-8j5rf
```

すでに動いていたPodを確認するとPendingにかわっていました。

```Shell
$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-648fb5bbfd-4t8g7   0/1     Pending   0          39s
nginx-deployment-648fb5bbfd-hmhkl   0/1     Pending   0          39s
```

これを戻すのも`kubectl uncordon docker-desktop`で可能です。

<a id="anchor3"></a>

## ノードからの安全な退避

上記でノードが複数あれば退避はできるのですが、
ノードを移す際に全Podが一時的に停止してしまいます。

ですので、Podを全停止せず、少しずつ移していくのが以下の`PodDisruptionBudget`です。

```Yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
  name: nginx-pod-disruption-budget
spec:
  minAvailable: 1 #最小の起動数
# maxAvailable: 1 #最大の停止数
  selector:
    matchLabels:
      app: nginx
```

`minAvailable`と`maxAvailable`はどちらか片方しか設定できず、
今回は、`minAvailable`を設定して、最低でも他のノードを含めて1つは動かしていないと止められなしています。

minAvailable、maxAvailableはパーセントでも指定できるようです。

これを適用すると、下記のように、別ノードがないので、停止できないよエラーを繰り返します。

```
kubectl drain docker-desktop --force --ignore-daemonsets
node/docker-desktop cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-proxy-8j5rf
evicting pod default/nginx-deployment-648fb5bbfd-xqwnf
evicting pod default/nginx-deployment-648fb5bbfd-87xfk
error when evicting pod "nginx-deployment-648fb5bbfd-87xfk" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
pod/nginx-deployment-648fb5bbfd-xqwnf evicted
evicting pod default/nginx-deployment-648fb5bbfd-87xfk
error when evicting pod "nginx-deployment-648fb5bbfd-87xfk" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
evicting pod default/nginx-deployment-648fb5bbfd-87xfk
error when evicting pod "nginx-deployment-648fb5bbfd-87xfk" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
pod/ingress-nginx-controller-c4f944d4d-tm8xg evicted
evicting pod default/nginx-deployment-648fb5bbfd-87xfk
error when evicting pod "nginx-deployment-648fb5bbfd-87xfk" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
evicting pod default/nginx-deployment-648fb5bbfd-87xfk
error when evicting pod "nginx-deployment-648fb5bbfd-87xfk" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
evicting pod default/nginx-deployment-648fb5bbfd-87xfk
error when evicting pod "nginx-deployment-648fb5bbfd-87xfk" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
```

<a id="anchor4"></a>

## まとめ

ノードのメンテナンスなどで安全に(=停止せず)に移すことができます。
最低限動くPodを指定して、全停止を抑止できます。

Docker Desktopでは1つのノードなので、ノードを切り替えることはありませんが、
本番稼働では十分考えられる運用なので調べて紹介しました。

