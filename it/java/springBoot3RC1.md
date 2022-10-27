---
layout: default
title: SpringBoot3のRC1を利用
description: SpringBoot3でRC1が出ました。M4からバージョンアップしてみるとSpringSecurityで動かなくなったところがあったので記載します。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-10-28
last_modifeid_at: 2022-10-28
---

SpringBoot3でRC1が出ました。M4からバージョンアップしてみるとSpringSecurityで動かなくなったところがあったので記載します。

## Configureを足さないと動かなくなった

もともと、`Configuration`のアノテーションをつけなくてもなんとなく動いてくれてたのですが、
どうもこれがないと動かなくなったようです。

```Java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig {
    ・・・
}
```

Githubで変更をコミットしてます。参考にしてください。

(こちら)[https://github.com/mtaketani113/mybatisDemoForSpringboot3/commit/d3cbe1e9b7255617da4bc4c294caea53778b5899]です。


## mvcMatchersが使えなくなった。

`mvcMatchers`が効かないんじゃなくて、なくなったようです。  
コンパイルエラーになりました。
かわりに`requestMatchers`を使えばよさそうです。

Githubで変更をコミットしてます。参考にしてください。

(こちら)[https://github.com/mtaketani113/mybatisDemoForSpringboot3/commit/8aecb466ab90f2ba776090b09ccb6d2fb7a6a621]です。

## 謎な部分

たぶん、以下をなくせそうな気がするのですが、
なくすとうまくいかず。

```Java
  @Bean
  public WebSecurityCustomizer webSecurityCustomizer() {
    return web -> web.ignoring().requestMatchers("/api/**");
  }
```

`.requestMatchers("/api/**").permitAll()`で行けるはずなんですが・・・  
またわかれば記載します。

## まとめ

自身が引っ掛かったのは以下2点でした
1. Configurationのアノテーションを追加
2. `mvcMatchers`→`requestMatchers`への変更

参考になればと思います。