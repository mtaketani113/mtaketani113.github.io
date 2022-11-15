---
layout: default
title: mavenのdeployで422
description: githubにmvn deployでPackageにデプロイする際、422がでました。今回の原因はartifactIdに大文字が入っているからで、小文字に変えることで解決しました。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-02-27
last_modifeid_at: 2022-02-27
---

今回は、Githubのmavenリポジトリーにmvn deployを使ってアップしました。

しかし、あるリポジトリはできるのにあるリポジトリは
`422 Unprocessable Entity`といったエラーができました。


## 422 Unprocessable Entityの原因

今回の原因は簡単で、pom.xmlの最初に以下のようなのを記載しますが、
artifactIdに**大文字**を使ってはいけないようです。

```Xml
  <groupId>xxxx</groupId>
  <artifactId>yyyyy</artifactId>
```

mvn installなどはうまくいくのですが、deployはなぜかうまくいかないよう・・・
知らなかった。

今回の話はここで終わってしまうので、
GithubのPackageを利用する方法を追記します。

## ~/.m2/settings.xmlにGithubの情報を追加

設定の方法は以下にまとまっています。
[Github Package](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-apache-maven-registry)

簡単に書くと以下のような感じです。

.m2/settings.xmlの
repositoriesの中に以下を記載します

OWNERのところがリポジトリの所有者、REPOSITORYのところを対象としたいリポジトリ
とする必要があります。

リポジトリが複数あって定義がめんどくさい場合は「OWNER/*」で全部とすることもできるようです。

```Xml
<repository>
  <id>github</id>
  <url>https://maven.pkg.github.com/OWNER/REPOSITORY</url>
  <snapshots>
  <enabled>true</enabled>
  </snapshots>
</repository>
```

Settingsタグに以下を追加します。

USERNAMEは追加したリポジトリに権限があるGithubのアカウント
TOKENには[GitHubのパスワードによるアクセス拒否](/it/github/githubPasswordDenied.html#%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%83%88%E3%83%BC%E3%82%AF%E3%83%B3%E3%81%AE%E4%BD%9C%E6%88%90%E6%96%B9%E6%B3%95)で紹介したPersonal access tokensで作成した値を登録します。

```Xml
<servers>
  <server>
    <id>github</id>
    <username>USERNAME</username>
    <password>TOKEN</password>
  </server>
</servers>
```

## pom.xmlにデプロイ情報を追加

pom.xmlをOWNERとREPOSITORYにはそれぞれリポジトリの所有者とリポジトリ名を追記します。

```Xml
<distributionManagement>
   <repository>
     <id>github</id>
     <name>GitHub OWNER Apache Maven Packages</name>
     <url>https://maven.pkg.github.com/OWNER/REPOSITORY</url>
   </repository>
</distributionManagement>
```

あとは`mvn deploy`とすればPackagesにデプロイされて、
他リポジトリから`dependence`に記載すれば利用できるようになります。

なんか便利

## まとめ

mvn deployで`422 Unprocessable Entity`が発生する原因の1つは**artifactIdに大文字**を使っているからのようです。mvn installとかは使えるのに、若干はめですよねｗ小文字に変えて対応しましょう。

githugのPackageを使うことでmavenのリポジトリとして使えます。
便利なので利用してみてはどうでしょうか。


