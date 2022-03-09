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
    let useString = "";
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

    let length = document.getElementById("passwordLength").value
    let lengthNumber
    if(isNaN(Number(length))){
      lengthNumber = 5;
    }else{
      lengthNumber = Number(length);
    }
    let result = Array.from(Array(lengthNumber))
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
<label for="useSymbol">記号を利用※</label>

※利用する記号・・・!#$%&()

## 文字数

<input type="text" id="passwordLength" value="5"/>

<input type="button" onclick="createPassword()" value="パスワード生成"/>

## 生成されたパスワード

<input type="text" id="createdPassword" readonly="readonly"/>



## 実装方法

実際に書いたプログラムを解説します。

まずはHTMLを作成

```Html
<input type="checkbox" id="useSmallAlphabet" checked="checked"/>
<label for="useSmallAlphabet">アルファベット(小文字)を利用</label>

<input type="checkbox" id="useLargeAlphabet" checked="checked"/>
<label for="useLargeAlphabet">アルファベット(大文字)を利用</label>

<input type="checkbox" id="useNumber" checked="checked"/>
<label for="useNumber">数字を利用</label>

<input type="checkbox" id="useSymbol"/>
<label for="useSymbol">記号を利用※</label>
```

つぎはJavascriptです。

次のサイトを参考にして関数を作成しました。
[JavaScriptでお手軽にランダム文字列の生成](https://qiita.com/fukasawah/items/db7f0405564bdc37820e)

まずは全体です。

```Javascript
const smallAlphabet = "abcdefghijk"
const largeAlphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
const number = "0123456789"
const symbol = "!#$%&()"
function createPassword(){
  let useString = "";
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

  let length = document.getElementById("passwordLength").value
  let lengthNumber
  if(isNaN(Number(length))){
    lengthNumber = 5;
  }else{
    lengthNumber = Number(length);
  }
  let result = Array.from(Array(lengthNumber))
                .map(()=>useString[Math.floor(Math.random()*useString.length)])
                .join('');
  document.getElementById("createdPassword").value = result;

}
window.onload = (event) => {
  createPassword()
};
```

まずは定数を準備。
チェックごとに利用する文字列を準備します。

```Javascript
const smallAlphabet = "abcdefghijk"
const largeAlphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
const number = "0123456789"
const symbol = "!#$%&()"
```



ここの部分でチェックを入れた文字列をuseStringに足していきます。
```Javascript
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
```

文字数のところは数字以外を入れる可能性があるので、
数字以外の場合はデフォルトで5としました。
```Javascript
  let length = document.getElementById("passwordLength").value
  let lengthNumber
  if(isNaN(Number(length))){
    lengthNumber = 5;
  }else{
    lengthNumber = Number(length);
  }
```

そしてランダム文字列を作って、結果を更新します。
```Javascript
  let result = Array.from(Array(lengthNumber))
                .map(()=>useString[Math.floor(Math.random()*useString.length)])
                .join('');
  document.getElementById("createdPassword").value = result;
```

`Array.from(Array(lengthNumber))`で文字数で指定した分の配列を作ります。
`Array(lengthNumber)`だけだと、幅が文字数分のArrayを作るだけで1つずつの要素には何も入っていない状態なので、
`map`をしても処理は繰り返されません。

そして`map`で書くように文字列仕様文字列からランダムな番号の文字を切り取り、1つずつ入れていきます。

その後`join`で配列を結合して文字列にします。
その作られた文字列を`document.getElementById("createdPassword").value = result;`で結果の欄に入れます。

以下では初期表示の時にこの関数を呼び出して、実行しています。
`window.onload`としているのは、画面を読み終わった後に実行するためです。
```Javascript
window.onload = (event) => {
  createPassword()
};
```

