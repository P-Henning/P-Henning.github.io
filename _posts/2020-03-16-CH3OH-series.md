---
title: CH3OH笔记：数列递推形式
author: P-Henning
permalink: /CH3OH-series
key: CH3OH-series
tags: 数列
---

## 数列递推形式

递推数列：当前项$a_n$可以通过一个关于它前面$k$项的函数得到。

$$a_n=f(a_{n-1},a_{n-2},a_{n-3},\dots,a_{n-k})$$

- 一阶线性：$x_{n+1}=3x_n+2, x_{n+1}=x_n+2, x_{n+1}=3x_n$
- 一阶非线性：
  - 分式线性：$x_{n+1}=\frac{3+3x_n}{3+x_n}$
  - $x_{n+1}=x_n^2+x_n$
- 二阶线性：$x_{n+1}=x_n+x_{n-1}$

<!--more-->

### 不动点法

不动点法可以求出形如下式的递推关系（分式线性）的数列的通项公式。

$$a_{n+1}=\frac{pa_n+q}{ra_n+s},p,q,r,s\in\mathbb R$$

引入$f(x)=\frac{px+q}{rx+s}$，则上式简记为$a_{n+1}=f(a_n)$。

我们希望构造一个新数列$\lbrace\varphi(a_n)\rbrace$，让该数列成等差或等比，能够求出通项，然后通过反函数求出$\lbrace a_n\rbrace$。

两边同时减去$\lambda$，得

$$a_{n+1}-\lambda=\frac{pa_n+q}{ra_n+s}-\lambda=\frac{(p-\lambda r)a_n+(q-\lambda s)}{ra_n+s}$$

我们希望右边的分子可以写成$(p-\lambda r)(a_n-\lambda)$，以构造一个类等比数列。令$1:(-\lambda)=(p-\lambda r):(q-\lambda s)$，变形得

$$r\lambda^2-p\lambda=q-\lambda s\Rightarrow r\lambda^2+(s-p)\lambda-q=0$$

观察发现，得到的这个一元二次方程等价于$x=f(x)$。只要数列中出现了某一项等于$\lambda$，之后的每一项都会等于前一项，即等于$\lambda$。所以$\lambda$就是一个不动点。

- $\lambda_1\neq\lambda_2$：
  
  $$a_{n+1}-\lambda_1=(p-\lambda_1r)\cdot\frac{a_n-\lambda_1}{ra_n+s}$$
  
  $$a_{n+1}-\lambda_2=(p-\lambda_2r)\cdot\frac{a_n-\lambda_2}{ra_n+s}$$
  
  $a_n\neq\lambda_1,\lambda_2$时，两式相除，得
  
  $$\frac{a_{n+1}-\lambda_1}{a_{n+1}-\lambda_2}=\frac{p-\lambda_1r}{p-\lambda_2r}\cdot\frac{a_n-\lambda_1}{a_n-\lambda_2}$$
  
  所以$\left\lbrace\frac{a_n-\lambda_1}{a_n-\lambda_2}\right\rbrace$是等比数列。
  
- $\lambda_1\neq\lambda_2=\lambda$：
  
  $$a_{n+1}-\lambda=(p-\lambda r)\cdot\frac{a_n-\lambda}{ra_n+s}$$
  
  两边同时取倒数，得
  
  $$\begin{aligned}
  \frac 1{a_{n+1}-\lambda}&=\frac{ra_n+s}{p-\lambda r}\cdot\frac 1{a_n-\lambda}\\
  \frac 1{a_{n+1}-\lambda}-\frac 1{a_n-\lambda}&=\frac{ra_n+s-p+\lambda r}{p-\lambda r}\cdot\frac 1{a_n-\lambda}
  \end{aligned}$$
  
  由韦达定理知$2\lambda=-\frac{s-p}r$，即$s-p=-2\lambda r$，代入得
  
  $$\frac 1{a_{n+1}-\lambda}-\frac 1{a_n-\lambda}=\frac r{p-\lambda r}$$
  
  所以$\left\lbrace\frac 1{a_n-\lambda}\right\rbrace$是等差数列。

**补充：数列极限**

