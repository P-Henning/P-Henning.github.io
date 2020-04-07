---
layout: article
title: 树链剖分
permalink: /codes/ds/hld
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 树链剖分

### 重链剖分

```cpp
const int maxn=1e5+5;

template<typename Tp>
class path_decomposition{
private:
  class segment_tree{
  public:
    struct node{
      node *son[2];
      int l,r;Tp sum,tag;
      void init(int l,int r){
        this->l=l,this->r=r,son[0]=son[1]=0,sum=tag=0;
      }
      void add(Tp d){sum+=d*(r-l+1),tag+=d;}
      void update(){sum=son[0]->sum+son[1]->sum;}
      void pushdown(){
        if(tag){son[0]->add(tag);son[1]->add(tag);tag=0;}
      }
    };
    node nodes[maxn],*root;int tot;
    void build(node *&p,Tp *a,int *rnk,int l,int r){
      p=nodes+(++tot);p->init(l,r);
      if(l==r){p->sum=a[rnk[l]];return;}
      build(p->son[0],a,rnk,l,l+r>>1);
      build(p->son[1],a,rnk,(l+r>>1)+1,r);
      p->update();
    }
    void modify(node *&p,int l,int r,Tp d){
      if(!p||p->r<l||p->l>r)return;
      if(l<=p->l&&p->r<=r){p->add(d);return;}
      p->pushdown();
      modify(p->son[0],l,r,d);
      modify(p->son[1],l,r,d);
      p->update();
    }
    Tp query(node *&p,int l,int r){
      if(!p||p->r<l||p->l>r)return 0;
      if(l<=p->l&&p->r<=r)return p->sum;
      p->pushdown();
      return query(p->son[0],l,r)+query(p->son[1],l,r);
    }
  };
  segment_tree auxi;
public:
  struct edge{
    int to;edge *next;
    edge(int to=0,edge *next=0):to(to),next(next){}
  };
  int id[maxn],rnk[maxn],n,edgtot,idtot;
  edge edges[maxn],*now[maxn];
  int fa[maxn],son[maxn],dep[maxn],size[maxn],top[maxn];
  void init(int n){
    this->n=n,edgtot=idtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v){
    edges[++edgtot]=edge(v,now[u]),now[u]=edges+edgtot;
  }
private:
  void dfs1(int u,int cfa,int cdep){
    fa[u]=cfa,dep[u]=cdep,size[u]=1,son[u]=0;
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(v!=fa[u]){
        dfs1(v,u,cdep+1);size[u]+=size[v];
        if(size[v]>size[son[u]])son[u]=v;
      }
    }
  }
  void dfs2(int u,int anc){
    id[u]=++idtot,rnk[idtot]=u,top[u]=anc;
    if(son[u])dfs2(son[u],anc);
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(v!=fa[u]&&v!=son[u])dfs2(v,v);
    }
  }
public:
  void build(Tp *a,int u){
    dfs1(u,0,0);dfs2(u,u);
    auxi.tot=0;auxi.build(auxi.root,a,rnk,1,n);
  }
  void modify_path(int u,int v,Tp d){
    while(top[u]!=top[v]){
      if(dep[top[u]]<dep[top[v]])swap(u,v);
      auxi.modify(auxi.root,id[top[u]],id[u],d);
      u=fa[top[u]];
    }
    if(dep[u]>dep[v])swap(u,v);
    auxi.modify(auxi.root,id[u],id[v],d);
  }
  void modify_tree(int u,Tp d){
    auxi.modify(auxi.root,id[u],id[u]+size[u]-1,d);
  }
  Tp query_path(int u,int v){
    Tp sum=0;
    while(top[u]!=top[v]){
      if(dep[top[u]]<dep[top[v]])swap(u,v);
      sum+=auxi.query(auxi.root,id[top[u]],id[u]);
      u=fa[top[u]];
    }
    if(dep[u]>dep[v])swap(u,v);
    sum+=auxi.query(auxi.root,id[u],id[v]);
    return sum;
  }
  Tp query_tree(int u){
    return auxi.query(auxi.root,id[u],id[u]+size[u]-1);
  }
  int lca(int u,int v){
    while(top[u]!=top[v]){
      if(dep[top[u]]<dep[top[v]])swap(u,v);
      u=fa[top[u]];
    }
    if(dep[u]>dep[v])swap(u,v);
    return u;
  }
};
```

### 长链剖分

```cpp
const int maxn=1e5+5;

class path_decomposition{
public:
  struct edge{
    int to;edge *next;
    edge(int to=0,edge *next=0):to(to),next(next){}
  };
  int lg[maxn],n,edgtot;
  vector<int> up[maxn],down[maxn];
  edge edges[maxn],*now[maxn];
  int fa[maxn][25],son[maxn],dep[maxn],len[maxn],top[maxn];
  void init(int n){
    this->n=n,edgtot=0;
    memset(now,0,sizeof(now));
  }
  void insert(int u,int v){
    edges[++edgtot]=edge(v,now[u]),now[u]=edges+edgtot;
  }
private:
  void dfs1(int u){
    dep[u]=dep[fa[u][0]]+1;
    int k=ceil(log2(dep[u]));
    for(int i=1;i<=k;i++)fa[u][i]=fa[fa[u][i-1]][i-1];
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(v!=fa[u][0]){
        fa[v][0]=u;dfs1(v);
        if(len[v]>len[son[u]])son[u]=v;
      }
    }
    len[u]=len[son[u]]+1;
  }
  void dfs2(int u,int anc){
    if(u==(top[u]=anc)){
      int v1=u,v2=u;
      for(int i=1;i<=len[u];i++){
        up[u].push_back(v1);
        down[u].push_back(v2);
        v1=fa[v1][0],v2=son[v2];
      }
    }
    if(son[u])dfs2(son[u],anc);
    for(edge *e=now[u];e;e=e->next){
      int v=e->to;
      if(v!=fa[u][0]&&v!=son[u])dfs2(v,v);
    }
  }
public:
  void build(int u){
    lg[0]=lg[1]=0;
    for(int i=2;i<=n;i++)lg[i]=lg[i>>1]+1;
    dfs1(u);dfs2(u,u);
  }
  int findkth(int u,int k){
    if(!k)return u;
    u=fa[u][lg[k]],k-=(1<<lg[k])+dep[u]-dep[top[u]];
    return k>0?up[top[u]][k]:down[top[u]][-k];
  }
};
```
