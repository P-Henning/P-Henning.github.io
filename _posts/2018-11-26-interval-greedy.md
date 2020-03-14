---
title: 区间贪心问题
author: P-Henning
permalink: /interval-greedy
key: interval-greedy
tags: 贪心
---

## 区间选点问题

数轴上有$n$个闭区间$[a_i,b_i]$。在数轴上选出尽量少的关键点，使得每个区间内都至少有一个关键点。

将区间按右端点由小到大排序，然后从左往右讨论每一个区间，若该区间内没有关键点，将右端点作为关键点。记录目前已选的最右边的点的位置$cur$。这样能保证选择的关键点最少，而且选择区间右端点作为关键点，能使右侧尽可能多的区间包含这个点。

<!--more-->

```cpp
struct node{int l,r;}d[maxn];
bool cmp(node a,node b){
  if(a.r==b.r)return a.l<b.l;
  return a.r<b.r;
}

sort(d+1,d+1+n,cmp);
int ans=1,cur=d[1].r;
for(int i=2;i<=n;i++)
  if(d[i].l>cur)cur=d[i].r,ans++;
```

## 选择不相交区间

数轴上有$n$个闭区间$[a_i,b_i]$。从中选择尽量多的区间，使得这些区间两两没有公共点。

将区间按右端点由小到大排序，然后从左往右讨论每一个区间，优先选择与前面的区间不相交且右端点较小的区间。记录目前已选区间中往右最远能覆盖到的位置$cur$。

```cpp
struct node{int l,r;}d[maxn];
bool cmp(node a,node b){
  if(a.r==b.r)return a.l<b.l;
  return a.r<b.r;
}

sort(d+1,d+1+n,cmp);
int ans=1,cur=d[1].r;
for(int i=2;i<=n;i++)
  if(d[i].l>cur)cur=d[i].r,ans++;
```

## 区间覆盖问题

数轴上有$n$个闭区间$[a_i,b_i]$。从中选择尽量少的区间，使得这些区间覆盖一段指定区间$[s,t]$。

将区间按左端点由小到大排序，若左端点相同，按右端点由大到小排序。若第一个区间起点在$s$右侧，则无解。否则从左往右讨论每个区间，优先选择右端点大的区间。记录目前已选区间中往右最远能覆盖到的位置$cur$。讨论所有$l\leqslant cur$且未被讨论过的区间，记录其中右端点的最大值$maxr$，若$maxr>cur$，则选择该区间后$cur$可以延伸到$maxr$的位置，否则无解。

```cpp
struct node{int r,l;}d[maxn];
bool cmp(node a,node b){
  if(a.l==b.l)return a.r>b.r;
  return a.l<b.l;
}
int solve(int s,int t){
  if(d[1].l>s)return -1;
  int cur=d[1].r,maxr=d[1].r,ans=1,i=2;
  if(cur>=t)return ans;
  while(i<=n){
    while(i<=n&&d[i].l<=cur)maxr=max(maxr,d[i++].r);
    if(maxr<=cur)return -1;
    ans++,cur=maxr;
    if(cur>=t)return ans;
  }
  return -1;
}
```

三个问题讨论的时间复杂度均为$O(n)$。
