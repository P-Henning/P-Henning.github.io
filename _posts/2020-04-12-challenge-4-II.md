---
title: 挑战四：Determined Umbrella (II)
author: P-Henning
permalink: /challenge-4-II
key: challenge-4-II
tags: [yuzan1830, 搜索]
---

### 说在前面

早在本蒟蒻还没有退役的时候，yuzan1830就已经酝酿好了题目。鉴于本蒟蒻的~~水瓶~~水平，当时题目没有解决。于是非常无情地，这道题被咕掉了。

题目原型是这样的：平面内有一些圆，每个圆有一定的半径。圆的位置可以移动，但不能有重叠。现在需要在平面内找到一个矩形，使得这些圆都在矩形内，且矩形的面积最小。求最小面积。yuzan1830并不喜欢特别多的输入，于是他只给出了半径为某个值的圆的数量，而不是给出每个圆的半径。

<!--more-->

这里不讨论过去一年的时间里yuzan1830是如何破茧成蝶、寻找成长的价值，以及本蒟蒻是如何放弃挑战、走向自闭的深渊，现在直接进入正题：模拟退火。

### 重拾挑战

模拟退火的核心是以一定概率接受随机产生的新解。以求函数$f(x)$的最小值为例，这个概率为（$T$为当前的温度）：

$$p=\begin{cases}
1&\Delta f<0\\
e^{\frac{-\Delta f}T}&\Delta f\geqslant 0
\end{cases}$$

除此之外就是如何产生新解以及调参的问题。

本题最大的问题是圆的位置不确定。不过用上模拟退火就显得简单粗暴了，将矩形的面积看成是关于圆的位置集合的一个函数$f(s),s=\lbrace(x_i,y_i)\rbrace$，随机确定每个圆的位置，然后可以通过求此时矩形的最小面积作为函数值，再用模拟退火求函数$f(s)$的最小值。

在圆的位置确定的情况下求矩形的最小面积成了另一个问题。显然矩形的每条边一定和至少一个圆相切。如果知道了矩形两条邻边的倾斜角$\theta_1,\theta_2$，就可以用两条倾斜角分别为$\theta_1,\theta_2$的直线去和每一个圆相切，得到两组平行线，最外边的四条直线围成的图形即为所求矩形。而$\theta_1,\theta_2$中一定有一个角在$[0,90^{\circ})$范围内（设为$\theta$），而另一个角就是$\theta+90^{\circ}$。此时矩形的面积又可以看成是关于$\theta$的函数$g_s(\theta)$。

<div align="center"><img src="https://s1.ax1x.com/2020/04/11/GHO93D.png"></div>

然后又用模拟退火求$g_s(\theta)$的最小值？没有必要。如果另行作一个函数$f'(s)=g_s(0)$表示$\theta=0$时矩形的面积[^func]，虽然$\theta=0$时矩形的面积不一定最小，但对问题的最终答案没有影响。考虑把圆的位置集合为$s$时面积最小的矩形及所有的圆绕原点旋转，保持相对位置不变，旋转到矩形的四条边与坐标轴平行为止。记旋转后圆的位置集合为$s'$，矩形的倾斜角为$\theta'$。于是$\theta'=0$，那么有

$$f'(s')=f(s')=f(s)$$

而圆的位置是随机的，$f$函数能取到的值，$f'$函数都能取到。因此只需要求$f'(s)$的最小值作为最终答案，连切线都不需要算了，直接取所有圆的上下左右边界。

初始解设置为所有的圆排成一列。生成新解的时候，随机选择一个圆，随机移动到附近的一个位置，移动的距离随温度下降而减少。如果移动后的圆与其它的圆出现了重叠，简单粗暴地重新生成新解。

关键代码：

```cpp
double search(){
  for(double t=20000;t>1e-10;t*=0.99996){
    int id;point tmp;
    for(;;){
      id=RAND(1,n),tmp=p[id];
      p[id].x+=RAND(-2000,2000)*t*0.0001;
      p[id].y+=RAND(-2000,2000)*t*0.0001;
      if(check(id))break;p[id]=tmp;
    }
    double d=f()-cur;
    if(d<0||exp(-d/t)>RAND_UNIT)cur+=d;
    else p[id]=tmp;
    if(cur<ans-EPS){
      ans=cur;
      for(int i=1;i<=n;i++)ansp[i]=p[i];
      for(int k=0;k<4;k++)ansv[k]=v[k];
    }
  }
  return ans;
}
```

众所周知，温度下降越慢，退火次数越多，得到的结果越精确。本文展示的代码中一共退火四次，每次退火完成后会向标准错误输出当前的最优解。

```cpp
cur=ans=f();
for(int k=0;k<4;k++)ansv[k]=v[k];
fprintf(stderr,"%.2lf\n",ans);
for(int k=0;k<4;k++)fprintf(stderr,"%.2lf\n",search());
printf("%.2lf\n",ans);
```

### 说在后面

原问题完全无法手算，也不能写暴力程序求解（毕竟要用到模拟退火这样的概率算法）。于是本蒟蒻只能画图验证解的正确性。

先来组数据gào一下：

    1
    1

> 达羌：滚。（考虑过Roselia的感受吗）

换一组：

    2 6 5 3
    1 1 1 1

之前的代码里有$ansp[i]$和$ansv[k]$，就是用来记录矩形面积最小时每个圆的圆心坐标和矩形四个顶点的坐标。

程序求出上面这组数据的答案为$316.96$，并在第四象限内找到了这样一个矩形（由于输出的坐标只保留了两位小数，面积可能会有偏差）：

<div align="center"><img src="https://s1.ax1x.com/2020/04/12/GL7EBn.png"></div>

可以看到四个圆的坐标严重偏离了原点，这也说明了算法的随机性。每次求出的坐标可能不一样，甚至求出的结果不一定就是最优解。运气好一次退火就得出了最优解，运气不好也许要退火好几次。如果圆很多的话更不好说。

怎么办呢？慢慢退火就是了。

<div align="center"><img src="https://s1.ax1x.com/2020/04/12/GLqd3T.jpg"></div>

最后来个猛一点的：

    25 17 34 38 6 11 40
    14 7 3 6 12 9 15

程序进行多次退火后算出的答案为$186541.93$。

<div align="center"><img src="https://s1.ax1x.com/2020/04/12/GLoYrR.png"></div>

### 在线运行

<div data-pym-src="https://www.jdoodle.com/embed/v0/1Zgl"></div>

<script src="https://www.jdoodle.com/assets/jdoodle-pym.min.js" type="text/javascript"></script>

[^func]: 这里的$f'(s)$不表示$f(s)$的导数。
