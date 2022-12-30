---
layout: default
title: JavaScriptでバーコード作成
description: Javascriptを使ってバーコードを作成するサービスです。どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。
category: javascript
image: /images/it/javascript/javascript_code.jpg
created_at: 2022-03-18
last_modifeid_at: 2022-03-19
---

<script src="https://cdn.jsdelivr.net/gh/mtaketani113/jquery-barcode@master/jquery-barcode.js"></script> 
<script type="text/JavaScript">

  $(function(){   
    $("#barcodeCreateButton").click(function(){
      let barcodeText = $("#barcode-text").val();
      let width = $("#width").val();
      let height = $("#height").val();
      let barcodeType = $('input:radio[name="barcodeType"]:checked').val();
      $("#img-barcord").html("");
      $("#img-barcord").barcode(barcodeText, barcodeType,{barWidth:width, barHeight:height,output:"css"});
    });

    $("#barcode-text,#width,#height").keypress(function(e){
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

## バーコードの種類

<input type="radio" name="barcodeType" id="code39" checked="checked" value="code39"/>
<label for="code39">code39</label>

<input type="radio" name="barcodeType" id="ean13" value="ean13"/>
<label for="ean13">ean13</label>

## バーコードのサイズ

<input type="text" id="width" value="3"/>横幅

<input type="text" id="height" value="60"/>縦の長さ

<input type="button" id="barcodeCreateButton" value="バーコード生成"/>

## バーコード

<div id="img-barcord">
未実行
</div>

↑生成後に右クリックして「画像をコピー」などで持っていけます。

# 実装方法

今回は「jquery-barcode.js」を利用しました。

[QRコード生成](/it/javascript/javascriptQRCode.html)とちがってCDNがなかったので、

GithubのファイルをCDNとして利用できる以下のサービスを利用しました。

https://www.jsdelivr.com/

元のリポジトリがなくなると使えないのでとりあえずForkして、
それを読み込みました。

以下のような感じです。

```Html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdn.jsdelivr.net/gh/mtaketani113/jquery-barcode@master/jquery-barcode.js"></script> 
```

`mtaketani113/jquery-barcode@master/jquery-barcode.js`ここの部分が
`{ユーザ}/{リポジトリ}@{ブランチまたはタグ}/{ファイルパス}`のようにすることで利用できます。

あとは、QRコード生成と同じようにjQueryで実装しています。
詳細は、F12で参照ください。

バーコードにはいろいろ種類があって、
今回は「code39」と「ean13」を利用できるようにしました。

「code39」は英数字と一部記号を利用できます。

「ean13」は12桁で数字のみです。なので12桁ないと生成できません。
アルファベットを入れても生成できません。また、13桁目に1つ数字型されます。チェックディジットといわれるやつです。1文字間違って読み込んだり入れ替わっても誤り訂正してくれます。
