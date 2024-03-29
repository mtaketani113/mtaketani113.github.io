---
layout: default
title: 掛け算の定義
description: 足し算の定義の続きで、ペアノの掛け算の定義を記載します。
image: /images/math/school_text02_sugaku.png
category: 数学
created_at: 2021-05-16
last_modifeid_at: 2021-06-02
---

<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script"></script>

[足し算の定義](/math/set/addDefinition.html)
の続きで、ペアノの掛け算を定義します。  
かなり足し算と似ています。

## 目次

[自然数の掛け算の定義](#anchor1)  
[自然数の分配法則](#anchor2)  

<a id="anchor1"></a>

## 自然数の掛け算の定義
早速ですが定義を書きます。以下のように再帰的な定義です。

$$
\begin{align*}
& n \times 0 = 0 \quad (n \in \mathbb{ N }) ・・・①\\
& n \times suc(m) = n \times m + n \quad (n, m \in \mathbb{ N }) ・・・②\\
\end{align*}
$$

<a id="anchor2"></a>

## 自然数の分配法則

②は分配法則っぽいですよね。

$$ l,m,n \in \mathbb{ N } $$ に対して  
$$ l \times (m + n) =  l \times m + l \times n $$を証明してみましょう。

使うのは数学的帰納法です。
数学的帰納法は昔から何か騙されているような気がしますｗ  
でも公理らしい(*'▽')

(1)$$ n = 0 $$ の場合

$$
\begin{align*}
l \times (m + n) &= l \times (m + 0) \\
&= l \times m \\
&= l \times m + 0 \\
&= l \times m + l \times 0 \quad ( \because ① ) \\
& = l \times m + l \times n
\end{align*}
$$

(2) $$ n = k $$ の場合に$$ l \times (m + k) =  l \times m + l \times k $$ が成り立つと仮定し  
$$ n = k + 1 $$ の時に成り立つことを示す。  

$$
\begin{align*}
k + 1 &= k + suc(0) \\
&= suc(k + 0) \\
&= suc(k)
\end{align*}
$$

となるので1を足すと次の数になります。

よって、以下が成り立ちます。

$$
\begin{align*}
l \times (m + (k + 1)) &= l \times (m + suc(k)) \\
&= l \times suc(m + k) \quad ( 足し算の定義参照 ) \\
&= l \times (m + k) + l \quad ( \because ② ) \\
&= l \times m + l \times k + l \\
&= l \times m + l \times suc(k) \quad ( \because ② ) \\
&= l \times m + l \times (k + 1)
\end{align*}
$$

よって $$ k + 1$$ の時も成り立つので、
数学的帰納法により
$$ l \times (m + n) =  l \times m + l \times n $$
が成り立ちます。

markdownで書いていると、何が何だか分からなくなってきますね( ;∀;)