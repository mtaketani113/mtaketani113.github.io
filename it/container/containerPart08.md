---
layout: default
title: Docker、k8sまとめ[第8回] IngressによるL7ロードバランシング
description: Docker、k8sまとめ。docker-composeによる、複数コンテナの連携IngressによるL7ロードバランシングについて記載します。
category: IT
---

[第7回](/it/container/containerPart07.html)ではdocker-composeを利用した、複数コンテナの連携について記載しました。

今回はIngress Controllerを利用したL7ロードバランシングについて記載します。  
バーチャルホストを利用できて、パスベースのロードバランシングやSSL(TLS)による暗号化などができます。

## 目次

- [Docker Desktop でIngressを利用する準備](#anchor1)  
- [Ingress Controllerの起動](#anchor2)
- [ファイルの配置](#anchor3) 
- [Hostsの登録](#anchor4)  
- [まとめ](#anchor4)  

<a id="anchor1"></a>

## Docker Desktop でIngressを利用する準備

Docker Desktopで利用するには、ingress-nginx controllerをインストールする必要です。
まー簡単で、次のコマンドを実行するだけです。

```Shell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml
```

以下で、ingress-nginxのnamespaceにインストールされていることが確認できると思います。
```Shell
$ kubectl get deployment -n ingress-nginx
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
ingress-nginx-controller   1/1     1            1           101m
```

これを見つけるのに半日くらいかかった・・・

<a id="anchor2"></a>

## Ingress Controllerの起動

以下のYamlを適用するだけでよいです。  
日本語が入っているのでUTF-8で保存してください。


```Yaml
# deploymentで2つのPodを作成
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
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
        image: nginx:1.13.12
        ports:
        - name: http-port
          containerPort: 80

---
# NodePortのServiceでPodに分散
apiVersion: v1
kind: Service
metadata:
  name: http-service
  labels:
    app: nginx
spec:
  type: NodePort
  ports:
  - port: 8080
    targetPort: http-port
  selector:
    app: nginx

---
# Ingress Controllerでhttp://sample.example.com/app1 と /app2でアクセスできるようする。
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: sample-ingress
spec:
  rules:
  - host: sample.example.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: http-service
            port:
              number: 8080
  - host: sample.example.com
    http:
      paths:
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: http-service
            port:
              number: 8080
```

以下のようにあると成功です。
```Shell
# READYが2/2になっている
$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           77m
```

```Shell
# ADDRESSがlocalhostになっている
$ kubectl get ingress
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
NAME             CLASS    HOSTS                                   ADDRESS     PORTS   AGE
sample-ingress   <none>   sample.example.com,sample.example.com   localhost   80      77m
```

Ingressですが、Beta版から`networking.k8s.io/v1`に変わったようです。
正式になったということかな？

そのさい、serviceの指定方法やportの指定方法が階層構造に代わっています。
apiVersionがBeta版のサイトを使ってもそのままは使えないので気を付けてください。

<a id="anchor3"></a>

## ファイルの配置

Pod名を指定して、以下のコマンドでログインして、以下のファイルを作ります。
- `/usr/share/nginx/html/app1/index.html`
- `/usr/share/nginx/html/app2/index.html`


```
kubectl exec --stdin --tty nginx-deployment-xxxxxxxxx -- /bin/bash
```

`echo app1-1 > index.html`などで文字を入れるとわかりやすいかも。

また、2つPodがあると思うので、2つとも実施してください。

こういう時Volumeとか使えば楽かな？Podが書き換わったら消えますし。


<a id="anchor4"></a>

## Hostsの登録

Docker Desktopでは、とくにDNSとかいるわけではないので、  
`C:\Windows\System32\drivers\etc\hosts`に以下を書きましょう。

`127.0.0.1     sample.example.com`

もちろん管理者権限で。

そのうえで、`http://sample.example.com/app1/`や`http://sample.example.com/app2/`
にアクセスすると「ファイルの配置」で記載した文字が出ると思います。

Ingressのhostで指定した`sample.example.com`、pathで指定した`app1`、`app2`でアクセスすると
Podにアクセスするようになっています。

試しに、同じIPになる`http://localhost/app2/`でアクセスしても404になると思います。

このようにホスト名とパスでロードバランスができるようになります。  
Sticky Sessionとかも使えるのかな？

<a id="anchor5"></a>

## まとめ

Docker DesktopでIngressを利用する方法を記載しました。

Ingressを利用することで、バーチャルホストを利用できて、パスベースのロードバランシングができます。
IngressはBeta版から変わったようで、書き方が少し変わっているので気を付けてください。  
エラーの内容見れば予測できると思います。