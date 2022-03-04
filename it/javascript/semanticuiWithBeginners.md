---
layout: default
title: Semantic UIを利用して開発
description: 開発時のメンバーがあまり開発したことがないメンバーで、画面を作れない状態でした。そこでSemantic UIを利用して開発した話です。
category: javascript
created_at: 2022-03-05
last_modifeid_at: 2022-03-05
---

## 経緯

結構前の開発の話ですが、結構な規模の開発をすることとなりました。
その開発メンバーですが、システムを内製で手の内化するというのもあり、内製メンバーも開発することになりました。

しかし、外部への委託がメインの会社だったので、あまり開発が得意ではありませんでした。
画面周りが弱くJavascript、cssなどは特に弱かったのです。

そこから教育をがっつりする時間もなかったので、
学習コストが少なく、とても有効活用できる[Semantic UI](https://semantic-ui.com/)というのを利用するようにしました。

ちなみにSemantic UIの開発は止まっているらしく[Fomantic UI](https://fomantic-ui.com/)

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

<iframe width="100%" height="300" src="//jsfiddle.net/mtaketani113/m5a216kc/embedded/result/dark/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

```Javascript
<button class="ui blue labeled icon button">
  <i class="save icon"></i>
  Save
</button>
```

`blue`などで色を指定できます。 `icon`も色あり今回使ったのは保存のアイコンです。
`labeled`とつければボタンの前に線が入ってアイコンが付きます。

そして`right`とつけるとアイコンが右に行きます。

```Javascript
<button class="ui blue right labeled icon button">
  <i class="save icon"></i>
  Save
</button>
```

<iframe width="100%" height="300" src="//jsfiddle.net/mtaketani113/m5a216kc/2/embedded/result/dark/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

また、`<div class="eight wide field">`のところでは16分割してそのうちのどれだけ使うかを指定できます。  
サンプル表示では幅が狭いので縦に並んでいます。幅が狭いと勝手に縦に並べてくれます。

## サンプルが豊富

Semantic UI(Fomantic UI)のサイトを参照すると、色々とサンプルがあり
コードをさっとコピーできるようになっています。

正直使うのを決めた理由はこれですね。
みんなコピペでガンガン使ってくれました。

ただ、外部の方も使って請負で作成してもらったのですが、
色々と言われてて大変でした。

何を言われたかといいますと、
「私たちはSemantic UIを使ったことがないので、利用できません。  
使わせたいのであれば、仕様をまとめてください」と。

え？？サイト見れば(*'▽')？？とマジで思いました・・・。
成果物の責任があるとはいえ、それはどうかと思いましたね。

すごい無駄なことなのですが、プロジェクトでよく使うであろうサンプルを
Excelにまとめて記載するということをしました・・・

## まとめ

Semantic UIは直感的に使いやすく、また、サンプルも豊富です。
画面の開発に精通していない人たちでも、比較的学習コストも少なく使えるようになります。

ただ、外部へ請負で出すときは、成果物の責任が請け負う方に行くので、
リスクを少なくするために使えないといわれるかもｗｗ
使ってもらうのに大変な可能性があるかも。