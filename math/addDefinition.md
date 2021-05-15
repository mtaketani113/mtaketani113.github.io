---
layout: default
title: 足し算の定義
description: 足し算の定義を記載します。
category: 数学
---

<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script"></script>

作成中

足し算の定義を書いてみます。
と言いながら、markdownで数式を書く練習をしてたりｗ

## 足し算の定義

以下の2つだけです。驚きですね。

$$
\begin{align*}
& ① n + 0 = n \quad (n \in \mathbb{ N })\\
& ② n + suc(m) = suc(n + m) \quad (n, m \in \mathbb{ N })\\
\end{align*}
$$

$$ suc(n) $$ は $$ n $$ の次の自然数という意味です。

高校だと自然数は $$ 0 $$ を含みませんでしたが、  
なにやら $$ 0 $$ を自然数に入れる派が大学では多かったような。

本当は存在や唯一性とかあるんですが、  
細かいことはおいおい。

具体例として $$ 1 + 1 = 2 $$ ですが

$$
\begin{align*}
1 + 1 &= 1 + suc(0) \\
&= suc(1 + 0) \\
&= suc(1) \\
&= 2
\end{align*}
$$

という感じになります。




