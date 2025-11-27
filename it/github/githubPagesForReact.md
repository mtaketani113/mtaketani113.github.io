---
layout: default
title: Github PagesにReactで作成したページを公開
description: 少しはまったので、Github PagesにReactで作成したページを公開する方法を記載します。
category: github
created_at: 2022-12-07
last_modifeid_at: 2022-12-15
---

少しはまったので、Github PagesにReactで作成したページを公開する方法を備忘録としても記載します。

## gh-pagesを利用して公開

gh-pagesを利用すると、
gh-pagesというブランチにbuildしたデータを配置してくれます。

gh-pagesというブランチを作ると、以下のようなURLで公開されます。

`https://{ユーザ名}.github.io/{リポジトリー名}`

されなければSettingのPagesでブランチとフォルダを設定してください。

## gh-pagesの設定

まずはインストールです。

```Shell
npm install gh-pages --save-dev
```

次に`package.json`の`scripts`に`predeploy`と`deploy`
を追加します。

```Json
  "scripts": {
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
  },
```

`gh-pages -d build`は`build`フォルダを公開するという意味になります。
デプロイするフォルダが違う場合は`build`の部分を変えてください。

ここまで設定すれば、以下のコマンドで公開できます。

```Shell
npm run deploy
```

## 404対策

実はこのままでは、直下のURLしか参照できません。  
つまり見えるのはここだけ。

`https://{ユーザ名}.github.io/{リポジトリー名}`

ここが、はまりポイントです。  
こちらのサイトに対策が記載されていました。

[GitHub Pages で React Router を使った SPA サイトを動かす方法](https://maku.blog/p/9u8it5f/)

まとめると、以下の2つを実施することで対策でいます。

① 404.htmlの配置

まずは404.htmlを公開されるフォルダ（create-react-appで作った場合はpublicの直下）
に配置します。

記載する内容の例は[こちら](https://github.com/mtaketani113/omu-karate-page/blob/main/public/404.html)です。

② index.htmlにスクリプト追加

publicの下にあるindex.htmlに以下を追加します。

```Html
    <script>
      (function(){
        const query = window.location.search;
        if (query.startsWith('?p=')) {
          const route = decodeURIComponent(query.substr(3));
          window.history.replaceState(null, null, route);
        }
      })();
    </script>
```

## Google Search Consoleの対策

ただし、Google Search Consoleなどで
作成した下位ページのIndexを登録しようとすると
404発生→404.htmlでリダイレクトという処理になっているせいか
404エラーを先に引っ掛けて登録できませんでした。

なので、対策として下位ページのindex.htmlをほぼコピーしたhtmlを
下位ページの名前にして作成しました。

何を言ってるかわからないかもしれないので、
参考が[こちら](https://github.com/mtaketani113/omu-karate-page/blob/main/public/schedule.html)です。

/scheduleというURLだったので、schdule.htmlを作って置きました。

/scheduleと/schedule/は異なり、前者はschdule.html 後者はschdule/index.htmlにアクセスしようとします。
両方対策するのは面倒ですし、そもそもページごとにファイル作成しないといけないですし、
あんまりスマートではないですよね・・・

一応登録はできたのですが・・・他に対策ないんでしょうかね？

## おまけ Github Actionsでデプロイ設定

ここまでで`npm run deploy`をすれば公開できるのですが、
Github Actionsを使ってTagを作成したらデプロイできるようにもできます。

書き方は[こちら](https://github.com/mtaketani113/omu-karate-page/blob/main/.github/workflows/deploy.yml)を参照ください。

## まとめ

Github Pagesを使ってReactで作成した画面を公開することができます。
是非やってみてください。