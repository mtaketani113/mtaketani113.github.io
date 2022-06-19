---
layout: default
title: AndとOrの演算子の優先度
description: プログラムのIF文などでAndとOrの演算子を混在させた場合、Andが優先されます。
category: IT
created_at: 2022-05-15
last_modifeid_at: 2022-05-15
---

プログラムのIF文などでAndとOrの演算子を混在させた場合、Andが優先されます。
今回は論理式の優先度について説明します。

## AndとOrの混在について

論理式でAndとOrを混在させると処理の順番によって結果が異なってきます。

`A ∧ B ∨ C`としたときに`(A ∧ B) ∨ C`と`A ∧ (B ∨ C)`とで結果が異なってきます。

例えばA・・・false B・・・false C・・・trueとすると

(A ∧ B) ∨ C・・・true  
A ∧ (B ∨ C)・・・false

となります。

このように、処理の順番によって変わってきます。

## プログラムで書いたとの優先度

上記のように処理の順番によって結果が変わるのですが、
私が好きなJavaでカッコをつけなくてもコンパイルエラーにならず普通に実行できます。

実はAndが優先され`A ∧ B ∨ C`は`(A ∧ B) ∨ C`として扱われます。

Java以外でも同じ優先度でAndが基本的に優先されます。

ですが、ぱっと見どっちが優先されるかわからないので可読性のためにも
カッコを付けたほうがよいと個人的には思います。

ちなみに`⋀`と`∩`の違いは
とがっている方が論理式で使われ、丸い方が集合で使われます。

## まとめ

AndとOrの演算子を混在させた場合、処理の順番によって結果が異なります。
混在させた場合はAndが優先されるのですが、
可読性のためにカッコを付け他方がよいかと思います。