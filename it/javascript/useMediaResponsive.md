---
layout: default
title: useMediaを利用したレスポンシブ対応
description: ReactでuseMediaを利用してレスポンシブ対応する方法を記載します。
category: javascript
image: /images/it/javascript/javascript_code.jpg
created_at: 2023-01-16
last_modifeid_at: 2023-01-16
---

ReactでuseMediaを利用してレスポンシブ対応する方法を記載します。
React18より前では、`use-media`をインストールするとよかったのですが、
古いためか`--legacy-peer-deps`などを付けないとインストールできない状態です。

代替となる`react-use`を利用する方法を紹介します。

## react-useの利用

ソースコードの例は[こちら](https://github.com/mtaketani113/omu-karate-page/blob/v1.5.10/src/components/Place.tsx)から参照できます。
参考にしてください。

まずはインストールです。
以下でインストールできます。

```Shell
npm install --save use-media
```

以下のように書くことで、条件に当てはまるか当てはまらないかを
`true`,`false`で取得できます。

```Javascript
const isWide = useMedia('(min-width: 780px)');
```

これを利用して参考のソースではスタイルを切り替えています。

```Javascript
<iframe
  title="xxx"
  src="xxxx"
  style={{ border: '0', width: isWide ? '90%' : '100%', aspectRatio: '16/9' }}
  allowFullScreen={true}
  loading="lazy"
  referrerPolicy="no-referrer-when-downgrade"
></iframe>
```

例ではGoogleの地図情報を表示しています。
幅によって`width`を切り替えています。

また、useMediaを利用すると、テストでエラーが出るようになります。  
その対処方法は[こちら](/it/javascript/useMediaMock.html)に記載しています。

## まとめ

`react-use`の`useMedia`を利用する方法を記載しました。
React18では`use-Media`だと少し古い扱いのようです。

useMediaを利用するとテストがエラーになるので[リンク](/it/javascript/useMediaMock.html)を参考に対処してみてください。
