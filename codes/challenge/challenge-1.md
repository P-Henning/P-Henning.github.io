---
layout: article
title: 挑战一：The Prologue
permalink: /codes/challenge/challenge-1
aside:
  toc: true
sidebar:
  nav: challenge
show_edit_on_github: true
---

### 小数化分数

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
typedef long long LL;
struct fract{
  LL n,d;
  fract(LL n=0,LL d=1):n(n),d(d){};
  fract operator+(fract b){
    LL nn=n*b.d+b.n*d,nd=d*b.d,gcd=__gcd(nn,nd);
    return fract(nn/gcd,nd/gcd);
  }
  fract operator*(LL b){
    LL nn=n*b,nd=d,gcd=__gcd(nn,nd);
    return fract(nn/gcd,nd/gcd);
  }
  fract operator/(LL b){
    LL nn=n,nd=d*b,gcd=__gcd(nn,nd);
    return fract(nn/gcd,nd/gcd);
  }
  friend void write(fract x){
    printf("%lld/%lld\n",x.n,x.d);
  }
};
char s[1000];
int main(){
  while(scanf("%s",s+1)==1){
    int len=strlen(s+1),i;
    fract A,C,D(1,1),ans;bool f=0;
    for(i=1;i<=len;i++){
      if(s[i]=='(')break;
      if(s[i]=='.'){f=1;continue;}
      if(f)D=D/10,A=A+D*(s[i]-'0');
      else A=A*10+(s[i]-'0');
    }
    LL base=1;
    for(i++;i<len;i++){
      D=D/10,base*=10;
      C=C+D*(s[i]-'0');
    }
    if(base==1)ans=A;
    else ans=A+C*base/(base-1);
    write(ans);
  }
  return 0;
}
```