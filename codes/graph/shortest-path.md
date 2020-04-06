---
layout: article
title: 最短路
permalink: /codes/graph/shortest-path
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## Dijkstra算法

### 堆优化的Dijkstra算法

```cpp
const int maxn=1e5+5,maxe=2e5+5;
const int INF=0x7fffff;

template<typename Tp>
class graph{
public:
  struct edge{
    int to;edge *next;Tp len;
    edge(int to=0,edge *next=0,Tp len=0):to(to),next(next),len(len){}
  };
  int n,edgtot;
  edge edges[maxe],*now[maxn];
  Tp dist[maxn];
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v,Tp w){
    edges[++edgtot]=edge(v,now[u],w),now[u]=edges+edgtot;
  }
  struct node{
    int id;Tp dist;
    node(int id=0,Tp dist=0):id(id),dist(dist){}
    bool operator<(node rhs)const{return dist>rhs.dist;}
  };
  void dijkstra(int s){
    for(int i=1;i<=n;i++)dist[i]=INF;
    priority_queue<node> q;
    q.push(node(s,dist[s]=0));
    while(!q.empty()){
      int u=q.top().id;Tp d=q.top().dist;q.pop();
      if(dist[u]!=d)continue;
      for(edge *e=now[u];e;e=e->next){
        int v=e->to;
        if(dist[u]+e->len<dist[v])
          q.push(node(v,dist[v]=dist[u]+e->len));
      }
    }
  }
};
```

---

## Floyd算法

```cpp
const int maxn=100+5;

int g[maxn][maxn],n;
void floyd(){
  for(int k=1;k<=n;k++)
    for(int i=1;i<=n;i++)
      for(int j=1;j<=n;j++)
        g[i][j]=min(g[i][j],g[i][k]+g[k][j]);
}
```

---

## Bellman-Ford算法

### 朴素的Bellman-Ford算法

```cpp
const int maxn=1e5+5,maxe=2e5+5;
const int INF=0x7fffff;

struct edge{int from,to,len;};
edge edges[maxe];
int dist[maxn],n,m;
bool bellman(int s){
  for(int i=1;i<=n;i++)dist[i]=INF;
  dist[s]=0;
  for(int i=1;i<=n;i++)
    for(int j=1;j<=m;j++){
      int u=edges[j].from,v=edges[j].to;
      dist[v]=min(dist[v],dist[u]+edges[j].len);
  }
  for(int j=1;j<=m;j++){
    int u=edges[j].from,v=edges[j].to;
    if(dist[v]>dist[u]+edges[j].len)return 1;
  }
  return 0;
}
```

### SPFA

```cpp
const int maxn=1e5+5,maxe=2e5+5;
const int INF=0x7fffff;

template<typename Tp>
class graph{
public:
  struct edge{
    int to;edge *next;Tp len;
    edge(int to=0,edge *next=0,Tp len=0):to(to),next(next),len(len){}
  };
  int cnt[maxn],n,edgtot;
  edge edges[maxe],*now[maxn];
  Tp dist[maxn];bool inq[maxn];
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v,Tp w){
    edges[++edgtot]=edge(v,now[u],w),now[u]=edges+edgtot;
  }
  bool spfa(int s){
    for(int i=1;i<=n;i++)dist[i]=INF,inq[i]=0,cnt[i]=0;
    inq[s]=1,dist[s]=0;
    queue<int>q;q.push(s);
    while(!q.empty()){
      int u=q.front();q.pop();inq[u]=0;
      for(edge *e=now[u];e;e=e->next){
        int v=e->to;
        if(dist[u]+e->len<dist[v]){
          dist[v]=dist[u]+e->len;
          if(!inq[v]){
            q.push(v);inq[v]=1;
            if(++cnt[v]>n)return 1;
          }
        }
      }
    }
    return 0;
  }
};
```

### DFS优化的SPFA

```cpp
const int maxn=1e5+5,maxe=2e5+5;
const int INF=0x7fffff;

template<typename Tp>
class graph{
public:
  struct edge{
    int to;edge *next;Tp len;
    edge(int to=0,edge *next=0,Tp len=0):to(to),next(next),len(len){}
  };
  int n,edgtot;
  edge edges[maxe],*now[maxn];
  Tp dist[maxn];bool inst[maxn];
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v,Tp w){
    edges[++edgtot]=edge(v,now[u],w),now[u]=edges+edgtot;
  }
private:
  bool dfs(int u){
    inst[u]=1;
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(dist[u]+e->len<dist[v]){
        dist[v]=dist[u]+e->len;
        if(inst[v]||dfs(v))return 1;
      }
    }
    return inst[u]=0;
  }
public:
  bool spfa(int s){
    for(int i=0;i<=n;i++)dist[i]=INF,inst[i]=0;
    return dfs(s);
  }
};
```
