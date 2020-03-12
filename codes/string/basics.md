---
layout: article
title: 基本算法
permalink: /codes/string/basics
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 最小表示法

```cpp
int minr(char *s){
  int i=1,j=2,k=0;
  int len=strlen(s+1);
  while(i<=len&&j<=len&&k<len){
    int tmp=s[i+k]-s[j+k];
    if(!tmp){k++;continue;}
    if(tmp>0)i+=k+1;else j+=k+1;
    if(i==j)j++;k=0;
  }
  return min(i,j);
}
```

---

## Manacher算法

```cpp
void initstr(char *s){
  int len=strlen(s+1);
  for(int i=len;i>=1;i--)s[i*2]=s[i],s[i*2-1]='#';
  s[0]=s[len*2+1]='#',s[len*2+2]=0;
}
int manacher(char *s,int *r){
  int ans=-1,p=0;
  int len=strlen(s+1),maxlen=0;
  for(int i=1;i<len;i++){
    r[i]=i<maxlen?min(r[2*p-i],maxlen-i+1):1;
    while(s[i-r[i]]==s[i+r[i]])r[i]++;
    if(maxlen<i+r[i]-1)p=i,maxlen=i+r[i]-1;
    ans=max(ans,r[i]-1);
  }
  return ans;
}
```

---

## KMP算法

### KMP算法

```cpp
const int maxn=1e5+5;

int fail[maxn],tlen;
char t[maxn];
void initfail(){
  tlen=strlen(t+1);
  for(int i=2,j=0;i<=tlen;i++){
    while(j&&t[j+1]!=t[i])j=fail[j];
    if(t[j+1]==t[i])j++;fail[i]=j;
  }
}
int find(char *s,int *pos){
  int len=strlen(s+1),cnt=0;
  for(int i=1,j=0;i<=len;i++){
    while(j&&t[j+1]!=s[i])j=fail[j];
    if(t[j+1]==s[i])j++;
    if(j==tlen)pos[++cnt]=i-tlen+1,j=fail[j];
  }
}
```

### 扩展KMP算法

```cpp
const int maxn=1e5+5;

int fail[maxn],ext[maxn],tlen;
char t[maxn];
void initfail(){
  fail[1]=tlen=strlen(t+1);
  int p=0,maxlen=0;
  for(int i=2;i<=tlen;i++){
    if(i<=maxlen)fail[i]=min(fail[i-p+1],maxlen-i+1);
    while(i+fail[i]<=tlen&&t[i+fail[i]]==t[fail[i]+1])fail[i]++;
    if(maxlen<i+fail[i]-1)p=i,maxlen=i+fail[i]-1;
  }
}
void findext(char *s){
  int len=strlen(s+1),p=0,maxlen=0;
  for(int i=1;i<=len;i++){
    if(i<=maxlen)ext[i]=min(fail[i-p+1],maxlen-i+1);
    while(i+ext[i]<=len&&s[i+ext[i]]==t[ext[i]+1])ext[i]++;
    if(maxlen<i+ext[i]-1)p=i,maxlen=i+ext[i]-1;
  }
}
```

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

## 康托展开

```cpp
const int maxn=10+5;

int a[maxn],fact[maxn];
bool vis[maxn];
int cantor(int n){
  int cnt=(a[1]-1)*fact[n-1];
  for(int i=2;i<=n;i++){
    int tmp=0;
    for(int j=1;j<i;j++)tmp+=a[j]<a[i];
    cnt+=(a[i]-tmp-1)*fact[n-i];
  }
  return cnt+1;
}
void decantor(int n,int k){
  memset(vis,0,sizeof(vis));k--;
  for(int i=1;i<=n;i++){
    int j,t=k/fact[n-i];
    for(j=1;j<=n;j++)if(!vis[j]&&!t--)break;
    a[i]=j,vis[j]=1,k%=fact[n-i];
  }
}
```