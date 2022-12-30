---
layout: default
title: jQueryをサイトに強制読み込み
description: 古いサイトの調査でjQueryが適用されていないサイトでjQueryを利用できるようにする方法です。
category: javascript
image: /images/it/javascript/javascript_code.jpg
created_at: 2021-05-31
last_modifeid_at: 2021-08-21
---

古いサイトの調査をしていて、javascriptの動作がわからず  
開発者ツールを利用して調査していました。  
しかし、そのサイトではjQueryが使えないので少し不便でした。  
なのでそのサイトでjQeuryを強制読みさせて、
F12の開発者ツールで使えるようにする方法を記載します。

結論を言うと以下のコードを開発者ツールのConsoleに書き込めばよいです。

```JavaScript
// jQueryのscriptの要素を作成
var script = document.createElement( 'script' );

script.type = 'text/javascript';
script.src = '//ajax.googleapis.com/ajax/libs/jquery/1.11.0/jquery.min.js';

//　scriptの要素を差し込み
var firstScriptElement = document.getElementsByTagName( 'script' )[ 0 ];
firstScriptElement.parentNode.insertBefore( script, firstScriptElement );
```

~~このサイトではjQueryは入っていないので、  
Consoleで`jQuery`と書くと`jQuery is not defined`と出るかと思います。~~

**jQueryをトップページへのリンクで使うようになったので、`ƒ (e,t){return new w.fn.init(e,t)}`と出るようになりました。**

利用できないサイトで、上記のコードをコピペしてConsoleで実行すると`ƒ (a,b){return new n.fn.init(a,b)}`  
と返ってきて利用できるかと思います。  
F12を押して、試してみてください。

古いサイトだと`frame`などを利用していることがあります。  
その際は、どこの`frame`で利用するとか、`script`がどこにあるかなどがあるので、  
少し工夫がいります。  

## まとめ
jQueryは後付けで読み込ませることもできます。  
古いサイトの調査で利用してみてください。  
他にも、サイトの自動化などでも利用できるかも。