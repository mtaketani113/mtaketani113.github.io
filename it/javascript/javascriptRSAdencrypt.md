---
layout: default
title: JavaScriptで暗号化
description: Javascriptを使って秘密鍵で複合化するサービスです。
category: javascript
created_at: 2022-04-01
last_modifeid_at: 2022-04-01
---
<script src="https://cdn.jsdelivr.net/gh/mtaketani113/cryptico@1.0.1/cryptico.min.js"></script> 

<script type="text/JavaScript">
  
  $(function(){

    $("#decrypt").click(() => {
      let encryptText = $("#encryptText").val();
      let rsaKeyString = $("#rsaKeyString").val();

      let rsaKey = cryptico.parseStringToRsaKey(rsaKeyString);
      
      var decryptionResult = cryptico.decrypt(encryptText, rsaKey);
      $("#decryptText").val(decryptionResult);
    });
  });

</script>

[JavaScriptで秘密鍵・公開鍵作成](/it/javascript/javascriptRSA.html)で作成した秘密鍵で復号化するサービスです。
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

以下を参考に作成しています。  
[JavaScriptで暗号化と復号化を行う方法を現役エンジニアが解説【初心者向け】](https://techacademy.jp/magazine/21244)

## 暗号化された文

<textarea id="encryptText" style="width: 90%;"></textarea>

## 秘密鍵

<textarea id="rsaKeyString" style="width: 90%;"></textarea>

<button id="decrypt">復号化</button>

## 復号化した文

<textarea readonly id="decryptText" style="width: 90%;"></textarea>