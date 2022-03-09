---
layout: default
title: JavaScriptでパスワード自動生成
description: Javascriptを使ってパスワードを自動生成するサービスです。どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。
category: javascript
created_at: 2022-03-05
last_modifeid_at: 2022-03-05
---

<script>
  const smallAlphabet = "abcdefghijk"
  const largeAlphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
  const number = "0123456789"
  const symbol = "!#$%&()"

  function createPassword(){
    var useString = "";
    if(document.getElementById("useSmallAlphabet").checked){
      useString += smallAlphabet;
    }
    if(document.getElementById("useLargeAlphabet").checked){
      useString += largeAlphabet;
    }
    if(document.getElementById("useNumber").checked){
      useString += number;
    }
    if(document.getElementById("useSymbol").checked){
      useString += symbol;
    }

    var length = document.getElementById("passwordLength").value
    var lengthNumber
    if(isNaN(Number(length))){
      lengthNumber = 5;
    }else{
      lengthNumber = Number(length);
    }
    var result = Array.from(Array(lengthNumber))
                  .map(()=>useString[Math.floor(Math.random()*useString.length)])
                  .join('');
    document.getElementById("createdPassword").value = result;

  }
  window.onload = (event) => {
    createPassword()
  };
</script>

Javascriptを使ってパスワードを自動生成するサービスです。どこにも情報を送っていないですし、この画面だけで完結しています。安心して使ってください。

## 利用する文字列

<input type="checkbox" id="useSmallAlphabet" checked="checked"/>
<label for="useSmallAlphabet">アルファベット(小文字)を利用</label>

<input type="checkbox" id="useLargeAlphabet" checked="checked"/>
<label for="useLargeAlphabet">アルファベット(大文字)を利用</label>

<input type="checkbox" id="useNumber" checked="checked"/>
<label for="useNumber">数字を利用</label>

<input type="checkbox" id="useSymbol"/>
<label for="useSymbol">記号を利用</label>

## 文字数

<input type="text" id="passwordLength" value="5"/>

<input type="button" onclick="createPassword()" value="パスワード生成"/>

## 生成されたパスワード

<input type="text" id="createdPassword" readonly="readonly"/>

