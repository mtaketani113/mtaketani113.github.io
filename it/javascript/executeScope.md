---
layout: default
title: $(document).ready内の関数を呼ぶ方法
description: $(document).ready内の関数を外部から無理やり呼び出す方法を紹介します。
category: IT
---

$(document).ready(function()の中の関数を外部から無理やり呼び出す方法を紹介します。

たとえば、以下のように`$(document).ready`内で  
関数を定義しても外部から呼び出すことはできません。  

```JavaScript
$(document).ready(function(){
　　const test = () => console.log('test');
});
```

ボタンなどで、`onclick=test()`などしても実行されません。
関数testはありません的なエラーが出ると思います。

しかし、DOMがロードされて操作可能になったタイミングで`onclick`も効くようにしたいとか  
あるかもしれませんｗｗ  
読み込みがすごい遅い画面とか、読み込み前にボタンを押されると困ることありますよね。  
（そもそもそんな遅い画面なのがまずい気がしますが・・・）

そんな時に、かなり無理やりな感じはしますが、以下のように  
windowに登録することで使えます。

```JavaScript
$(document).ready(function(){
　　window.FUNC = {test : () => console.log('test')};
});
```

これにより外部からも  
`window.FUNC.test()`や`window`を省略して`FUNC.test()`  
とすることで呼び出せます。

`FUNC`とかも書くのがいやだったら
```JavaScript
$(document).ready(function(){
　　window.test = () => console.log('test');
});
```
とすれば、`test()`で実行できます。

外部から呼べるか呼べないかといえば、頑張れば呼べますという紹介でした。