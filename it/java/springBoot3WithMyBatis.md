---
layout: default
title: 開発中のSpringBoot3でMyBatis利用
description: SpringBoot3は2022年11月リリース予定で現在2022年8月時点では開発中のものを利用できます。試しで利用しているのですが、MyBatisと連携したかったのですが、こちらも開発中でSnapShotを利用しないと利用できません。その方法を記載します。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-08-06
last_modifeid_at: 2022-08-06
---

SpringBoot3は2022年11月リリース予定で現在2022年8月時点では開発中のものを利用できます。

試しで利用しているのですが、MyBatisと連携したかったのですが、こちらも開発中でSnapShotを利用しないと利用できません。

その利用方法を記載します。

## 開発中のSpringBoot3の利用

2022年8月時点では3.0.0-M4を利用できます。
pom.xmlで以下のようにすると使えます。

```Xml
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.0.0-M4</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
```

VSCodeなどでSpring Boot Extention Packなどを利用すれば、3.0.0-M4も利用できます。
そういったので作成するのが確実化と思います。

僕はこちらのサイトを参考に作成しました。

[【簡単】VisualStudioCodeでSpringBoot開発環境を構築する方法](https://blackbird-blog.com/vsc-springboot-start)


## SnapShotのあるリポジトリの追加

SpringBootであれば、`mybatis-spring-boot-starter`を利用すれば、MyBatisを簡単に使えるのですが、
MyBatisのサイトを見ると以下のように書いています。

「master(2.3.x) : MyBatis 3.5+, MyBatis-Spring 2.1, Java 8+ and Spring Boot 2.5+ (First version for supporting Spring Boot 3), not release yet」

2.3以降がSoringBoot3で利用できるけどまだリリースしてませんと。

ただ、こちらにSnapShotがあるようです。

[2.3.0-SNAPSHOT](https://oss.sonatype.org/content/repositories/snapshots/org/mybatis/spring/boot/mybatis-spring-boot/2.3.0-SNAPSHOT/)

これを利用してみましょう。

## MyBatisの利用

まず、Pom.xmlのrepositorysタグに以下を追加します。

```Xml
<repository>
	<id>sonatype-nexus-snapshots</id>
    <name>Sonatype Nexus Snapshots</name>
	<url>https://oss.sonatype.org/content/repositories/snapshots/</url>
</repository>
```

そして依存関係(Pom.xmlのdependenciesタグ)に以下を追加します。

```Xml
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>2.3.0-SNAPSHOT</version>
</dependency>
```

後は以下のサイトを参考にプログラムを書けば、完了です。

[Spring Boot + MyBatisでデータベースに接続する方法](https://medium-company.com/spring-boot-mybatis/)

## まとめ

SpringBoot3はまだ開発中で2022年11月リリース予定です。事前検証などでMyBatisとの連携を試すときはSnapShotを利用して試してみましょう。