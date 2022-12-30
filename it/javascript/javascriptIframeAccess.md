---
layout: default
title: JavaScriptでiframeにアクセスの注意
description: JavaScriptでiframeにアクセスする方法と、アクセス拒否について記載します。また、開発者ツールを使うときの注意を記載します。
category: javascript
image: /images/it/javascript/javascript_code.jpg
created_at: 2022-05-25
last_modifeid_at: 2022-05-25
---

`iframe`へのJavaScriptでのアクセスですが、アクセスする方法が少し異なるので記載します。
また、2点ほど注意点を記載します。

## iframeへのJavaScriptでのアクセス方法

アクセス方法は簡単で、  
`document.querySelector("XX")`などで要素を取得して  
`contentWindow.document`で取得できます。

実際に以下の画像を取得するために開発者ツールで  
`document.querySelector("#executeScope").contentWindow.document`  
と記載すると`#document`と結果が出てアクセスできるかと思います。

<iframe src="https://mtaketani113.github.io/it/javascript/executeScope.html" id="executeScope"></iframe>

## iframeへのJavaScriptでのアクセスが拒否される

しかし、すべてアクセスできるわけではなく
`iframe`で埋め込んでいるサイトによってはアクセスが拒否されます。

試しに以下の画像を取得するために開発者ツールで  
`document.querySelector("#twitter-widget-1").contentWindow.document`  
と記載してみてください。

<blockquote class="twitter-tweet"><p lang="und" dir="ltr"><a href="https://twitter.com/hashtag/%E9%9B%A8%E5%AE%BF%E3%82%8A?src=hash&amp;ref_src=twsrc%5Etfw">#雨宿り</a> <a href="https://twitter.com/hashtag/%E3%81%A4%E3%81%B0%E3%82%81?src=hash&amp;ref_src=twsrc%5Etfw">#つばめ</a> <a href="https://t.co/WEhispwUg2">pic.twitter.com/WEhispwUg2</a></p>&mdash; mtaketani113 (@mtaketani113) <a href="https://twitter.com/mtaketani113/status/1517111524002656257?ref_src=twsrc%5Etfw">April 21, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

以下のような結果が出ると思います。

```JavaScript
VM3797:1 Uncaught DOMException: Blocked a frame with origin "https://mtaketani113.github.io" from accessing a cross-origin frame.
    at <anonymous>:1:58
```

originというのがhttps://サイト:ポート　のことでこれが異なるということでブロックされています。
埋め込んでいるサイトで許可する必要があるので、アクセスできない場合もあります。

## 開発者ツールでの要素検索後の挙動

開発者ツールで要素の検索をする機能があると思います。
それで`iframe`の要素をクリックすると、埋め込んだサイトのほうに主が移るようです。

実際に埋め込んでる画像をクリックすると、  
`document.querySelector("#twitter-widget-1").contentWindow.document`  
が効かなくなるかと思います。

##　まとめ

`iframe`のアクセス方法は`contentWindow.document`でアクセスできますが、
相手側が拒否してるとアクセスできない場合があります。

また、開発者ツールで要素検索でクリックすると、埋め込んだサイトのほうに移ってしまうようです。
開発者ツールでiframeの情報を見る場合は少し注意ください。