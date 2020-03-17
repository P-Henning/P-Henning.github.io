---
layout: article
title: 线段树相关
permalink: /codes/data-struct/segment
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 线段树

### 单点修改+区间查询

```cpp
const int maxn=1e5+5;

template<typename Tp>
class segment_tree{
public:
  struct node{
    node *son[2];
    int l,r;Tp sum;
    void init(int l,int r){
      this->l=l,this->r=r,son[0]=son[1]=0,sum=0;
    }
    void update(){sum=son[0]->sum+son[1]->sum;}
  };
  node nodes[maxn],*root;int tot;
private:
  void build(node *&p,Tp *a,int l,int r){
    p=nodes+(++tot);p->init(l,r);
    if(l==r){p->sum=a[l];return;}
    build(p->son[0],a,l,l+r>>1);
    build(p->son[1],a,(l+r>>1)+1,r);
    p->update();
  }
  void modify(node *&p,int x,Tp d){
    if(!p||p->l>x||p->r<x)return;
    if(p->l==p->r){p->sum+=d;return;}
    modify(p->son[0],x,d);
    modify(p->son[1],x,d);
    p->update();
  }
  Tp query(node *&p,int l,int r){
    if(!p||p->l>r||p->r<l)return 0;
    if(l<=p->l&&p->r<=r)return p->sum;
    return query(p->son[0],l,r)+query(p->son[1],l,r);
  }
public:
  void build(Tp *a,int n){tot=0;build(root,a,1,n);}
  void modify(int x,Tp d){modify(root,x,d);}
  Tp query(int l,int r){return query(root,l,r);}
};
```

### 区间修改+区间查询

```cpp
const int maxn=1e5+5;

int a[maxn];

template<typename Tp>
class segment_tree{
public:
  struct node{
    node *son[2];
    int l,r;Tp sum,tag;
    void init(int l,int r){
      this->l=l,this->r=r,son[0]=son[1]=0,sum=tag=0;
    }
    void add(Tp t){sum+=(r-l+1)*t,tag+=t;}
    void update(){sum=son[0]->sum+son[1]->sum;}
    void pushdown(){
      if(tag){
        son[0]->add(tag);son[1]->add(tag);tag=0;
      }
    }
  };
  node nodes[maxn],*root;int tot;
private:
  void build(node *&p,Tp *a,int l,int r){
    p=nodes+(++tot);p->init();
    if(l==r){p->sum=a[l];return;}
    build(p->son[0],a,l,l+r>>1);
    build(p->son[1],a,(l+r>>1)+1,r);
    p->update();
  }
  void modify(node *&p,int l,int r,Tp d){
    if(!p||p->l>r||p->r<l)return;
    if(l<=p->l&&p->r<=r){p->add(d);return;}
    p->pushdown();
    modify(p->son[0],l,r,d);
    modify(p->son[1],l,r,d);
    p->update();
  }
  Tp query(node *&p,int l,int r){
    if(!p||p->l>r||p->r<l)return 0;
    if(l<=p->l&&p->r<=r)return p->sum;
    p->pushdown();
    return query(p->son[0],l,r)+query(p->son[1],l,r);
  }
public:
  void build(Tp *a,int n){tot=0;build(root,a,1,n);}
  void modify(int l,int r,Tp d){modify(root,l,r,d);}
  Tp query(int l,int r){return query(root,l,r);}
};
```

### 动态开点与线段树合并

```cpp
const int maxn=1e5+5;

template<typename Tp>
class segment_tree{
public:
  struct node{
    node *son[2];Tp sum;
    void init(){son[0]=son[1]=0,sum=0;}
    void update(){
      sum=0;
      if(son[0])sum+=son[0]->sum;
      if(son[1])sum+=son[1]->sum;
    }
  };
  node nodes[maxn],*root[maxn];int n,tot;
  void init(int n){this->n=n,tot=0;}
private:
  void modify(node *&p,int pl,int pr,int x,Tp d){
    if(x<pl||x>pr)return;
    if(!p){p=nodes+(++tot);p->init();}
    if(pl==pr){p->sum+=d;return;}
    modify(p->son[0],pl,pl+pr>>1,x,d);
    modify(p->son[1],(pl+pr>>1)+1,pr,x,d);
    p->update();
  }
  Tp query(node *&p,int pl,int pr,int l,int r){
    if(!p||l>pr||r<pl)return 0;
    if(l<=pl&&pr<=r)return p->sum;
    return query(p->son[0],pl,pl+pr>>1,l,r)+query(p->son[1],(pl+pr>>1)+1,pr,l,r);
  }
  node *merge(node *&p,node *&q,int l,int r){
    if(!p||!q)return p?p:q;
    if(l==r){p->sum+=q->sum;return p;}
    p->son[0]=merge(p->son[0],q->son[0],l,l+r>>1);
    p->son[1]=merge(p->son[1],q->son[1],(l+r>>1)+1,r);
    p->update();return p;
  }
public:
  void modify(node *&p,int x,Tp d){modify(p,1,n,x,d);}
  Tp query(node *&p,int l,int r){return query(p,1,n,l,r);}
  node *merge(node *&p,node *&q){return merge(p,q,1,n);}
};
```

### 可持久化线段树

```cpp
const int maxn=1e5+5;

template<typename Tp>
class segment_tree{
public:
  struct node{
    node *son[2];Tp sum;
    void init(){son[0]=son[1]=0,sum=0;}
    void update(){sum=son[0]->sum+son[1]->sum;}
  };
  node nodes[maxn],*root[maxn];int n,tot;
private:
  void build(node *&p,int l,int r){
    p=nodes+(++tot),p->init();
    if(l==r)return;
    build(p->son[0],l,l+r>>1);
    build(p->son[1],(l+r>>1)+1,r);
  }
  void modify(node *prev,node *&p,int l,int r,int k,Tp d){
    p=nodes+(++tot),*p=*prev,p->sum+=d;
    if(l==r)return;
    if(k<=(l+r>>1))modify(prev->son[0],p->son[0],l,l+r>>1,k,d);
    else modify(prev->son[1],p->son[1],(l+r>>1)+1,r,k,d);
    p->update();
  }
  Tp query(node *&p,node *&q,int l,int r,int k){
    if(l>=r)return l;
    int cnt=q->son[0]->sum-p->son[0]->sum;
    if(cnt>=k)return query(p->son[0],q->son[0],l,l+r>>1,k);
    return query(p->son[1],q->son[1],(l+r>>1)+1,r,k-cnt);
  }
public:
  void init(int n){this->n=n,tot=0;build(root[0],1,n);}
  void modify(node *prev,node *&p,int k,Tp d){modify(prev,p,1,n,k,d);}
  Tp query(node *&p,node *&q,int k){return query(p,q,1,n,k);}
};
```

---

## 树链剖分

### 重链剖分

```cpp
const int maxn=1e5+5;

template<typename Tp>
class decomposition{
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
      p=nodes+(++tot);p->init();
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
