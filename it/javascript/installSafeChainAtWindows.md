---
layout: default
title: safe-chainのWindowsでの利用
description: safe-chainをWindowsで利用しようとすると、なぜかnpmのAliasがうまく作られないようで、無理やり対処した方法を記載します
category: javascript
image: /images/it/javascript/javascript_code.jpg
created_at: 2025-11-28
last_modifeid_at: 2025-11-28
---

Shai-Hulud が2度わたって、npmのマルウェアが広がっていることもあり、  
safe-chainを入れようと思いました。

VSCode(Windows)でsafe-chainを利用しようとすると、  
なぜかnpmのAliasがうまく作られないようで、無理やり対処した方法を記載します。

## 現象

safe-chainのインストール手順は、以下です。
1. 以下のコマンドを実行
```TypeScript
npm install -g @aikidosec/safe-chain
safe-chain setup
```
safe-chain setupでnpm等のaliasを作成される・・・はず。

2. ターミナルを再起動

3. 以下のコマンドを実行してインストールに失敗すればOK
```TypeScript
npm install safe-chain-test
```
エラーメッセージはこんな感じなのが出ればOKです。

```TypeScript
✖ Safe-chain: Malicious changes detected:
 - safe-chain-test@0.0.1-security

Exiting without installing malicious packages.
```

しかし、再起動して「3」を実行しても`safe-chain-test`がインストールされてしまう。

`aikido-npm`でinstallしてみると失敗するのでsafe-chainは入っているよう

```TypeScript
aikido-npm install safe-chain-test
✖ Safe-chain: Malicious changes detected:
 - safe-chain-test@0.0.1-security
```

なので、おそらくaikido-npmをnpmに置き換えるAliasがうまくいってないよう。

## 解決方法

1. 以下のコマンドでProfileを開く
```PowerShell
notepad $PROFILE
```

2. 開かなかった場合は以下のコマンドで作成
```PowerShell
New-Item -type file -force $PROFILE
```

3. 開いたPROFILEに以下を記載して保存 
```PowerShell
New-Item -type file -force $profile
```

4. ターミナルを再起動

これで`npm`を実行すると`aikido-npm`が実行されるようにできました。  ただ、もっとうまい方法とかある気がするんですよね。

## まとめ

WindwosのVSCodeでだとPowerShellのせいか、うまくAliasが作られないようです。  
無理やりPowerShellのAliasに登録して`npm`でも利用できるようにできますが、`aikido-npm`をそのまま利用してもよい気がしますね。  
もし参考になれば幸いです。