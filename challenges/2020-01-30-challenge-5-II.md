---
title: 挑战五：How to Get Highest Score in Garupa (II)
author: P-Henning
date: 2020-01-30
permalink: /challenge-5-II
key: challenge-5-II
sharing: true
license: true
aside:
  toc: true
show_edit_on_github: true
show_subscribe: true
pageview: true
---

### 说在前面

在控制疫情的关键时期，位于根结点的军队却始终没有被分配到儿子结点上[^noip]，多才多艺的yuzan1830选择了BanG Dream。

据yuzan1830描述，这款音游中每个note的得分靠按键准度，分为perfect、great、good、bad、miss。该音游的得分机制见原题目。在只考虑perfect、great、miss，并且技能倍率为$1$、基础分数为常数的情况下，设基础分数为$a$，玩家准度（perfect数占note总数的比）为$p$，note总数为$k$，miss数为$x$，求理论最高分和最低分，并各输出一种方案。

yuzan1830给出的数据范围：$1000\leqslant a\leqslant 3000$，$0.8\leqslant p\leqslant 1.0$，$600\leqslant k\leqslant 1200$，$0\leqslant x\leqslant 10$。

不得不吐槽，yuzan1830给出的数据范围居然是有下限的。

考虑到本蒟蒻的水平，实际数据规模可能会比给定的范围低。

### 接受挑战

求理论最高分和最低分，容易想到动态规划。

> 达羌：关于贪心，他死了。

为了方便，记连击$t$次的combo倍率为$combo[t]$，perfect数为$np=k\cdot p$，great数为$ng=k-np-x$。同时，在讨论过程中不考虑基础分数$a$，因为对分数高低没有影响。（所以下文的$a$不一定指基础分数）

**法1：** 设状态$f[n][a][b][c][t]$表示前$n$个note中有$a$次perfect、$b$次great、$c$次miss，且目前连击$t$次，此时的理论最高分。最终答案为$\max\limits_i\lbrace f[k][np][ng][x][i]\rbrace$。

讨论第$n$个note分别是perfect、great、miss的三种情况，得到状态转移方程：

$$f[n][a][b][c][t]=\max\begin{cases}
f[n-1][a-1][b][c][t-1]+d_1[t]\\
f[n-1][a][b-1][c][t-1]+d_2[t]\\
f[n-1][a][b][c-1][t']&t=0
\end{cases}$$

其中$d_1[t]$、$d_2[t]$表示连击次数为$t$时perfect、great的得分。

$$\begin{aligned}
d_1[t]&=1.1\cdot combo[t]\\
d_2[t]&=0.8\cdot combo[t]
\end{aligned}$$

注意到$n=a+b+c$，因此可以丢掉一维，又因为miss数和great数都较少，选择丢掉$a$，状态变成了$f[n][b][c][t]$，状态数降至$O\left(k^3x\right)$。

在状态转移的过程中，$n$这一维可以用滚动数组滚掉，空间复杂度$O(k^2x)$。

每次状态转移需要枚举$t'$。可以用$mf[n][a][b][c]$记录$f[n][a][b][c][t']$的最大值，每次转移就变成$O(1)$，总时间复杂度为$O\left(k^3x\right)$。

**法2：** 所有的note可以看成是若干段连击被若干个miss分隔开。注意到miss是不得分的，因此总得分就是若干段连击的得分之和。

设状态$f[m][a][b]$表示总共有$a$次perfect、$b$次great的$m$段连击的理论最高分。最终答案为$\max\limits_{i=1}^{x+1}\lbrace f[i][np][ng]\rbrace$。

状态转移方程：

$$f[m][a][b]=\max\lbrace f[m-1][a-t_1][b-t_2]+d[t_1][t_2]\rbrace$$

其中$d[t_1][t_2]$表示一段包含$t_1$个perfect、$t_2$个great的连击的最高得分。由于combo倍率递增，根据排序不等式，perfect应该在great之后。于是$d[t_1][t_2]$可以递推求出：

$$d[t_1][t_2]=\begin{cases}
d[0][t_2-1]+d_2[t_2]&t_1=0\\
d[t_1-1][t_2]+d_1[t_1+t_2]&t_1\neq 0
\end{cases}$$

状态数为$O\left(k^2x\right)$，即空间复杂度。每次转移需要枚举$t_1,t_2$，复杂度$O\left(k^2\right)$。注意到$f[m-1][a-t_1][b-t_2]+d[t_1][t_2]$的值关于$t_1,t_2$都是单峰的，因此可以三分$t_1,t_2$求出最值，每次转移就变成了$O\left(\log^2k\right)$，总时间复杂度为$O\left(k^2x\log^2k\right)$。比法1优秀，但常数大。

> 达羌：我才不会告诉你这个“注意到”花了我多少时间。

然后是输出方案。这时候就体现出了法2的优势。因为状态转移过程中$t_1,t_2$就记录了每一段连击中perfect和great的个数，避免重新计数；两者的位置又可以根据排序不等式直接确定。miss就穿插在连击之间。

对于每个状态$f[m][a][b]$，记录$h_1,h_2$，表示$f[m][a][b]$是由状态$f[m-1][a-h_1][b-h_2]$转移来的。从最终状态向转移到它的状态后退，途径的每个状态的$h_1,h_2$作为答案输出。

至于法1……只单独考虑了每一个note。而且法1的状态数本来是爆仓了的，DP的时候用了滚动数组，一滚动之前的note就没了。不过每个note只有3种情况，可以考虑三进制压位。

> 达羌：……说的像我可以写出来一样。

最低分的求法类似。

其实最高分有一个显然的贪心做法，就是把所有的perfect和great组成一段连击，然后great在前，perfect在后。时间复杂度$O(k)$。

也许最低分也有类似的做法，但本蒟蒻没有看出来。

代码中求最高分采用的是贪心，求最低分采用法2。~~三分套三分真恶心。~~

### 说在后面

提供几组样例：

**样例输入 1**

    1035 0.85 720 4

**样例输出 1**

    834872.400
    gr104-p612-m4
    796682.970
    p146-m-p143-m-p108-gr29-m-p113-gr23-m-p102-gr52

**样例输入 2**

    2333 1 233 0

**样例输出 2**

    614500.535
    p233
    614500.535
    p233

**样例输入 3**

    3000 0.8 1200 10

**样例输出 3**

    4038390.000
    gr230-p960-m10
    3771150.000
    p93-gr29-m-p91-gr31-m-p91-gr15-m-p96-gr13-m-p96-gr19-m-p93-gr5-m-p95-gr7-m-p93-gr8-m-p98-gr6-m-p57-gr40-m-p57-gr57

### 在线运行

<div data-pym-src="https://www.jdoodle.com/embed/v0/1VDi"></div>

<script src="https://www.jdoodle.com/assets/jdoodle-pym.min.js" type="text/javascript"></script>

[^noip]: 来自NOIP2012提高组D2T3。
