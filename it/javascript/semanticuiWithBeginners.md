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

結構前の開発の話ですが、結構な規模の開発をすることとなりました。
その開発メンバーですが、システムを内製で手の内化するというのもあり、内製メンバーも開発することになりました。

しかし、外部への委託がメインの会社だったので、あまり開発が得意ではありませんでした。
画面周りが弱くJavascript、cssなどは特に弱かったのです。

そこから教育をがっつりする時間もなかったので、
学習コストが少なくとても有効活用できる[Semantic UI](https://semantic-ui.com/)というのを利用するようにしました。

## 利用準備

CDNがあるので、以下を記載すれば利用できます。
```
<script src="https://cdn.jsdelivr.net/npm/jquery@3.3.1/dist/jquery.min.js"></script>
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/fomantic-ui@2.8.8/dist/semantic.min.css">
<script src="https://cdn.jsdelivr.net/npm/fomantic-ui@2.8.8/dist/semantic.min.js"></script>
```


## クラス名で直感的に書ける

Semantic UIはクラス名で直感的にわかるようになっています。

たとえば、以下のように書いたときの結果です。

```Javascript
<div class="ui form">
  <div class="inline fields">
    <div class="eight wide field">
      <label>Name</label>
      <input type="text" placeholder="姓">
    </div>
    <div class="five wide field">
      <input type="text" placeholder="名">
    </div>
    <div class="three wide field">
      <button class="ui blue labeled icon button">
        <i class="save icon"></i>
        Save
      </button>
    </div>
  </div>
</div>
```

<iframe width="100%" height="300" src="//jsfiddle.net/mtaketani113/m5a216kc/embedded/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

## サンプルが豊富



## まとめ
