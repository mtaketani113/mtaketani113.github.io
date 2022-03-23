---
layout: default
title: JavaScriptでQRコードリーダー
description: Javascriptを使ってQRコードを読み込み、内容を表示するサービスです。
category: javascript
created_at: 2022-03-24
last_modifeid_at: 2022-03-24
---

<script type="text/JavaScript">

  $(function(){
    // 読み込みのボタン
    const $start = $("#start");
    // video要素
    const $video = $("#video");

    $("#start").click(() => {
      navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" }, audio: false })
        .then(stream => $video[0].srcObject = stream)
        .catch(err => alert(`${err.name} ${err.message}`));
    });
  });

</script>

Javascriptを使ってQRコードを読み込む作成するサービスです。(作成中)
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

<video id="video" autoplay=""></video>

<button id="start">QRコード読み込み</button>

## QRコードのデコード

<div id="qr-text">
未実行
</div>