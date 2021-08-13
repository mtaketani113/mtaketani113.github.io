---
layout: default
title: GitHub PagesへのAnalytics設定
description: GitHub PagesのGoogle Analysticの設定方法を旧画面との違いを中心にまとめました。トラッキングがなくストリーミングに代わっていることなど。
category: github
---

今まで会社の閉じた世界でしかITを触ってこなかったので、  
どんなものか知りたくやってみました。

GitHub PagesのGoogle Analysticの設定方法を旧画面との違いを中心にまとめました。  
トラッキングがなくストリーミングに代わっていることなど。

## 目次

[Analysticの設定](#anchor1)  
[測定IDをGithub Pagesに設定](#anchor2)  
[感想](#anchor3)  

<a id="anchor1"></a>

## Analysticの設定

Googleアカウントはあるのにさらにアカウント作成を要求される？  
なんだろうと思って調べたら、別物らしい。
Googleアカウントの中にAnalysticのアカウントが複数ある感じのようです。

なんか`Google Analytics`で`使い方`見ないな感じで検索したんですが、  
なんか出てくる画面の感じと違う・・・  
トラッキングIDというのが見つからず、代わりにストリーミングの測定IDになってるようです。

コード体系も、
UA-XXXXXXXXXX→G-XXXXXXXXXX
になっているようです。

ストリームIDと測定IDの2つがあるのですが、測定IDをサイトに設定するのが正しいようです。  
本当かな？初めてなので確認しつつやってみます。

<a id="anchor2"></a>

## 測定IDをGithub Pagesに設定

~~Github Pagesでの設定は簡単で`_config.yaml`に
`google_analytics: G-XXXXXXXXXX`と書けばよいようです。~~

上記、やってみたのですが、自分がアクセスしても、  
知り合いにアクセスしてもらってもアクセス数が「0」から変化しませんでした。

ですので、以下を`head`に追加して、解決しました。

```
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

`G-XXXXXXXXXX`のところに測定IDを入力しました。  
`google_analytics: G-XXXXXXXXXX`は削除しました。

default.htmlを追加しているので、何か壊れてるのかな？

<a id="anchor3"></a>

## 感想

まだ、どんなことができるのか手探りで見ている段階です。  
設定してもあまり意味はないとは思いますが、  
世間エンジニアの人がどんなことをやっているのか触れてみたく  
手を出しましたｗ

これから少しずつ勉強していきたいと思います。
