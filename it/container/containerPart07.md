---
layout: default
title: Docker、k8sまとめ[第7回] docker-composeの利用
description: Docker、k8sまとめ。docker-composeによる、複数コンテナの連携について記載します。
category: IT
---

[第6回](/it/container/containerPart06.html)ではk8sによるデプロイの管理について記載しました。

今までは、1つのコンテナでアプリで動かしてきましたが、複数のコンテナでアプリを動かしていこうと思います。  
まずはdocker-composeを利用した、複数コンテナの連携について記載します。

## 目次

- [docker-composeとは](#anchor1)  
- [docker-composeでWordPressを実行](#anchor2)  
- [停止、起動とコンテナへのログイン](#anchor3)  
- [まとめ](#anchor4)  

<a id="anchor1"></a>

## docker-composeとは

docker-composeは、複数のコンテナを連携してサービスを実行するコマンドで、
docker-compose.yamlに構成を記載すると実行できます。  
dockerコマンドを簡略化してくれます。

1つのコンテナで全部動かせばいいというのを考えてしまいますが  
コンテナは1つ1プロセスが原則です。

お互いの影響をなくせたり、コンテナ自体を小さくできますので。

<a id="anchor2"></a>

## docker-composeでWordPressを実行

以下にWordPressのクイックスタートの方法が書いてるのでこれを使って少し追加して説明しようと思います。

[クィックスタート: Compose と WordPress](https://docs.docker.jp/compose/wordpress.html)

上記のリンクに書いてあるyamlに`network`というのを足しています。
デフォルトで設定されるので書かなくても良いのですが、説明のために足しました。
```Yaml
version: '3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     networks:
       - default
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     networks:
       - default
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data:
networks:
  default:
    driver: bridge
```

dockerではこのnetworkというので、複数のコンテナを名前で連携することができます。   
`wordpress`から見て`db`という名前でアクセスしてますね。

今回はWordPressとDBだけなので1つだけですが  
たとえばWEB-AP-DBのアプリだと、  
WEB-APとAP-DBはつながる必要はあるが、WEB-DBはつながる必要がない場合など、
2つのnetworkに分けることもできます。

上記のYamlを`docker-compose.yaml`というファイルにして、  
ファイルのあるフォルダで、以下のコマンドを実行すると、起動できます。

```
docker-compose up -d
```

コマンドの実行が完了したら、`http://localhost:8000/`でWordPressのサイトが見えると思います。

<a id="anchor3"></a>

## 停止、起動とコンテナへのログイン

起動したコンテナを停止するには、以下のコマンドです。
```
docker-compose stop
```

このコマンドは`docker-compose.yaml`で実行する必要があります。  
どれを止めていいのかわからないですかね？

また、再度起動するときは以下のコマンドです。
```
docker-compose start
```
upだとキャッシュを使いますが、再度イメージ作成からになるのかな？

また、コンテナへのログインも簡単で以下でそれぞれログインできます。
```Shell
# wordpressへのログイン
$ docker-compose exec wordpress /bin/bash
root@15e44a642e10:/var/www/html#
# dbへのログイン
$ docker-compose exec db /bin/bash
root@deaa85a2906f:/#
```

<a id="anchor4"></a>

## まとめ

docker-composeを利用すると複数のコンテナを連携して実行できます。  
コンテナは1つ1プロセスが原則で、WEB、AP、DBなどを別々のコンテナで動かして、  
連携させる必要があります。