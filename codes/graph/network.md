---
layout: article
title: 网络流
permalink: /codes/graph/network
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## ISAP算法

```cpp
const int maxn=1e5+5,maxe=2e5+5;
const int INF=0x7fffffff;

template<typename Tp>
class graph{
public:
  struct edge{
    int to;edge *next;Tp cap;
    edge(int to=0,edge *next=0,Tp cap=0):to(to),next(next),cap(cap){}
  };
  int dist[maxn],cnt[maxn],n,s,t,edgtot;
  edge edges[maxe],*now[maxn];
  #define D(e) (edges+((e)-edges^1))
  void init(int n,int s,int t){
    this->n=n,this->s=s,this->t=t,edgtot=-1;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v,Tp cap){
    edges[++edgtot]=edge(v,now[u],cap),now[u]=edges+edgtot;
    edges[++edgtot]=edge(u,now[v],0),now[v]=edges+edgtot;
  }
private:
  Tp dfs(int u,Tp flow){
    if(u==t)return flow;
    Tp tmp,d=0;
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(e->cap>0&&dist[u]==dist[v]+1){
        tmp=dfs(v,min(flow-d,e->cap));
        e->cap-=tmp,D(e)->cap+=tmp,d+=tmp;
        if(d==flow||dist[s]>=t)return d;
      }
    }
    if(dist[s]>=t)return d;
    if(!--cnt[dist[u]])dist[s]=t;
    cnt[++dist[u]]++;return d;
  }
public:
  Tp isap(){
    Tp ans=0;
    for(int i=1;i<=n;i++)dist[i]=cnt[i]=0;
    while(dist[s]<t)ans+=dfs(s,INF);
    return ans;
  }
};
```

---

## MCMF算法

```cpp
const int maxn=1e5+5,maxe=2e5+5;
const int INF=0x7fffffff;

template<typename Tp>
class graph{
public:
  struct edge{
    int to;edge *next;Tp cap,cost;
    edge(int to=0,edge *next=0,Tp cap=0,Tp cost=0):to(to),next(next),cap(cap),cost(cost){}
  };
  int n,s,t,edgtot;
  edge edges[maxe],*now[maxn],*path[maxn];
  Tp dist[maxn];bool inq[maxn];
  #define D(e) (edges+((e)-edges^1))
  void init(int n,int s,int t){
    this->n=n,this->s=s,this->t=t,edgtot=-1;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v,Tp cap,Tp cost){
    edges[++edgtot]=edge(v,now[u],cap,cost),now[u]=edges+edgtot;
    edges[++edgtot]=edge(u,now[v],0,-cost),now[v]=edges+edgtot;
  }
private:
  bool spfa(Tp &maxflow,Tp &mincost){
    for(int i=1;i<=t;i++)dist[i]=INF,path[i]=0,inq[i]=0;
    dist[s]=path[s]=0,inq[s]=1;
    queue<int> q;q.push(s);
    while(!q.empty()){
      int u=q.front();q.pop();inq[u]=0;
      for(edge *e=now[u];e;e=e->next){
        int v=e->to;
        if(e->cap>0&&dist[v]>dist[u]+e->cost){
          dist[v]=dist[u]+e->cost,path[v]=e;
          if(!inq[v]){inq[v]=1;q.push(v);}
        }
      }
    }
    if(dist[t]==INF)return 0;
    Tp flow=INF;int i=t;
    while(i!=s)flow=min(flow,path[i]->cap),i=D(path[i])->to;
    maxflow+=flow,mincost+=flow*dist[t],i=t;
    while(i!=s)path[i]->cap-=flow,D(path[i])->cap+=flow,i=D(path[i])->to;
    return 1;
  }
public:
  Tp mcmf(Tp &mincost){
    Tp maxflow=0;mincost=0;
    while(spfa(maxflow,mincost));
    return maxflow;
  }
};
```
