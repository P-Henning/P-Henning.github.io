---
title: 刚体初步
author: P-Henning
permalink: /rigid-body
key: rigid-body
tags: [笔记, 物理]
---

## 力矩平衡

初中杠杆平衡知识：$F_1l_1=F_2l_2$

<div align="center"><img src="https://s1.ax1x.com/2020/03/22/844YBF.png"></div>

<!--more-->

力$F$与力臂$l$的乘积称为力矩$M$。如果转轴到作用点的距离为$r$，$\alpha$为$\vec F,\vec r$的夹角，就有

$$M=Fl=Fr\sin\alpha$$

$F_1$使得杆往逆时针转，$F_2$使得杆往顺时针转。于是将$F_1l_1$改写成$M_{\text{逆}}$，将$F_2l_2$改写成$M_{\text{顺}}$，就有

$$M_{\text{逆}}=M_{\text{顺}}$$

刚体平衡的条件：

- 不动：$\sum\vec F=0$。
- 不转：以任意一点为转轴，$\sum\vec M=0$，即$M_{\text{顺}}=M_{\text{逆}}$。

对于一根杆，只有两端受力。如果是下图情况，$\sum\vec F=0$，但$\sum\vec M\neq 0$，这样一对力称为力偶。在刚体平衡中要避免力偶。

<div align="center"><img src="https://s1.ax1x.com/2020/03/22/84HaND.png"></div>

如果二力要平衡，一定是下图的情况，即二力平衡必共线。

<div align="center"><img src="https://s1.ax1x.com/2020/03/22/84HUAO.png"></div>

如果三力要平衡，将两端的力反向延长交于点$A$，则以$A$为轴，两个力的力矩$M_{A_1},M_{A_2}$都为$0$。所以第三个力的力矩$M_{A_3}$也为$0$。于是就有了三力平衡必共点。

<div align="center"><img src="https://s1.ax1x.com/2020/03/22/84HtHK.png"></div>

在牛顿第二定律中有一个结论：轻物受合力一定为$0$。因为$m=0,F=ma$，如果$F\neq 0$，加速度$a$将趋于$+\infty$。

通过类比，轻钢体的力矩一定为$0$，否则会转飞~~（???）~~。轻钢体可以动、可以转，但必须满足$F=0,M=0$。

**例：**（2017中科大）如图所示，一长$L$、线密度为$\rho$的细棒斜靠在半径为$R$的圆筒外侧，细棒与地面的夹角为$\theta$，顶端正好与圆筒相切，所有接触的位置都存在摩擦，整个系统处于静止状态。

<div align="center"><img src="https://s1.ax1x.com/2020/03/22/84xGTI.png"></div>

（1）请给出细棒和圆筒的受力分析。

首先棒会受到地面的支持力$N_1$和圆筒的支持力$N_2$，以及自身的重力$mg$。此时还分析不出地面和圆筒提供的摩擦力$f_1,f_2$的方向。

圆筒受到棒的压力$N_2$和地面的支持力$N_3$，以及重力$m'g$。$N_2,N_3,m'g$都过圆心$O$，对$O$的力矩为$0$，因此棒和地面提供的摩擦力$f_2,f_3$的合力对$O$的力矩应该为$0$。于是有

$$f_2\cdot R=f_3\cdot R\tag 1$$

且$f_2,f_3$的旋转方向相反。

又因为$N_2,N_3,m'g$的合力在水平方向的分力向右，因此$f_2,f_3$在水平方向要有向左的分力。综合考虑可以得出$f_2,f_3$的方向。

<div align="center"><img src="https://s1.ax1x.com/2020/03/22/84xNff.png"></div>

现在只剩下地面给棒的摩擦力$f_1$。设圆筒与地面相切于点$A$，则$N_3,m'g,f_3$对$A$的力矩为$0$，所以$N_2,f_2$的合力对$A$的力矩为$0$，即过点$A$（沿下图虚线方向）。所以对棒而言，$N_2,f_2$的合力在水平方向的分力向左，所以$f_1$向右。

<div align="center"><img src="https://s1.ax1x.com/2020/03/22/84zRUI.png"></div>

（2）试给出圆筒与地面之间的摩擦力。

以圆筒为研究对象，根据力矩平衡得到$(1)$式，根据水平方向受力平衡可以得到

$$f_2\cos\theta+f_3=N_2\sin\theta\tag 2$$

设棒与地面交于点$B$，则$N_1,f_1,f_2$对$B$的力矩为$0$，于是$mg,N_2$对$B$的力矩为$0$，即

$$N_2\cdot L=mg\cdot\dfrac L2\cdot\cos\theta\tag 3$$

由$(1)(2)(3)$得

$$f_3=\dfrac{\dfrac 12\rho gL\sin\theta\cos\theta}{1+\cos\theta}$$

（3）分析一下结果的合理性。

合理性即系统平衡的条件。设棒与圆筒相切于点$P$，于是只要$A,B,P$三处不发生滑动即可。

$$\begin{cases}
A:\mu_3\geqslant f_3/N_3\\
B:\mu_1\geqslant f_1/N_1\\
P:\mu_2\geqslant f_2/N_2\\
\end{cases}$$

其中$N_3$由于$m'$未知而无法求出，其余物理量都可以通过~~乱七八糟的~~平衡方程解出来。此处略。

巧选转轴：若某点上存在未知力，可以以该点为轴，使未知力的力矩为$0$。

## 角动量

一个质量为$m$、到点$O$的距离为$r$的质点以点$O$为轴转动，其速度可以分解为切向的$v_{\text{切}}$和法向的$v_{\text{法}}$。设$\vec r,\vec v$的夹角为$\alpha$，定义该质点对轴的角动量为

