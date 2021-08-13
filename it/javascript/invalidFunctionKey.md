---
layout: default
title: JavaScriptによるファンクションキー無効化の落とし穴
description: JavaScriptによるファンクションキーを無効にできますが、URLにフォーカスをあてると有効になります。
category: javascript
---
<script>
    window.document.onkeydown = function(event){
        if(event.keyCode >= 112 && event.keyCode <= 123){
            event.keyCode = null;
            event.returnValue = false;
        }
    };
</script>

このページでは以下のコードにより、ファンクションキーが効かないようにしています。

```JavaScript
<script>
    window.document.onkeydown = function(event){
        if(event.keyCode >= 112 && event.keyCode <= 123){
            event.keyCode = null;
            event.returnValue = false;
        }
    };
</script>
```

しかしながら、URLの所にフォーカスをあてて実行すると、
実行されると思います。

実際にURLのところにフォーカスをあてて、F12など実行してみてください。
おそらく開発者ツールが出てくると思います。

古いIEなどでは、これにプラスして、URLやメニューなどを表示しないようにしています。  
これで絶対に実行できないと思うかもしれませんが、残念ながら実行できます。  
メニュー非表示するには新しいwindowで開いて元のwindowを閉じる必要があるのですが、  
それをIEの設定で、「常に新しいタブでポップアップを開く」を設定しておくと、  
メニューを消す機能が効かずURLもメニューも表示されます。  
戻るも押せます。これをやってるから戻るのことを全く考慮をせず、えらいことになってるのを見たことがありますw  
残念ですが、これを回避する方法はクライアントの機能なのでどうしようもないでしょう( ;∀;)  
社内システムでは、みんなにお願いすれば何とかなるかもしれませんが、それでもやる人はやる・・・  
気を付けてください。

## まとめ
こういったJavaScriptによるキーの無効化は、あくまでユーザのポカヨケ程度です。  
これにより、絶対に実行できないというわけではないので気を付けましょう。