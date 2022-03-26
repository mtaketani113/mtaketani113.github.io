---
layout: default
title: JavaScriptでQRコードリーダー
description: Javascriptを使ってQRコードを読み込み、内容を表示するサービスです。
category: javascript
created_at: 2022-03-24
last_modifeid_at: 2022-03-26
---
<script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.min.js"></script>

<script type="text/JavaScript">
  
  $(function(){
    var video = document.createElement("video");
    var canvasElement = document.getElementById("canvas");
    var canvas = canvasElement.getContext("2d");
    var loadingMessage = document.getElementById("loadingMessage");
    var outputContainer = document.getElementById("output");
    var outputMessage = document.getElementById("outputMessage");
    var outputData = document.getElementById("outputData");

    function drawLine(begin, end, color) {
      canvas.beginPath();
      canvas.moveTo(begin.x, begin.y);
      canvas.lineTo(end.x, end.y);
      canvas.lineWidth = 4;
      canvas.strokeStyle = color;
      canvas.stroke();
    }

    function tick() {
      loadingMessage.innerText = "Loading..."
      if (video.readyState === video.HAVE_ENOUGH_DATA) {
        loadingMessage.hidden = true;
        canvasElement.hidden = false;
        outputContainer.hidden = false;

        canvasElement.height = video.videoHeight;
        canvasElement.width = video.videoWidth;
        canvas.drawImage(video, 0, 0, canvasElement.width, canvasElement.height);
        var imageData = canvas.getImageData(0, 0, canvasElement.width, canvasElement.height);
        var code = jsQR(imageData.data, imageData.width, imageData.height, {
          inversionAttempts: "dontInvert",
        });
        if (code) {
          // QRコードの部分を赤枠で囲う
          drawLine(code.location.topLeftCorner, code.location.topRightCorner, "#FF3B58");
          drawLine(code.location.topRightCorner, code.location.bottomRightCorner, "#FF3B58");
          drawLine(code.location.bottomRightCorner, code.location.bottomLeftCorner, "#FF3B58");
          drawLine(code.location.bottomLeftCorner, code.location.topLeftCorner, "#FF3B58");
          outputMessage.hidden = true;
          outputData.parentElement.hidden = false;
          let codeData = code.data;
          if(codeData != null && (codeData.startsWith('https://') || codeData.startsWith('http://'))){
            outputData.innerHTML = '<a href="' +  codeData + '">' + codeData + '</a>'
          }else{
            outputData.innerText = codeData;
          }
          
        } else {
          //処理なし
        }
      }
      requestAnimationFrame(tick);
    }

    let localStream = null;

    $("#start").click(() => {
      navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" }, audio: false })
        .then(stream => {
          localStream = stream;
          video.srcObject = stream;
          video.setAttribute("playsinline", true); 
          video.play();
          requestAnimationFrame(tick);
          $("#start").hide();
          $("#stop").show();
        }).catch(err => alert(`${err.name} ${err.message}`));
    });

    $("#stop").click(() => {
      localStream.getVideoTracks().forEach((track) => {
        track.stop();
      });
      $("#stop").hide();
      $("#start").show();
    });
  });

</script>

Javascriptを使ってQRコードを読み込む作成するサービスです。
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

以下を参考に作成しています。  
[JavaScript で QRコードのデコード](https://qiita.com/youtoy/items/d94a5bf835d3f4007c81)

カメラ利用の許可が出ますので、許可していただくと使えます。

<button id="start">QRコード読み込みを開始</button>
<button id="stop" hidden>停止</button>

## QRコードのデコード

<div id="output" hidden>
  <div id="outputMessage">こちらにかざした読み込んだQRコードのデコード結果を表示します。</div>
  <div hidden><b>読み込んだデータ:</b> <span id="outputData"></span></div>
</div>

<div id="loadingMessage"></div>
<canvas id="canvas" hidden></canvas>