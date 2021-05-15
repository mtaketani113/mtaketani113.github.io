---
layout: default
title: 足し算の定義
description: 足し算の定義を記載します。
category: 数学
---

<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script"></script>

足し算の定義を書いてみます。
と言いながら、markdownで数式を書く練習をしてたりｗ

[自然数での足し算の定義](#anchor1)  
[自然数からの拡張](#anchor2)  

<a id="anchor1"></a>

## 自然数での足し算の定義

以下の2つだけです。驚きですね。

$$
\begin{align*}
& n + 0 = n \quad (n \in \mathbb{ N }) ・・・①\\
& n + suc(m) = suc(n + m) \quad (n, m \in \mathbb{ N }) ・・・②\\
\end{align*}
$$

$$ suc(n) $$ は $$ n $$ の次の自然数\mathbb{ N }という意味です。

高校だと自然数は $$ 0 $$ を含みませんでしたが、  
なにやら $$ 0 $$ を自然数に入れる派が大学では多かったような。

本当は存在や唯一性とかあるんですが、  
細かいことはおいおい。

具体例として $$ 1 + 1 = 2 $$ ですが

$$
\begin{align*}
1 + 1 &= 1 + suc(0) \\
&= suc(1 + 0) \quad (\because ①) \\
&= suc(1) \quad (\because ②) \\
&= 2
\end{align*}
$$

という感じになります。

<a id="anchor2"></a>

## 自然数からの拡張

自然数 $$ n $$ に対して、足すと $$ 0 $$ になる数字を  
$$ -n $$ とすることで、 整数 $$ \mathbb{ Z } $$ に拡張

掛け算は定義していないのでまたどこかで書きますが、  
$$ \frac{n}{m} $$ を $$ m $$ をかけると $$ n $$　になる数字  
として有理数 $$ \mathbb{ Q } $$ に拡張。

そして有理数をデデキントの切断で実数 $$ \mathbb{ Z } $$ に拡張。

という感じで足し算を拡張していきます。

専門家からしたら、突っ込みどころ満載かもしれませんが、  
大体こんなイメージかと思います。
