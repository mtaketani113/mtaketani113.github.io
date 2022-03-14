---
layout: default
title: Windows11でIEを開く
description: 2022年6月15日にIEのサポートがなくなって使えなくなります。Windows11ではIEのExeファイルを実行してもEDGEが開くようになっていますが、VBScriptでIEを開く方法があります。ただ、2022年6月15日以降も使えるかは不明。
category: IT
created_at: 2022-03-15
last_modifeid_at: 2022-03-15
---

 2022年6月15日にIEのサポートがなくなって使えなくなります。Windows11ではIEのExeファイルを実行してもEDGEが開くようになっていますが、VBScriptでIEを開く方法があります。
 超簡単。

1. 「.vbs」という拡張子で、ファイルを作成する

2. 作成したファイルに以下を記載する。

```JavaScript
Dim objIE
Set objIE = CreateObject("InternetExplorer.Application")

objIE.Visible = True

objIE.Navigate2 "https://mtaketani113.github.io/"
```
 
3. ファイルをダブルクリックして実行する。

これでIEが起動します。
Windows11の初期段階ではEDGEが起動したのですが、
現状は変更されてIEが起動するようになったようです。

 ただ、2022年6月15日以降も使えるかは不明です。
 多分使い続けられそうな雰囲気・・・

 これがあると、VBSやVBAなどでIEを使って自動プログラムを作成している場合、
 移行しなくていいという特典が(*'▽')

 実際うちの会社では以降しんでいいかもーとか言ってる人がいましたｗ

 個人的には、いつまでもIEが使われる原因になるのでなくなってほしい。
 Windowsの気分でなくなるかもしれないですしね。

 ## まとめ

 VBScriptなどの`CreateObject`でWindows11でもIEを起動できます。
 いつまでできるのかは不明ですが、Windows11の初期はEDGEが起動していました。変わったようです。
 これを使って、昔作ったIEの自動化ツールなどを利用可能ですが、
 いつまでもIEに依存するのも微妙化と思うので早く移行すべきかと個人的には思います。