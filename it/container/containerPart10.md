---
layout: default
title: Docker、k8sまとめ[第10回] ヘルスチェック
description: Docker、k8sまとめ。k8sでのヘルスチェックについて記載します。Podの状態を監視して異常と判定した場合に再起動はトラフィック停止することができます。
category: container
---

[第9回](/it/container/containerPart09.html)ではSticky Sessionについて記載しました。

今回はヘルスチェックについて記載します。

## 目次

- [ヘルスチェックで何ができるか](#anchor1)  
- [ヘルスチェックの種類](#anchor2)
- [ヘルスチェックを実施](#anchor3)
- [まとめ](#anchor4)

<a id="anchor1"></a>

## ヘルスチェックの種類

まず、ヘルスチェックですが、k8sでは、Podが正常に動いているかをチェックし続けることができます。
異常と判定したときに、再起動したり、通信されないようにすることができます。

<a id="anchor2"></a>

## ヘルスチェックの種類

k8sで用意されているのは2種類で、「LivenessProbe」「ReadinessProbe」があります。

LivenessProbeは異常終了したときに再起動します。  
ReadinessProbeは異常終了したときにトラフィックを流さないようにします。
こちらは再起動しません。

exec、httpGet、tcpSocketという3種類のチェック方式があります。

execはコマンド実行して、終了コードが0以外であれば異常と判定します。  
httpGetはHttpのGetリクエストを出して、200~399が返ってこない場合に異常と判定します。  
tcpSocketはセッションを確立できない場合に異常と判定します。

今回は`httpGet`を利用しようと思います。

<a id="anchor3"></a>

## ヘルスチェックを実施

何はともあれ動かしてみましょう。まずは「LivenessProbe」  
[第4回](https://mtaketani113.github.io/it/container/containerPart04.html)で使ったYamlをいじります。

```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1 # わかりやすいようにレプリカの数を1としておきます。
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
        livenessProbe: # ヘルスチェックの設定
          httpGet: 
            path: /index.html #ヘルスチェックする
            port: 80
            scheme: HTTP
          timeoutSeconds: 1 # タイムアウトまでの時間
          successThreshold: 1 # 成功とみなすまでのチェック回数
          failureThreshold: 2 # 失敗とみなすまでのチェック回数
          initialDelaySeconds: 5 # 初回ヘルスチェック開始までの秒数
          periodSeconds: 3 # ヘルスチェックの感覚

---

apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - name: http
      port: 8080
      targetPort: 80
```

起動すると`http://localhost:8080/`でnginxの画面が出ると思います。

index.htmlをみてヘルスチェックをしているので、
このファイルを削除してみましょう。

`kubectl get pods`でpodの名前を取得して、以下のコマンドでファイルを削除しましょう

```Shell
$ kubectl exec -it nginx-deployment-xxxxxxxxxx-xxxxx -- rm /usr/share/nginx/html/index.html
```

すぐに`http://localhost:8080/`にアクセスすると403になると思いますが、しばらくするとnginxの画面が見えるかと思います。

podの状態を見ると以下のようにRESTARTSが「1」になっています。
再起動したいということです。

```Shell
$ kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-7c7457844-ws97g   1/1     Running   1          3m28s
```

何度か削除を繰り返すと、Statusが「CrashLoopBackOff」になりしばらく動かなくなります。
しかし、しばらくすると再開します。おそらく、何度も短時間でヘルスチェックに引っかかるとしばらく動かないようになるようです。

```Shell
$ kubectl get pods
NAME                               READY   STATUS             RESTARTS   AGE
nginx-deployment-7c7457844-ws97g   0/1     CrashLoopBackOff   4          2m43s
```

次に「ReadinessProbe」を使ってみましょう

```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # 1のままだと全部止まるので2にしておきます。
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /index.html
            port: 80
            scheme: HTTP
          timeoutSeconds: 1
          successThreshold: 1
          failureThreshold: 2
          initialDelaySeconds: 5
          periodSeconds: 3

---

apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - name: http
      port: 8080
      targetPort: 80
```

同様にファイルを削除してみましょう。

```Shell
$ kubectl exec -it nginx-deployment-xxxxxxxxxx-xxxxx -- rm /usr/share/nginx/html/index.html
```

すると、削除したほうが0/1となって、トラフィックを流さなくなります。
もちろん両方ファイル削除すると・・・動かなくなります。

```Shell
$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-648fb5bbfd-cpqfm   1/1     Running   0          30m
nginx-deployment-648fb5bbfd-tnjr9   0/1     Running   0          29m
```

逆にファイルを作成すると、復活します。
```Shell
$ kubectl exec -it nginx-deployment-xxxxxxxxxx-xxxxx -- touch /usr/share/nginx/html/index.html
$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-648fb5bbfd-cpqfm   1/1     Running   0          38m
nginx-deployment-648fb5bbfd-tnjr9   1/1     Running   0          37m
```

## まとめ

ヘルスチェックを利用して、アプリの状態を監視し、状況によって再起動やトラフィックを停止することができます。
ヘルスチェックには2種類あり、監視方法は3種類あります。(バージョンアップで増えたりするのかな？)

ヘルスチェックの種類

| 種類 | 挙動 |
| -- | -- |
| LivenessProbe | 異常終了したときに再起動 |
| ReadinessProbe | 異常終了したときにトラフィックを流さない |

監視方法の種類

| 監視の種類 | 判定方法 |
| -- | -- |
| exec | マンド実行して、終了コードが0以外であれば異常と判定 |
| httpGet | HttpのGetリクエストを出して、200~399が返ってこない場合に異常と判定 |
| tcpSocket | セッションを確立できない場合に異常と判定 |


