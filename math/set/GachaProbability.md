---
layout: default
title: レアガチャが当たる確率
description: スマホゲームで1%のガチャを100回引いたときに少なくとも1回以上引ける確率がいくらになるかです。高校時代に学んだ自然対数の底「e」がかかわってきます。
image: /images/math/school_text02_sugaku.png
category: 数学
created_at: 2022-03-23
last_modifeid_at: 2022-03-23
---

<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script"></script>

スマホゲームのレアガチャ、例えば1%の確率のガチャを100回引いたときに少なくとも1回以上引ける確率がいくらになるかという話です。結論から言うと大体63%くらいです。
これは0.1%の確率のガチャを1000回引いたに条件を変えてもあまり変わりません。とある値に収束していきます。


まず、1回以上当たるを考えるのは難しいので1回も当たらないを考えます。
当たる確率を $$ \frac{1}{n} $$ として $$ n $$ 回引いたとしましょう。

1回がガチャを引いて外れる確率は $$ 1 - \frac{1}{n} $$ なので
n回とも外れる確率は $$ \left(1 - \displaystyle\frac{1}{n}\right)^n $$ です。

ここで少し計算してみましょう。

$$
\begin{align*}
\left(1 - \displaystyle\frac{1}{n}\right)^n &= \left(\displaystyle\frac{n-1}{n}\right)^n \\
&= \left(\displaystyle\frac{n-1}{(n-1) + 1}\right)^n \\
&= \left(\displaystyle\frac{(n-1) + 1}{(n-1)}\right)^{-n} \\
&= \left(1 + \displaystyle\frac{1}{n-1}\right)^{-(n-1)-1} \\
&= \left(1 + \displaystyle\frac{1}{N}\right)^{-N} \times \left(1 - \displaystyle\frac{1}{N}\right)^{-1} \quad (N=n-1)
\end{align*}
$$

$$ \left(1 - \displaystyle\frac{1}{N}\right)^N $$ は高校数学で学んだ自然対数の底 $$ e ≒ 2.718 $$
に収束しますので、 $$ \left(1 - \displaystyle\frac{1}{N}\right)^{-N} $$ は $$ \frac{1}{e} $$ に収束します。
また $$ \left(1 - \displaystyle\frac{1}{N}\right)^{-1} $$ は $$ N $$ を大きくすると$$ 1 $$ に収束します。

なのでn回とも外れる確率は $$ \frac{1}{e} $$ に収束します。

1回以上あたりを引く事象は1回も当たらない事象の余事象なので、1回以上あたりを引く確率は

$$
\begin{align*}
1 - \frac{1}{e} &\fallingdotseq 1 - \frac{1}{2.713} \\
&\fallingdotseq 0.63 \\
\end{align*}
$$

となり、だいたい63%になります。

$$ e $$　への収束は結構早く10%の確率で10回でも、だいたい65％くらいです。

皆さんが茶を引くときは1%の確率を100回引いても確実には当たらず63%くらいしか当たらないという覚悟で引いてください。。


