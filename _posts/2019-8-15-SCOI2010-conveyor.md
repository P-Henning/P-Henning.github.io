---
title: SCOI2010 传送带
author: P-Henning
permalink: /SCOI2010-conveyor
key: SCOI2010-conveyor
tags: [题目, 二分答案]
---

#### 题目大意

在一个二维平面上有两条传送带，可以看成是两条线段$AB$和$CD$。在$AB$上的移动速度为$P$，在$CD$上的移动速度为$Q$，在平面上的移动速度$R$。求从$A$点走到$D$点所需的最少时间，结果保留到小数点后$2$位。

<!--more-->

---

#### 思路

假设在线段$AB$上的点$M$处离开该线段，在线段$CD$上的点$N$处进入该线段，即移动路线为$A\rightarrow M\rightarrow N\rightarrow D$。

![](https://s1.ax1x.com/2020/03/26/8xzJsg.png)

则移动所需的时间为

$$\frac{\vert AM\vert}P+\frac{\vert MN\vert}R+\frac{\vert ND\vert}Q$$

当点$N$的位置确定时，考虑点$M$的位置对答案的影响（不妨设$\angle AMN\geqslant 90^{\circ}$）：

![](https://s1.ax1x.com/2020/03/26/8xzGQS.png)

$BM$上的点$M_2$都不用考虑（显然时间不是最短）。对于$AM$上的点$M_1$，有

$$\begin{aligned}
\frac{\vert AM_1\vert}P+\frac{\vert M_1N\vert}R&=\frac{\vert AM\vert-\vert MM_1\vert}P+\frac{\vert MN\vert+(\vert M_1N\vert-\vert MN\vert)}R\\
&=\left(\frac{\vert AM\vert}P+\frac{\vert MN\vert}R\right)+\frac{P\cdot\vert M_1N\vert-R\cdot\vert MM_1\vert-P\cdot\vert MN\vert}{PR}
\end{aligned}$$

其中只有$P\cdot\vert M_1N\vert-R\cdot\vert MM_1\vert$是变量。

- 当$P>R$时，由于$\vert MM_1\vert$增长比较快，但$\vert M_1N\vert$的系数较大，所以两者在同时增大时$P\cdot\vert M_1N\vert-R\cdot\vert MM_1\vert$先减小后增大，即存在一个最小值。
- 当$P\leqslant R$时，显然$\vert MM_1\vert$最大时$P\cdot\vert M_1N\vert-R\cdot\vert MM_1\vert$最小。

因此移动所需的时间是关于点$M$位置的一个单峰函数，考虑三分答案求最小值。但是点$N$的位置实际并不确定。同理可以先三分点$N$的位置，对于每个位置再三分点$M$的位置即可。

```cpp
#include<cstdio>
#include<cmath>
#include<algorithm>
using namespace std;
const int INF=0x7fffffff;
const double EPS=1e-6;

template<typename Tp>
struct point{
  Tp x,y;
  point(Tp x=0,Tp y=0):x(x),y(y){}
  point operator+(point b){return point(x+b.x,y+b.y);}
  point operator-(point b){return point(x-b.x,y-b.y);}
  point operator*(Tp b){return point(x*b,y*b);}
  point operator/(Tp b){return point(x/b,y/b);}
  friend Tp dist(point a,point b){
    return sqrt((a.x-b.x)*(a.x-b.x)+(a.y-b.y)*(a.y-b.y));
  }
};
point<double> A,B,C,D;
double P,Q,R;
double tsearch2(point<double> t){
  point<double> l=C,r=D;
  while(fabs(r.x-l.x)>EPS||fabs(r.y-l.y)>EPS){
    point<double> lmid=l+(r-l)/3,rmid=l+(r-l)*2/3;
    double f1=dist(A,t)/P+dist(t,lmid)/R+dist(lmid,D)/Q;
    double f2=dist(A,t)/P+dist(t,rmid)/R+dist(rmid,D)/Q;
    if(f1>f2)l=lmid;
    else r=rmid;
  }
  return dist(A,t)/P+dist(t,l)/R+dist(l,D)/Q;
}
double tsearch1(){
  point<double> l=A,r=B;
  while(fabs(r.x-l.x)>EPS||fabs(r.y-l.y)>EPS){
    point<double> lmid=l+(r-l)/3,rmid=l+(r-l)*2/3;
    if(tsearch2(lmid)>tsearch2(rmid))l=lmid;
    else r=rmid;
  }
  return tsearch2(l);
}
int main(){
  scanf("%lf%lf%lf%lf",&A.x,&A.y,&B.x,&B.y);
  scanf("%lf%lf%lf%lf",&C.x,&C.y,&D.x,&D.y);
  scanf("%lf%lf%lf",&P,&Q,&R);
  printf("%.2lf\n",tsearch1());
  return 0;
}
```