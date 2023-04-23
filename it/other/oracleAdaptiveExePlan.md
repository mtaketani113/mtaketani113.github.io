---
layout: default
title: Oracleの12cの適応計画の外し方
description: Oracleで12cの適応計画というのがあり、それが逆効果の時があります。そうなったときの簡単な対処方法を記載します。
category: IT
created_at: 2023-04-23
last_modifeid_at: 2023-04-23
---

Oracle12cでは適応計画というのがあり、
実際に動作した実行計画から変えて最適なのを探すという機能があります。

しかし、SQLが大きくなって複雑になると逆効果の時があります。
早くなったり遅くなったりと。

そんなときのために、暫定的な対策としてヒント句を利用する方法があります。  
今回はそれを紹介します


## ヒント句を利用してOptimizerを古いのにする

対処方法は簡単です。
ヒント句で`OPTIMIZER_FEATURES_ENABLE`を利用して11gに戻すだけです。

```SQL
select /*+ OPTIMIZER_FEATURES_ENABLE('11.2.0.4') */
```

しかしながら、11gではできなかった書き方で書いてると怒られることがあります。

たとえば、外部結合で福問い合わせをしている場合です。

以下のSQLでは、最終ログイン日のデータをleft joinしています。

```SQL
left join CUSTOMER_LOGIN CUST
  on CUST.LOGIN_DATE = (select max(LOGIN_DATE)
    from CUSTOMER_LOGIN TMP
    where CUST.ID = TMP.ID)
```

これだと、  
`ORA-01799: 列は副問合せに対して外部結合されません。`  
となってしまいます。

そこで、以下のように`not exists`を利用して以下のように書きます。

```SQL
left join CUSTOMER_LOGIN CUST
  not exists (select '1'
    from CUSTOMER_LOGIN TMP
    where CUST.ID = TMP.ID
      and CUST.LOGIN_DATE < TMP.LOGIN_DATE)
```

`<`と`>`を間違えそうで怖い・・・

ただ、11gにもどすと、データに偏りがあった時に誤った実行計画が作られて
遅くなった場合に改善されなかったりする気がするので、
できるだけ適応計画を利用したほうがよい気がします。

## まとめ

oracle12cからの適応計画ですが、SQLが複雑すぎる場合、逆効果の時があるようです。
その際は、ヒント句を利用してOptimizerを11gにすることで適応計画を外すことができます。

その際に、11gで利用できない書き方をしているとエラーになるので修正が必要です。

あくまで、最終手段と思われるので、SQLを簡潔にするなどしてできるだけ対処するほうがよいとは思います。