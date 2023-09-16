---
layout: default
title: SpringBootとApache連携
description: SpringBootとAJP/1.3でApache連携する際に、はまったことを記載します。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-11-13
last_modifeid_at: 2023-09-16
---

SpringBootとAJP/1.3でApache連携する際に、はまったことを記載します。

## ajpとhttpの違い。

apacheとSpringBootに内包されているTomcatは、
ajpというプロトコルで連携できます。

ajpはhttpより早いそうで、
理由は以下のためだそうです。

- TCP/IPレベルの接続を維持したまま接続を使いまわすことをしている。
- テキストベースのhttpに対してajpは一部、情報をバイナリ化している。

## Spring Boot とApacheの連携

記載したソースコードは今のような感じです。

```Java
package com.example.demo;

import java.net.InetAddress;
import java.net.UnknownHostException;

import org.apache.catalina.connector.Connector;
import org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory;
import org.springframework.boot.web.server.WebServerFactoryCustomizer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.apache.coyote.ajp.AbstractAjpProtocol;

@Configuration
public class AppConfig {
 
    @Bean
    public WebServerFactoryCustomizer<TomcatServletWebServerFactory> servletContainer() {
        Connector connector = new Connector("AJP/1.3");
        connector.setPort(8009);
        connector.setRedirectPort(8443);
        // はまりポイント①
        ((AbstractAjpProtocol<?>) connector.getProtocolHandler()).setSecretRequired(false);

        // はまりポイント② >>>>
        InetAddress ip = null;
        try {
            ip = InetAddress.getByName("0.0.0.0");
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
        ((AbstractAjpProtocol<?>) connector.getProtocolHandler()).setAddress(ip);
        // はまりポイント② <<<<
        return factory -> factory.addAdditionalTomcatConnectors(connector);    
    }
}
```

## secretRequiredをfalseに設定

はまったことの1つ目はここです。  
```Java
((AbstractAjpProtocol<?>) connector.getProtocolHandler()).setSecretRequired(false);
```

これがないと動作しませんでした。  
Tomcatを9.0.34でsecretというのが追加されたようです。
新しいのを使う場合は、気を付けてください。

## IPアドレスの設定

はまったことの2つ目はここです。  
```Java
InetAddress ip = null;
try {
    ip = InetAddress.getByName("0.0.0.0");
} catch (UnknownHostException e) {
    e.printStackTrace();
}
((AbstractAjpProtocol<?>) connector.getProtocolHandler()).setAddress(ip);
```

0.0.0.0を設定することで、すべてのIPからのアクセスが許可されます。

これがないと、127.0.0.1でapache→tomcatにアクセスしないと
AJPの連携ができません。

以下のようにApacheの設定ファイル`httpd.conf`に記載した場合、サーバ名のところが127.0.0.1でないと動作しません。
```
ProxyPass / ajp://サーバー名:8009/
```

なのでapacheとtomcatが同一サーバでないと動作しません。  
0.0.0.0はやりすぎかもしれませんが、設定が必要です。

## まとめ

spring bootとApacheをajpで連携させるために、@Configurationで設定しますが、
以下2点の設定が必要でした。
- secretの設定
- IP「0.0.0.0」の設定

皆さん気を付けてください。