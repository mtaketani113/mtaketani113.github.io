---
layout: default
title: リリースされたSpringBoot3でMyBatis利用
description: 2022年11月になり、SpringBoot3だけでなくMybatisもリリースされました。以前書いた設定方法が変わりましたので追加で記載します。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-11-27
last_modifeid_at: 2022-11-28
---

以前、[開発中のSpringBoot3でMyBatis利用](https://mtaketani113.github.io/it/java/springBoot3WithMyBatis.html)や
[開発中のSpringBoot3でMyBatis利用 その2](https://mtaketani113.github.io/it/java/springBoot3WithMyBatisPart2.html)で開発中のSpringBoot3とMyBtaisを利用する方法を記載しましたが、
2022年11月になり、SpringBoot3だけでなくMybatisもリリースされました。以前書いた設定方法が変わりましたので追加で記載します。

## SpringBoot3の利用

2022年11月24日に3.0.0を利用できるようになりました。
Mavenであればpom.xmlに以下のようにすると使えます。

```Xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.0.0</version>
  <relativePath/> <!-- lookup parent from repository -->
</parent>
```

Gradleであれば以下の通りです。

```Groovy
plugins {
	id 'org.springframework.boot' version '3.0.0'
	id 'io.spring.dependency-management' version '1.0.14.RELEASE'
	id 'java'
}
```

## MyBatisの利用

注意点は、開発中は2.3.0からSpringBoot3をサポートすると書いていましたが、
正式には3.0.0と変わったようです。

以前

**master(2.3.x) : MyBatis 3.5+, MyBatis-Spring 2.1, Java 8+ and Spring Boot 2.5+ (First version for supporting Spring Boot 3), not release yet**

2022年11月27日時点

**master : MyBatis 3.5+, MyBatis-Spring 3.0, Java 17+ and Spring Boot 3.0**  
**2.3.x : MyBatis 3.5+, MyBatis-Spring 2.1, Java 8+ and Spring Boot 2.5-2.7**

masterは2022年11月27日時点で3.0.0です。


Mavenだと以下のようになります。

```Xml
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>3.0.0</version>
</dependency>
```

Gradleだと以下のようになります。

```Groovy
plugins {
	id 'org.springframework.boot' version '3.0.0'
	id 'io.spring.dependency-management' version '1.0.14.RELEASE'
	id 'java'
}
```

## まとめ

SpringBoot3、MyBatisともリリースされました。
正式版をどんどん使っていきたいと思います。