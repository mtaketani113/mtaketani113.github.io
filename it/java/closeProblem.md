---
layout: default
title: Javaでcloseを書かないとどうなるか
description: Javaでclose処理を書かないと、サーバダウンなどにつながります。実施に経験した事象を紹介します。
category: IT
---

Javaでファイル参照などすると  
`finally`でクローズ処理を書くことがあると思います。

```
  } finally {
    xxx.close();
  }
```
Java7から`try-with-resources`が使えるので  
`try`の後にカッコで書けば`AutoCloseable`が実装  
されていれば勝手にcloseされるようになってます。  
しかし、もし、close処理を怠ったらどうなるのでしょうか。

## 目次

- [ファイルディスクリプタの上限](#anchor1)  
- [APサーバが勝手にシャットダウン](#anchor2)  
- [ログを大量に出力してディスクフル](#anchor3)  
- [APサーバのバグを誘発してハング](#anchor4)  
- [まとめ](#anchor5)

<a id="anchor1"></a>

## ファイルディスクリプタの上限
ファイルディスクリプタ
Linuxではファイルディスクリプタというのがあって、  
OSがどのファイル・コネクションを操作するかなどを判断しています。

そして、このファイルディスクリプタはファイルのオープンや  
コネクションを張ったりすると増えていきます。  
そしてそれは、同一ファイルでも2回オープンすると2つ作られます。

プロセスIDがわかれば、以下のフォルダを見れば状況を確認できます。

`/proc/{プロセスID}/fd`

そして、このファイルディスクリプタには  
上限があり、デフォルトは1024です。  
上限は増やすこともできますが、上限を超えると、  
問題が発生します。  
一時的にプロセスが立って消えるものでは、  
あまり問題ないかもしれませんが、  
APサーバなどずっと動いてるものだと、いきなり爆発します。  
その事例を紹介します。

<a id="anchor2"></a>

## APサーバが勝手にシャットダウン

APサーバの使用によると思いますが、私が使っていたAPサーバは  
ファイルディスクリプタの上限に達するとしれっとシャットダウンしました・・・  
ログに、「上限に達したので落とします」的な内容が書かれていましたｗ  
どうしようもないので仕方ありませんが、パニックｗｗ

<a id="anchor3"></a>

## ログを大量に出力してディスクフル

「ファイルアクセス数の上限に達しました」的なログを1秒間に何十、何百行も出力して  
ディスクフルになりました。鳴りやまない警告( ;∀;)  
ディスクフルになるとそのフォルダを  
共有しているほかのプロセスも動かなくなって  
色々悲しいことが発生します(*'▽')

<a id="anchor4"></a>

## APサーバのバグを誘発してハング

これが一番最悪でした。  
APサーバに対してコネクションを張っている常駐プロセスがいて、  
そのプロセスがAPサーバに対して数分に1回コネクションを張っていました。  
もちろんクローズせずに・・・  
APサーバの方はファイルディスクリプタの上限を増やしているので、  
先に常駐プロセスのほうが落ちてしまいます。  
落ちるときに、強制的にコネクションがクローズされるのですが、  
その際に1000近くのコネクションが一気にクローズされます。  

![1000件のクローズ](/images/it/java/apAndResidentBug.png)

そのAPサーバは古くバグがあって、クローズ処理が並列でタイミング悪く重なると、  
グローバル変数で大事な変数が書き換わって固まるのです・・・  
ですので、1000ものクローズ処理が一気に走るので、週1くらいのペースで固まっていました・・・  

しかも悪いことに、その原因を探るためにデバッグログを設定すると、  
処理がある程度直列になるため、ほとんど発生しなくなりました・・・  

僕はその当時、そのバグを調査しろと言われて毎週上司から詰められてましたｗ  
どうやったらバグを見つけられるか戦略を立てろとか、なんで解決できないんだとか、  
今から考えても、かなりきつかったですねｗｗ

運よくデバッグログを取得できてサポートに問い合わせた解決できたのですが、  
その時のサポートの解答も最悪でした。  
実は次のバージョンでは直っていると・・・  
だったらバグの修正履歴とかで分かんなかったのか？と言いたかったけど、  
解決したので、まーいっかなとｗｗ


常駐プロセスのクローズ処理にさっさと気づいていれば、  
APサーバのバグは直らなくても、ほぼ発生しなくできたのですが、  
まだ駆け出しの自分には荷が重かったですね~  
へっぽこなのでｗ  
常駐プロセスが落ちるときにハングが発生しているのはわかっていたので、  
もっと常駐プロセスのプログラムを調査すべきでしたね。

<a id="anchor5"></a>

## まとめ

close処理は怠ると、様々な不具合につながります。  
短期のテストでは発生しないので見落としがちです。  
ずっと動かすサーバとかなるといつか発生します。  
`try-with-resources`などを利用して、書く量も減るので、  
忘れずに書いてくださいね。  
しばらく動かしてファイルディスクリプタの数が増えていっていないか  
定期的に確認するのもよいかもしれません。  
`/proc/{プロセスID}/fd`で見えるので試してみてください。