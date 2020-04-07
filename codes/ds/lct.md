---
layout: article
title: 动态树
permalink: /codes/ds/lct
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## Link-Cut树

```cpp
const int maxn=1e5+5;

template<typename Tp>
class linkcut_tree{
public:
  struct node{
    node *fa,*son[2];
    int size,rev;Tp val,sum;
    void init(Tp t){
      fa=son[0]=son[1]=0,size=1,rev=0,val=sum=t;
    }
    bool isroot(){
      if(!fa)return 1;
      return fa->son[0]!=this&&fa->son[1]!=this;
    }
    int ident(){return fa?fa->son[1]==this:0;}
    void swapson(){rev^=1;swap(son[0],son[1]);}
    void update(){
      size=1,sum=val;
      if(son[0])size+=son[0]->size,sum+=son[0]->sum;
      if(son[1])size+=son[1]->size,sum+=son[1]->sum;
    }
    void pushdown(){
      if(rev){
        if(son[0])son[0]->swapson();
        if(son[1])son[1]->swapson();
        rev=0;
      }
    }
  };
  node nodes[maxn];
private:
  void connect(node *son,node *fa,int r){
    if(son)son->fa=fa;
    if(fa)fa->son[r]=son;
  }
  void rotate(node *p){
    node *fp=p->fa,*ffp=fp->fa;
    int r=p->ident(),fr=fp->ident();
    p->fa=ffp;
    if(!fp->isroot())connect(p,ffp,fr);
    connect(p->son[r^1],fp,r);
    connect(fp,p,r^1);
    fp->update();p->update();
  }
  void push(node *p){
    if(!p->isroot())push(p->fa);
    p->pushdown();
  }
  void splay(node *p){
    push(p);
    while(!p->isroot()){
      node *fp=p->fa;
      if(!fp->isroot())rotate(p->ident()^fp->ident()?p:fp);
      rotate(p);
    }
    p->update();
  }
  void access(node *p){
    for(node *sp=0;p;sp=p,p=p->fa){
      splay(p);p->son[1]=sp;p->update();
    }
  }
public:
  void makeroot(node *p){access(p);splay(p);p->swapson();}
  node *findroot(node *p){
    access(p);splay(p);
    while(p->son[0]){p->pushdown();p=p->son[0];}
    splay(p);return p;
  }
  void link(node *p,node *q){
    makeroot(p);
    if(findroot(q)!=p)p->fa=q;
  }
  void cut(node *p,node *q){
    makeroot(p);
    if(findroot(q)==p&&q->fa==p&&!q->son[0]){
      q->fa=p->son[1]=0;p->update();
    }
  }
  void modify(node *p,Tp d){splay(p);p->val=d;}
  Tp query(node *p,node *q){
    makeroot(p);access(q);splay(q);
    return q->sum;
  }
};
```
