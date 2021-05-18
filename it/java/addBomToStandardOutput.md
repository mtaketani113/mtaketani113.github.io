---
layout: default
title: Javaの標準出力にBOMを追加
description: Javaの標準出力にBOMを追加する方法を記載しました。
category: IT
---

ファイル作成時にBOMをつける方法は、  
色々サイトに書いてあって
`OutputStream`に`0xef`,`0xbb`,`0xbf`にwriteすればよいです。

ただ今回は、CSVファイル形式のデータを  
サーバ経由でUTF-8でレスポンスが返ってくるので  
それを標準出力としてリダイレクトするという制約がありました(*'▽')

なんでこんなまどろっこしいことするかは置いといて、  
BOMなしのUTF-8のCSVファイルはExcelで開くと日本語が文字化けします。

ですので、BOM付きのファイルを読み込んで、BOMを取り除くプログラムを参考に  
逆にBOMをStringに追加するプログラムを書いてみました。
結果として、Excelで文字化けしないようになりました(*'ω'*)

↓ strからBOMを付加してresultを作成
```Java
byte[] b = str.getBytes();
byte[] n = new byte[b.length + 3];
n[0] = -17;
n[1] = -69;
n[2] = -65;
for(int i=3,k=0; i < n.length; i++, k++){
  n[i] = b[k];
}
String result = new String(n);
```

頭の3バイトにBOMを足してその後は元の文字列を足していく感じです。

そもそも、CSVじゃなくてjsonじゃないのとか  
色々突っ込みどころ満載でしょうが、  
まだまだCSVが好きなんですよね～

環境にもよると思いますが、上記のプログラムをjavaコマンドで動かすときは`-Dfile.encoding=UTF-8`のオプションがいるかもですね。  
Windowsなら(｀･ω･´)ｷﾘ

## 参考サイト

[Java で操作するUTF-8エンコードのＣＳＶに BOMを付ける。](https://oboe2uran.hatenablog.com/entry/2018/07/12/223138)  
[Java UTF-8のテキストファイルをBOM付きで作成する](https://www.javalife.jp/2018/02/26/post-441/)