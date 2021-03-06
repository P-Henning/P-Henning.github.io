---
layout: article
title: 二分图
permalink: /codes/graph/bipartite
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 匈牙利算法

```cpp
const int maxn=1e5+5,maxe=2e5+5;

class graph{
public:
  struct edge{
    int to;edge *next;
    edge(int to=0,edge *next=0):to(to),next(next){}
  };
  int match[maxn],vis[maxn],n,edgtot,vistot;
  edge edges[maxe],*now[maxn];
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v){
    edges[++edgtot]=edge(v,now[u]),now[u]=edges+edgtot;
  }
private:
  bool dfs(int u){
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(vis[v]!=vistot){
        vis[v]=vistot;
        if(!match[v]||dfs(match[v])){match[v]=u;return 1;}
      }
    }
    return 0;
  }
public:
  int hungary(){
    int ans=0;vistot=0;
    for(int i=1;i<=n;i++)match[i]=vis[i]=0;
    for(int i=1;i<=n;i++)vistot++,ans+=dfs(i);
    return ans;
  }
};
```
