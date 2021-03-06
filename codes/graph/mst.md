---
layout: article
title: 最小生成树
permalink: /codes/graph/mst
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## Kruskal算法

```cpp
const int maxn=1e5+5,maxe=2e5+5;

int fa[maxn],n,m;
int find(int x){return fa[x]==x?x:fa[x]=find(fa[x]);}
struct edge{
  int a,b,len;
  bool operator<(edge rhs)const{return len<rhs.len;}
};
edge edges[maxe];
int kruskal(){
  int ans=0;
  sort(edges+1,edges+1+m);
  for(int i=1;i<=n;i++)fa[i]=i;
  for(int i=1,cnt=0;i<=m&&cnt<n-1;i++){
    int fx=find(edges[i].a),fy=find(edges[i].b);
    if(fx!=fy)fa[fx]=fy,ans+=edges[i].len,cnt++;
  }
  return ans;
}
```

---

## Prim算法

### 朴素的Prim算法

```cpp
const int maxn=5000+5;
const int INF=0x7fffffff;

int x[maxn],y[maxn],n;
int dist[maxn],path[maxn];
bool vis[maxn];
inline int len(int u,int v){
  return abs(x[u]-x[v])+abs(y[u]-y[v]);
};
int prim(int s){
  for(int i=1;i<=n;i++)dist[i]=len(i,s),path[i]=s,vis[i]=0;
  dist[s]=0,vis[s]=1;
  for(int cnt=1;cnt<n;cnt++){
    int k=0;dist[k]=INF;
    for(int i=1;i<=n;i++)
      if(!vis[i]&&dist[i]<dist[k])k=i;
    vis[k]=1;
    for(int i=1;i<=n;i++)
      if(!vis[i]&&dist[i]>len(i,k))
        dist[i]=len(i,k),path[i]=k;
  }
  int ans=0;
  for(int i=1;i<=n;i++)
    if(path[i]!=i)ans+=len(path[i],i);
  return ans;
}
```

### 堆优化的Prim算法

```cpp
const int maxn=1e5+5,maxe=2e5+5;

template<typename Tp>
class graph{
public:
  struct edge{
    int to;edge *next;Tp len;
    edge(int to=0,edge *next=0,Tp len=0):to(to),next(next),len(len){}
    bool operator<(edge rhs)const{return len>rhs.len;}
  };
  int n,edgtot;
  edge edges[maxe],*now[maxn];
  bool vis[maxn];
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v,Tp w){
    edges[++edgtot]=edge(v,now[u],w),now[u]=edges+edgtot;
  }
  Tp prim(int s){
    for(int i=1;i<=n;i++)vis[i]=0;
    priority_queue<edge> q;
    for(edge *e=now[s];e;e=e->next)q.push(*e);
    Tp ans=0;int cnt=0;vis[s]=1;
    while(!q.empty()&&cnt<n-1){
      edge k=q.top();q.pop();
      if(vis[k.to])continue;
      ans+=k.len,cnt++,vis[k.to]=1;
      for(edge *e=now[k.to];e;e=e->next)
        if(!vis[e->to])q.push(*e);
    }
    return ans;
  }
};
```
