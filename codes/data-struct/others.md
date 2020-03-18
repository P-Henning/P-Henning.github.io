---
layout: article
title: 其它
permalink: /codes/data-struct/others
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

---

## 并查集

### 普通并查集

```cpp
const int maxn=1e5+5;

class disjoint_set{
public:
  int fa[maxn];
  void init(int x){fa[x]=x;}
  int find(int x){
    return fa[x]==x?x:fa[x]=find(fa[x]);
  }
  void merge(int x,int y){
    int fx=find(x),fy=find(y);
    if(fx!=fy)fa[fx]=fy;
  }
};
```

### 带权并查集

```cpp
const int maxn=1e5+5;

template<typename Tp>
class disjoint_set{
public:
  int fa[maxn];Tp dist[maxn];
  void init(int x){fa[x]=x,dist[x]=0;}
  int find(int x){
    if(fa[x]==x)return x;
    int anc=find(fa[x]);
    dist[x]+=dist[fa[x]];
    return fa[x]=anc;
  }
  void merge(int x,int y,Tp len){
    int fx=find(x),fy=find(y);
    if(fx!=fy)fa[fy]=fx,dist[fy]=len+dist[x]-dist[y];
  }
};
```

---

## 稀疏表

```cpp
const int maxn=1e5+5;

int log_f[maxn];
void initlog_f(int n){
  log_f[1]=0;
  for(int i=2;i<=n;i++)log_f[i]=log_f[i>>1]+1;
}

template<typename Tp>
class sparse_table{
public:
  Tp f[maxn][25];
  void build(Tp *a,int n){
    for(int i=1;i<=n;i++)f[i][0]=a[i];
    for(int j=1;j<=log_f[n];j++)
      for(int i=1;i<=n-(1<<j)+1;i++)
        f[i][j]=max(f[i][j-1],f[i+(1<<j-1)][j-1]);
  }
  Tp findmax(int x,int y){
    int k=log_f[y-x+1];
    return max(f[x][k],f[y-(1<<k)+1][k]);
  }
};
```
