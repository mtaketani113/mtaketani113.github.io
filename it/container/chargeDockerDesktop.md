---
layout: default
title: Docker Desktopの有料化の対応
description: Docker Desktopが8月31日から従業員数250名以上または年間売上高1000万ドル以上の企業の場合、有料化されます。対策としてWSL2を利用してDockerを動かす方法が色々出ていますが、会社環境で動かしたときに、はまったことを記載します。
category: container
created_at: 2021-10-02
last_modifeid_at: 2021-10-02
---

Docker Desktopが8月31日から従業員数250名以上または年間売上高1000万ドル以上の企業の場合、有料化されます。

対策としてWSL2を利用してDockerを動かす方法が色々出ています。

[Docker Desktopに依存しない、WindowsでのDocker環境](https://qiita.com/ohtsuka1317/items/617a865b8a9d4fb67989)

ですが、会社環境で動かしたときにいろいろと、はまったことを記載します。

## WSL2へのアップデート

大きな会社だとPCのバージョンが古いことがあり、
そのためかWSL2ではなくWSLのままの場合があります。

僕の会社もそうでした。なのでWSL2へのアップデートが必要です。

こちらのサイトが参考になるかと思います。

[WSL1からWSL2へバージョンアップ・変換する方法](https://loumo.jp/archives/25294)

## OSのproxyの設定

会社にはproxyがあることが多いと思います。
特に大企業とかになると。このproxyの設定を入れる必要があります。

まず、wslを起動してログインしたときにproxyの環境変数を設定するようにする必要があります。

`.bashrc`の末尾に以下のようにproxyの情報を追加します。

```Shell
export http_proxy="http://xxxx"
export https_proxy="http://xxxx"
```

そしてログインしなおすと完了です。

しかし、`sudo`を利用するので、`visudo`でproxyの設定をするか、オプションとして`-E`を付けることでログインユーザの環境変数を引き継ぐ必要があります。

↓`-E`を付ける場合はこんな感じです。  
`sudo -E apt-get update`

`visudo`はこちらのサイトを参考にするとよいと思います。

[proxy 越しに sudo を使う](https://kokufu.blogspot.com/2012/05/proxy-sudo.html)

## DockerデーモンへのProxy設定

上記まででインストールはできますが、
`sudo service docker start`で
Dockerのデーモンを起動しても`hello world`をうまく動かせません。

こちらが参考になると思います。

[プロキシーサーバー利用の設定](https://matsuand.github.io/docs.docker.jp.onthefly/network/proxy/)

フォルダの場所は僕の場合`/etc`の下になってました。

## まとめ

会社でDockerを利用すると、やはりProxyやPCのバージョンが古いなどが問題になってきますね。

あまり記載してくれてるサイトも少ないので、皆さんがこちらを見て助けになればと思います。
