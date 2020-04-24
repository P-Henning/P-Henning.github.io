---
title: 欧几里得算法与中国剩余定理
author: P-Henning
permalink: /gcd-and-crt
key: gcd-and-crt
tags: [笔记, OI]
---

## 欧几里得算法

### 欧几里得算法

计算两个整数$a,b$的最大公约数$\gcd(a,b)$。

$a\bmod b$可以表示成$a-\left\lfloor\dfrac ab\right\rfloor b$。设$d\vert a,d\vert b$，则$\dfrac ad,\dfrac bd\in \mathbb Z$，于是有

$$a\bmod b=a-\left\lfloor\dfrac ab\right\rfloor b=\left(\dfrac ad-\left\lfloor\dfrac ab\right\rfloor\dfrac bd\right)d$$

因为$\dfrac ad-\left\lfloor\dfrac ab\right\rfloor\dfrac bd$是整数，所以$d\vert(a\bmod b)$，即$d$是$b$和$a\bmod b$的公约数。

<!--more-->

又设$d'\vert b,d'\vert(a\bmod b)$，则$\dfrac b{d'},\dfrac{a\bmod b}{d'}\in \mathbb Z$。同理可得

$$a=\left\lfloor\dfrac ab\right\rfloor b+(a\bmod b)=\left(\left\lfloor\dfrac ab\right\rfloor\dfrac b{d'}+\dfrac{a\bmod b}{d'}\right)d'$$

即$d'\vert a$，即$d'$是$a$和$b$的公约数。于是$a,b$的公约数与$b,a\bmod b$的公约数相同，所以

$$\gcd(a,b)=\gcd(b,a\bmod b)$$

根据上式，$a,b$的值不断减小，于是可以递归求解，边界为$\gcd(a,0)=a$。

```cpp
int gcd(int a,int b){
  return b?gcd(b,a%b):a;
}
```

### 扩展欧几里得算法
在已知整数$a,b$的情况下求$ax+by=\gcd(a,b)$的一组整数解$a,b$。

设

$$\begin{aligned}
ax_1+by_1&=\gcd(a,b)\\
bx_2+(a\bmod b)y_2&=\gcd(b,a\bmod b)
\end{aligned}$$

由欧几里得算法得

$$\begin{aligned}
ax_1+by_1&=bx_2+(a\bmod b)y_2\\
&=bx_2+\left(a-\left\lfloor\dfrac ab\right\rfloor b\right)y_2\\
&=ay_2+b\left(x_2-\left\lfloor\dfrac ab\right\rfloor y_2\right)
\end{aligned}$$

观察上式可知

$$\begin{cases}x_1=y_2\\
y_1=x_2-\left\lfloor\dfrac ab\right\rfloor y_2
\end{cases}$$

按照欧几里得算法递归求解，$\gcd(a,b)$中$b=0$时，$x=1,y=0$即为该方程的一组解，此时回溯即可求出$x,y$最初的解。

```cpp
int ext_gcd(int a,int b,int &x,int &y){
  if(!b){x=1,y=0;return a;}
  int d=ext_gcd(b,a%b,y,x);
  y-=a/b*x;return d;
}
```

对于不定方程$ax+by=c$，设$\gcd(a,b)=d$，用扩展欧几里得算法求出的方程$ax+by=d$的解为$x',y'$。则当$c\bmod d=0$时，原方程有通解：

$$\begin{cases}
x=\dfrac cdx'+\dfrac bdk\\
y=\dfrac cdy'-\dfrac adk
\end{cases}$$

## 中国剩余定理

### 中国剩余定理

求解下列方程组（$w_1,w_2,\ldots,w_n$两两互质）：

$$\begin{cases}
x\equiv b_1\pmod{w_1}\\
x\equiv b_2\pmod{w_2}\\
\vdots\\
x\equiv b_n\pmod{w_n}
\end{cases}$$

令

$$\begin{aligned}
p&=\prod\limits_{i=1}^nw_i\\
m_i&=\dfrac p{w_i}\\
m_i{m_i}^{-1}&\equiv 1\pmod{w_i}
\end{aligned}$$

则上面方程组的解为

$$x=\left(\sum_{i=1}^nm_i{m_i}^{-1}b_i\right)\bmod p$$

将$x$代入任意一个方程即可得证，此处略。

```cpp
int ext_gcd(int a,int b,int &x,int &y){
  if(!b){x=1,y=0;return a;}
  int d=ext_gcd(b,a%b,y,x);
  y-=a/b*x;return d;
}
int crt(int *b,int *w,int n){
  int x,y,ans=0,mi,p=1;
  for(int i=1;i<=n;i++)p*=w[i];
  for(int i=1;i<=n;i++){
    mi=p/w[i];
    ext_gcd(mi,w[i],x,y);
    ans=(ans+x*mi*b[i])%p;
  }
  return ans?ans:ans+p;
}
```

### 扩展中国剩余定理

求下列方程组的最小正整数解（$w_1,w_2,\ldots,w_n$不一定两两互质）：

$$\begin{cases}
x\equiv b_1\pmod{w_1}\\
x\equiv b_2\pmod{w_2}\\
\vdots\\
x\equiv b_n\pmod{w_n}
\end{cases}$$

考虑求前$i$个方程组成的方程组的解$x$。假设已经求出前$i-1$个方程的一个解为$x'$，这些方程的模数$w$的最小公倍数为$m$。则$x$也满足前$i-1$个方程，即满足

$$x\equiv x'\pmod m$$

同时$x$要满足第$i$个方程，即

$$x\equiv b_i\pmod{w_i}$$

这两个同余方程可以转化成两个不定方程（未知数为$x,k_1,k_2$）：

$$\begin{cases}
x-k_1m=x'\\
x-k_2w_i=b_i
\end{cases}$$

消元得

$$k_1m-k_2w_i=b_i-x'$$

用扩展欧几里得算法解出$k_1,k_2$，即可解出$x$。依次讨论完每一个方程即可得出最终解。

```cpp
int ext_gcd(int a,int b,int &x,int &y){
  if(!b){x=1,y=0;return a;}
  int d=ext_gcd(b,a%b,y,x);
  y-=a/b*x;return d;
}
int ext_crt(int *b,int *w,int n){
  int ans=0,m=1,p;
  for(int i=1;i<=n;i++){
    int x,y,d=ext_gcd(m,w[i],x,y),t=w[i]/d;
    if((b[i]-ans)%d)return -1;
    x=(x%t+t)%t,p=m*t;
    int k=((b[i]-ans)/d*x%t+t)%t;
    ans=(ans+k*m)%p,m=p;
  }
  return ans?ans:ans+p;
}
```
