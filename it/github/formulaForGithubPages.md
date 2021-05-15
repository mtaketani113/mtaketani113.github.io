---
layout: default
title: GitHub Pagesでの数式代入
description: GitHub Pagesでの数式代入方法を記載します。
category: IT
---

<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script"></script>

[足し算の定義](/math/set/addDefinition.html)で数式を使うときに学んだ  
GitHub Pagesでの数式代入方法を記載します。  
はまった点などを重点的に記載します。

## mathjaxのscriptを読み込み

以下のscriptをmarkdownに記載します。  
それにより数式を使えるようになります。

```
<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script"></script>
```

`async`がないと、数式に代わりませんでした。  
HTMLを読み込んだ後に変換するからですかね？

## 数式の記載

`$$`で囲むことで、数式に変わります。

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


