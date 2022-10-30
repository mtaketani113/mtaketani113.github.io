---
layout: default
title: GradeleでJava19のビルド
description: GradeleではJava19は、まだサポートしてませんが、22年10月に7.6-rc-1が出て正式ではないですが可能になりました。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-10-30
last_modifeid_at: 2022-10-30
---

GradeleではJava19は、まだサポートしてませんが、22年10月に7.6-rc-1が出て正式ではないですが可能になりました。
[7.6.0-M1](https://github.com/gradle/gradle/releases/tag/v7.6.0-M1)の時に少し試したのですが、GithubActionsで動かなかったです。今回試しましたが、RC1では動作しました。
ですがので、適用方法を記載します。

今回の紹介は[7.6-rc-1](https://github.com/gradle/gradle/releases/tag/v7.6.0-RC1)です。

## gradleのダウンロード

gradleの7.6RC1のダウンロードのリンクは、
正式サイトを探したのですが見つかりませんでした。まだリンクを付けてないのかRCだから付けないのかわからないですが
リンクは[こちら](https://services.gradle.org/distributions/gradle-7.6-rc-1-bin.zip)です。

後は展開して環境変数を修正すれば利用可能です。

## gradlew(Gradle Wra)pper)のアップデート

以下のコマンドで簡単にできます。
`./gradlew wrapper --gradle-version=7.6-rc-1`

## まとめ

GradleでJava19をバージョンはRCではありますが、利用できるようになりました。
また、正式版が出たらそちらに変えます。

今回の記事が参考になればと思います。