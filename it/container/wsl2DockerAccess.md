---
layout: default
title: WSL2でのDockerへの他PCからのアクセス
description: WSL2でのDockerへ、他PCからアクセスすると、なぜかアクセスできません。原因は2つでWSL2へのネットワークが流れてないのとファイヤーウォールでした。
category: container
created_at: 2022-02-12
last_modifeid_at: 2022-02-12
---

[Docker Desktopの有料化の対応](/it/container/chargeDockerDesktop.html)で
WSL2を使ってDockerを動かせるようにはなったのですが、
localhostからのアクセス以外、例えば自分自身のPCからでもIPアドレスやPC名などでアクセスできないのです。

他PCからも当然アクセスできませんでした。

調べてみると、原因は2つでWSL2へのネットワークが流れてないのとファイヤーウォールでした。

今回はその解決方法を記載します。

## WSL2へのネットワークが流れてない

`ipconfig`でネットワークの情報を見ると
`イーサネット アダプター vEthernet (WSL):`というのがあるかと思います。

IP受け取っても、こちらに流れないので、起動したDockerが動かないようです。

なので以下のコマンドで、そちらに流れるようにします。

今回はtomcatのポート8080で起動しているとして記載します。ポート番号は各自変更お願いします。

PowerShellを管理者権限で実行してください。
PowerShellを検索して右クリックすると出てくると思います。

```Shell
netsh.exe interface portproxy add v4tov4 listenaddress=${自身のPCのipアドレス} listenport=8080 connectaddress=${ipconfigで調べたWSLのipアドレス} connectport=8080
```

設定確認は以下です。
```
netsh.exe interface portproxy show v4tov4
```

削除しないとローカルでTomcatを動かしたときに流れなくなるので、不要になったら削除です。
```Shell
netsh.exe interface portproxy delete v4tov4 listenport=8080 listenaddress=${自身のPCのipアドレス}
```

これを設定すると、ローカルからIPアドレスやPC名などでアクセスできるようになります。

## FWの開放

他PCからアクセスするにはまだありまして、
それがファイヤーウォールです。

普通であれば、画面から変更できて、特定のPC空などからアクセスを制御できたりするのですが、
環境のためか、なぜか変更できない・・・

そこで緊急回避策です。  
PowerShellで開放できます。

```Shell
New-NetFirewallRule -DisplayName "ALLOW TCP PORT 8080" -Direction inbound -Profile Any -Action Allow -LocalPort 8080 -Protocol TCP
```

終わったら閉じましょう
```Shell
Remove-NetFirewallRule -DisplayName "ALLOW TCP PORT 8080"
```

## まとめ

WSL2でローカルで動かしてテストするだけなら、[Docker Desktopの有料化の対応](/it/container/chargeDockerDesktop.html)で十分ですが、
どこかのPCで動かして、サーバっぽく使うにはもう一工夫必要です。

ECSなどいろいろサービスあるので、そちらを使ったほうがよい気がしますが。