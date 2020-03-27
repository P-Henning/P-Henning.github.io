---
layout: article
title: CH3OH笔记：复数与多项式
author: P-Henning
permalink: /CH3OH-complex
key: CH3OH-complex
sharing: true
license: true
aside:
  toc: true
show_edit_on_github: true
show_subscribe: true
---

## 复数的三角形式

### 复数的三角形式

复数具有向量的性质、数的概念。复平面有实轴和虚轴，复数对应复平面上的点。一般用从原点指向该点的向量表示复数。如向量$(a,b)$表示复数$z=a+bi$，其中$a,b$分别为该向量在实轴和虚轴上的投影。

已知向量的长度$r$和其与实轴的夹角$\theta$也可以表示该点，此时有$a=r\cos\theta,b=r\sin\theta$，即

$$z=r\cos\theta+r\sin\theta\cdot i=r(\cos\theta+i\sin\theta)$$

上式即为复数的三角形式。

<!--more-->

下面通过三角形式理解复数乘除的几何意义。令$z_1=r_1(\cos\theta_1+i\sin\theta_1)$，$z_2=r_2(\cos\theta_2+i\sin\theta_2)$。对$z_1,z_2$做乘法，得到

$$z_1z_2=r_1r_2[\cos(\theta_1+\theta_2)+i\sin(\theta_1+\theta_2)]$$

可以看出两个复数相乘得到的仍是复数，且模长等于两个复数模长的乘积，辐角等于两个复数辐角的和。

对$z_1,z_2$做除法，可以得到类似的结论：

$$\frac{z_1}{z_2}=\frac{r_1}{r_2}[\cos(\theta_1-\theta_2)+i\sin(\theta_1-\theta_2)]$$

用三角形式表示共轭复数：

$$\bar z=a-bi=r[\cos(-\theta)+i\sin(-\theta)]$$

据此可得

$$z\cdot\bar z=r^2$$

### 基本例题

**例：**（2016北大博雅）设$a,b,c$为实数，方程$a,c\neq 0$，$ax^2+bx+c=0$的两个虚根$x_1,x_2$满足$\frac{x_1^2}{x_2}$为实数，则$\sum\limits_{k=0}^{2015}\left(\frac{x_1}{x_2}\right)^k$等于（）

- [ ] A. $1$
- [ ] B. $0$
- [ ] C. $\sqrt 3i$
- [ ] D. 前三项都不正确

根据等比数列求和公式有

$$\sum_{k=0}^{2015}\left(\frac{x_1}{x_2}\right)^k=\frac{\left(\frac{x_1}{x_2}\right)^{2016}-1}{\frac{x_1}{x_2}-1}$$

猜测：在$a,b,c$都不确定的情况下，最终答案要么与$a,b,c$有关，要么就是$0$。

**补充：虚根成对原理**

由求根公式可知，二次方程的两个复根$x_1,x_2$互为共轭，即$x_1=\bar x_2$。令$x_1=r(\cos\theta+i\sin\theta)$，$x_2=r[\cos(-\theta)+i\sin(-\theta)]$，于是

$$\frac{x_1^2}{x_2}=r(\cos 3\theta+i\sin 3\theta)$$

因为$\frac{x_1^2}{x_2}$为实数，所以$3\theta=k\pi,k\in\mathbb Z$。

注意到$\frac{x_1}{x_2}$的辐角为$2\theta$，于是$\left(\frac{x_1}{x_2}\right)^{2016}$的辐角为$2\theta\cdot 2016$，即$2k\pi\cdot 672$。于是

$$\left(\frac{x_1}{x_2}\right)^{2016}=\left(\frac rr\right)^{2016}[\cos(2k\pi\cdot 672)+i\sin(2k\pi\cdot 672)]=1$$

**例：** 求$\tan 20^{\circ}+4\sin 20^{\circ}$的值。

这道题当然可以用三角恒等变形来做。不过复数更具有普适性。

原式中只有$20^{\circ}$，引入$z=\cos 20^{\circ}+i\sin 20^{\circ}$。考虑用$z$表示$20^{\circ}$的三角函数。

