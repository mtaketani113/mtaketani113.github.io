---
layout: default
title: Java6,7のlog4jの脆弱性対応
description: log4j2の最新版はJava8以降でないといけません。なのでJavaのバージョンアップが必要になる可能性があります。Githubのlog4j2のリポジトリを見てると、log4j-2.3.1-rc1や2.12.2が出てきています。どうやら古いJavaのバージョンのために作ってくれているようです。
category: java
created_at: 2021-12-22
last_modifeid_at: 2021-12-22
---

log4j2の最新版はJava8以降でないといけません。なのでJavaのバージョンアップが必要になる可能性があります。

各バージョンは以下のようになっています。

|  log4jのバージョン |  Javaのバージョン  |
| ---- | ---- |
| log4j-2.3 | 6以降
| log4j-2.4 ～ 2.12.1 | 7以降 |
| log4j-2.13.0 以降  | 8以降  |

なので2.12.1以前のものを使っている最新に上げるにはJava8へのアップデートが必要です。

2021年12月22日時点で
[2.12.2](https://github.com/apache/logging-log4j2/releases/tag/rel%2F2.12.2)と[2.3.1-rc1](https://github.com/apache/logging-log4j2/releases/tag/log4j-2.3.1-rc1)
のタグが作られていました。

[2.12.2のリリースノート](https://github.com/apache/logging-log4j2/blob/rel/2.12.2/RELEASE-NOTES.md)を見るとJava7の人のために作ったと書いてます。


[2.3.1-rc1のリリースノート](https://github.com/apache/logging-log4j2/blob/log4j-2.3.1-rc1/RELEASE-NOTES.txt)にも`LOG4J2-3198`が対応されていると書いてるので、Java6を使ってる人のためにやってくれたみたいですね。

まだrc(出荷候補)がついてますが、つよつよエンジニアさんたちが頑張ってくれてるんですね(>_<)

## まとめ

Java6,7はクラスを抜いたバージョンを作ってデプロイとかしなくてはいけませんでしたが、2.12.2や2.3.1を利用するとよいかもしれません。

まだまだ情報がいろいろ飛んでるので、わかりませんが、2021年12月22日の時点の話です。私のようなへっぽこのエンジニアの話だけではなく、いろいろ情報を集めて判断していく必要がありそうです。