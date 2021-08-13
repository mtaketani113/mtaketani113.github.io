---
layout: default
title: GitHub Pagesの作成から初期設定
description: GitHub Pagesの作成から初期設定の仕方のまとめ
category: github
---
Github Pagesの設定でやったことをまとめます。

## 目次

[Github Pagesの作成](#anchor1)  
[テーマの選択](#anchor2)  
[pages-themesからコピー](#anchor3)  
[default.htmlのカスタマイズ](#anchor4)

<a id="anchor1"></a>

## Github Pagesの作成

以下のサイトを参考にしてすぐできました。  
作成だけはだいぶ前にやったのであまり覚えていない(*'▽')

[Github Pagesの作成](https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site)

<a id="anchor2"></a>

## テーマの選択

リポジトリの[setting]->[Pages]->[Change theme]を選択して、  
自分の使いたいテーマを[Select theme]で選択する。

私は、Archtect themeを選択しました。  
文字がかわいいからｗ

<a id="anchor3"></a>

## pages-themesからコピー

このままでも、markdownのファイルを作成すれば、  
画面が追加されていくのですが、
テーマをいじったりできるようなので、  
以下のサイトから
- _layouts
- _sass
- assets

をコピーしてGithubに上げます。

[pages-themes/architect](https://github.com/pages-themes/architect)

自身の選択したテーマを選んでください。  
[pages-themes](https://github.com/pages-themes/)

<a id="anchor4"></a>

## default.htmlのカスタマイズ

### headにtitleを追加を追加しました。

`\{\{ site.XXXXX \}\}`と書くことで、`_config.yml`のXXXXに該当する値をとってくるよう(^^)/  
`\{\{ page.XXXXX \}\}`と書くことで、読み込んでいるページの頭に書いた以下のXXXXの部分を呼んでくれるみたい！！

```
---
XXXX: ほげほげ
---
```

具体例はここを見てください。
[createGithuPages.md](https://github.com/mtaketani113/mtaketani113.github.io/blob/master/it/github/createGithuPages.md)

なので`default.html`に以下を追加して、タイトルが出るようにしました。

```html
<title>
  \{\{ page.title \}\} | \{\{ site.title \}\} - @mtaketani113
</title>
```

注意：`{}`がなぜかコードブロックでも読まれてしますので
`\`をつけています。  
利用する場合は`\`を一括削除してくださいm(__)m

### ページ一覧を追加

また、トップページなどにサイトを追加するたびにリンクを足していくのは面倒かと思い、ページを追加するごとに自動で足せないかと思いました。  
また、このサイトにはIT関連、数学、日記と3つのカテゴリーがあるので、カテゴリーごとにリンクをまとめられないかと。

どうも`site.html_pages`でサイトの一覧をとれるようなので、
さっきの`{{ page.XXXX }}`とあわせて、
各ページに
```
category: IT
```
などを足すことにしました。

これで、以下のように書くことで、自動で出力されるようにした。

```html
<h3>IT関連</h3>
\{\% for html_page in site.html_pages \%\}
  \{\% if  html_page.category contains 'IT' \%\}
    <li><a href="\{\{ html_page.url \}\}">\{\{ html_page.title \}\}</a></li>
  \{\% endif \%\}
\{\% endfor \%\}
<h3>数学</h3>
\{\% for html_page in site.html_pages \%\}
  \{\% if  html_page.category contains '数学' \%\}
    <li><a href="\{\{ html_page.url \}\}">\{\{ html_page.title \}\}</a></li>
  \{\% endif \%\}
\{\% endfor \%\}
<h3>日記</h3>
\{\% for html_page in site.html_pages \%\}
  \{\% if  html_page.category contains '日記' \%\}
    <li><a href="{{ html_page.url }}">\{\{ html_page.title \}\}</a></li>
  \{\% endif \%\}
\{\% endfor \%\}
```

ただ、2点課題が。
1. for文3回実行されているので、buildが遅くなる？？  
(大したレベルじゃないかな？)
2. ~~`html_page`何順に取得されるのだろう？？~~

`html_page`はどうやらビルドされた  
htmlのファイル名順になっているようです。。  
ファイル名の頭に番号をつけて  
順番をコントロールするか、何かの方法でソートするか  
考える必要がありそうです。  

まー、やりながら修正していこうかと思います。  
わかったらまたここに記載します。