$$\begin{aligned}
z+\bar z=2\cos 20^{\circ}&\Rightarrow\cos 20^{\circ}=\frac 12(z+\frac 1z)\\
z-\bar z=2i\sin 20^{\circ}&\Rightarrow\sin 20^{\circ}=\frac 1{2i}(z-\frac 1z)
\end{aligned}$$

通分代入原式，得

$$\begin{aligned}
\tan 20^{\circ}+4\sin 20^{\circ}&=\frac{z^2-1}{\left(z^2+1\right)i}+\frac{2\left(z^2-1\right)}{zi}\\
&=\frac 1i\cdot\frac{z^3-z+2z^4-2}{z^3+z}
\end{aligned}$$

因为$20^{\circ}\times 3=60^{\circ}$，所以有$z^3=\frac 12+\frac{\sqrt3}2i$，可以给上式降次。代入解得原式$=\sqrt 3$。

## 复数与多项式

### 单位根与多项式

复数的乘除对应辐角的加减。我们知道幂的乘除对应指数的加减，于是可以引入如下公式（欧拉公式，至于底数为什么是$e$之类的问题先不管）：

$$e^{i\theta}=\cos\theta+i\sin\theta$$

把复数$z=r(\cos\theta+i\sin\theta)$记成$r\cdot e^{i\theta}$，同样满足乘除运算法则。

给定$n\in\mathbb N^{\ast}$，考虑如下方程：

$$z^n=1$$

在实数范围内，有

$$z^n-1=(z-1)(z^{n-1}+\cdots+z+1)=0$$

可以得出一个根$z=1$，而其它的根为后面的多项式的零点。

如果用复数表示$z=r\cdot e^{i\theta},1=e^{i\cdot 2k\pi}$，有

$$r^n\cdot e^{i\cdot n\theta}=e^{i\cdot 2k\pi}\Rightarrow\begin{cases}
r=1\\
\theta=\frac{2k\pi}n
\end{cases}$$

其中$k$取$0,1,\dots,n-1$。$n$次方程最多只有$n$个复根，因此这里找到的$n$个根就是原方程的所有复根。

引入$z_k=\cos\frac{2k\pi}n+i\sin\frac{2k\pi}n$，则$z_0,z_1,\dots,z_{n-1}$称为$n$次单位根，其中$z_0=1$。于是有

$$\begin{aligned}
(z-1)(z^{n-1}+\cdots+z+1)&=(z-z_0)(z-z_1)\cdots(z-z_{n-1})\\
z^{n-1}+\cdots+z+1&=(z-z_1)\cdots(z-z_{n-1})
\end{aligned}$$

根据韦达定理，如果把右边打开，其$n-2$次项系数为

$$-(z_1+\cdots+z_{n-1})=1\Rightarrow z_1+\cdots+z_{n-1}=-1$$

**例：**求$\sin\frac{\pi}n\cdot\sin\frac{2\pi}n\cdots\sin\frac{(n-1)\pi}n$的值。

引入$z_k=\cos\frac{2k\pi}n+i\sin\frac{2k\pi}n$。在单位圆中，以$1$和$z_k$表示的向量为腰形成一个等腰三角形，其底边长为$2\sin\frac{k\pi}n$，于是可以表示出$\sin\frac{k\pi}n$：

$$\vert 1-z_k\vert=2\sin\frac{k\pi}n\Rightarrow\sin\frac{k\pi}n=\frac{\vert 1-z_k\vert}2$$

代入原式，得

$$\begin{aligned}
\sin\frac{\pi}n\cdots\sin\frac{(n-1)\pi}n&=\frac 1{2^{n-1}}\vert(1-z_1)\cdots(1-z_{n-1})\vert\\
&=\frac 1{2^{n-1}}\left\vert 1+1^1+\cdots+1^{n-1}\right\vert\\
&=\frac n{2^{n-1}}
\end{aligned}$$

用同样的方法可以求$\cos\frac{\pi}n\cdots\cos\frac{(n-1)\pi}n$。在原来的等腰三角形中，其底边上的高为$\left\vert\cos\frac{k\pi}n\right\vert$，而等腰三角形底边上的高也是底边的中线，于是

