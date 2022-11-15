---
layout: default
title: キャッシュ削除
description: JavaScriptやCSSなど改修した際にキャッシュが残ってしまいユーザの動作がおかしくなるのを防ぐ方法を紹介します。
category: javascript
created_at: 2022-06-08
last_modifeid_at: 2022-06-08
---

JavaScriptやCSSなど改修した際にキャッシュのせいでユーザの動作がおかしくなることがあります。

例えば、クリックしたときの処理を追加したのにJavascriptのキャッシュが残っていて
クリック処理がないJavascriptが実行されると、処理がないので当然動きません。

自分たちの環境では動くけど、ユーザの環境では動かないという状況が起こります。


## 緊急対策Ctrl+F5でユーザのキャッシュを消してもらう

起きてしまった時の緊急対策です。

`Ctr+F5`とするとJavascript、CSSなどキャッシュを無視して再読み込みします。
これでJavascript、CSSを最新にして障害を回避するという方法です。

ただ、これはあくまで緊急対応かと思います。

## クエリ文字を追加して、再読み込みさせる

改修の際に少し工夫することで、ユーザが画面を開いたときに
再読み込みさせることができます。

例えばこんな感じです。

```JavaScript
<script src="/xx/yyyy.js?v=202206080121"></script>
```

上記のようにクエリ文字`?v=202206080121`を追加し改修のたびに変更していくことで再読み込みをさせることができます。
`?v=202206080121`のところまで含めて新しいかどうかを判定してくれるのでこんなことができます。

ただ、改修するたびに修正したJavascriptがどこから呼ばれているかを見てクエリ文字を変えるのはめんどくさいです。
なので、たとえば以下のように`{BuildTimeStamp}`のように書いておいて、
Build時に日時をとってきて書き換えるようにしておけば、リリースのたびに書き換わって楽です。

```JavaScript
<script src="/xx/yyyy.js?v={BuildTimeStamp}"></script>
```

ただ、リリースるたびに再読み込みされるので初回アクセス時に遅くなるというデメリットはあります。


## キャッシュしないようにする。

そもそもファイルをキャッシュしないようにする方法です。

昔であれば`http-equiv`をHTMLに書くとキャッシュしないようにできました。
以下のような感じです。

```Html
<meta http-equiv="Pragma" content="no-cache">
<meta http-equiv="Cache-Control" content="no-cache">
<meta http-equiv="Expires" content="0">
```

しかし、これはHTML5（今はHTML Living Standard？）では上記は無効だそうです。

なのでApacheなどのほうで設定してレスポンスヘッダーにキャッシュしないことを設定する必要があります。
.htaccessやApacheの設定に以下のように書くとできます。

```Xml
<Files ~ "\.(js|css)$">
  Header set Pragma no-cache
  Header set Cache-Control no-cache
  Header set Expires "Thu, 01 Dec 1994 16:00:00 GMT"
</Files>
```

`.htaccess`で制御する場合は、Apacheのせってで無効になっていないかの確認が必要です。

でもこれは、JavascriptやCSSにはあんまりしないですかね。

##　まとめ

JavascriptやCSSはキャッシュすることでは約画面を描画できますが、
改修したときにキャッシュのせいで動かなくなったりします。そうならないために
クエリ文字を入れておくことや、キャッシュしない設定をしておきましょう。

もし起こってしまったら`Ctrl+F5`で回避しましょう。
