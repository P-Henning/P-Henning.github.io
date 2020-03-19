---
layout: article
title: 平衡树
permalink: /codes/data-struct/balance
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 替罪羊树

```cpp
const int maxn=1e5+5;

template<typename Tp>
class scapegoat_tree{
public:
  static const double alpha=0.75;
  struct node{
    node *son[2];
    int cnt,size;Tp val;bool exi;
    void init(Tp t){
      son[0]=son[1]=0,cnt=size=1,val=t,exi=1;
    }
    void update(){
      cnt=exi,size=1;
      if(son[0])cnt+=son[0]->cnt,size+=son[0]->size;
      if(son[1])cnt+=son[1]->cnt,size+=son[1]->size;
    }
    bool balance(){
      if(!son[0]&&!son[1])return 1;
      if(!son[0]||!son[1])return 0;
      return son[0]->size<=size*alpha&&son[1]->size<=size*alpha;
    }
  };
  int tot,poltop,seqtot;
  node nodes[maxn],*pool[maxn],*root,**tmproot;
  Tp seq[maxn];
  void init(){root=nodes,tmproot=&root,tot=poltop=seqtot=0;}
private:
  node *newnode(Tp t){
    node *p=poltop?pool[poltop--]:nodes+(++tot);
    p->init(t);return p;
  }
  void flatten(node *&p){
    if(p->son[0])flatten(p->son[0]);
    pool[++poltop]=p;
    if(p->exi)seq[++seqtot]=p->val;
    if(p->son[1])flatten(p->son[1]);
  }
  void build(node *&p,int l,int r){
    int mid=l+r>>1;
    p=newnode(seq[mid]);
    if(l<mid)build(p->son[0],l,mid-1);
    if(mid<r)build(p->son[1],mid+1,r);
    p->update();
  }
  void rebuild(){
    if(!root->balance()||*tmproot!=root){
      seqtot=0;flatten(*tmproot);
      if(seqtot)build(*tmproot,1,seqtot);
      tmproot=&root;
    }
  }
  int rank(node *&p,Tp t){
    if(!p)return 0;
    if(p->val>=t)return rank(p->son[0],t);
    int lcnt=p->son[0]?p->son[0]->cnt:0;
    return rank(p->son[1],t)+lcnt+p->exi;
  }
  void insert(node *&p,Tp t){
    if(!p){p=newnode(t);return;}
    p->cnt++,p->size++;
    insert(p->son[p->val<t],t);
    if(!p->balance())tmproot=&p;
  }
  void remove(node *&p,int rnk){
    int lcnt=p->son[0]?p->son[0]->cnt:0;p->cnt--;
    if(p->exi&&rnk==lcnt+1){p->exi=0;return;}
    if(rnk<=lcnt+p->exi)remove(p->son[0],rnk);
    else remove(p->son[1],rnk-lcnt-p->exi);
  }
  Tp findkth(node *&p,int k){
    if(!p)return 0;
    int lcnt=p->son[0]?p->son[0]->cnt:0;
    if(p->exi&&lcnt+1==k)return p->val;
    if(lcnt>=k)return findkth(p->son[0],k);
    return findkth(p->son[1],k-lcnt-p->exi);
  }
public:
  void build(Tp *a,int n){
    for(int i=1;i<=n;i++)seq[i]=a[i];
    sort(seq+1,seq+1+n);
    build(root,1,seqtot=n);
  }
  int rank(Tp t){return rank(root,t)+1;}
  void insert(Tp t){insert(root,t);rebuild();}
  void remove(Tp t){remove(root,rank(t));}
  Tp findkth(int k){return findkth(root,k);}
  Tp findpre(Tp t){return findkth(rank(t)-1);}
  Tp findsuc(Tp t){return findkth(rank(t+1));}
};
```

---

## 伸展树

### 权值伸展树

