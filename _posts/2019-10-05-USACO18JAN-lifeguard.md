---
title: USACO18JAN Lifeguards P
author: P-Henning
permalink: /USACO18JAN-lifeguard
key: USACO18JAN-lifeguard
tags: [动态规划, 单调队列]
---

#### 题目大意

FJ的泳池每天在时刻$1$开门，一直到时刻$10^9$才关闭。为确保奶牛的安全，他雇佣了$n$只救生牛，分别编号为$1,2,\dots,n$。救生牛$i$的工作时段为$(s_i,t_i]$（不含起始时刻，含结束时刻）。现在FJ需要解雇恰好$k$头救生牛。求剩余的救生牛最多能够覆盖多少时刻（某个时刻被覆盖当且仅当这时有至少一个救生牛在工作）。

$1\leqslant k\leqslant 100$，$k\leqslant n\leqslant 10^5$。

<!--more-->

---

#### 思路

在定动规状态之前，可以做一些预处理。如果某一头救生牛的工作时段被另外一头完全覆盖，这头救生牛就可以解雇，从而保证最后覆盖的时刻尽可能多。这样处理后，救生牛的工作时段之间就只有相交和相离的关系，若按起始时刻由小到大排序，显然结束时刻也是由小到大排列的。然后从左往右讨论。

状态$f[i][j]$表示在前$i$头救生牛中解雇$j$头牛，且保留第$i$头牛的最大覆盖长度。考虑之前的某一头牛$x$，解雇掉$x$和$i$之间的$i-x-1$头牛，得到状态转移方程：

$$f[i][j]=\max_{i-x-1\leqslant k}\lbrace f[x][j−(i−x−1)]+t[i]−\max\lbrace s[i],t[x]\rbrace\rbrace$$

直接枚举$x$会让每次转移花费$O(k)$的时间。

如果$f[x][y]$能够转移到$f[i][j]$，根据方程，有$y=j-(i-x-1)$，即$x-y=i-j-1$。

- 若$x,i$两头牛的工作时段有重叠，即$s[i]\leqslant t[x]$，有
  
  $$\begin{aligned}
f[i][j]&=\max\lbrace f[x][y]+t[i]-t[x]\rbrace\\
&=\max\lbrace f[x][y]-t[x]\rbrace+t[i]
\end{aligned}$$
  
  此时可以用双端队列$q[m]$维护所有满足$x-y=m$的$f[x][y]-t[x]$的最大值，状态转移就实现了$O(1)$。
  
- 若$x,i$两头牛的工作时段没有重叠，即$s[i]>t[x]$，有
  
  $$\begin{aligned}
f[i][j]&=\max\lbrace f[x][y]+t[i]-s[i]\rbrace\\
&=\max\lbrace f[x][y]\rbrace+t[i]-s[i]
\end{aligned}$$
  
  可以用另一个双端队列维护$f[x][y]$的最大值，不过没有这个必要。注意到双端队列$q[m]$中元素的$t[x]$是单调递增的，所以这些元素一定是前面一部分和$i$不重叠，后面一部分和$i$重叠。当我们求重叠的情况的$f[x][y]-t[x]$的最大值的时候，前面的不重叠的$f[x][y]-t[x]$出队以后就没有用了，此时就可以拿出来加上$t[x]$，变成$f[x][y]$，在不断出队的过程中统计$f[x][y]$的最大值，用来更新状态$f[i][j]$。需要注意的是这个最大值要存入一个数组$p[m]$中，供以后使用。

这样做以后，每一个状态仍然仅有一次进队和出队的机会，因此时间复杂度为$O(nk)$。

最终答案为$\max\limits_{j+n-i=k}\lbrace f[i][j]\rbrace$。

```c++
#include<cstdio>
#include<algorithm>
#include<deque>
using namespace std;
const int maxn=1e5+5,maxk=100+5;
struct node1{int s,t;}a[maxn],b[maxn];
bool cmp(node1 a,node1 b){
  if(a.s==b.s)return a.t>b.t;
  return a.s<b.s;
}
struct node{
  int f,id;
  node(int f=0,int id=0):f(f),id(id){}
};
deque<node> q[maxn];
int f[maxn][maxk],p[maxn],s[maxn],t[maxn],n,k;
int main(){
  scanf("%d%d",&n,&k);
  for(int i=1;i<=n;i++){
    scanf("%d%d",&a[i].s,&a[i].t);
  }
  sort(a+1,a+1+n,cmp);
  int tot=0,maxt=-1;
  for(int i=1;i<=n;i++){
    if(a[i].t<=maxt)k--;
    else b[++tot]=a[i],maxt=a[i].t;
  }
  k=max(k,0),n=tot;
  for(int i=1;i<=n;i++)
    for(int j=0;j<=k&&j<i;j++){
      int m=i-j-1;
      while(!q[m].empty()&&b[q[m].front().id].t<b[i].s){
        p[m]=max(p[m],q[m].front().f+b[q[m].front().id].t);
        q[m].pop_front();
      }
      f[i][j]=max(f[i][j],p[m]+b[i].t-b[i].s);
      if(!q[m].empty())f[i][j]=max(f[i][j],q[m].front().f+b[i].t);
      int tmpf=f[i][j]-b[i].t;m++;
      while(!q[m].empty()&&q[m].back().f<tmpf)q[m].pop_back();
      q[m].push_back(node(tmpf,i));
  }
  int ans=0;
  for(int i=1;i<=n;i++)
    for(int j=0;j<=k&&j<i;j++)
      if(j+n-i==k)ans=max(ans,f[i][j]);
  printf("%d\n",ans);
  return 0;
}
```
