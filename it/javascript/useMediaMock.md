---
layout: default
title: useMediaを利用した場合のテスト
description: ReactでuseMediaを利用すると、テストが動作しなくなります。その解消方法を紹介します。
category: javascript
created_at: 2022-12-29
last_modifeid_at: 2022-12-29
---

ReactでResponsive対応でuseMediaを利用しました。  
すると、以下のようなエラーが出てテストできなくなります。

```TypeScript
typeerror: window.matchmedia is not a function
```

matchmediaをモックにする必要がありそうなエラーですね。

今回はその対処方法を記載します。

## 解決方法

早速ですが、今回解決した方法を記載します。

テストに以下を追記しました。

```TypeScript
  beforeAll(() => {
    window.matchMedia =
      window.matchMedia ||
      function () {
        return {
          matches: false,
          addListener: function () {},
          removeListener: function () {},
        };
      };
  });
```

beforeAllに入れる必要はないかもしれないですがｗ

サンプルコードは[こちら](https://github.com/mtaketani113/omu-karate-page/blob/v1.2.6/src/components/TrainingMenue.test.tsx)

## うまくいかなかった方法

Google先生で調べて、色々出てきたのですが、
最初に見つかったのが以下を追加する方法でした。

```TypeScript
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation(query => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(), // Deprecated
    removeListener: jest.fn(), // Deprecated
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});
```

参照サイトは[こちら](https://jestjs.io/docs/manual-mocks#mocking-methods-which-are-not-implemented-in-jsdom)

これを実施すると以下のようなエラーが出ました。
```TypeScript
typeerror: cannot read properties of undefined (reading 'matches')
```

うーん、エラーが変わったんでモックは効いてるっぽいですけど・・・

何か間違ってるためだと思いますが・・・
何かわからず。もっと手っ取り早い方法を見つけたのが
前に記載した方法です。

ちなみに、
`use-media`はReact18だと`--legacy-peer-deps`などを付けないとインストールできなかったので
`react-use`を利用しました。

これも影響してるのかもしれませんが、
いまいちわからず・・・とりあえず動いたから良しとしてますが気になるな~

## まとめ

useMediaを利用する場合は、テストでモックを利用する必要があります。
今回解決した方法を記載しましたので、お試しください。