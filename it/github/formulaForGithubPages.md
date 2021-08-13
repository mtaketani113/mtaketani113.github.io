---
layout: default
title: GitHub Pagesでの数式代入
description: GitHub Pagesでの数式代入方法を記載します。
category: github
---

<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script"></script>

[足し算の定義](/math/set/addDefinition.html)で数式を使うときに学んだ  
GitHub Pagesでの数式代入方法を記載します。  
はまった点などを重点的に記載します。

## 目次

[mathjaxのscriptを読み込み](#anchor1)  
[数式の記載](#anchor2)  

<a id="anchor1"></a>

## mathjaxのscriptを読み込み

以下のscriptをmarkdownに記載します。  
それにより数式を使えるようになります。

```
<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script"></script>
```

`async`がないと、数式に代わりませんでした。  
HTMLを読み込んだ後に変換するからですかね？

<a id="anchor2"></a>

## 数式の記載

`$$`で囲むことで、数式に変わります。
`\\`で改行です。`\quad`はスペースです。

```
$$
\begin{align*}
& n + 0 = n \quad (n \in \mathbb{ N })\\
& n + suc(m) = suc(n + m) \quad (n, m \in \mathbb{ N })\\
\end{align*}
$$
```

上記のように記載すると、以下のようになります。

$$
\begin{align*}
& n + 0 = n \quad (n \in \mathbb{ N }) ・・・①\\
& n + suc(m) = suc(n + m) \quad (n, m \in \mathbb{ N }) ・・・②\\
\end{align*}
$$

以下で囲むことで、中央に書かれますが、
右がそろいます。

```
\begin{align*}
・・・
\end{align*}
```

こんな感じ

$$
\begin{align*}
n + 0 = n \quad (n \in \mathbb{ N }) ・・・①\\
n + suc(m) = suc(n + m) \quad (n, m \in \mathbb{ N }) ・・・②\\
\end{align*}
$$

そこで`&`を各行の頭につけることで左寄せになります。  
`&`を書いたところで、そろうようなので
$$ = $$ で揃えたい場合は、以下のように　`=`　の前に`&`を書くことでそろいます。

```
$$
\begin{align*}
1 + 1 &= 1 + suc(0) \\
&= suc(1 + 0) \quad (\because ②) \\
&= suc(1) \quad (\because ①) \\
&= 2
\end{align*}
$$
```

以下のようになります。

$$
\begin{align*}
1 + 1 &= 1 + suc(0) \\
&= suc(1 + 0) \quad (\because ②) \\
&= suc(1) \quad (\because ①) \\
&= 2
\end{align*}
$$

数式の記号の書き方は以下のサイトを参考になりました。

[Easy Copy MathJax](https://easy-copy-mathjax.nakaken88.com/)