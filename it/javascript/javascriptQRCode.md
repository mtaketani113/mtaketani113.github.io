---
layout: default
title: JavaScriptでQRコード作成
description: Javascriptを使ってQRコードを作成するサービスです。どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。
category: javascript
created_at: 2022-03-17
last_modifeid_at: 2022-03-17
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.qrcode/1.0/jquery.qrcode.min.js"></script> 
<script type="text/JavaScript">

  $(function(){
    let qrtext = $("qrtext").val();
    let utf8qrtext = unescape(encodeURIComponent(qrtext));
    
    $("#qrCreateButton").click(function(){
      $("#img-qr").html("");
      $("#img-qr").qrcode({text:utf8qrtext}); 
    });
  });

</script>

Javascriptを使ってQRコードを作成するサービスです。
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

## QRコードにしたい文字列

<input type="text" id="qrtext"/>

<input type="button" id="qrCreateButton" value="QRコード生成"/>

## QRコード

<div id="img-qr"></div>

# 実装方法（作成中）

実際に書いたプログラムを解説します。
