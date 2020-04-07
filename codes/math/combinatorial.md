---
layout: article
title: 组合数学
permalink: /codes/math/combinatorial
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 排列组合

### 组合数

```cpp
int C(int a,int b){
  if(a<b)return 0;
  if(a==b)return 1;
  if(b>a-b)b=a-b;
  int A=1,B=1;
  for(int i=0;i<b;i++)A*=a-i,B*=b-i;
  return A/B;
}
```

```cpp
const int maxn=1000+5;

int C[maxn][maxn];
void initC(int n){
  for(int i=0;i<=n;i++)C[i][0]=1;
  for(int i=1;i<=n;i++)
    for(int j=1;j<=i;j++)
      C[i][j]=C[i-1][j]+C[i-1][j-1];
}
void initC_n(int n){
  C[n][0]=1;
  for(int i=1;i<=n;i++)C[n][i]=C[n][i-1]*(n-i+1)/i;
}
```

### 斯特林数

```cpp
const int maxn=1000+5;

int s2[maxn][maxn],s1[maxn][maxn];
void inits2(int n){
  s2[0][0]=1;
  for(int i=1;i<=n;i++)
    for(int j=1;j<=i;j++)
      s2[i][j]=s2[i-1][j-1]+j*s2[i-1][j];
}
void inits1(int n){
  s1[0][0]=1;
  for(int i=1;i<=n;i++)
    for(int j=1;j<=i;j++)
      s1[i][j]=s1[i-1][j-1]+(i-1)*s1[i-1][j];
}
```

### 卡特兰数

```cpp
const int maxn=1e5+5;

int Cn[maxn];
void initCn(int n){
  Cn[1]=1;
  for(int i=2;i<=n;i++)Cn[i]=Cn[i-1]*(4*i-2)/(i+1);
}
```

---

## Lucas定理

```cpp
int powmod(int a,int b,int c){
  int ans=1;
  for(a%=c;b;b>>=1,a=a*a%c)if(b&1)ans=ans*a%c;
  return ans;
}
int C(int a,int b,int p){
  if(a<b)return 0;
  if(a==b)return 1;
  if(b>a-b)b=a-b;
  int A=1,B=1;
  for(int i=0;i<b;i++)A=A*(a-i)%p,B=B*(b-i)%p;
  return A*powmod(B,p-2,p)%p;
}
int lucas(int n,int m,int p){
  return m?C(n%p,m%p,p)*lucas(n/p,m/p,p)%p:1;
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
