---
layout: default
title: Docker、k8sまとめ[第5回] k8sを利用したデプロイの管理
description: Docker、k8sまとめ。k8sを利用したデプロイの管理ついて、バージョンの戻し方やアップデートの方法について記載します。
category: IT
---

[第5回](/it/container/containerPart04.html)はtomcatのセッションの永続化でした。

今回はk8sを利用したデプロイの管理について  
バージョンの戻し方やアップデートの方法について記載します。

## 目次

- [バージョンの戻し](#anchor1)  
- [RecreateとRollingUpdate](#anchor2)  
- [デプロイの一時停止](#anchor3)  
- [まとめ](#anchor4)  

<a id="anchor1"></a>

## バージョンの戻し

まず、[第3回のバージョンアップ](https://mtaketani113.github.io/it/container/containerPart04.html#anchor3)であったように、バージョンアップします。

すると、以下のように履歴が登録されます。

```Shell
$ kubectl rollout history deployment nginx-deployment
deployment.apps/nginx-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

上記のように2件、登録されているのがわかります。

それぞれのバージョンを確認すると、nginxのバージョンが変わっているのがわかります。

```Shell
$ kubectl rollout history deployment nginx-deployment --revision 1
deployment.apps/nginx-deployment with revision #1
Pod Template:
  Labels:       app=nginx
        pod-template-hash=f77774fc5
  Containers:
   nginx:
    Image:      nginx:1.12
    Port:       80/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>

$ kubectl rollout history deployment nginx-deployment --revision 2
deployment.apps/nginx-deployment with revision #2
Pod Template:
  Labels:       app=nginx
        pod-template-hash=5d47ff8589
  Containers:
   nginx:
    Image:      nginx:1.13.12
    Port:       80/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>
```

リリースして、前のバージョンに戻さないといけないことがあると思います。  
その際に、リビジョンを戻すことができます。

```Shell
# 指定したリビジョンに戻す場合
$ kubectl rollout undo deployment nginx-deployment --to-revision 1
deployment.apps/nginx-deployment rolled back

# 1つ前のリビジョンに戻す場合
$ kubectl rollout undo deployment nginx-deployment
deployment.apps/nginx-deployment rolled back
```

戻すとリビジョン外貨のようになります。
```Shell
$ kubectl rollout history deployment nginx-deployment
deployment.apps/nginx-deployment
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
```
戻したリビジョンが一番最新のリビジョンになって、戻したリビジョンのほうはなくなります。

3つ以上あって途中のバージョンに変えると、飛び番になります。
↓5がない
```
$ kubectl rollout history deployment nginx-deployment
deployment.apps/nginx-deployment
REVISION  CHANGE-CAUSE
4         <none>
6         <none>
7         <none>
```

リビジョン3を確認すると、バージョンが1.12になってます。
```Shell
$ kubectl rollout history deployment nginx-deployment --revision 3
deployment.apps/nginx-deployment with revision #3
Pod Template:
  Labels:       app=nginx
        pod-template-hash=f77774fc5
  Containers:
   nginx:
    Image:      nginx:1.12
    Port:       80/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>

```

このようにして、何か問題があったときはすぐに戻せるようになっています。

<a id="anchor2"></a>

## RecreateとRollingUpdate

[第3回のバージョンアップ](https://mtaketani113.github.io/it/container/containerPart04.html#anchor3)
で、アップデートすると徐々に変わっていくのを見ることができたと思いますが、  
この変わり方をコントロールすることができます。

そこで使うのがRecreateとRollingUpdateです。

Recreateはいったん全部Podを削除して、再作します。
なので、停止するタイミングがあります。あまり使わないのかな？

yamlを以下のように記載して、適用します。
```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  strategy:
    type: Recreate # 全部Podを削除して、再作
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

```Shell
$ kubectl get replicasets --watch
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-5d47ff8589   0         0         0       17h
nginx-deployment-f77774fc5    2         2         2       18h
nginx-deployment-5d47ff8589   0         0         0       17h
nginx-deployment-f77774fc5    0         2         2       18h
nginx-deployment-f77774fc5    0         2         2       18h
nginx-deployment-f77774fc5    0         0         0       18h
nginx-deployment-5d47ff8589   2         0         0       17h
nginx-deployment-5d47ff8589   2         0         0       17h
nginx-deployment-5d47ff8589   2         2         0       17h
nginx-deployment-5d47ff8589   2         2         1       17h
nginx-deployment-5d47ff8589   2         2         2       17h
```

nginx-deployment-f77774fc5が一旦0になってから、nginx-deployment-5d47ff8589が作られているのが見えます。

つぎがRollingUpdateです。
こちらは、何個Podを減らせるか、何個Podを余分に作れるかを制御できます。

yamlを以下のように修正してます。
`maxUnavailable`が何個Pod減らせるか`maxSurge`が何個余分にPodを作るかです。
今回は`maxUnavailable`が0で`maxSurge`が1なので、Podは2つ起動したまま、余分に新しいPodを1個ずつ作って切り替えていきます。

```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0 # 何個Pod減らせるか
      maxSurge: 1 # 何個余分にPodを作るか
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

```
$ kubectl get replicasets --watch
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-5d47ff8589   2         2         2       18h
nginx-deployment-75b69bd684   0         0         0       9m49s
nginx-deployment-75b69bd684   0         0         0       9m52s
nginx-deployment-75b69bd684   1         0         0       9m52s
nginx-deployment-75b69bd684   1         0         0       9m52s
nginx-deployment-75b69bd684   1         1         0       9m52s
nginx-deployment-75b69bd684   1         1         1       9m55s
nginx-deployment-5d47ff8589   1         2         2       18h
nginx-deployment-75b69bd684   2         1         1       9m55s
nginx-deployment-5d47ff8589   1         2         2       18h
nginx-deployment-75b69bd684   2         1         1       9m55s
nginx-deployment-5d47ff8589   1         1         1       18h
nginx-deployment-75b69bd684   2         2         1       9m55s
nginx-deployment-75b69bd684   2         2         2       9m57s
nginx-deployment-5d47ff8589   0         1         1       18h
nginx-deployment-5d47ff8589   0         1         1       18h
nginx-deployment-5d47ff8589   0         0         0       18h
```

nginx-deployment-75b69bd684のREADYが1つ先にできてからnginx-deployment-5d47ff8589が1つ減っています 
一時的にPodの総数が3つになってます。

```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1 # 何個Pod減らせるか
      maxSurge: 0 # 何個余分にPodを作るか
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

```
$ kubectl get replicasets --watch
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-5d47ff8589   0         0         0       18h
nginx-deployment-75b69bd684   2         2         2       17m
nginx-deployment-5d47ff8589   0         0         0       18h
nginx-deployment-75b69bd684   1         2         2       17m
nginx-deployment-5d47ff8589   1         0         0       18h
nginx-deployment-75b69bd684   1         2         2       17m
nginx-deployment-5d47ff8589   1         0         0       18h
nginx-deployment-75b69bd684   1         1         1       17m
nginx-deployment-5d47ff8589   1         1         0       18h
nginx-deployment-5d47ff8589   1         1         1       18h
nginx-deployment-75b69bd684   0         1         1       17m
nginx-deployment-5d47ff8589   2         1         1       18h
nginx-deployment-75b69bd684   0         1         1       17m
nginx-deployment-75b69bd684   0         0         0       17m
nginx-deployment-5d47ff8589   2         1         1       18h
nginx-deployment-5d47ff8589   2         2         1       18h
nginx-deployment-5d47ff8589   2         2         2       18h
```

こちらは、nginx-deployment-75b69bd684のREADYが1減って、その後、nginx-deployment-5d47ff8589が1つ増えます。
総数は2を超えないようになっています。

リソースの制限などで、Pod数を増やせないときなどに役に立ちそうですね。

ちなみにデフォルトは25パーセントらしいです。%で設定もできるんですね。
```
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
```

<a id="anchor3"></a>

## デプロイの一時停止

安全のため、Deploymentを一時停止することができます。

```Shell
# 停止
$ kubectl rollout pause deployment nginx-deployment
deployment.apps/nginx-deployment paused
```

停止した状態で、バージョンを変えてyamlを適用しても、
`kubectl get replicasets --watch`をで確認すると、変わらないと思います。

そして、以下の再開のコマンドを実行すると、更新が開始されると思います。

```Shell
# 再開
$ kubectl rollout resume deployment nginx-deployment
deployment.apps/nginx-deployment resumed
```

このように、更新を停止することができます。

<a id="anchor4"></a>

## まとめ

k8sを利用すると、バージョンアップの方法やタイミングをコントロールできます。  
また、問題が発生したときに元に戻すのも簡単にできます。

それぞれの戦略に合わせてどのように更新していくとか決める必要がありそうですね。