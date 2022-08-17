---
layout: default
title: 開発中のSpringBoot3でMyBatis利用 その2
description: 開発中のSpringBoot3でMyBatisを利用する方法を書きましたが、実施にプログラムを書いて動かしてみました。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-08-15
last_modifeid_at: 2022-08-17
---

[前回](https://mtaketani113.github.io/it/java/springBoot3WithMyBatis.html)
、開発中のSpringBoot3でMyBatisを利用する方法を書きましたが、実施にプログラムを書いて動かしてみました。

以下がサンプルです。せっかくなのでGradleを利用してみました。

[mybatisDemoForSpringboot3](https://github.com/mtaketani113/mybatisDemoForSpringboot3/tree/0.0.2)

## srcのフォルダ構成
srcのフォルダは適当ですが以下のようにしています。

```Java
src
  ├─main
  │  ├─java
  │  │  └─com
  │  │      └─example
  │  │          └─demo
  │  │              │  DemoApplication.java
  │  │              │
  │  │              ├─controller
  │  │              │      CustomerController.java
  │  │              │
  │  │              ├─model
  │  │              │      Customer.java
  │  │              │
  │  │              ├─repository
  │  │              │      CustomerMapper.java
  │  │              │
  │  │              └─service
  │  │                      CustomerService.java
  │  │
  │  └─resources
  │      │  application.properties
  │      │
  │      ├─com
  │      │  └─example
  │      │      └─demo
  │      │          └─repository
  │      │                  CustomerMapper.xml
  │      │
  │      ├─static
  │      └─templates
  │              customer.html
  │
  └─test
      └─java
          └─com
              └─example
                  └─demo
                          DemoApplicationTests.java
```

## build.gradle

build.gradleは以下の通りです。
SpringBootは8月時点の`3.0.0-M4`をMyBatisは`2.3.0-SNAPSHOT`を利用しています。

```Groovy
plugins {
	id 'org.springframework.boot' version '3.0.0-M4'
	id 'io.spring.dependency-management' version '1.0.12.RELEASE'
	id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '18'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
	maven { url 'https://repo.spring.io/milestone' }
	maven { url 'https://oss.sonatype.org/content/repositories/snapshots' }
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.3.0-SNAPSHOT'
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	runtimeOnly 'mysql:mysql-connector-java'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}
```

VSCodeなどでSpring Boot Extention Packなどを利用すれば、3.0.0-M4も利用できます。
そういったので作成するのが確実かと思います。

僕はこちらのサイトを参考に作成しました。

[【簡単】VisualStudioCodeでSpringBoot開発環境を構築する方法](https://blackbird-blog.com/vsc-springboot-start)

## application.properties

データベース(MySQL)の接続情報と、MyBatisのスネークケースからキャメルケースへの自動変換する設定、
`mybatis.configuration.map-underscore-to-camel-case=true`を追加しています。

```Java
spring.datasource.url=${DATASOURCE_URL:jdbc:mysql://localhost:3306/demo}
spring.datasource.username=${DATASOURCE_USERNAME:demo}
spring.datasource.password=${DATASOURCE_PASSWORD:demo}
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
mybatis.configuration.map-underscore-to-camel-case=true
```

## Mapper

SpringBootでMybatisを利用すると`@Mapper`と書いて
resourceの同一フォルダにXMLファイルを配置すると勝手に呼んでくれます。

なんと便利。

```Java
package com.example.demo.repository;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;

import com.example.demo.model.Customer;

@Mapper
public interface CustomerMapper {

    List<Customer> searchAllCustomer();
}
```

```Xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo.repository.CustomerMapper">
    <select id="searchAllCustomer" resultType="com.example.demo.model.Customer">
        SELECT * FROM customer ORDER BY id
    </select>
</mapper>
```

## テーブル作成

Customerのテーブルを以下で作成すると動作します。

```Sql
CREATE TABLE customer (
    id       INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
    name     TEXT,
    post     TEXT,
    address  TEXT,

    PRIMARY KEY(id)
);
```

## まとめ

SpringBoot3でMyBatisを実際に動かしてみました。
特に、SpringBoot2の時と使い方は変わらなさそう。

あとMavenと違って、Gradleの書き方は人間にやさしい感じですね。