---
layout: article
title: 其它
permalink: /codes/maths/others
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 快速幂与快速乘

### Montgomery算法

```cpp
int powmod(int a,int b,int c){
  int ans=1;
  for(a%=c;b;b>>=1,a=a*a%c)if(b&1)ans=ans*a%c;
  return ans;
}
int mulmod(int a,int b,int c){
  int ans=0;
  for(a%=c;b;b>>=1,a=(a<<1)%c)if(b&1)ans=(ans+a)%c;
  return ans;
}
```

### $O(1)$快速乘

```cpp
typedef long long LL;

LL mulmod(LL a,LL b,LL c){
  return (a*b-(LL)((long double)a/c*b)*c+c)%c;
}
```

### 光速幂

```cpp
const int maxn=1e5+5;

template<typename Tp>
class lightspeed_pow{
public:
  Tp pw[maxn],pw2[maxn],mod;int s;
  void initpow(Tp a,Tp b,Tp c){
    pw[0]=pw2[0]=1,s=sqrt(b)+1,mod=c;
    for(int i=1;i<=s;i++)pw[i]=pw[i-1]*a%c;
    for(int i=1;i<=s;i++)pw2[i]=pw2[i-1]*pw[s]%c;
  }
  Tp pow(Tp b){
    return b<=s?pw[b]:pw2[b/s]*pw[b-b/s*s]%mod;
  }
};
```

---

## 矩阵乘法与矩阵快速幂

```cpp
const int maxn=100+5;

template<typename Tp>
struct matrix{
  int n;
  Tp a[maxn][maxn];
  void init(int n){
    this->n=n;
    memset(a,0,sizeof(a));
  }
  Tp *operator[](int x){return a[x];}
  matrix<Tp> operator*(matrix<Tp> b){
    matrix<Tp> c;c.init(n);
    for(int i=1;i<=n;i++)
      for(int j=1;j<=n;j++)
        for(int k=1;k<=n;k++)
          c[i][j]+=a[i][k]*b[k][j];
    return c;
  }
  matrix<Tp> operator^(Tp b){
    matrix<Tp> ans,e=*this;ans.init(n);
    for(int i=1;i<=n;i++)ans[i][i]=1;
    for(;b;b>>=1,e=e*e)if(b&1)ans=ans*e;
    return ans;
  }
};
```