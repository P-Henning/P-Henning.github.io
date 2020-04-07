---
layout: article
title: 并查集
permalink: /codes/ds/dsu
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
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
