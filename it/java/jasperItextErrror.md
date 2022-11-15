---
layout: default
title: jasperReportsが急に動かなくなる
description: jasperReposrtsがmavenで急にエラーが出て動かなくなりました。itextの依存関係のせいなのですが、その詳細を記載します。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2021-06-09
last_modifeid_at: 2021-06-13
---

jasperReportsの6.3.1を利用していたのですが、6月になって急にmavenで利用できなくなりました。

`could not find artifact com.lowagie:itext:jar:2.1.7.js5`  
や  
`Conflict`などのエラーが出るようになりました。


[jasperreports 6.3.1](https://mvnrepository.com/artifact/net.sf.jasperreports/jasperreports/6.3.1)
の依存関係には`itext.2.1.7.js5.jar`があるのですが、どうもこれが2021年の6月くらいから  
mavenのデフォルトのリポジトリ"http://repo1.maven.org/maven2/"で利用できなくなったからのようです。  
確かではないのですが、参照しているURLをたどっても404になっているのを見ると、そのように推察できます。

なので`setting.xml`の`repositories`に以下をのリポジトリを足さないといけないようです。
```XML
<repository>
  <id>jaspersoft-third-party</id>
  <url>https://jaspersoft.jfrog.io/jaspersoft/third-party-ce-artifacts/</url>
</repository>
```

急に動かなくなるとびっくりしますよね。
しかも、conflictとか意味不明なエラーが出ると、はい？？ってなります。

ちなみに`itext.2.1.7.jsX.jar`はjasperreprots用にカスタマイズされたitextらしいです。

やっぱり、世の中変わっていくので、  
自分たちも柔軟に変わっていかないといけないですね。