---
layout: article
title: 自动机
permalink: /codes/string/automaton
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 字典树

### 普通字典树

```cpp
const int maxn=1e5+5,maxc=26;

class trie_tree{
public:
  struct node{
    node *son[maxc];int cnt;
    void init(){
      memset(son,0,sizeof(son));cnt=0;
    }
  };
  node nodes[maxn],*root;int tot,h;
  void init(int h){root=nodes,tot=0,this->h=h;}
private:
  node *newnode(){
    node *p=nodes+(++tot);p->init();return p;
  }
public:
  void insert(char *s){
    node *p=root;
    for(;*s;p=p->son[*s++-'a'])
      if(!p->son[*s-'a'])p->son[*s-'a']=newnode();
    p->cnt++;
  }
  void remove(char *s){
    node *p=root;
    for(;*s;p=p->son[*s++-'a']);
    p->cnt--;
  }
  int findcnt(char *s){
    node *p=root;
    for(;*s;p=p->son[*s++-'a'])
      if(!p->son[*s-'a'])return 0;
    return p->cnt;
  }
};
```

### 01字典树

```cpp
const int maxn=1e5+5;

template<typename Tp>
class trie_tree{
public:
  struct node{
    node *son[2];int cnt;
    void init(){son[0]=son[1]=0,cnt=0;}
  };
  node nodes[maxn],*root;int tot,h;
  void init(int h){root=nodes,tot=0,this->h=h;}
private:
  node *newnode(){
    node *p=nodes+(++tot);p->init();return p;
  }
public:
  void insert(Tp t){
    node *p=root;p->cnt++;
    for(int i=h;i>=0;i--){
      int r=t>>i&1;
      if(!p->son[r])p->son[r]=newnode();
      p=p->son[r],p->cnt++;
    }
  }
  void remove(Tp t){
    node *p=root;p->cnt--;
    for(int i=h;i>=0;i--){
      int r=t>>i&1;p=p->son[r],p->cnt--;
    }
  }
  int rank(Tp t) {
    node *p=root;int cnt=0;
    for(int i=h;i>=0;i--){
      int r=t>>i&1;
      if(r&&p->son[0])cnt+=p->son[0]->cnt;
      p=p->son[r];
    }
    return cnt;
  }
  Tp findkth(int k){
    node *p=root;Tp ans=0;
    for(int i=h;i>=0;i--){
      int lcnt=!p->son[0]?0:p->son[0]->cnt;
      if(k>lcnt)ans|=1<<i,k-=lcnt,p=p->son[1];
      else p=p->son[0];
    }
    return ans;
  }
  Tp findmax_xor(Tp t){
    node *p=root;Tp ans=0;
    for(int i=h;i>=0;i--){
      int r=t>>i&1;
      if(p->son[r^1]&&p->son[r^1]->cnt)
        p=p->son[r^1],ans|=1<<i;
      else p=p->son[r];
    }
    return ans;
  }
};
```

### 可持久化01字典树

```cpp
const int maxn=1e5+5;

template<typename Tp>
class trie_tree{
public:
  struct node{
    node *son[2];int cnt;
    void init(){son[0]=son[1]=0,cnt=0;}
  };
  node nodes[maxn],*root[maxn];int tot,h;
  void init(int h){root=nodes,tot=0,this->h=h;}
private:
  node *newnode(){
    node *p=nodes+(++tot);p->init();return p;
  }
public:
  void insert(node *prev,node *&now,Tp t){
    now=newnode();node *p=now;
    if(prev)*p=*prev;p->cnt++;
    for(int i=h;i>=0;i--){
      int r=t>>i&1;p->son[r]=newnode();
      prev=!prev?0:prev->son[r],p=p->son[r];
      if(prev)*p=*prev;p->cnt++;
    }
  }
  Tp findmax_xor(node *p,node *q,Tp t){
    Tp ans=0;
    for(int i=h;i>=0;i--){
      int r=t>>i&1;
      int pcnt=!p||!p->son[r^1]?0:p->son[r^1]->cnt;
      int qcnt=!q||!q->son[r^1]?0:q->son[r^1]->cnt;
      if(qcnt-pcnt)p=!p?0:p->son[r^1],q=!q?0:q->son[r^1],ans|=1<<i;
      else p=!p?0:p->son[r],q=!q?0:q->son[r];
    }
    return ans;
  }
};
```

---

## 后缀自动机

```cpp
const int maxn=1e5+5,maxc=26;

class suffix_automaton{
public:
  struct node{
    node *to[maxc],*fa;int len;
    void init(int l){
      memset(to,0,sizeof(to));fa=0,len=l;
    }
  };
  node nodes[maxn],*root,*now;int tot;
  void init(){now=root=nodes+1,tot=1;now->init(0);}
private:
  node *newnode(int l){
    node *p=nodes+(++tot);p->init(l);return p;
  }
public:
  void insert(int t){
    node *p=now,*q,*np=newnode(p->len+1),*nq;now=np;
    for(;p&&!p->to[t];p=p->fa)p->to[t]=np;
    if(!p){np->fa=root;return;}
    q=p->to[t];
    if(q->len==p->len+1){np->fa=q;return;}
    nq=newnode(p->len+1);
    memcpy(nq->to,q->to,sizeof(q->to));
    nq->fa=q->fa,q->fa=np->fa=nq;
    for(;p&&p->to[t]==q;p=p->fa)p->to[t]=nq;
  }
#define NODE(i) (nodes+(i))
  int id[maxn],c[maxn];
  void toposort(){
    memset(c,0,sizeof(c));
    for(int i=1;i<=tot;i++)c[NODE(i)->len]++;
    for(int i=1;i<=tot;i++)c[i]+=c[i-1];
    for(int i=1;i<=tot;i++)id[c[NODE(i)->len]--]=i;
  }
#undef NODE(i)
};
```
