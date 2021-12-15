---
layout: default
title: log4j-serverがなくなってる
description: log4j2の脆弱性でバージョンアップを迫られていると思います。バージョン2.9.0からlog4j-serverが別リポジトリに移ってしまっているようです。調べて分かったことなどを記載します。
category: java
created_at: 2021-12-15
last_modifeid_at: 2021-12-15
---

log4j2の脆弱性で大変なことになっていますね。

そういっている私も対応しなくてはならず、バージョンを単純に上げようかと思ったのですが、
バージョンを上げるとコンパイルが通らなくなってしまいました。

原因は、`log4j-server`のクラスを使っていて、それがlog4j2のバージョン2.9.0からなくなっているためのようです。


その経緯は以下のようです。

以下のコミットで同じリポジトリのlog4j-serverに移っています。  
[log4j-serverのフォルダ移動](https://github.com/apache/logging-log4j2/commit/f515fa3c4ab8ecca83f75bcb29ceb71b54c61bc4)

そして、以下のコミットでlog4j-serverが削除されてます・・・  
[log4j-serverのリポジトリ移動](https://github.com/apache/logging-log4j2/commit/8865124fb11a3a9d39ea8900e6469e491d26bc22)

そして行先は、こちらのよう  
[logging-log4j-tools](https://github.com/apache/logging-log4j-tools)

なんと、mavenのリポジトリにもいないし、tagとかもない。

リリースのブランチがあるだけ。  
https://github.com/apache/logging-log4j-tools/tree/release-2.x

仕方がないので、cloneしてjarを作ろうとしたけど、エラーになる・・・

どうも以下を編集しないといけないようです。

[log-serverのpom.xml](https://github.com/apache/logging-log4j-tools/blob/release-2.x/pom.xml)

バージョン1.14.1のままにするなら、toolsを消してSNAPSHOTを消せば通りました。
```xml
    <artifactId>log4j-tools</artifactId>
    <version>2.14.1-SNAPSHOT</version>
    ↓
    <artifactId>log4j</artifactId>
    <version>2.14.1</version>
```

バージョン2.15.0にすると2か所修正
toolsを消してバージョンを修正
```xml
    <artifactId>log4j-tools</artifactId>
    <version>2.14.1-SNAPSHOT</version>
    ↓
    <artifactId>log4j</artifactId>
    <version>2.15.0</version>
```

hamcrest-allのバージョンを追加
```xml
      <dependency>
        <groupId>org.hamcrest</groupId>
        <artifactId>hamcrest-all</artifactId>
        <scope>test</scope>
      </dependency>
      ↓
      <dependency>
        <groupId>org.hamcrest</groupId>
        <artifactId>hamcrest-all</artifactId>
        <version>1.3</version>
        <scope>test</scope>
      </dependency>
```

これでpom.xmlでlog4j-serverのjarファイルを作成できます。

しかし、パッケージ名が変わっていて`core`がなくなっています。  

`org.apache.logging.log4j.core.server`  
↓  
`org.apache.logging.log4j.server`

また、前までなかった投げてなかった検査例外も投げているようです。  


## まとめ

log4j-serverが2.9.0から別のリポジトリに移されています。

log4jのバージョンアップが必要とされていますが、利用しているバージョンやクラスによっては単純ではなさそうです。

バージョンアップできない場合に、設定変更やクラスを抜く方法が紹介されています。
そういったのも検討する必要がありそうです。