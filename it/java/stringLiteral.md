---
layout: default
title: Javaの文字列の==での比較
description: Javaの文字列を比較するときに`==`でもリテラルで指定するとtrueになります。その理由などを説明します。
category: java
---

Javaで文字列の比較する際、`==`はオブジェクトの比較なので文字列を正しく比較できないです。
なので、`equals`を利用するのが普通です。

ただ、例外がありリテラルで定義した場合は同じオブジェクトを参照するようになっています。
ですので以下のような結果になります。

```Java
String str1 = "abc";
String str2 = "abc";
String str3 = new String("abc");
String str4 = str3.intern();
String str5 = String.valueOf("abc");

System.out.println(str1 == str2); //true
System.out.println(str1 == str3); //false
System.out.println(str1 == str4); //true
System.out.println(str1 == str5); //true
System.out.println(str1.equals(str3)); //true
```

もう少し正確に言うと、
String Constant Poolというところにリテラルで指定すると格納されるようです。

また、`intern()`を使うと、String Constant Poolにあるかを確認して、あればそのオブジェクトを返します。
なければ、作成して返すようです。なので`System.out.println(str1 == str4);`の結果は`true`です。

`new String("abc")`の場合は、異なるオブジェクトになります。
なので結果は`false`です。

`String.valueOf`は以下の通りです。なので、リテラルで指定した物そのものが返ってくるので、
`System.out.println(str1 == str5);`の結果は`true`です。
```Java
    public static String valueOf(Object obj) {
        return (obj == null) ? "null" : obj.toString();
    }

    public String toString() {
        return this;
    }

```

とすると、とある文字列を何回も`equals`で比較するときに定数として定義していて、
`定数.equals(変数);`と書いていました。
これでメモリも節約できるのかなと思っていましたが、実はそんなことはなかったのかな。

まー可読性の問題は別ですが。