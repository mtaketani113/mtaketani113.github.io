---
layout: default
title: Docker、k8sまとめ[第5回] tomcatのセッションの永続化
description: Docker、k8sまとめ。第5回はredisを使ってtomcatのセッションを永続化します。
category: IT
---

[第4回](/it/container/containerPart04.html)はkubernetesを利用して、  
nginxを動作させました。  
今回はkubernetesを利用して、アプリケーションサーバを並列稼働したときに  
必要になってくるセッション永続化について記載します。

## 目次

- [セッション永続化の必要性](#anchor1)  
- [今回作成する構成](#anchor2)  
- [Redisの作成](#anchor3)  
- [tomcatと連携](#anchor4)  
- [まとめ](#anchor5)

<a id="anchor1"></a>

## セッション永続化の必要性

kubernetesを利用して、アプリケーションサーバを並列稼働した場合、  
アクセスするアプリケーションが変わると、ログイン画面に戻ってしまったりします。

そこでスティッキーセッションを利用して､  
アクセスするユーザー毎にアプリケーションサーバを固定する方法がありますが、  
これだと、スケールアウトだと印ですが、  
スケールインや、サーバ落ちた時のオートヒーリングや  
稼働中のデプロイなどでサーバ自体がなくなると、動作しなくなります。

そこでセッションを別のところに記録しておいて、サーバが変わったら引き出すということをしなくてはなりません。
それを実現するのがRedisというNoSQL(非リレーショナルデータベース)です。

<a id="anchor2"></a>

## 今回作成する構成

今回作成する構成は以下のような感じです。  
APサーバにはtomcatを利用します。

![Redisの構成](/images/it/container/redisStructure.png)

ロードバランサーから直接tomcatに行ってますが、本来であればWebサーバをかますのかな？

<a id="anchor3"></a>

## Redisの作成

せっかくDocker Desktopを利用しているのでRedisをローカルに立ててみましょう。

`docker-compose.yaml`というファイルを作成して、以下のように記載します。

```Yaml
version: '3'
services:
  redis:
    container_name: redis
    image: "redis:latest"
    ports:
      - "6379:6379"
    volumes:
      - "./data/redis:/data"
```

そして、ファイルのあるフォルダに移動して以下のコマンドを実行します。
```
docker-compose up -d
```

docker-composeについてはまた記載します。

```Yaml
    volumes:
      - "./data/redis:/data"
```
ここの部分で、Dockerとローカルのフォルダを紐づけて、  
コンテナがなくなったりしても永続的にデータを保持できるようになります。

コマンドを実行したフォルダの下に`data/redis`というフォルダができていると思います。

これでRedisの起動は完了です。

以下のように実行すると中身を確認できます。
```Shell
>docker exec -it redis /bin/bash
root@930eabd450bd:/data# redis-cli
127.0.0.1:6379> keys *
(empty array)
```

<a id="anchor4"></a>

## tomcatと連携

今回利用するのは以下です。  
[memcached-session-manager](https://github.com/magro/memcached-session-manager/)  
[jedis](https://mvnrepository.com/artifact/redis.clients/jedis/3.0.0)

tomcat9を利用しますので、
まずはそれに必要なファイルをダウンロードします。  
[memcached-session-manager-tc9/](https://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager-tc9/)  


context.xmlに以下を追加したファイルを用意します。

```Xml
<Context>
  ...
  <Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
    memcachedNodes="redis://localhost:6379"
    sticky="false"
    sessionBackupAsync="false"
    lockingMode="auto"
    requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"
    transcoderFactoryClass="de.javakaffee.web.msm.serializer.kryo.KryoTranscoderFactory"
    />
</Context>
```

アプリのDockerfileでイメージを作成します。
上でダウンロードしたファイルと記載を変えたcontext.xml、  
アプリのwarファイルをコピーするようにします。

また、具体的な例は追記していきます。（少し疲れたｗ）

そして以下のyamlファイルを作成して`kubectl apply -f ./xxx.yaml`とすると完成です。

```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment
spec:
  selector:
    matchLabels:
      app: tomcat
  replicas: 2 # 起動するコンテナの数
  template:
    metadata:
      labels:
        app: tomcat
    spec:
      containers:
      - name: tomcat
        image: ${作成したtomcatイメージ}
        ports:
        - containerPort: 8080

---

apiVersion: v1
kind: Service
metadata:
  name: tomcat
spec:
  type: LoadBalancer
  selector:
    app: tomcat
  ports:
    - name: http
      port: 80 
      targetPort: 8080 
```

`kubectl get deployment`と実行すると、起動しているか確認できます。

```Shell
>kubectl get deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
tomcat-deployment   2/2     2            2           8m52s
```

<a id="anchor5"></a>

## まとめ

ステートレスなアプリであれば必要ないですが、  
既存アプリで、並列稼働を考慮していなかったり  
ログインとかでセッション情報を利用していると    
セッション永続化が必要になってきます。

k8sを利用するのであれば、セッション永続化などで  
並列稼働に対応しておいたほうがよいかと思います。