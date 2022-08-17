---
layout: default
title: SpringBoot3でDocker利用
description: SpringBoot3+mysqlでdocker-composeを利用しての環境構築です。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-08-17
last_modifeid_at: 2022-08-17
---

SpringBoot3+mysqlでDockerを利用して、どこでも同じ環境を作れるようにしました。

こちらを参考にしています。  
[Spring-Boot + MySQL + MyBatisを dockerで実行する](https://qiita.com/Frihesa/items/16ec97d17f61663a845f)

以下にソースコードを作成しています。  
[mybatisDemoForSpringboot3](https://github.com/mtaketani113/mybatisDemoForSpringboot3/tree/0.0.3)

## Dockerfile作成

Dockerfileは以下の通りです。

```Dockerfile
FROM gradle:7.5.1-jdk18-alpine AS build
COPY --chown=gradle:gradle . /home/gradle/src
WORKDIR /home/gradle/src
RUN gradle clean build --no-daemon 

FROM openjdk:18-jdk-slim

EXPOSE 8080

RUN mkdir /app

COPY --from=build /home/gradle/src/build/libs/*.jar /app/spring-boot-application.jar

ENTRYPOINT ["java","-jar","/app/spring-boot-application.jar"]
```

今回はGradleなので`gradle:7.5.1-jdk18-alpine`を利用しています。

また、このままだと`/gradle/src/build/libs/`にjarファイルが2ファイル（`xx.jar`と`xx-plain.jar`）がでいるため、`build.gradle`に以下を追加します。

じゃないと  
`COPY --from=build /home/gradle/src/build/libs/*.jar /app/spring-boot-application.jar`  
がうまくいきません。

```Groovy
jar {
    enabled = false
}
```

## docker-compose.yaml作成

docker-compose.yamlは以下の通りです。

```Yaml
version: "3"
services:
# MySQLのサービス
  db:
    image: mysql:8
    container_name: "spring_db"
    ports:
      - "3306:3306"
    volumes:
      - ./mysql/sql/:/docker-entrypoint-initdb.d
      - ./mysql/settings/:/var/lib/mysql
      - ./mysql/sql/mysql.cnf:/etc/mysql/conf.d/my.cnf
    environment:
      MYSQL_DATABASE: "demo"
      MYSQL_USER: "demo"
      MYSQL_PASSWORD: "demo"
      MYSQL_ROOT_USER: "root"
      MYSQL_ROOT_PASSWORD: "root"
      TZ: "Asia/Tokyo"

# springアプリケーションのサービス
  spring:
    build: ./
    container_name: "spring"
    restart: always
    ports:
      - "9090:8080"
    tty: true
    depends_on:
      - db
    volumes:
      - ./my-workbook:/srv:cached
    working_dir: /srv
    environment:
      DATASOURCE_URL: "jdbc:mysql://db:3306/demo"
```

動作させるには他に、
my.cnfとinit.sqlが必要になります。

my.cnfは以下の通りです。
```
[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci

[client]
default-character-set=utf8mb4
```

init.sqlは以下の通りです。このファイルに必要テーブルやレコードのSQLを登録します。
```Sql
CREATE TABLE IF NOT EXISTS customer (
    id       INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
    name     TEXT,
    post     TEXT,
    address  TEXT,

    PRIMARY KEY(id)
);

INSERT INTO customer(name, post, address) VALUES('name', 'post', 'address');
```

## docker-composeの実行

以下のコマンド実行できます。
```
docker-compose up -d
```

停止するときは以下です。
```
docker-compose stop
```

## 今後の改善点

多分ですが、再実行などするとDockerfileの以下の部分で、
Mysqlのデータもコピーしてしまう気がするので、必要なものだけをコピーするようにするようにする必要があります。

`COPY --chown=gradle:gradle . /home/gradle/src`

今後改善していこうかと。

## まとめ

docker-composeを利用して、どこでも環境構築できるようにしてみました。
参考にしてください。