---
layout: article
title: 连通性
permalink: /codes/graph/tarjan
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## Tarjan算法

### 有向图强联通分量

```cpp
const int maxn=1e5+5,maxe=2e5+5;

class graph{
public:
  struct edge{
    int to;edge *next;
    edge(int to=0,edge *next=0):to(to),next(next){}
  };
  int n,edgtot,scctot,dfntot;
  edge edges[maxe],*now[maxn];
  int bel[maxn],dfn[maxn],low[maxn];
  int st[maxn],top;bool inst[maxn];
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v){
    edges[++edgtot]=edge(v,now[u]),now[u]=edges+edgtot;
  }
private:
  void dfs(int u){
    dfn[u]=low[u]=++dfntot,st[++top]=u,inst[u]=1;
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(!dfn[v]){dfs(v);low[u]=min(low[u],low[v]);}
      else if(inst[v])low[u]=min(low[u],dfn[v]);
    }
    if(dfn[u]==low[u]){
      int v;scctot++;
      do v=st[top--],bel[v]=scctot,inst[v]=0;while(u!=v);
    }
  }
public:
  void tarjan(){
    top=scctot=dfntot=0;
    for(int i=1;i<=n;i++)dfn[i]=0,inst[i]=0;
    for(int i=1;i<=n;i++)if(!dfn[i])dfs(i);
  }
};
```

### 无向图割点、割边

```cpp
const int maxn=1e5+5,maxe=2e5+5;

class graph{
public:
  struct edge{
    int to;edge *next;
    edge(int to=0,edge *next=0):to(to),next(next){}
  };
  int dfn[maxn],low[maxn],n,edgtot,dfntot;
  edge edges[maxe],*now[maxn];
  bool iscutp[maxn];int brid;
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v){
    edges[++edgtot]=edge(v,now[u]),now[u]=edges+edgtot;
  }
private:
  void dfs(int u,int cfa){
    int cnt=0;dfn[u]=low[u]=++dfntot;
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(!dfn[v]){
        dfs(v,u);low[u]=min(low[u],low[v]),cnt++;
        if(cfa&&low[v]>=dfn[u])iscutp[u]=1;
        if(low[v]>dfn[u])brid++;
      }
      else if(v!=cfa)low[u]=min(low[u],dfn[v]);
    }
    if(!cfa&&cnt>1)iscutp[u]=1;
  }
public:
  void tarjan(){
    dfntot=brid=0;
    for(int i=1;i<=n;i++)dfn[i]=0,iscutp[i]=0;
    for(int i=1;i<=n;i++)if(!dfn[i])dfs(i,0);
  }
};
```

### 无向图点双连通分量

```cpp
const int maxn=1e5+5,maxe=2e5+5;

class graph{
public:
  struct edge{
    int from,to;edge *next;
    edge(int from=0,int to=0,edge *next=0):from(from),to(to),next(next){}
  };
  int n,edgtot,bcctot,dfntot;
  vector<int> bcc[maxn];
  edge edges[maxe],*now[maxn];
  int bel[maxn],dfn[maxn],low[maxn];
  bool iscutp[maxn];
  edge *st[maxe];int top;
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v){
    edges[++edgtot]=edge(u,v,now[u]),now[u]=edges+edgtot;
  }
private:
  void dfs(int u,int cfa){
    int cnt=0;dfn[u]=low[u]=++dfntot;
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(!dfn[v]){
        st[++top]=e,cnt++;
        dfs(v,u);low[u]=min(low[u],low[v]);
        if(low[v]>=dfn[u]){
          int p,q;iscutp[u]=1;
          bcc[++bcctot].clear();
          do{
            edge *k=st[top--];p=k->from,q=k->to;
            if(bel[p]!=bcctot){bcc[bcctot].push_back(p);bel[p]=bcctot;}
            if(bel[q]!=bcctot){bcc[bcctot].push_back(q);bel[q]=bcctot;}
          }while(p!=u||q!=v);
        }
      }
      else if(v!=cfa&&dfn[v]<dfn[u])
        st[++top]=e,low[u]=min(low[u],dfn[v]);
    }
    if(!cfa&&cnt<=1)iscutp[u]=0;
  }
public:
  void tarjan(){
    top=bcctot=dfntot=0;
    for(int i=1;i<=n;i++)dfn[i]=bel[i]=0,iscutp[i]=0;
    for(int i=1;i<=n;i++)if(!dfn[i])dfs(i,0);
  }
};
```
