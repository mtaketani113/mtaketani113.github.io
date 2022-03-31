---
layout: default
title: JavaScriptで暗号化
description: Javascriptを使って公開鍵で暗号化するサービスです。
category: javascript
created_at: 2022-04-01
last_modifeid_at: 2022-04-01
---
<script src="https://cdn.jsdelivr.net/gh/mtaketani113/cryptico@1.0.1/cryptico.min.js"></script> 

<script type="text/JavaScript">
  
  $(function(){

    $("#crypt").click(() => {
      let planText = $("#planText").val();
      let publicKey = $("#publicKey").val();

      var encryptionResult = cryptico.encrypt(planText, publicKey);
      $("#cryptText").text(encryptionResult.cipher);
    });
  });

</script>

[JavaScriptで秘密鍵・公開鍵作成](/it/javascript/javascriptRSA.html)で作成した公開鍵で暗号化するサービスです。
どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

以下を参考に作成しています。  
[JavaScriptで暗号化と復号化を行う方法を現役エンジニアが解説【初心者向け】](https://techacademy.jp/magazine/21244)

## 暗号化する文

<input type="text" id="planText"/>

## 公開鍵

<textarea id="publicKey" style="width: 90%;"></textarea>

<button id="crypt">暗号化</button>

## 暗号化した文

<textarea id="encryptText" style="width: 90%;"></textarea>