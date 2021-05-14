---
layout: default
title: GitHub PagesへのAnalytics設定
description: GitHub PagesのGoogle Analysticの設定方法を旧画面との違いを中心にまとめました。トラッキングがなくストリーミングに代わっていることなど。
category: IT
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
UA-XXXXX→G-XXXXX
になっているようです。

ストリームIDと測定IDの2つがあるのですが、測定IDをサイトに設定するのが正しいようです。  
本当かな？初めてなので確認しつつやってみます。

<a id="anchor2"></a>

## 測定IDをGithub Pagesに設定

Github Pagesでの設定は簡単で`_config.yaml`に
`google_analytics: G-XXXXXX`と書けばよいようです。

<a id="anchor3"></a>

## 感想

まだ、どんなことができるのか手探りで見ている段階です。  
設定してもあまり意味はないとは思いますが、  
世間エンジニアの人がどんなことをやっているのか触れてみたく  
手を出しましたｗ

これから少しずつ勉強していきたいと思います。
