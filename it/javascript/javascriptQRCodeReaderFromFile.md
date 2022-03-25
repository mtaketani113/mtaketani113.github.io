---
layout: default
title: JavaScriptでファイルからQRコード読み込み
description: Javascriptを使ってファイルからQRコードを読み込み、内容を表示するサービスです。
category: javascript
created_at: 2022-03-25
last_modifeid_at: 2022-03-25
---
<script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.min.js"></script>

<script type="text/JavaScript">
  
  $(function(){
    var canvas = document.getElementById( 'canvas' );
    var ctx = canvas.getContext( '2d' );
    var outputData = document.getElementById("outputData");
  
    var file_image = document.getElementById( 'file-image' );
    
    function selectReadFile( e ){
      var file = e.target.files;
      var reader = new FileReader();
      reader.onload = function(){
        readDrawImg( reader, canvas, 0, 0 );
      }
      reader.readAsDataURL( file[0] );
    }
    
    function readDrawImg( reader, canvas, x, y ){
      var img = readImg( reader );
      img.onload = function(){
        var w = img.width;
        var h = img.height;
    
        // resize
        var resize = resizeWidthHeight( 1024, w, h );
        drawImgOnCav( canvas, img, x, y, resize.w, resize.h );
      }
    }
    
    function readImg( reader ){
      var result_dataURL = reader.result;
      var img = new Image();
      img.src = result_dataURL;
    
      return img;
    }
    
    function drawImgOnCav( canvas, img, x, y, w, h ){
      canvas.width = w;
      canvas.height = h;
      ctx.drawImage( img, x, y, w, h );
    
      checkQRCodeAndDisp();
    }
    
    function resizeWidthHeight( target_length_px, w0, h0 ){
      var length = Math.max( w0, h0 );
      if( length <= target_length_px ){
        return({
          flag: false,
          w: w0,
          h: h0
        });
      }
  
      var w1;
      var h1;
      if( w0 >= h0 ){
        w1 = target_length_px;
        h1 = h0 * target_length_px / w0;
      }else{
        w1 = w0 * target_length_px / h0;
        h1 = target_length_px;
      }
  
      return({
        flag: true,
        w: parseInt( w1 ),
        h: parseInt( h1 )
      });
    }
  
    function checkQRCodeAndDisp(){
      var imageData = ctx.getImageData( 0, 0, canvas.width, canvas.height );
      var code = jsQR( imageData.data, canvas.width, canvas.height );
      if( code ){
        canvas.hidden = false;
  
        outputData.parentElement.hidden = false;
        let codeData = code.data;
        if(codeData != null && (codeData.startsWith('https://') || codeData.startsWith('http://'))){
          outputData.innerHTML = '<a href="' +  codeData + '">' + codeData + '</a>'
        }else{
          outputData.innerText = codeData;
        }
      }else{
        alert( "No QR Code found." );
      }
    }

    file_image.addEventListener( 'change', selectReadFile, false );
  });
  
</script>

Javascriptを使ってファイルからQRコードを読み込み、内容を表示するサービスです。
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

以下を参考に作成しています。  
[HTML と JavaScript だけで QR コード読み取り](http://dotnsf.blog.jp/archives/1078584611.html)

<input type="file" accept="image/*" capture="camera" name="file" id="file-image"/>

## QRコードのデコード

<div hidden><b>読み込んだデータ:</b> <span id="outputData"></span></div>


<div id="loadingMessage"></div>
<canvas id="canvas" hidden></canvas>