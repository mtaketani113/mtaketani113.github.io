---
layout: default
title: EDGEを使ってhttp経由でOfficeファイルをダウンロードできない
description: EDGEを使ってhttp経由でOfficeファイルをダウンロードすると、「安全にダウンロードすることはできません」と出てダウンロードできません。その原因と対処法を説明します。
category: IT
created_at: 2022-01-18
last_modifeid_at: 2022-01-18
---

EDGEを使ってhttp経由でOfficeファイルをダウンロードすると、「安全にダウンロードすることはできません」と出てダウンロードできません。その原因と対処法を説明します。

## 原因

原因は大まかに以下の2つです。

1. Office Viewerで開く設定がされている。
2. httpsからhttpのファイルをダウンロードすると混在したコンテンツということでダウンロードをブロックする仕様になっている。  
EDGEの85以降からファイルの種類を順次拡大していくようです。  
[混在するコンテンツのダウンロード](https://docs.microsoft.com/ja-jp/deployedge/edge-learnmore-mixed-content-downloads)

「１」でOffice Viewerをhttpsで開き、
httpのファイルをダウンロードするため、「２」で混在するコンテンツとみなされてダウンロードをブロックされるようです。

たまに起きない人もいるようで、その差は謎です・・・。
またわかったら追記します。

## 対策

大まかに考えられる対策は以下の2つです。

1. 毎回ブロックされるが、ダウンロードファイルの横の「・・・」をクリックし「保存」→「保持」とクリックしてダウンロードする。

2. 「・・・」→「ダウンロード」→「Officeファイルをブラウザで開く」をOffにする。

![OfficeViwerOff](/images/it/other/OfficeViwerOff.png)

他にも、企業だと
[サポート ポリシー](https://docs.microsoft.com/ja-jp/deployedge/edge-learnmore-mixed-content-downloads#supporting-policies)
を変更することで対応可能のようですが詳細は不明・・・

## まとめ

社内システムだとhttpでやり取りするシステムもあるかと思います。

その場合、上記のようなダウンロードができない問題が発生することがあるようです。

発生した場合に急に問い合わせが来てびっくりするかも。

そんなときに参考にしてください。