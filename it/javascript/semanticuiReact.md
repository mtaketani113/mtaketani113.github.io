---
layout: default
title: Semantic UI React利用して開発
description: ReactでSemanticUIを利用してみました。環境構築で、「Cannot read properties of undefined (reading 'get')during rendering of asset asset/inline|data:application/x-font-ttf;charset=utf-8;;base64,・・・」と出て構築できませんでした。解決方法を記載します。
category: javascript
created_at: 2022-08-18
last_modifeid_at: 2022-08-18
---

create-react-appで作成したプロジェクトで、
SemanticUIを利用してみました。環境構築で、「Cannot read properties of undefined (reading 'get')during rendering of asset asset/inline|data:application/x-font-ttf;charset=utf-8;;base64,・・・」と出て構築できませんでした。解決方法を記載します。

## 構築方法

方法は簡単で、以下を実施すれば問題ないです。

[Get Started](https://react.semantic-ui.com/usage)

しかし、このままだと、`npm start`しても上記で記載したようなエラーが出て動作します。

## 原因

原因はSemantic UIのバグのようです。
解決方法もありました。

[バグの解決方法](https://github.com/Semantic-Org/Semantic-UI/issues/7073)

1. 以下を実施すれば、解決しました。

`semantic-ui-react/css-patch`をインストール
```
yarn add --dev @semantic-ui-react/css-patch
# or
npm install --dev @semantic-ui-react/css-patch
```

2. package.jsonに以下を追加します。scriptsが既にあれば、そこに`"postinstall": "semantic-ui-css-patch"`を追加です。

```Json
{
 "scripts": {
   "postinstall": "semantic-ui-css-patch"
 }
}
```

## まとめ

Semantic UIをReactで利用する方法を記載しました。
Patchを適用しないと利用できないので、気を付けてください。