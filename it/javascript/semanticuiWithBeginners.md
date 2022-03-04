---
layout: default
title: Semantic UIを利用して開発
description: 開発時のメンバーがあまり開発したことがないメンバーで、画面を作れない状態でした。そこでSemantic UIを利用して開発した話です。
category: javascript
created_at: 2022-03-05
last_modifeid_at: 2022-03-05
---

作成中

## 経緯

<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/fomantic-ui@2.8.8/dist/semantic.min.css">
<script src="https://cdn.jsdelivr.net/npm/fomantic-ui@2.8.8/dist/semantic.min.js"></script>

結構前の開発の話ですが、結構な規模の開発をすることとなりました。
その開発メンバーですが、システムを内製で手の内化するというのもあり、内製メンバーも開発することになりました。

しかし、外部への委託がメインの会社だったので、あまり開発が得意ではありませんでした。
画面周りが弱くJavascript、cssなどは特に弱かったのです。

そこから教育をがっつりする時間もなかったので、
学習コストが少なくとても有効活用できる[Semantic UI](https://semantic-ui.com/)というのを利用するようにしました。

## クラス名で直感的に書ける

Semantic UIはクラス名で直感的にわかるようになっています。

たとえば

```Javascript
<div class="ui grid">
  <div class="eight wide column">
    <a class="ui button" href="#">東京都</a>
  </div>
  <div class="eight wide column">
    <a class="ui button" href="#">神奈川県</a>
  </div>
</div>
```

<div class="ui grid">
  <div class="eight wide column">
    <a class="ui button" href="#">東京都</a>
  </div>
  <div class="eight wide column">
    <a class="ui button" href="#">神奈川県</a>
  </div>
</div>

```Javascript
<div class="ui center aligned grid">
  <div class="eight wide column">
    <a class="ui button" href="#">東京都</a>
  </div>
  <div class="eight wide column">
    <a class="ui button" href="#">神奈川県</a>
  </div>
</div>
```

<div class="ui center aligned grid">
  <div class="eight wide column">
    <a class="ui button" href="#">東京都</a>
  </div>
  <div class="eight wide column">
    <a class="ui button" href="#">神奈川県</a>
  </div>
</div>

## サンプルが豊富



## まとめ
