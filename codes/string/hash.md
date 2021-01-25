---
layout: article
title: 哈希
permalink: /codes/string/hash
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 字符串哈希

### BKDR哈希

```cpp
typedef unsigned int _int;

_int bkdr_hash(char *s){
  _int ha=0,seed=131;
  while(*s)ha=ha*seed+(*s++);
  return ha&0x7ffff;
}
```

### 区间哈希

```cpp
typedef unsigned int _int;
const int maxn=1e5+5;

_int ha[maxn],pw[maxn],p=233;
void inithash(char *s){
  int len=strlen(s+1);pw[0]=1;
  for(int i=1;i<=len;i++)ha[i]=ha[i-1]*p+s[i];
  for(int i=1;i<=len;i++)pw[i]=pw[i-1]*p;
}
_int query(int l,int r){
  return ha[r]-ha[l-1]*pw[r-l+1];
}
```

---

## 哈希表

```cpp
const int maxn=1e5+5;
const int MOD=0x7ffff;

template<typename Tp>
class hash_table{
public:
  struct node{
    Tp val;node *next;
    node(Tp val=0,node *next=0):val(val),next(next){}
  };
  node nodes[maxn],*now[MOD];int tot;
  int hash(Tp t){return t%MOD;}
  bool insert(Tp t){
    int idx=hash(t);
    for(node *i=now[idx];i;i=i->next)if(i->val==t)return 0;
    nodes[++tot]=node(t,now[idx]),now[idx]=nodes+tot;
    return 1;
  }
};
```
