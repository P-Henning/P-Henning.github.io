---
title: 礼尚往来
author: P-Henning
modify_date: 2021-02-21
permalink: /reciprocity
key: reciprocity
tags: [数学]
---

## 前言

求任意相邻两位不全是$1$的$n$位数的个数。

设任意相邻两位不全是$1$的正整数有$a_n$个。可以手算出$a_1=9,a_2=89,a_3=882,a_4=8739$。

考虑找递推关系，即如何从$a_{n-1}$变成$a_n$。显然要在原来的$n-1$位数里加入一个数字$k$，变成$n$位数。为了避免讨论最高位是否为$0$，这里将$k$放在$n-1$位数的最右边，成为$n$位数的个位。

<!--more-->

- 当$k\neq 1$时，每一个$n-1$位数对应一个个位是$k$的$n$位数。此时$k$有$9$种取值，贡献为$9a_{n-1}$。
- 当$k=1$时，$n$位数的个位是$1$的前提是$n-1$位数的个位不能是$1$。由上一种情况可知，个位不为$1$的$n$位数的个数为$9a_{n-1}$，于是个位不为$1$的$n-1$位数的个数为$9a_{n-2}$。每一个这样的$n-1$位数对应一个个位是$1$的$n$位数。贡献为$9a_{n-2}$。

于是得到递推式：

$$a_n=9a_{n-1}+9a_{n-2}$$

发现这是一个二阶线性递推，可以用特征方程求其通项：

$$a_n=\dfrac{52+15\sqrt{13}}{117}\left(\dfrac{9+3\sqrt{13}}2\right)^n+\dfrac{52-15\sqrt{13}}{117}\left(\dfrac{9-3\sqrt{13}}2\right)^n$$

**更一般地：** 求任意相邻两位不全是$k$的$n$位$m$进制正整数的个数。

设任意相邻两位不全是$k$的$n$位$m$进制正整数有$a_n$个。同理可得递推式：

$$a_n=(m-1)a_{n-1}+(m-1)a_{n-2}$$

$\lbrace a_n\rbrace$的初始值需要分情况讨论：

- 当$k=0$时，有$a_1=m-1,a_2=m^2-m$。解得

  $$a_n=\dfrac{m-1+\sqrt{m^2+2m-3}}{2\sqrt{m^2+2m-3}}\left(\dfrac{m-1+\sqrt{m^2+2m-3}}2\right)^n-\dfrac{m-1-\sqrt{m^2+2m-3}}{2\sqrt{m^2+2m-3}}\left(\dfrac{m-1-\sqrt{m^2+2m-3}}2\right)^n$$
- 当$k\neq 0$时，有$a_1=m-1,a_2=m^2-m-1$。解得

  $$a_n=\dfrac{m^2-m+(m-2)\sqrt{m^2+2m-3}}{2(m-1)\sqrt{m^2+2m-3}}\left(\dfrac{m-1+\sqrt{m^2+2m-3}}2\right)^n-\dfrac{m^2-m-(m-2)\sqrt{m^2+2m-3}}{2(m-1)\sqrt{m^2+2m-3}}\left(\dfrac{m-1-\sqrt{m^2+2m-3}}2\right)^n$$

---

## 来

已知集合$M=\lbrace1,2,\dots,q\rbrace$，矩阵$A_0=\begin{bmatrix}
a_{11}&a_{12}&\cdots&a_{1n}\\
a_{21}&a_{22}&\cdots&a_{2n}\\
\vdots&\vdots&\ddots&\vdots\\
a_{m1}&a_{m2}&\cdots&a_{mn}
\end{bmatrix}$满足$a_{ij}\in M,1\leqslant i\leqslant m,1\leqslant j\leqslant n$。

记$\sigma(A)$为矩阵$A$的各项和。

定义变换$\varphi_k$：对于原矩阵的每一行，若存在某一项等于$k$或$-k$，将该行每一项乘以$-1$，否则不变。

若集合$S=\lbrace e_1,e_2,\dots,e_l\rbrace$，$e_1<e_2<\cdots<e_l$，则将矩阵$A_0$依次经过变换$\varphi_{e_1},\varphi_{e_2},\dots,\varphi_{e_l}$后得到$T_S$。

对于任意给定的$n,m$，求$\sum\limits_{S\neq\varnothing,S\subseteq M}\sigma(T_S)$的最大值。

**解：** 记$\sigma_i(A)$为矩阵$A$第$i$行的各项和。由于矩阵每一行的变换相互独立，考虑第$i$行（$1\leqslant i\leqslant m$）：