对$a_{n+1}=f(a_n)$的一阶递推关系（数列极限存在），设极限$\lim\limits_{n\rightarrow +\infty}a_n=a$。当$a_n\rightarrow a$时，在函数意义下有$f(a_n)\rightarrow f(a)$，即$a_{n+1}\rightarrow f(a)$；同时在数列意义下有$a_{n+1}\rightarrow a$。而数列极限若存在只会有一个，所以满足

$$a=f(a)$$

所以数列极限本身就是一个不动点。

**例：**（2013复旦）数列$\lbrace x_n\rbrace$满足$x_1=1$，$x_{n+1}=\frac{3+3x_n}{3+x_n}$，则数列$\lbrace x_n\rbrace$的极限是____。

先求不动点，令$x=\frac{3+3x}{3+x}\Rightarrow x=\pm\sqrt 3$。由于题目给定了数列的首项，数列极限只可能有一个。考虑数列的每一项与不动点的差：

$$x_{n+1}-\sqrt 3=\frac{3-\sqrt 3}{3+x_n}\left(x_n-\sqrt 3\right)\tag 1$$

$$x_{n+1}+\sqrt 3=\frac{3+\sqrt 3}{3+x_n}\left(x_n+\sqrt 3\right)\tag 2$$

显然$x_n>0$。由$(1)$式可知$x_{n+1}-\sqrt 3$与$x_n-\sqrt 3$同号。又因为$x_1-\sqrt 3<0$，所以$x_n-\sqrt 3<0\Rightarrow x_n<\sqrt 3$。

对于$(1)$式，后一项与$\sqrt 3$的差和前一项与$\sqrt 3$的差的比值

$$\left\vert\frac{x_{n+1}-\sqrt 3}{x_n-\sqrt 3}\right\vert=\frac{3-\sqrt 3}{3+x_n}<\frac{3-\sqrt 3}3<1$$

所以$n$变大时，$x_n$与$\sqrt 3$的差在不断变小。

对于$(2)$式，同理有

$$\left\vert\frac{x_{n+1}+\sqrt 3}{x_n+\sqrt 3}\right\vert=\frac{3+\sqrt 3}{3+x_n}>\frac{3+\sqrt 3}{3+\sqrt 3}=1$$

所以$n$变大时，$x_n$与$-\sqrt 3$的差在不断变大。故$x_n$最后趋近于$\sqrt 3$。

### 特征方程法

特征方程法可以求出形如下式的递推关系（二阶线性）的数列的通项公式。

$$a_{n+2}=pa_{n+1}+qa_n,p,q\in\mathbb R$$

两边同时减去$\lambda a_{n+1}$，得

$$a_{n+2}-\lambda a_{n+1}=(p-\lambda)a_{n+1}+qa_n$$

我们希望右边可以写成$(p-\lambda)(a_{n+1}-\lambda a_n)$，以构造一个等比数列$\lbrace a_{n+1}-\lambda a_n\rbrace$。令$1:(-\lambda)=(p-\lambda):q$，变形得

$$(-\lambda)(p-\lambda)=1\cdot q\Rightarrow \lambda^2-p\lambda-q=0$$

将$k$阶递推式中最靠后的一项标为$\lambda^k$，往前依次标为$\lambda^{k-1},\dots,\lambda^0$，得到的关于$\lambda$的方程称为特征方程，对应的$\lambda$的解称为特征根。上面关于$\lambda$的一元二次方程即为二阶线性递推的特征方程。

- $\lambda_1\neq\lambda_2$：由韦达定理知$\lambda_1+\lambda_2=p$，代入得
  
  $$a_{n+2}-\lambda_1 a_{n+1}=\lambda_2(a_{n+1}-\lambda_1 a_n)$$
  
  $$a_{n+2}-\lambda_2 a_{n+1}=\lambda_1(a_{n+1}-\lambda_2 a_n)$$
  
  两式相除，得
  
  $$\frac{a_{n+2}-\lambda_1 a_{n+1}}{a_{n+2}-\lambda_2 a_{n+1}}=\frac{\lambda_2}{\lambda_1}\cdot\frac{a_{n+1}-\lambda_1 a_n}{a_{n+1}-\lambda_2 a_n}$$
  
  所以$\left\lbrace\frac{a_{n+1}-\lambda_1 a_n}{a_{n+1}-\lambda_2 a_n}\right\rbrace$是等比数列。
  
