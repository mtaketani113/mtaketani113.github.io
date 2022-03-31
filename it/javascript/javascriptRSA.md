---
layout: default
title: JavaScriptで秘密鍵・公開鍵作成
description: Javascriptを使ってRSA暗号の秘密鍵と公開鍵を作成するサービスです。
category: javascript
created_at: 2022-03-28
last_modifeid_at: 2022-04-01
---
<script src="https://cdn.jsdelivr.net/gh/mtaketani113/cryptico@1.0.1/cryptico.min.js"></script> 

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

    $("#registCookie").click(() => {
      if(confirm("cookieに秘密鍵と公開鍵を登録します。よいですか？")){
        let m = 60*60*24*30;
        document.cookie = "_publickKey=" + $("#publicKey").text()
                            + "; path=/it/javascript/;max-age=" + m;
        document.cookie = "_privateKey=" + $("#privateKey").text()
                            + "; path=/it/javascript/;max-age=" + m;
      }
    });

    $("#deleteCookie").click(() => {
      if(confirm("cookieから削除します。よいですか？")){
        document.cookie = "_publickKey=;max-age=0";
        document.cookie = "_privateKey=;max-age=0";
      }
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

<button id="registCookie">cookieに登録</button>

<button id="deleteCookie">cookieから削除</button>