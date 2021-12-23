---
layout: default
title: Jconsoleでのリモートの監視
description: Spring Bootで起動したアプリやTomcatで起動したアプリをJconsoleで監視する方法を記載します。
category: java
created_at: 2021-12-24
last_modifeid_at: 2021-12-24
---

JavaのプロセスのCPUやメモリ使用量などを監視するツールにJconsoleというのがあります。

JavaのJDKをインストールしているば、コマンドプロンプトで`jconsole`と記載すると起動します。(JREでもできるかな？)

![Jconsole Start menue](/images/it/java/JconsoleStart.png)

ローカルで起動しているJavaのプロセスが「ローカル・プロセス」に出てきます。監視したいプロセスを選択して接続すると表示されます。

(通信が保護されていないので警告が出ます。)

![Jconsole Graph](/images/it/java/JconsoleGraph.png)

本題の、Spring Bootで起動しているプロセスを監視ですが、  
サーバ上で動かしているので「リモート・プロセス」の方でつなぎます。

しかし、それには準備が必要で、Spring Bootの起動時にJconsole空のアクセスを許可するようにする必要があります。

具体的には、以下のオプションをJavaの起動時時に追加する必要があります。
**改行せず**につける必要があります。

```Shell
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.port={接続したいポート}
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false
```

テストで接続するようなのでSSLも認証も設定していません。
実際に本番で使うのであればこのあたりの設定が必要でしょう。

このオプションを追加して起動し、
「リモート・プロセス」で使用方法に書いてある通り
`ホストネーム:指定したポート番号`を入力します。

ユーザ名とパスワードは`-Dcom.sun.management.jmxremote.authenticate=false`なので空です。

これで「接続」をクリックすると監視できるようになります。

Tomcatで起動しているアプリの場合、Linuxの場合はsetenv.shに
以下を入力して起動すると同様に監視できるようになります。

```Shell
export JAVA__OPTS="-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port={接続したいポート} -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false"
```

## まとめ

JconsoleでリモートのJavaプロセスを監視できるようになります。

自分はよく負荷テストなどしたときにメモリなどを監視するときに使っていました。また、原因不明のOut of Memoryのエラーが出る時の調査に使ったことも。

意外に使える子だと思うので、利用してみてください。