- $\lambda_1=\lambda_2=\lambda$：根据等比数列求和，有
  
  $$a_{n+2}-\lambda a_{n+1}=\lambda(a_{n+1}-\lambda a_n)=\lambda^n(a_2-\lambda a_1)$$
  
  两边同时除以$\lambda^{n+2}$，得
  
  $$\frac{a_{n+2}}{\lambda^{n+2}}-\frac{a_{n+1}}{\lambda^{n+1}}=\frac 1{\lambda^2}(a_2-\lambda a_1)$$
  
  所以$\left\lbrace\frac{a_n}{\lambda^n}\right\rbrace$是等差数列。

更一般地：

- $\lambda_1\neq\lambda_2$时，$a_n=c_1\cdot\lambda_1^n+c_2\cdot\lambda_2^n$。
- $\lambda_1=\lambda_2=\lambda$时，$a_n=(c_1+nc_2)\cdot\lambda^n$。

其中系数$c_1,c_2$由数列当中任意两项确定。

**例：** （清华领军）数列$\lbrace a_n\rbrace$满足$a_1=5,a_2=13,a_{n+2}=\frac{a_{n+1}^2+6^n}{a_n}$，则（）

- [ ] A. $a_{n+2}=5a_{n+1}-6a_n$
- [ ] B. $a_n$是整数
- [ ] C. $a_n>4^n$
- [ ] D. $\lbrace a_n\rbrace$中与$2015$最接近的一项是$a_7$

初看递推式，貌似超出了我们的能力范围。但选项A提示我们这个递推式实际上是一个二阶线性递推。

递推式中$6^n$是与原数列无关的量，把$6^n$提出来：

$$\begin{aligned}
a_{n+2}a_n-a_{n+1}^2&=6^n\\
a_{n+1}a_{n-1}-a_n^2&=6^{n-1}
\end{aligned}$$

两式相除，移项，得

$$\begin{aligned}
a_{n+2}a_n-a_{n+1}^2&=6\left(a_{n+1}a_{n-1}-a_n^2\right)\\
a_n(a_{n+2}+6a_n)&=a_{n+1}(a_{n+1}+6a_{n-1})\\
\frac{a_{n+2}+6a_n}{a_{n+1}}&=\frac{a_{n+1}+6a_{n-1}}{a_n}
\end{aligned}$$

于是数列$\left\lbrace\frac{a_{n+2}+6a_n}{a_{n+1}}\right\rbrace$前后两项始终相等，为一常数$k$。则

$$a_{n+2}+6a_n=ka_{n+1}$$

将$a_1,a_2,a_3$代入上式，解得$k=5$。选项A正确。

**例：** （清华领军）数列$\lbrace a_n\rbrace$满足$a_1=1,a_2=2,a_{n+2}=6a_{n+1}-a_n$，则（）

- [ ] A. $\left\lbrace a_{n+1}^2-a_{n+2}a_n\right\rbrace$为常数列
- [ ] B. $\lbrace 8a_{n+1}a_n-7\rbrace$各项为完全平方数
- [ ] C. $\lbrace 4a_{n+1}a_n-7\rbrace$各项为完全平方数
- [ ] D. $a_n\equiv 1\pmod 9$或$a_n\equiv 2\pmod 9$

这道题就是上一道题的逆过程。将原递推式变形得到

$$\begin{aligned}
\frac{a_{n+2}+a_n}{a_{n+1}}&=6=\frac{a_{n+1}+a_{n-1}}{a_n}\\
a_{n+2}a_n-a_{n+1}^2&=a_{n+1}a_{n-1}-a_n^2
\end{aligned}$$

于是$\left\lbrace a_{n+2}a_n-a_{n+1}^2\right\rbrace$为常数列，选项A正确。

将$a_1,a_2,a_3$代入上式，求出这个常数为$-7$。将$-7=a_{n+2}a_n-a_{n+1}^2=(6a_{n+1}-a_n)a_n-a_{n+1}^2$代入选项B、C即可判断。

选项D直接暴力计算前几项即可。