$$L=mv_{\text{切}}r=mvr\sin\alpha$$

对于圆周运动，有$v_{\text{切}}=\omega r$，所以上式又可以写成：

$$L=m\omega r^2$$

<div align="center"><img src="https://s1.ax1x.com/2020/03/22/8oZwGD.png"></div>

假设该点做圆周运动，即$r$不变，$v_{\text{法}}=0$。如果给该点施加一个法向的力$F_{\text{法}}$，改变不了切向速度，也就改变不了角动量。因此只需考虑切向的作用力$F_{\text{切}}$。此时由牛顿第二定律得

$$a_{\text{切}}=\dfrac{\Delta v}{\Delta t}=\dfrac{F_{\text{切}}}m$$

于是

$$\dfrac{\Delta L}{\Delta t}=mr\cdot\dfrac{\Delta v}{\Delta t}=mr\cdot\dfrac{F_{\text{切}}}m=F_{\text{切}}\cdot r$$

因此一个切向的力给角动量贡献了一个力矩$F_{\text{切}}r$（这也说明力矩是角动量的变化，力矩为$0$时角动量守恒）。

但法向的作用力可能导致圆周运动的半径$r$改变。此时贡献的力矩为

$$\dfrac{\Delta L}{\Delta t}=m\cdot\dfrac{\Delta\left(v_{\text{切}}r\right)}{\Delta t}=mr\cdot\dfrac{\Delta v_{\text{切}}}{\Delta t}+mv_{\text{切}}\cdot\dfrac{\Delta r}{\Delta t}$$

此时$F_{\text{切}}$并不等于$m\cdot\dfrac{\Delta v_{\text{切}}}{\Delta t}$，原因是切向的力改变了法向的速度。

对于一个刚体，如果以$O$点为轴、以角速度$\omega$转动，此时刚体的角动量为

$$L=\sum m_i\omega r_i^2=\left(\sum m_ir_i^2\right)\omega=I\omega$$

其中$I$称为转动惯量~~（提前剧透）~~。

在前面的力矩平衡中，任意一点都可以为转轴。但力矩不平衡时，以不同点为轴，得到的力矩不同，转动惯量也不同。

**补充：关于切向加速度**

在极坐标系中，有

$$a_{\text{切}}=\dfrac{\Delta v_{\text{切}}}{\Delta t}+\dfrac{v_{\text{切}}v_{\text{法}}}r$$

所以在非圆周运动中，$F_{\text{切}}=0$并不意味着$\Delta v_{\text{切}}=0$。

**例：**水平的光滑桌面上开有一个小孔，一条绳穿过小孔，其两端各系有一质量为$m$的小球，开始时，用手握住下面的小球，桌上的小球以$v_0=\dfrac 32\sqrt{2gr_0}$的速率做半径为$r_0$的匀速圆周运动，然后放手，求桌上绳索的最大长度和最小长度。

放手时桌上小球运动的半径$r$会变大，此时小球有向外的速度$v_{\text{法}}$，因此虽然$F_{\text{切}}=0$，但$\Delta v_{\text{切}}\neq 0$。接下来的运动情形：

1. $v_{\text{法}}>0$，$r$变大，桌上绳索变长。
2. $v_{\text{法}}=0$，桌上绳索达到最长。
3. $v_{\text{法}}<0$，$r$变小，桌上绳索变短。
4. $v_{\text{法}}=0$，桌上绳索达到最短。回到1。

以小孔为轴，桌上小球的力矩$M=0$，于是角动量$L$守恒。

$$L=mv_{\text{切}}r$$

桌上小球的速度$v_1=\sqrt{v_{\text{切}}^2+v_{\text{法}}^2}$。由于沿绳方向速度相等，所以桌下小球的速度$v_2=v_{\text{法}}$。以桌面为零势能面，设绳长为$l$，则整个系统的总能量为

$$E=-mg(l-r)+\dfrac 12mv_{\text{法}}^2+\left(\dfrac 12mv_{\text{法}}^2+\dfrac 12mv_{\text{切}}^2\right)$$

由于整个系统无外力做功，因此总能量守恒。

通过之前的分析可知桌上绳索长度达到最值时$v_{\text{法}}=0$，此时

$$E=mgr-mgl+\dfrac 12mv_{\text{切}}^2$$

最初桌上小球做匀速圆周运动时，速度为切线方向，此时

$$\begin{aligned}
E&=mgr_0-mgl+\dfrac 12m\cdot\dfrac 92gr_0\\
L&=mr_0\cdot\dfrac 32\sqrt{2gr_0}
\end{aligned}$$

根据能量、角动量守恒，联立方程，解得

$$r_{\min}=r_0,r_{\max}=3r_0$$

## 转动惯量

$$I=\sum m_ir_i^2$$

对于一根质量为$m$、长度为$L$的杆，其端点$A$处的转动惯量为$I_A=\dfrac 13mL^2$，其中点$O$处的转动惯量为$I_O=\dfrac 1{12}mL^2$。

对于一个质量为$m$、半径为$R$的圆环，其转动惯量为$I_O=mR^2$。

对于一个质量为$m$、半径为$R$的圆盘/圆柱，其转动惯量为$I_O=\dfrac 12mR^2$。

柯尼希定理：

$$E_{k\text{转}}=\dfrac 12I\omega^2=\dfrac 12mv_C^2+\dfrac 12I_C\omega^2$$

如果质心$C$到转轴的距离为$d$，有$v_C=\omega d$，得到平行轴定理：

$$I=md^2+I_C$$

其中$v_C$为质心的速度，$I_C$为相对质心的转动惯量。

物理题太难写了QWQ。不写了。
