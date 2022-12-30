---
layout: default
title: JavaScriptでQRコード作成
description: Javascriptを使ってQRコードを作成するサービスです。どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。
category: javascript
image: /images/it/javascript/javascript_code.jpg
created_at: 2022-03-17
last_modifeid_at: 2022-03-17
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.qrcode/1.0/jquery.qrcode.min.js"></script> 
<script type="text/JavaScript">

  $(function(){   
    $("#qrCreateButton").click(function(){
      let qrtext = $("#qrtext").val();
      let utf8qrtext = unescape(encodeURIComponent(qrtext));
      let size = $("#qrsize").val();
      $("#img-qr").html("");
      $("#img-qr").qrcode({width:size,height:size,text:utf8qrtext}); 
    });

    $("#qrtext, #qrsize").keypress(function(e){
      if(e.which == 13){
        $("#qrCreateButton").click();
      }
    });
  });

</script>

Javascriptを使ってQRコードを作成するサービスです。
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

## QRコードにしたい文字列

<input type="text" id="qrtext"/>

## QRコードのサイズ

<input type="text" id="qrsize" value="160"/>

<input type="button" id="qrCreateButton" value="QRコード生成"/>

## QRコード

<div id="img-qr">
未実行
</div>

↑生成後に右クリックして「画像をコピー」などで持っていけます。
# 実装方法

実際に書いたプログラムを解説します。

こちらを参考にしています。

[JavaScriptでQRコードを作成【jQuery】](https://onoredekaiketsu.com/create-qr-code-with-javascript/)

以下が記載したプログラムです。
```HTML
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.qrcode/1.0/jquery.qrcode.min.js"></script> 
<script type="text/JavaScript">

  $(function(){   
    $("#qrCreateButton").click(function(){
      let qrtext = $("#qrtext").val();
      let utf8qrtext = unescape(encodeURIComponent(qrtext));
      let size = $("#qrsize").val();
      $("#img-qr").html("");
      $("#img-qr").qrcode({width:size,height:size,text:utf8qrtext}); 
    });
    
    $("#qrtext, #qrsize").keypress(function(e){
      if(e.which == 13){
        $("#qrCreateButton").click();
      }
    });
  });

</script>

<input type="text" id="qrtext"/>
<input type="text" id="qrsize" value="160"/>
<input type="button" id="qrCreateButton" value="QRコード生成"/>

<div id="img-qr">
未実行
</div>
```

以下で、JqueryとQRCodeを生成するJqueryの拡張のCDNを読み込みます。
```Html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.qrcode/1.0/jquery.qrcode.min.js"></script> 
```

以下が入力と実行ボタンと結果を出力する箇所です。IDを振ってます。
```Html
<input type="text" id="qrtext"/>
<input type="text" id="qrsize" value="160"/>
<input type="button" id="qrCreateButton" value="QRコード生成"/>

<div id="img-qr">
未実行
</div>
```

以下でボタンをクリックしたときの動作を記載しています。
```Javascript
  $("#qrCreateButton").click(function(){
    let qrtext = $("#qrtext").val();
    let utf8qrtext = unescape(encodeURIComponent(qrtext));
    let size = $("#qrsize").val();
    $("#img-qr").html("");
    $("#img-qr").qrcode({width:size,height:size,text:utf8qrtext}); 
  });
```

`unescape(encodeURIComponent(qrtext));`で文字化けしないようにしています。

こちらのソースによってinput項目にフォーカスした状態で
エンターを押すと更新されます。
```Javascript
  $("#qrtext, #qrsize").keypress(function(e){
    if(e.which == 13){
      $("#qrCreateButton").click();
    }
```

`e.which == 13`・・・エンターは13番なのですね。