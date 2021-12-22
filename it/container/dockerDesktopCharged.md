---
layout: default
title: Docker Desktop のWSL2の代替環境をProxy環境下で
description: Docker Desktop のが規模の大きい会社だと有料化するため、代替環境をWSL2で作りました。しかし、proxy環境下であることや、バージョンが古いことなどからいくつか、つまづいたので記載します。
category: container
---

Docker Desktop のが規模の大きい会社だと有料化するため、代替環境をWSL2で作りました。しかし、Proxy環境下であることや、バージョンが古いことなどからいくつか、つまづいたので記載します。

色々サイトはありますが、参考にしたのは以下のサイトです。

[Windows 10 + WSL 2 でDocker環境を構築する（Docker Desktop有料化対策）](https://blog.ecbeing.tech/entry/2021/09/07/150000)

## WSL2にアップデート

自分の使っていたパソコンはWSL2ではなくWSL「1」でした・・・
色々設定して最後の最後で動かなくてなんでかと調べてたらバージョン古いことに気づきました。

後からでもアップデートできるようで、アップデートは以下の手順です。

[WSL1からWSL2へバージョンアップ・変換する方法](https://loumo.jp/archives/25294)

少し時間はかかりましたが、何とかアップデートに成功しましたｗｗ

## 環境変数にproxyの設定

作成手順に`sudo apt-get xxxx`などありますが、
会社環境だとProxy経由のことが多いので環境変数にまず、proxyの設定をします。

コマンドならこんな感じ。
```Shell
$ export https_proxy="http://xxxxxx/"
$ export http_prox=‘‘"http://xxxxxx/"
$ export ftp_proxy="ttp://xxxxxx/"
```

ログイン時に設定するのがめんどくさいので`~/.bash_profile`に記載するとよいかと。

そして`sudo`で環境変数を引き継ぐために、手っ取り早くやるなら
`sudo -E apt-get xxxx`として`-E`のオプションを付けると環境変数が引き継がれます。

`visudo`でProxyの設定するほうがいいと思いますが、`visudo`が`vi`と動きが違うので意外に使いづらかったｗ

↓参考にしたサイト  
[【Proxyの外に出れない僕達のために】Proxy 環境下で sudo add-apt-repository する方法【と、sudoとhttp_proxyのなんやかんや】](https://qiita.com/katoken-0215/items/18f9b8553f8ad4117d79#%E4%BB%96%E3%81%AE%E4%BA%BA%E3%81%AE%E8%A7%A3%E7%AE%A1%E7%90%86%E8%80%85%E3%82%A2%E3%82%AB%E3%82%A6%E3%83%B3%E3%83%88%E3%81%8C%E3%81%82%E3%82%8B%E5%A0%B4%E5%90%88)

## DockerのProxy設定

最後にDockerにProxyの設定をします。

`/etc/default/docker`だったかな？
`export http_proxy="http://xxxxxx/"`など記載して起動すればOKです。


## docker-composeのインストール

ついでにdocker-composeも入れておきましょう。

`sudo -E apt install docker-compose`

でいけますかね。

## まとめ

ほぼほぼProxyの設定でしたｗ
やっぱりProxyでつまづきますね。