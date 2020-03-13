---
layout: article
title: 线性代数
permalink: /codes/maths/linear
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 矩阵乘法

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

---

## 高斯消元法

```cpp
const int maxn=100+5;
const double EPS=1e-6;

double a[maxn][maxn];
bool gauss(int n){
  for(int i=1;i<=n;i++){
    int r=i;
    for(int j=i+1;j<=n;j++)
      if(fabs(a[j][i])>fabs(a[r][i]))r=j;
    if(r!=i)for(int j=1;j<=n+1;j++)swap(a[r][j],a[i][j]);
    if(fabs(a[i][i])<=EPS)return 0;
    for(int j=n+1;j>=i;j--)
      for(int k=i+1;k<=n;k++)
        a[k][j]-=a[k][i]/a[i][i]*a[i][j];
  }
  for(int i=n;i>=1;i --){
    for(int j=i+1;j<=n;j++)
      a[i][n+1]-=a[j][n+1]*a[i][j];
    a[i][n+1]/=a[i][i];
  }
  return 1;
}
```
