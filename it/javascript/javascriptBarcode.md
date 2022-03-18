---
layout: default
title: JavaScriptバーコード作成
description: Javascriptを使ってバーコードを作成するサービスです。どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。
category: javascript
created_at: 2022-03-17
last_modifeid_at: 2022-03-17
---

<script src="https://cdn.jsdelivr.net/gh/mtaketani113/jquery-barcode@master/jquery-barcode.js"></script> 
<script type="text/JavaScript">

  $(function(){   
    $("#barcodeCreateButton").click(function(){
      let barcodeText = $("#barcode-text").val();
      $("#img-barcord").html("");
      $("#img-barcord").barcode(barcodeText, "code39",{barWidth:1, barHeight:20,output:"css"});
    });

    $("#barcode-text,").keypress(function(e){
      if(e.which == 13){
        $("#barcodeCreateButton").click();
      }
    });
  });

</script>

Javascriptを使ってバーコードを作成するサービスです。
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

## バーコードにしたい文字列

<input type="text" id="barcode-text"/>


<input type="button" id="barcodeCreateButton" value="バーコード生成"/>

## バーコード

<div id="img-barcord">
未実行
</div>

↑生成後に右クリックして「画像をコピー」などで持っていけます。

# 実装方法(作成中)