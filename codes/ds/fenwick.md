---
layout: article
title: 树状数组
permalink: /codes/ds/fenwick
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 树状数组

### 单点修改+区间查询

```cpp
const int maxn=1e5+5;

template<typename Tp>
class fenwick_tree{
public:
  Tp c[maxn];int n;
  void modify(int x,Tp d){
    for(int i=x;i<=n;i+=i&-i)c[i]+=d;
  }
  Tp query(int x,int y){
    Tp sum=0;
    for(int i=y;i>=1;i-=i&-i)sum+=c[i];
    for(int i=x-1;i>=1;i-=i&-i)sum-=c[i];
    return sum;
  }
};
```

### 区间修改+单点查询

```cpp
const int maxn=1e5+5;

template<typename Tp>
class fenwick_tree{
public:
  Tp c[maxn];int n;
  void modify(int x,int y,Tp d){
    for(int i=x;i<=n;i+=i&-i)c[i]+=d;
    for(int i=y+1;i<=n;i+=i&-i)c[i]-=d;
  }
  Tp query(int x){
    Tp sum=0;
    for(int i=x;i>=1;i-=i&-i)sum+=c[i];
    return sum;
  }
};
```

### 区间修改+区间查询

```cpp
const int maxn=1e5+5;

template<typename Tp>
class fenwick_tree{
public:
  Tp c[maxn],c2[maxn];int n;
  void modify(int x,int y,Tp d){
    for(int i=x;i<=n;i+=i&-i)c[i]+=d,c2[i]+=x*d;
    for(int i=y+1;i<=n;i+=i&-i)c[i]-=d,c2[i]-=(y+1)*d;
  }
  Tp query(int x,int y){
    Tp sum=0;
    for(int i=y;i>=1;i-=i&-i)sum+=(y+1)*c[i]-c2[i];
    for(int i=x-1;i>=1;i-=i&-i)sum-=x*c[i]-c2[i];
    return sum;
  }
};
```

### 权值树状数组

```cpp
const int maxn=1e5+5;

template<typename Tp>
class fenwick_tree{
public:
  int c[maxn];
  void insert(Tp t){
    for(int i=t;i<maxn;i+=i&-i)c[i]++;
  }
  void remove(Tp t){
    for(int i=t;i<maxn;i+=i&-i)c[i]--;
  }
  int rank(Tp t){
    int sum=0;t--;
    for(int i=t;i>=1;i-=i&-i)sum+=c[i];
    return sum+1;
  }
  Tp findkth(int k){
    Tp ans=0;
    for(int i=floor(log2(n));i>=0;i--){
      ans+=1<<i;
      if(ans>n||c[ans]>=k)ans-=1<<i;
      else k-=c[ans];
    }
    return ans+1;
  }
  Tp findpre(Tp t){return findkth(rank(t)-1);}
  Tp findsuc(Tp t){return findkth(rank(t+1));}
};
```
