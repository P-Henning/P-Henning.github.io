---
layout: article
title: 数论
permalink: /codes/math/number
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 欧几里得算法

### 欧几里得算法

```cpp
int gcd(int a,int b){
  return b?gcd(b,a%b):a;
}
```

### 扩展欧几里得算法

```cpp
int ext_gcd(int a,int b,int &x,int &y){
  if(!b){x=1,y=0;return a;}
  int d=ext_gcd(b,a%b,y,x);
  y-=a/b*x;return d;
}
```

---

## 中国剩余定理

### 中国剩余定理

```cpp
int ext_gcd(int a,int b,int &x,int &y){
  if(!b){x=1,y=0;return a;}
  int d=ext_gcd(b,a%b,y,x);
  y-=a/b*x;return d;
}
int crt(int *b,int *w,int n){
  int x,y,ans=0,mi,p=1;
  for(int i=1;i<=n;i++)p*=w[i];
  for(int i=1;i<=n;i++){
    mi=p/w[i];
    ext_gcd(mi,w[i],x,y);
    ans=(ans+x*mi*b[i])%p;
  }
  return ans?ans:ans+p;
}
```

### 扩展中国剩余定理

```cpp
int ext_gcd(int a,int b,int &x,int &y){
  if(!b){x=1,y=0;return a;}
  int d=ext_gcd(b,a%b,y,x);
  y-=a/b*x;return d;
}
int ext_crt(int *b,int *w,int n){
  int ans=0,m=1,p;
  for(int i=1;i<=n;i++){
    int x,y,d=ext_gcd(m,w[i],x,y),t=w[i]/d;
    if((b[i]-ans)%d)return -1;
    x=(x%t+t)%t,p=m*t;
    int k=((b[i]-ans)/d*x%t+t)%t;
    ans=(ans+k*m)%p,m=p;
  }
  return ans?ans:ans+p;
}
```

---

## 质数

```cpp
bool isprime(int x){
  if(x<2)return 0;
  if(x<4)return 1;
  for(int i=2;i*i<=x;i++)if(!(x%i))return 0;
  return 1;
}
```

### 埃氏筛与线性筛

```cpp
const int maxn=1e5+5;

bool isnp[maxn];
int p[maxn],ptot;
void initprime(int n){
  for(int i=2;i<=n;i++)if(!isnp[i])
    for(int j=i+i;j<=n;j+=i)isnp[j]=1;
}
void initprime_euler(int n){
  for(int i=2;i<=n;i++){
    if(!isnp[i])p[++ptot]=i;
    for(int j=1;j<=ptot&&i*p[j]<=n;j++){
      isnp[i*p[j]]=1;
      if(!(i%p[j]))break;
    }
  }
}
```

---

## 欧拉函数

```cpp
int phi(int x){
  int ans=x;
  for(int i=2;i*i<=x;i++)if(!(x%i)){
    ans-=ans/i;
    while(!(x%i))x/=i;
  }
  if(x>1)ans-=ans/x;
  return ans;
}
```

### 埃氏筛与线性筛

```cpp
const int maxn=1e5+5;

bool isnp[maxn];
int p[maxn],ptot;
void initphi(int *phi,int n){
  for(int i=1;i<=n;i++)phi[i]=i;
  for(int i=2;i<=n;i++)if(phi[i]==i)
    for(int j=i;j<=n;j+=i)phi[j]=phi[j]/i*(i-1);
}
void initphi_euler(int *phi,int n){
  for(int i=2;i<=n;i++){
    if(!isnp[i])p[++ptot]=i,phi[i]=i-1;
    for(int j=1;j<=ptot&&i*p[j]<=n;j++){
      isnp[i*p[j]]=1;
      if(i%p[j])phi[i*p[j]]=phi[i]*(p[j]-1);
      else{phi[i*p[j]]=phi[i]*p[j];break;}
    }
  }
}
```

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

## 乘法逆元

### 费马小定�?

```cpp
int powmod(int a,int b,int c){
  int ans=1;
  for(a%=c;b;b>>=1,a=a*a%c)if(b&1)ans=ans*a%c;
  return ans;
}
#define inv(x,p) powmod(x,p-2,p)
```

### 扩欧求�?

```cpp
int ext_gcd(int a,int b,int &x,int &y){
  if(!b){x=1,y=0;return a;}
  int d=ext_gcd(b,a%b,y,x);
  y-=a/b*x;return d;
}
int inv(int a,int p){
  int x,y,d=ext_gcd(a,p,x,y);
  return d==1?(x%p+p)%p:-1;
}
```

### 线性求�?

```cpp
void initinv(int *inv,int n,int p){
  inv[1]=1;
  for(int i=2;i<=n;i++)inv[i]=(p-p/i)*inv[p%i]%p;
}
```
