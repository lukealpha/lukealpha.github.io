---
layout: post
title: "Master Theorem"
subtitle: "使用主定理分析分治算法的时间复杂度"
date: 2022-09-11
author: "Luke Alpha"
header-img: "img/post-bg-2015.jpg"
mathjax: true
tags: 
    - 算法基础
---

## Master Theorem (主定理)

对于$T(n) = KT(\frac{n}{m}) + n^d$且$T(1)=1$

$$
T(n)=\left\{\begin{align}
  O(n^d)\ \ \ \ \ k < m^d\\
  O(n^dlog_{m}n)\ \ \ \ \ k = m\ \ \\
  O(n^{log_mk})\ \ \ \ \ k > m^d
\end{align}\right.
$$

## 不严谨的推导

$$
\begin{eqnarray}    \label{eq}
T(n)&=&KT(\frac{n}{m}) + n^d    \\
&=&K(K(KT(\frac{n}{m}) + (\frac{n}{m^2})^d)+(\frac{n}{m})^d) + n^d \nonumber    \\
&=&K(K(K(...(KT(\frac{n}{m^i}) +(\frac{n}{m^{i - 1}})^d)...) + (\frac{n}{m^2})^d)+(\frac{n}{m})^d) + n^d \nonumber    \\
&=&K^iT(\frac{n}{m^i}) + n^d((\frac{k}{m^d})^{i - 1} + ... + (\frac{k}{m^d})^0) \nonumber    \\
&\ &当i= \log_{m}{n}时    \\
T(n)&=&k^{\log_{m}n}T(1) + n^d(\frac{1 - q^i}{1-q})\nonumber    \\
\\
&\ &令q = \frac{k}{m^d}\\
&\ &当q < 1\ ,即k < m^d时\ , q^i \to 0    \\
T(n)&\approx&k^{\log_{m}n} + \frac{n^d}{1-q}\nonumber\\
&=&O(n^d)\\
\\
&\ &当q > 1\ ,即k > m^d时\    \\
T(n)&\approx&k^{\log_{m}n} + n^dq^i\nonumber\\
&=&O(k^{\log_{m}n}) \\
&=&O(n^{\log_{m}k})\\
\\
&\ &当q = 1\ ,即k = m^d时\    \\
T(n)&=&k^{\log_{m}n} + n^di\nonumber    \\
&=&O(n^d\log_{m}{n})
\end{eqnarray}
$$

## 时间复杂度记号

此部分参考这篇文章 - [时间复杂度的五个记号](https://blog.csdn.net/qq_41976613/article/details/105026946)

$\Theta$: 既是上界也是下界(tight)，就是相等，准确的复杂度

$O$: 表示渐进上界(tightness unknown)，小于等于的意思，近似复杂度

$o$: 表示上界(not tight)，小于的意思，明确的知道小于它，准确计算出来的

$\Omega$: 表示渐进下界(tightness unknown)，大于等于的意思，近似复杂度

$\omega$: 表示下界(not tight)，大于的意思，明确的知道大于它，准确计算出来的