记$a_{ij}$（$1\leqslant j\leqslant n$）有$\alpha$种不同的取值，组成集合$P$。则对于给定的$\beta$，满足$\text{Card}(P\cap S)=\beta$且$S\subseteq M$的集合$S$的个数为$C_{\alpha}^{\beta}\cdot 2^{q-\alpha}$。

由题意，$\beta$为奇数时，$\sigma_i(T_S)=-\sigma_i(A_0)$；$\beta$为偶数时，$\sigma_i(T_S)=\sigma_i(A_0)$。于是有

$$\begin{aligned}
\sum_{S\subseteq M}\sigma_i(T_S)&=\sum_{S\subseteq M}(-1)^{\beta}\sigma_i(A_0)\\
&=\sum_{\beta=0}^{\alpha}C_{\alpha}^{\beta}\cdot 2^{q-\alpha}(-1)^{\beta}\sigma_i(A_0)\\
&=2^{q-\alpha}\cdot\sigma_i(A_0)\sum_{\beta=0}^{\alpha}C_{\alpha}^{\beta}(-1)^{\beta}\\
&=2^{q-\alpha}\cdot\sigma_i(A_0)\cdot(1-1)^{\alpha}\\
&=0
\end{aligned}$$

所以

$$\begin{aligned}
\sum_{S\neq\varnothing,S\subseteq M}\sigma(T_S)&=\sum_{S\subseteq M}\sigma(T_S)-\sigma(T_{\varnothing})\\
&=\sum_{i=1}^m\sum_{S\subseteq M}\sigma_i(T_S)-\sigma(A_0)\\
&=-\sigma(A_0)
\end{aligned}$$

要使所求值最大，只需$\sigma(A_0)$最小，即$a_{ij}=1,1\leqslant i\leqslant m,1\leqslant j\leqslant n$。所以最大值为$-mn$。

---

## 往

已知集合$M=\lbrace 1,2,\dots,n\rbrace$。

$k$维空间中有$n$个点$P_1,P_2,\dots,P_n$，第$i$个点（$1\leqslant i\leqslant n$）的坐标为$P_i(x_{i1},x_{i2},\dots,x_{ik})$。这$n$个点的坐标满足$x_{ij}\in M,1\leqslant i\leqslant n,1\leqslant j\leqslant k$，且对任意的$j$，$x_{1j},x_{2j},\dots,x_{nj}$的值互不相同。

$m$元组$(a_1,a_2,\dots,a_m)$满足$a_{\beta}\in M,1\leqslant\beta\leqslant m\leqslant n$，且对任意的$j$（$1\leqslant j\leqslant k$），关于$\beta$的函数$f_j(\beta)=x_{a_{\beta}j}$都单调递增。

对任意给定的$n,m$，记$k$维空间中满足条件的$m$元组有$\xi_k$个，求证：$\lim\limits_{t\to+\infty}\sum\limits_{k=1}^tE(\xi_k)>C_n^m$。

**解：** $n$个点的坐标不确定，导致$\xi_k$为变量。记$n$个点的坐标的情况数为$N$。显然$a_1,a_2,\dots,a_m$互不相同，满足该条件的$m$元组有$A_n^m$个。

定义$g(r,c)$（$1\leqslant r\leqslant N,1\leqslant c\leqslant A_n^m$）：若$n$个点的坐标为第$r$种情况时第$c$个$m$元组满足题中条件，$g(r,c)=1$，否则$g(r,c)=0$。

由于$n$个点每一维的坐标相互独立，考虑第$j$维（$1\leqslant j\leqslant k$）：

$x_{1j},x_{2j},\dots,x_{nj}$的取值的情况数为$n!$。其中，对给定的$m$元组，使得$f_j(\beta)=x_{a_{\beta}j}$单调递增的情况数为$A_n^{n-m}$。

所以任意给定的$m$元组满足题中条件的概率为$\left(\dfrac{A_n^{n-m}}{n!}\right)^k$。

根据期望的定义，有

$$\begin{aligned}
E(\xi_k)&=\sum_{r=1}^N\sum_{c=1}^{A_n^m}g(r,c)\cdot\dfrac 1N\\
&=\sum_{c=1}^{A_n^m}\dfrac{\sum\limits_{r=1}^N g(r,c)}N\\
&=\sum_{c=1}^{A_n^m}\left(\dfrac{A_n^{n-m}}{n!}\right)^k\\
&=A_n^m\left(\dfrac{A_n^{n-m}}{n!}\right)^k\\
&=\dfrac{C_n^m}{(m!)^{k-1}}
\end{aligned}$$

所以

$$\lim_{t\to+\infty}\sum_{k=1}^tE(\xi_k)=\dfrac{C_n^m}{1-\tfrac 1{m!}}>C_n^m$$