```cpp
const int maxn=1e5+5;

template<typename Tp>
class splay_tree{
public:
  struct node{
    node *fa,*son[2];
    int size,cnt;Tp val;
    void init(Tp t){
      son[0]=son[1]=fa=0,size=cnt=1,val=t;
    }
    int ident(){return !fa?0:fa->son[1]==this;}
    void update(){
      size=cnt;
      if(son[0])size+=son[0]->size;
      if(son[1])size+=son[1]->size;
    }
  };
  node nodes[maxn],*root;int tot;
  void init(){root=nodes,root->size=0,tot=0;}
private:
  node *newnode(Tp t){
    node *p=nodes+(++tot);
    p->init(t);return p;
  }
  void connect(node *son,node *fa,int r){
    if(son)son->fa=fa;
    if(fa)fa->son[r]=son;
    else root=son;
  }
  void rotate(node *p){
    node *fp=p->fa,*ffp=fp->fa;
    int r=p->ident(),fr=fp->ident();
    connect(p,ffp,fr);
    connect(p->son[r^1],fp,r);
    connect(fp,p,r^1);
    fp->update();
  }
  void splay(node *p,node *to){
    while(p->fa!=to){
      node *fp=p->fa,*ffp=fp->fa;
      if(ffp!=to)rotate(p->ident()^fp->ident()?p:fp);
      rotate(p);
    }
    p->update();
  }
  void find(node *&p,Tp t){
    if(!p)return;
    if(p->val==t){splay(p,0);return;}
    find(p->son[p->val<t],t);
  }
  void insert(node *&p,Tp t){
    if(p->val==t){p->cnt++;splay(p,0);return;}
    node *&sp=p->son[p->val<t];
    if(!sp){sp=newnode(t),sp->fa=p;splay(sp,0);}
    else insert(sp,t);
  }
  Tp findkth(node *&p,int k){
    if(!p)return 0;
    int lcnt=p->son[0]?p->son[0]->size:0;
    if(lcnt+p->cnt>=k&&lcnt<k)return p->val;
    if(lcnt>=k)return findkth(p->son[0],k);
    return findkth(p->son[1],k-lcnt-p->cnt);
  }
public:
  void insert(Tp t){
    if(!root||!root->size){root=newnode(t);return;}
    insert(root,t);
  }
  void remove(Tp t){
    find(root,t);
    if(root->val!=t)return;
    node *k=root;
    if(k->cnt>1){k->cnt--;return;}
    if(!k->son[0]&&(root=k->son[1]))root->fa=0;
    if(k->son[0]){
      node *p=k->son[0];
      while(p->son[1])p=p->son[1];
      splay(p,k);
      root=p,root->fa=0;
      connect(k->son[1],root,1);
    }
  }
  int rank(Tp t){
    find(root,t);
    return !root->son[0]?1:root->son[0]->size+1;
  }
  Tp findkth(int k){return findkth(root,k);}
  Tp findpre(Tp t){
    insert(t);
    node *p=root->son[0];
    while(p->son[1])p=p->son[1];
    remove(t);return p->val;
  }
  Tp findsuc(Tp t){
    insert(t);
    node *p=root->son[1];
    while(p->son[0])p=p->son[0];
    remove(t);return p->val;
  }
};
```

### 区间伸展树

