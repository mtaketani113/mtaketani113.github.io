---
layout: default
title: Docker、k8sまとめ[第2回]
description: Docker、k8sまとめ。第2回はDocker Desktop for Windowsをインストールして利用します。
category: IT
---

[第1回](/it/container/containerPart01.html)はDocker、k8sの概要と  
なぜ注目されているかを書きました。  
今回は、Docker Desktop for Windowsについて記載します。  
少し実際に使っていこうと思います。

Windowsですので(#^^#)

## 目次

- [Docker Desktop for Windwosの構造](#anchor1)  
- [Docker Desktopのインストール](#anchor2)  
- [イメージ作成](#anchor3)  
- [コンテナ起動](#anchor4)  
- [まとめ](#anchor5)

<a id="anchor1"></a>

## Docker Desktop for Windwosの構造

前回、DockerはLinuxコンテナと言いました。  
Docker Desktop for WindowsはWindowsにインストールします。  

あれ？？  
DockerはホストOSを利用するのでホストOSがWindowsだと動くの？？

実はこんな感じで動いています。(一昔前)

![Docker Desktopの構造](/images/it/container/DockerForWindowsStructure.png)

Hyper-VはWindows PROにしか入っておらず、  
Windwos Homeでは使えませんでした。  
なので古いやつ（Docker Toolbox）で動かさないといけなかったのですが、  
現在はWSL2で利用できるようになったので  
Windows Homeでも利用できるようになりました。

やりますね~

<a id="anchor2"></a>

## Docker Desktopのインストール

ここは他のサイトに任せましょうｗｗ
僕よりずっとよくまとめてくれているところがあるので。

[Docker Desktop for Windows のインストール](https://qiita.com/gahoh/items/7b21377b5c9e3ffddf4a)

ただ、Windows Homeで動かなかった時代のなので、Hyper-Vのあたりは不要だと思います。

<a id="anchor3"></a>

## イメージ作成

では、実際に`nginx`を動かして、以下の画面を出してみましょう。

![nginx](/images/it/container/nginx.png)

一発で実行はできるのですが、イメージとコンテナについて説明するために
2つに分けます。

まず、以下のコマンドでイメージを落としてきます。

```
docker pull nginx:latest
```

イメージというのは、アプリを動かすための  
インストールメディアみたいな感じです。

これはまだ動いていません。

イメージがDocker Hubというところにあり、  
そこからイメージをとってきて！というのが`docker pull`のところです。

Docker Hubの`nginx`から最新(`latest`)をとってきてと書いています。  
`latest`は省略可能かと。

`docker images`で落としてきたnginxのイメージがあることを確認できます。

ベースとなるイメージをDocker Hubから持ってきて  
それを編集して自分用のアプリのイメージを作ることができます。  
そして、それをDocker Hubで公開することもできます。

イメージを作るには、いろいろありますが、  
Dockerfileを使ってやるのが一般的かと思うので別の回で記載します。

<a id="anchor4"></a>

## コンテナ起動

イメージは作ったので次はコンテナを起動して、利用できるようにします。  

コマンドは以下の通りです。

```
docker run --name nginx1 -d -p 8080:80 nginx
```

`docker pull`を実行しなくても、pullも自動でしてくれると思います。  

コマンドの意味ですが、

`docker run`がコンテナを作って起動してね!です。  

`--name nginx1`が名前付けです。後で起動、停止、削除などを簡単にできるようにつけてます。  
つけない場合は、自動で振られるIDを利用します。

`-d`でバックグラウンドで動くようになります。  
つけないと、ずっと動くプロセスなのでコマンド打てなくなるかと。  
なくてもnginxは動きます。

`8080:80`は左の`8080`が外からのポート、`80`がコンテナ内部のポート  
これを紐づけてます。  
ですので`8080`でブラウザからアクセスるとコンテナ内の`80`につないでnginxがつかえる。  
という仕組みです。

ブラウザで`http://localhost:8080/`とアクセスすると、以下の画面が出ると思います。

![nginx](/images/it/container/nginx.png)

作成し、起動しているコンテナは以下のコマンドで確認できます。  
```
docker ps
```  
停止したのもすべて見たい場合は、`-a`オプションをつけて以下です。  
```
docker ps -a
```

コンテナを停止したい場合は、以下です。  
```
docker stop nginx1
```

再度起動したい場合は、以下です。  
```
docker start nginx1
```

`docker run`で指定した条件で起動してくれます。

いっぱいコンテナを作ると、よくわからなくなってくるので削除するには  
```
docker rm nginx1
```
で削除できます。nginx1じゃなくて自動で振られているIDでも削除できます。

イメージを削除するときは以下です。  
```
docker rmi nginx
```
こちらも自動で振られているIDでも削除できます。

<a id="anchor5"></a>

## まとめ

Dockerはイメージを作成して、コンテナを起動して利用します。  
イメージはアプリのインストールメディアみたいなもので、  
コンテナが実際に動くアプリにあたります。

Docker Hubにイメージがいっぱいあるので  
そこから落としてきて、修正してイメージを作り  
コンテナを起動します。

今回は修正はしませんでしたが、今後書いていこうと思います。