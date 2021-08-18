---
layout: default
title: Docker、k8sまとめ[第3回] Dockerfileの利用
description: Docker、k8sまとめ。第3回はDockerfileを紹介します。
category: container
created_at: 2021-06-05
last_modifeid_at: 2021-06-06
---

[第2回](/it/container/containerPart02.html)はDocker Desktopで実行しましたが、  
今回はDockerfileを紹介しようと思います。

## 目次

- [Dockerfileとは](#anchor1)  
- [Dockerfileを書いてみよう](#anchor2)  
- [まとめ](#anchor3)

<a id="anchor1"></a>

## Dockerfileとは

前回、Docker Hubからnginxを落としてきてそのまま利用しましたが、  
実際は、公開されているものをそのまま動かすというより、  
そこに何らかの変更を加えて、イメージを作りコンテナとして動かします。

<a id="anchor2"></a>

## Dockerfileを書いてみよう

まず、ファイル名ですが`Dockerfile`にします。  
こうしていおくと`docker`コマンドでデフォルトで読み込んでくれます。  
これ以外の名前だと`-f ファイル名`のオプションが必要です。

では、前回使った`nginx`に`403.html`を足したイメージを作ってみましょう。

まず、403.htmlを作ります。

```html:403.html
<html><head>
<title>Forbidden</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Forbidden</h1>
<p>403 Forbidden</p>
</body></html>
```

`403.html`ファイルと同じところにDockerfileを作成し、以下のように書きます。

```Dockerfile
FROM nginx:latest
ADD ./403.html /usr/share/nginx/html
```

`FROM nginx:latest`が元なるイメージです。  
そこに`ADD ./403.html /usr/share/nginx/html`で  
ファイルを追加します。

`ENTRYPOINT`や`CMD`で実行するコマンドを記載するのですが、  
今回は作成元のnginxに書かれているので省略します。

これで`Dockerfile`のあるフォルダで以下を実行すると、イメージを作成できます  

```
docker build -t nginx403:latest
```
こんな風に出たら成功です。
```
docker build -t nginx403:latest .
[+] Building 1.8s (7/7) FINISHED
 => [internal] load build definition from Dockerfile                                                               0.1s
 => => transferring dockerfile: 92B                                                                                0.0s
 => [internal] load .dockerignore                                                                                  0.1s
 => => transferring context: 2B                                                                                    0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                    0.0s
 => [internal] load build context                                                                                  0.4s
 => => transferring context: 297B                                                                                  0.0s
 => [1/2] FROM docker.io/library/nginx:latest                                                                      0.9s
 => [2/2] ADD ./403.html /usr/share/nginx/html                                                                     0.2s
 => exporting to image                                                                                             0.2s
 => => exporting layers                                                                                            0.1s
 => => writing image sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx                       0.0s
 => => naming to docker.io/library/nginx403:latest                                                                 0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

`docker images`でイメージ`nginx403`ができていることを確認しましょう。

次にコンテナを作って実行しましょう。

```
docker run --name nginx1 -d -p 8080:80 nginx403
```

`http://localhost:8080/403.html`にアクセスアクセスると、  
作成した`403.html`を参照できると思います。

<a id="anchor3"></a>

## まとめ

Dockerfileを利用すると、  
Docker Hubなどのコンテナイメージのリポジトリーから元を落としてきて、  
変更をかけて地震のアプリを作ることができます。

ここで紹介したのは、ほんの基礎なので、  
また色々書いていこうと思います。