```cpp
const int maxn=1e5+5;
const int INF=0x7fffffff;

template<typename Tp>
class splay_tree{
#define TAGNONE INF
public:
  struct node{
    node *fa,*son[2];
    int size,rev;Tp val,sum,tag;
    void init(Tp t){
      son[0]=son[1]=fa=0;
      size=1,val=sum=t,rev=0,tag=TAGNONE;
    }
    int ident(){return fa?fa->son[1]==this:0;}
    void set(Tp t){val=tag=t,sum=t*size;}
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
      if(tag!=TAGNONE){
        if(son[0])son[0]->set(tag);
        if(son[1])son[1]->set(tag);
        tag=TAGNONE;
      }
    }
  };
  int tot,poltop,seqtot;
  node nodes[maxn],*pool[maxn],*root;
  Tp seq[maxn];
  void init(){root=0,tot=poltop=seqtot=0;}
private:
  node *newnode(Tp t){
    node *p=poltop?pool[poltop--]:nodes+(++tot);
    p->init(t);return p;
  }
  void delnode(node *p){
    if(!p)return;
    delnode(p->son[0]);
    delnode(p->son[1]);
    p->fa->son[p->ident()]=0,pool[++poltop]=p;
  }
  void build(node *&p,int l,int r){
    int mid=l+r>>1;
    p=newnode(seq[mid]);
    if(l<mid){build(p->son[0],l,mid-1);p->son[0]->fa=p;}
    if(mid<r){build(p->son[1],mid+1,r);p->son[1]->fa=p;}
    p->update();
  }
  void connect(node *son,node *fa,int r){
    if(son)son->fa=fa;
    if(fa)fa->son[r]=son;
    else root=son;
  }
  void rotate(node *p){
    node *fp=p->fa,*ffp=fp->fa;
    p->pushdown();fp->pushdown();
    int r=p->ident(),fr=fp->ident();
    connect(p,ffp,fr);
    connect(p->son[r^1],fp,r);
    connect(fp,p,r^1);
    fp->update();p->update();
  }
  void splay(node *p,node *to){
    while(p->fa!=to){
      node *fp=p->fa,*ffp=fp->fa;
      if(ffp!=to)rotate(p->ident()^fp->ident()?p:fp);
      rotate(p);
    }
    if(!to)root=p;
  }
  node *findkth(node *&p,int k){
    if(!p)return 0;
    p->pushdown();
    int lcnt=p->son[0]?p->son[0]->size:0;
    if(lcnt+1>=k&&lcnt<k)return p;
    if(lcnt>=k)return findkth(p->son[0],k);
    return findkth(p->son[1],k-lcnt-1);
  }
public:
  void build(Tp *a,int n){
    seq[1]=-INF,seq[seqtot=n+2]=-INF;
    for(int i=1;i<=n;i++)seq[i+1]=a[i];
    build(root,1,seqtot);
  }
  void insert(int k,Tp *a,int n){
    seqtot=n;
    for(int i=1;i<=n;i++)seq[i]=a[i];
    splay(findkth(root,k+1),0);
    splay(findkth(root,k+2),root);
    build(root->son[1]->son[0],1,seqtot);
    root->son[1]->son[0]->fa=root->son[1];
    root->son[1]->update();
    root->update();
  }
  void remove(int l,int r){
    splay(findkth(root,l),0);
    splay(findkth(root,r+2),root);
    delnode(root->son[1]->son[0]);
    root->son[1]->update();
    root->update();
  }
  void modify(int l,int r,Tp d){
    splay(findkth(root,l),0);
    splay(findkth(root,r+2),root);
    root->son[1]->son[0]->set(d);
    root->son[1]->update();
    root->update();
  }
  void reverse(int l,int r){
    splay(findkth(root,l),0);
    splay(findkth(root,r+2),root);
    root->son[1]->son[0]->swapson();
    root->son[1]->update();
    root->update();
  }
  Tp query(int l,int r){
    splay(findkth(root,l),0);
    splay(findkth(root,r+2),root);
    return root->son[1]->son[0]->sum;
  }
#undef TAGNONE
};
```

---

## K-D树