$$\frac{\vert z_k+1\vert}2=\left\vert\cos\frac{k\pi}n\right\vert$$

代入原式，得

$$\begin{aligned}
\left\vert\cos\frac{\pi}n\cdots\cos\frac{(n-1)\pi}n\right\vert&=\frac 1{2^{n-1}}\vert(z_1+1)\cdots(z_{n-1}+1)\vert\\
&=\frac 1{2^{n-1}}\left\vert 1+(-1)^1+\cdots+(-1)^{n-1}\right\vert\\
&=\frac {1-(-1)^n}{2^{n}}
\end{aligned}$$

至于如何去掉绝对值，需要讨论$\frac{k\pi}n$在第几项超过了$\frac{\pi}2$。此处略。

### 基本例题

**例：**已知$e^{i\theta}=\cos\theta+i\sin\theta$，求值$\left\vert 2+2e^{\frac{2\pi}5i}+e^{\frac{6\pi}5i}\right\vert$。

引入$z=\cos\frac{2\pi}5+i\sin\frac{2\pi}5=e^{\frac{2\pi}5i}$，则原式可以写成$\left\vert 2+2z+z^3\right\vert$。$z$是$5$次单位根，同时有

$$z+z^2+z^3+z^4=-1\Rightarrow 1+z=-z^2-z^3-z^4$$

代入，得

$$\begin{aligned}
\left\vert 2+2z+z^3\right\vert&=\left\vert z^3-2(z^2+z^3+z^4)\right\vert\\
&=\left\vert z^3\right\vert\left\vert 2\left(z+\frac 1z\right)+1\right\vert\\
&=\left\vert 4\cos\frac{2\pi}5+1\right\vert
\end{aligned}$$

问题变成了如何求$\cos\frac{2\pi}5$。回到这个式子：

$$z+z^2+z^3+z^4=-1$$

两边实部相等，有

$$\begin{aligned}
&\cos\frac{2\pi}5+\cos\frac{4\pi}5+\cos\frac{6\pi}5+\cos\frac{8\pi}5\\
=&2\left(\cos\frac{2\pi}5+\cos\frac{4\pi}5\right)\\
=&2\left(\cos\frac{2\pi}5+2\cos^2\frac{2\pi}5-1\right)=-1
\end{aligned}$$

解得$\cos\frac{2\pi}5=\frac{\sqrt 5-1}4$，所以原式$=\sqrt 5$。

**例：**（2017清华）设$\omega=\cos\frac{2\pi}5+i\sin\frac{2\pi}5$，$P(x)=x^2+x+2$，则$P(\omega)P\left(\omega^2\right)P\left(\omega^3\right)P\left(\omega^4\right)=$____。

由条件$\omega,\omega^2,\omega^3,\omega^4$都是$5$次单位根。

在复数范围内，任何多项式都可以彻底因式分解。有$P(x)=(x-\varepsilon)(x-\bar\varepsilon)$，$P(\varepsilon)=\varepsilon^2+\varepsilon+2=0$。因此原式变为

$$\begin{aligned}
&(\omega-\varepsilon)\cdots\left(\omega^4-\varepsilon\right)(\omega-\bar\varepsilon)\cdots\left(\omega^4-\bar\varepsilon\right)\\
=&\left(\varepsilon^4+\varepsilon^3+\cdots+1\right)\left(\bar\varepsilon^4+\bar\varepsilon^3+\cdots+1\right)\\
\end{aligned}$$

由于$\varepsilon^4+\varepsilon^3+\cdots+1$和$\bar\varepsilon^4+\bar\varepsilon^3+\cdots+1$共轭，所以上式又可以化成

$$\left\vert\varepsilon^4+\varepsilon^3+\varepsilon^2+\varepsilon+1\right\vert^2=\vert 2\varepsilon+3\vert^2$$

根据$P(\varepsilon)=0$可以解出$\varepsilon=-\frac 12\pm\frac{\sqrt 7}2i$，代入得到答案为$11$。

## 复数与方程

代数基本定理：$n$次复系数方程在复数范围内一定有$n$个复根。

实系数方程虚根成对原理：若$x$是某方程的虚根，则$\bar x$也是该方程的一个虚根。
