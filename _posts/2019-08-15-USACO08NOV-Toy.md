---
title: USACO18JAN Lifeguards P
author: P-Henning
permalink: /USACO08NOV-Toy
key: USACO08NOV-Toy
tags: [题目, 二分答案, 贪心]
---

#### 题目大意

Bessie需要在连续的$D$天内给奶牛们提供玩具，第$i$天需要的玩具数为$t_i$。买进一个新玩具需要花费$t_c$元，且已经过消毒。Farmer John担心没有经过消毒的玩具会带来传染病，因此玩具每使用一次后必须消毒。 有两种消毒的方式，第1种方式需要收费$c_1$美元，需要$n_1$个晚上的时间；第2种方式需要收费$c_2$美元，需要$n_2$个晚上的时间。请找出提供玩具的最少花费。

$1\leqslant D\leqslant 100000$，其中$70\%$的测试数据满足$1\leqslant D \leqslant 500$。

<!--more-->

---

#### 思路

对于$70\%$的数据，这道题就是经典的餐巾计划的费用流模型。

不妨设$c_1>c_2$。下面只讨论最常见也是最复杂的情况：$t_c>c_1>c_2$且$n_1<n_2$。

设$D$天内买$x$个玩具且保证每天的玩具供应的最小花费为$f(x)$，其中给玩具消毒所需的花费为$g(x)$，有

$$f(x)=g(x)+t_c\cdot x$$

如果$x$比较大，有相对较多的新玩具可以使用，那么给玩具消毒的次数就要少一些，即

$$g(x)-g(x-1)<0$$

并且$x$越大，选择较慢的消毒方式的机会越多，消毒的花费增加得就越少，即

$$g(x)-g(x-1)\leqslant g(x+1)-g(x)$$

所以$g(x)$的斜率是单调的。

把上面$f(x)$的式子变形后代入该不等式，可以得出$f(x)$的斜率也是单调的。于是$f(x)$是一个单峰函数，可以三分答案求最小值。

对于分到的答案$x$，考虑贪心求出$f(x)$。由于思考每一天的玩具的来源十分困难，可以换一种思路整体思考：先把$x$个玩具全部买好，把使用过的玩具全部消毒后储存起来，再根据取出来再次使用的时间决定花费是$c_1$还是$c_2$。用三个双端队列$N,M,O$分别存储消毒时间小于$n_1$天、达到$n_1$天而小于$n_2$天、达到$n_2$天的玩具，队中每个元素记录玩具的个数和开始消毒的时间（第几天）。则$N$中的玩具都不能使用，$M$中的玩具花费为$c_1$，$O$中的玩具花费为$c_2$。时间每往后推一天，三个队列中的元素都要更新一次。

贪心策略：

- 对于$M,O$中的玩具，优先使用$O$中的，一是当前的花费便宜，二是$M$中的玩具经过一段时间后消毒时间可能达到$n_2$天，从而进入队列$O$中，花费降低。
- 对于同属于$M$中的玩具，优先使用消毒时间靠后的，因为所有玩具花费都相同，而消毒时间靠前的玩具在将来更有可能进入队列$O$中，花费降低。

为了避免特殊处理，可以把一开始购买$x$个已经消毒的玩具看成是$0$时刻以前以单价$t_c-c_2$购买的$x$个未经消毒的玩具。当取出这些玩具使用时，会自动补上“消毒”费用$c_2$，从而保证每个玩具的花费为$t_c$。

```cpp
#include<cstdio>
#include<cmath>
#include<algorithm>
#include<deque>
using namespace std;
const int maxn=100000+5;
const int INF=0x7fffffff;
struct node{
  int day,cnt;
  node(int day=0,int cnt=0):day(day),cnt(cnt){}
};
deque<node> N,M,O;
int t[maxn],d,n1,n2,c1,c2,tc;
int l=0,r=0,ans=INF;
int f(int x){
  while(!N.empty())N.pop_front();
  while(!M.empty())M.pop_front();
  while(!O.empty())O.pop_front();
  int cost=(tc-c2)*x;
  N.push_back(node(-200000,x));
  for(int i=1;i<=d;i++){
    while(!N.empty()&&i-N.front().day>=n1){
      M.push_back(N.front());N.pop_front();
    }
    while(!M.empty()&&i-M.front().day>=n2){
      O.push_back(M.front());M.pop_front();
    }
    int rest=t[i];
    while(rest&&!O.empty()){
      if(O.back().cnt>rest)
        O.back().cnt-=rest,cost+=c2*rest,rest=0;
      else{
        rest-=O.back().cnt,cost+=c2*O.back().cnt;
        O.pop_back();
      }
    }
    while(rest&&!M.empty()){
      if(M.back().cnt>rest)
        M.back().cnt-=rest,cost+=c1*rest,rest=0;
      else{
        rest-=M.back().cnt,cost+=c1*M.back().cnt;
        M.pop_back();
      }
    }
    if(rest)return INF;
    N.push_back(node(i,t[i]));
  }
  return cost;
}
int main(){
  scanf("%d%d%d%d%d%d",&d,&n1,&n2,&c1,&c2,&tc);
  for(int i=1;i<=d;i++){scanf("%d",&t[i]);r+=t[i];}
  if(n1>n2)swap(n1,n2),swap(c1,c2);
  if(c1<c2)c2=c1;
  while(r-l>10){
    int lmid=l+(r-l)/3,rmid=l+(r-l)*2/3;
    int tmp=f(lmid);
    if(tmp<INF&&tmp<=f(rmid))r=rmid;
    else l=lmid;
  }
  for(int i=l;i<=r;i++)ans=min(ans,f(i));
  printf("%d\n",ans);
  return 0;
}
```