```c++
const int maxn=1e5+5;
const int INF=0x7fffff;

template<typename Tp>
class kd_tree{
public:
  static const double alpha=0.7;
  struct point{
    Tp x[2];int *d;
    point(Tp x=0,Tp y=0){this->x[0]=x,this->x[1]=y;}
    bool operator<(point rhs)const{
      if(x[*d]==rhs.x[*d])return x[*d^1]<rhs.x[*d^1];
      return x[*d]<rhs.x[*d];
    }
    friend Tp dist(point a,point b){
      return abs(a.x[0]-b.x[0])+abs(a.x[1]-b.x[1]);
    }
  };
  struct node{
    node *son[2];
    int size;point val;Tp minv[2],maxv[2];
    void init(point t){
      son[0]=son[1]=0,size=1,val=t;
      for(int i=0;i<2;i++)minv[i]=maxv[i]=val.x[i];
    }
    void update(){
      for(int i=0;i<2;i++){
        minv[i]=maxv[i]=val.x[i];
        for(int r=0;r<2;r++)if(son[r]){
          minv[i]=min(minv[i],son[r]->minv[i]);
          maxv[i]=max(maxv[i],son[r]->maxv[i]);
        }
      }
      size=1;
      if(son[0])size+=son[0]->size;
      if(son[1])size+=son[1]->size;
    }
    bool balance(){
      if(!son[0]&&!son[1])return 1;
      if(!son[0]||!son[1])return 0;
      return son[0]->size<=size*alpha&&son[1]->size<=size*alpha;
    }
  };
  node nodes[maxn],*pool[maxn],*root,**tmproot;
  int tot,d,tmpd,poltop,seqtot;
  point seq[maxn];
  void init(){root=nodes,tmproot=&root,tot=poltop=seqtot=0,d=tmpd=0;}
private:
  node *newnode(point t){
    node *p=poltop?pool[poltop--]:nodes+(++tot);
    p->init(t);return p;
  }
  void flatten(node *&p){
    if(p->son[0])flatten(p->son[0]);
    pool[++poltop]=p,seq[++seqtot]=p->val;
    if(p->son[1])flatten(p->son[1]);
  }
  void build(node *&p,int l,int r,int cd){
    int mid=l+r>>1;d=cd;
    nth_element(seq+l,seq+mid,seq+1+r);
    p=newnode(seq[mid]);
    if(l<mid)build(p->son[0],l,mid-1,cd^1);
    if(mid<r)build(p->son[1],mid+1,r,cd^1);
    p->update();
  }
  void rebuild(){
    if(!root->balance()||*tmproot!=root){
      seqtot=0;flatten(*tmproot);
      if(seqtot)build(*tmproot,1,seqtot,tmpd);
      tmproot=&root;
    }
  }
  void insert(node *&p,point t,int cd){
    if(!p){p=newnode(t);return;}
    d=cd;insert(p->son[p->val<t],t,cd^1);
    p->update();
    if(!p->balance())tmproot=&p,tmpd=cd;
  }
  Tp maxdist(node *&p,point t){
    if(!p)return -INF;
    Tp ans=0;
    for(int i=0;i<2;i++)
      ans+=max(abs(t.x[i]-p->maxv[i]),abs(t.x[i]-p->minv[i]));
    return ans;
  }
  Tp mindist(node *&p,point t){
    if(!p)return INF;
    Tp ans=0;
    for(int i=0;i<2;i++){
      ans+=max(t.x[i]-p->maxv[i],(Tp)0);
      ans+=max(p->minv[i]-t.x[i],(Tp)0);
    }
    return ans;
  }
  Tp findmax(node *&p,point t){
    Tp ans=dist(t,p->val);
    Tp ans0=maxdist(p->son[0],t);
    Tp ans1=maxdist(p->son[1],t);
    if(ans0>ans1){
      if(ans0>ans)ans=max(ans,findmax(p->son[0],t));
      if(ans1>ans)ans=max(ans,findmax(p->son[1],t));
    }
    else{
      if(ans1>ans)ans=max(ans,findmax(p->son[1],t));
      if(ans0>ans)ans=max(ans,findmax(p->son[0],t));
    }
    return ans;
  }
  Tp findmin(node *&p,point t){
    Tp ans=dist(t,p->val);
    Tp ans0=mindist(p->son[0],t);
    Tp ans1=mindist(p->son[1],t);
    if(ans0<ans1){
      if(ans0<ans)ans=min(ans,findmin(p->son[0],t));
      if(ans1<ans)ans=min(ans,findmin(p->son[1],t));
    }
    else{
      if(ans1<ans)ans=min(ans,findmin(p->son[1],t));
      if(ans0<ans)ans=min(ans,findmin(p->son[0],t));
    }
    return ans;
  }
  priority_queue<Tp,vector<Tp>,greater<Tp> >q;
  void find(node *&p,point t){
    Tp ans=dist(t,p->val);
    Tp ans0=maxdist(p->son[0],t);
    Tp ans1=maxdist(p->son[1],t);
    if(ans>q.top()){q.pop();q.push(ans);}
    if(ans0>ans1){
      if(ans0>q.top())find(p->son[0],t);
      if(ans1>q.top())find(p->son[1],t);
    }
    else{
      if(ans1>q.top())find(p->son[1],t);
      if(ans0>q.top())find(p->son[0],t);
    }
  }
public:
  void build(point *a,int n){
    for(int i=1;i<=seqtot;i++)seq[i]=a[i],seq[i].d=&d;
    build(root,1,seqtot=n,0);
  }
  void insert(point t){t.d=&d;insert(root,t,0);rebuild();}
  Tp findmax(point t){return findmax(root,t);}
  Tp findmin(point t){return findmin(root,t);}
  Tp findkth(point t,int k){
    while(!q.empty())q.pop();
    for(int i=1;i<=k;i++)q.push(-INF);
    t.d=&d;find(root,t);
    return q.top();
  }
};
```
