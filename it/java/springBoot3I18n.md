---
layout: default
title: SpringBoot3で日英切替
description: SpringBoot3で日英切り替えする方法と、Cookieに日英をの情報を保持して再描画した際に設定が引き継ぐ方法を試してみました。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-08-06
last_modifeid_at: 2022-08-06
---

SpringBoot3で日英切り替えする方法と、Cookieに日英をの情報を保持して
再描画した際に設定が引き継ぐ方法を記載します。

## 日英切り替えのプログラム追加

以下にソースコードを作成しています。
[mybatisDemoForSpringboot3](https://github.com/mtaketani113/mybatisDemoForSpringboot3/tree/0.0.2)

SpringBootのmain関数のあるクラスと同じところに`LocaleConfig`を追加します。
クラス名は別になんでもよいです。

```Java
@Configuration
public class LocaleConfig implements WebMvcConfigurer  {
  @Override 
    public void addInterceptors(InterceptorRegistry registry) { 
      registry.addInterceptor(localeChangeInterceptor()); 
    } 
  @Bean 
  public LocaleChangeInterceptor localeChangeInterceptor() { 
    LocaleChangeInterceptor localeChangeInterceptor = new LocaleChangeInterceptor(); 
    localeChangeInterceptor.setParamName("lang"); 
    return localeChangeInterceptor; 
  }
  
  @Bean 
  public CookieLocaleResolver localeResolver() { 
    CookieLocaleResolver localeResolver = new CookieLocaleResolver(); 
    localeResolver.setDefaultLocale(Locale.JAPAN);
    localeResolver.setCookieMaxAge(604800);
    localeResolver.setCookiePath("/");
    return localeResolver; 
  }
  
  @Bean 
  public ReloadableResourceBundleMessageSource messageSource() { 
    ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource(); 
    messageSource.setBasename("classpath:i18n/message"); 
    messageSource.setDefaultEncoding("UTF-8");
    return messageSource; 
  }
} 
```

少し説明すると、以下の部分が日英切り替え時に動きます。
パラメータで`lang`を渡すとその値をセットします。

```Java
  public LocaleChangeInterceptor localeChangeInterceptor() { 
    LocaleChangeInterceptor localeChangeInterceptor = new LocaleChangeInterceptor(); 
    localeChangeInterceptor.setParamName("lang"); 
    return localeChangeInterceptor; 
  }
```

次に以下の部分で、デフォルトとCookieを利用すること設定しています。
有効期限を`setCookieMaxAge`で設定し、別画面での切り替えにも対応するように`/`をパスにしています。

```Java
  public CookieLocaleResolver localeResolver() { 
    CookieLocaleResolver localeResolver = new CookieLocaleResolver(); 
    localeResolver.setDefaultLocale(Locale.JAPAN);
    localeResolver.setCookieMaxAge(604800);
    localeResolver.setCookiePath("/");
    return localeResolver; 
  }
```

セッションを利用したい場合は`CookieLocaleResolver`→`SessionLocaleResolver`とすることで可能です。

そして最後にメッセージがどこにあるかと、文字コードを設定sh知恵います。

```Java
  @Bean 
  public ReloadableResourceBundleMessageSource messageSource() { 
    ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource(); 
    messageSource.setBasename("classpath:i18n/message"); 
    messageSource.setDefaultEncoding("UTF-8");
    return messageSource; 
  }
```
今回は`resources/i18n/message`に配置して、UTF-8で利用します。


## 日英切り替えのメッセージを追加

message_ja.propertiesとmessage_en.propertiesとを
`resouces/i18n`に配置します

それぞれ中身はこんな感じです。
```
customer.title=顧客一覧
customer.detail=詳細
customer.name=名前
customer.post=郵便番号
customer.address=住所
```

```
customer.title=Customers
customer.detail=Detail
customer.name=Name
customer.post=Post
customer.address=Address
```

## Thymeleafに日英切り替えを追加

適当な場所に以下を追加して、langのパラメータを送信するようにします。
```Html
  <a href="?lang=ja" th:href="@{?lang=ja}">日本語</a>
  <br/>
  <a href="?lang=en" th:href="@{?lang=en}">English</a>
```

そして、Thymeleafのth:text属性で`#{プロパティーの値}`とすると日本語、英語を切り替えられます。

```Html
<th th:text="#{customer.name}">名前</th>
<th th:text="#{customer.post}">郵便番号</th>
<th th:text="#{customer.address}">住所</th>
```

## まとめ

Cookieを利用して日英切り替えを保持する方法を記載しました。
ソースのサンプルもあるので、必要に応じてご利用ください。