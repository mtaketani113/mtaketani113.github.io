---
layout: default
title: Docker、k8sまとめ[第9回] IngressでSticky Session
description: Docker、k8sまとめ。IngressでSticky Sessionを実現したいと思います。
category: container
---

[第8回](/it/container/containerPart08.html)ではIngressを使ってL7ロードバランシングを作りました。

今回はIngressでSticky Sessionを実装しています。

## 目次

- [Sticky Sessionを使う理由](#anchor1)  
- [Ingressから呼び出すアプリの準備](#anchor2)
- [Sticky Sessionの設定](#anchor3)
- [cookieの確認](#anchor4) 
- [まとめ](#anchor5)

<a id="anchor1"></a>

## Sticky Sessionを使う理由

Sticky Sessionとはロードバランシングで複数のPod（サーバ）がある場合、  
cookieを利用して、アクセスするサーバを固定するものです。

[第5回](/it/container/containerPart05.html)であったように、Redisを使ってセッションを分離していればよいのですが、そうでない場合も多いのではないでしょうか。そんな時に使うのが、Sticky Sessionです。

<a id="anchor2"></a>

## Ingressから呼び出すアプリの準備

[第8回](/it/container/containerPart08.html)の前半部と同じです。
Yamlを再度記載しておきます。

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
```

2つPodができるので、それぞれ、中身を変えて作成ください。
- `/usr/share/nginx/html/app1/index.html`
- `/usr/share/nginx/html/app2/index.html`

私は`app1-1`、`app1-2`、`app2-1`、`app2-2`、という感じで登録しました。


<a id="anchor3"></a>

## Sticky Sessionの設定

まずは、設定のYamlを記載します。

```Yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: sample-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/session-cookie-path: /
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "route"
    nginx.ingress.kubernetes.io/session-cookie-hash: "sha1"
spec:
  rules:
  - host: sample.example.com
    http:
      paths:
      - path: /(app1/.*)
        pathType: Prefix
        backend:
          service:
            name: http-service
            port:
              number: 8080
  - host: sample.example.com
    http:
      paths:
      - path: /(app2/.*)
        pathType: Prefix
        backend:
          service:
            name: http-service
            port:
              number: 8080
```

前回との違いは、
annotationsを足しています。
```Yaml
metadata:
  name: sample-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/session-cookie-path: /
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "route"
    nginx.ingress.kubernetes.io/session-cookie-hash: "sha1"
```

また、pathを正規表現にしています。
```Yaml
spec:
  rules:
  - host: sample.example.com
    http:
      paths:
      - path: /(app1/.*)
```

正規表現を使っている場合は
`nginx.ingress.kubernetes.io/session-cookie-path`
を設定する必要があります。

`nginx.ingress.kubernetes.io/rewrite-target`の`$1`は正規表現`/(app1/.*)`
でヒットした1番目という意味です。ここは１つしか書いていないので、パスすべてが取れます。

<a id="anchor4"></a>

## cookieの確認

上記を設定して`http://sample.example.com/app1/`もしくは`http://sample.example.com/app1/`
にアクセスして、Ctrl+F5を繰り返して押してください。

初回画面はキャッシュがかかるせいでうまく出ないかもしれませんが、
繰り返しても別のPodにいかないことがわかると思います。

F12でApplication→Cookiesを見ると、`root`という値でCookieが登録されています。

![Cookie確認](/images/it/container/confirmCookie.png)

これにより、Sticky Sessionを実現できます。

<a id="anchor5"></a>

## まとめ

Sticky Sessionを利用して、アクセスするPod(Server)を固定することができました。
これにより、Statefulなアプリも複数APサーバで実行できます。
利用するには`annotations`を使うだけなので、意外に簡単ですね。

ですが、やはりオートヒーリング、オートスケールにちゃんと対応するため
Redisなどを使ってセッションを永続化するのがよいと思われます。

[第5回](/it/container/containerPart05.html)を参考にしてください。