---
layout: default
title: JavaScriptで秘密鍵・公開鍵作成
description: Javascriptを使ってRSA暗号の秘密鍵と公開鍵を作成するサービスです。
category: javascript
created_at: 2022-03-28
last_modifeid_at: 2022-03-28
---
<script src="https://cdn.jsdelivr.net/gh/mtaketani113/cryptico@master/cryptico.min.js"></script> 

<script type="text/JavaScript">
  
  $(function(){

    $("#rsaCreate").click(() => {
      // 秘密鍵を作成
      let passPhrase = $("#passPhrase").val();
      let privateKey = cryptico.generateRSAKey(passPhrase, 1024);

      $("#privateKey").text(JSON.stringify(privateKey));
      
      // 公開鍵を作成
      var publicKey = cryptico.publicKeyString(privateKey);

      $("#publicKey").text(publicKey);
    });
  });

</script>

Javascriptを使ってRSA暗号の秘密鍵と公開鍵を作成するサービスです。
`cryptico.js`を使って作成しています。
パスフレーズによって鍵は変わっていきます。秘密鍵にパスフレーズが含まれているようです。
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

以下を参考に作成しています。  
[JavaScriptで暗号化と復号化を行う方法を現役エンジニアが解説【初心者向け】](https://techacademy.jp/magazine/21244)

## パスフレーズ

<input type="text" id="passPhrase"/>

<button id="rsaCreate">鍵作成</button>

## 秘密鍵

<textarea readonly id="privateKey" style="width: 90%;"></textarea>

## 公開鍵

<textarea readonly id="publicKey" style="width: 90%;"></textarea>