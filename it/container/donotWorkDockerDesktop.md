---
layout: default
title: Docker Desktop for Windows が動かない
description: Proxy環境下でDocker Desktop for Windows が動かないときの対処法を記載します。
category: IT
---

会社環境でDocker Desktop for windowsを使っていて  
3回ほど動かなくて試行錯誤したので、まとめます。

前提として、会社ではProxy環境下で作業しています。  
また、会社のPCは半年～1年くらい世間から遅れたバージョンを使っています。  
大きくはこの2つが問題を引き起こしていたようです( ;∀;)

ちなみにWindowsなので(｀･ω･´)ｷﾘ

## 目次

[Proxyを設定](#anchor1)  
[Build Kitの設定を削除](#anchor2)  
[Build Kitの設定をflaseに](#anchor3)  
[Use the WSL 2 をOffに](#anchor4)
[感想](#anchor5)

<a id="anchor1"></a>

## Proxyを設定

Proxy環境化で作業する場合、Proxyの設定を記載する必要があります。
Dockerfileにかけますが、Docker Desktopの設定でProxyを設定しましょう。

![プロキシー設定](/images/it/container/proxySettingForDockerDesktop.png)

<a id="anchor2"></a>

## Build Kitの設定を削除

パソコンを変えることになって、新しいパソコンに  
Dcoker Desktopを入れるとなぜか動かない・・・

` [internal] load metadata for docker.io XXXX` みたいな感じのエラーが出て動かない。

調べてみるとBuild KitというのがProxy環境下では普通では使えないようで、  
設定を削除する必要があるよう。

なので、Docker Desktopの  
[Settings]->[Docker Engine]の以下の設定を削除して再起動

```
  "features": {
    "buildkit": true
  },
```

なんか起動が遅いけど動くようになった・・・

<a id="anchor3"></a>

## Build Kitの設定をflaseに

Build Kitの設定を消してから数か月。  
dockerから少し離れていて、久しぶりに動いたら
また同じエラーが・・・  

` [internal] load metadata for docker.io XXXX` 

何かわからないけど、数か月の間に何度かDocker Desktopをアップデートしていたのでそれが原因かと。    
試行錯誤して、数か月前に削除した設定を復活させて  
設定を`flase`にしたら動きました。

設定は以下に変えました。

```
  "features": {
    "buildkit": false
  },
```

おそらくですが、buildkitのデフォルト設定が`true`に変わったんじゃないのかと思っています。

<a id="anchor4"></a>

## Use the WSL 2 をOffに

次に後輩が、Docker Desktopを入れるとなぜか今度は起動すらしない。  
クジラさんのマークが赤くなって起動していない・・・

自分との設定の差を見て
[Settings]->[General]の`Use the WSL 2 based engine`にチェックが入っていたのでそれをOffにすると動いた・・・

Docker DesktopはWindows Homeでも動くように  
WSL2条でも動くようになりました。

僕らのはWindowsのバージョンが古いから？  
Proだから？動かなかったのか？  
そこは不明ですがとりあえず動いたのでよしとしましょうｗ

<a id="anchor2"></a>

## 感想

会社環境は色々と自宅のPCとは状況が違うので  
家で試したら何もなく動くのに、会社だと動かないとか、  
よくありますよね( ;∀;)  
ほんと難しい。。
