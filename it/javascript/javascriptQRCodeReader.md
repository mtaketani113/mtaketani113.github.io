---
layout: default
title: JavaScriptでQRコードリーダー
description: Javascriptを使ってQRコードを読み込み、内容を表示するサービスです。
category: javascript
created_at: 2022-03-24
last_modifeid_at: 2022-03-24
---
<script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.min.js"></script>

<script type="text/JavaScript">
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
        drawLine(code.location.topLeftCorner, code.location.topRightCorner, "#FF3B58");
        drawLine(code.location.topRightCorner, code.location.bottomRightCorner, "#FF3B58");
        drawLine(code.location.bottomRightCorner, code.location.bottomLeftCorner, "#FF3B58");
        drawLine(code.location.bottomLeftCorner, code.location.topLeftCorner, "#FF3B58");
        outputMessage.hidden = true;
        outputData.parentElement.hidden = false;
        outputData.innerText = code.data;
      } else {
        outputMessage.hidden = false;
        outputData.parentElement.hidden = true;
      }
    }
    requestAnimationFrame(tick);
  }

  $(function(){
    // 読み込みのボタン
    const $start = $("#start");
    // video要素
    const $video = $("#video");

    $("#start").click(() => {
      navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" }, audio: false })
        .then(stream => {
          $video[0].srcObject = stream
          video.srcObject = stream;
          video.setAttribute("playsinline", true); 
          video.play();
          requestAnimationFrame(tick);
        }).catch(err => alert(`${err.name} ${err.message}`));
    });
  });

</script>

Javascriptを使ってQRコードを読み込む作成するサービスです。(作成中)
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

<video id="video" autoplay=""></video>

<button id="start">QRコード読み込み</button>

## QRコードのデコード

<div id="loadingMessage"></div>
<canvas id="canvas" hidden></canvas>
<div id="output" hidden>
  <div id="outputMessage">No QR code detected.</div>
  <div hidden><b>Data:</b> <span id="outputData"></span></div>
</div>