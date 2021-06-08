---
layout: default
title: Github Pagesでコードハイライト
description: GitHub Pagesでコードハイライトする方法。
category: IT
---

Github Pagesをデフォルトで利用すると、コードハイライトがされずに  
読みづらくなってました。

それを、以下のようにコードハイライトする方法を記載します。

```JavaScript
$(document).ready(function(){
　　window.test = () => console.log('test');
});
```

今回使ったのは、[highlight.js](https://highlightjs.org/)です。  
利用は簡単で、[ダウンロードページ](https://highlightjs.org/download/)から
Downloadします。

「You can download a custom bundle including only the languages you need.」  
とあったので、自分は`Dockerfile`を追加しました。たまに使うので。

`styles`はいっぱいあるので、今回は、`github.css`を利用しました。

`github.css`を`/assets/css/`に  
`highlight.min.js`を`/assets/js/`に配置します。

そして以下のようにheaderに記載すると、利用可能です。

```HTML
<link rel="stylesheet" href="{{ '/assets/css/github.css' | relative_url }}">
<script src="{{ '/assets/js/highlight.min.js' | relative_url }}"></script>
<script>hljs.initHighlightingOnLoad();</script>
```

全ページに反映されるように`layout`のhtmlに反映させたので、  
これで色がついて少しサイトが見やすくなったかな？ｗ

なにやら、`_config.yaml`で実現する方法もあるようですが・・・  
よくわからなかったので、今回は`highlight.js`を利用しました。

ダウンロードして配置しなくても、サイトにあるのを利用することも可能なようです。  
詳しくは公式を確認ください。  
今回は、バージョンが変わったり、サイトが利用できないときに利用できないことがないよう、  
自分のところに持ってきました。
