---
layout: article
title: ��սһ��The Prologue
author: P-Henning
key: challenge-1
date: 2019-4-26
tags: yuzan1830
sharing: true
license: true
aside:
  toc: true
show_edit_on_github: true
show_subscribe: true
pageview: true
---

### ˵��ǰ��

���죬Ҳ���ǹ�Ԫ2019��4��26�գ��������һ�����ң���Ŷ��յ�yuzan1830�����X�m������һ��С��ת���ɷ���������������������

$$1.2\dot 3\dot 4=\frac{611}{495}$$

yuzan1830����׼������Dev-C++��׼���ñ��X�m�ڽ��ҵ�һ�����~~��������~~�׳󣬹��¾�������ʼ�ˡ���

### ������ս

����Ҫ�����������������ƣ��������С����ѭ���ڣ���ѭ������һ��С����������������ķ�������������������ĸΪ$1$ʱ��Ҫ�����ĸ������ֵ����$1$ʱ����ٷ�����

    **��������**
    
    1.2(34)
    
    **�������**
    
    611/495

���X�m������������ǰɪɪ������

���Ƚ����������ֳ������֣����޵Ĳ���$A$��ѭ���Ĳ���$B$��

$$1.2\dot 3\dot 4=1.2+0.0\dot 3\dot 4$$

Ϊ�˷��㣬������������е����ݶ��÷��������÷��Ӻͷ�ĸ�������㡣

$A$���Ժܼ򵥵ر߶���ߴ���

$$1.2=\frac{12}{10}$$

�ؼ�������ΰ�$B$ת���ɷ����������ǹ��̡�

PS��yuzan1830ֱ������Сѧ�������ݡ�

$$\begin{aligned}
B&=0.0\dot 3\dot 4\\
100B&=3.4\dot 3\dot 4\\
99B&=3.4\\
B&=\frac{3.4}{99}=\frac{34}{990}
\end{aligned}$$

һ��أ��ǵ�һ��ѭ���ڼ���֮ǰ��С������Ϊ$C$��ѭ���ڳ���Ϊ$k$��$base=10^k$����

$$B=\frac{C\cdot base}{base-1}$$

Ȼ���X�m�Ϳ�ʼ�ڴ�������д�ˣ�����ɪɪ������

�º�ƾӡ��д���Ĵ������£�

```c++
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<iostream>
using namespace std;
struct fract{
  int n,d;
  fract(int n=0,int d=1):n(n),d(d){};
};
int main(){
  char s[1000];
  while(scanf("%s",s+1)==1){
    int len=strlen(s+1),i;
    fract A,C;bool f=0;
    for(i=1;i<=len;i++){
      if(s[i]=='(')break;
      if(s[i]=='.'){f=1;continue;}
      A.n=A.n*10+s[i]-'0';
      if(f)A.d*=10,C.d*=10;
    }
    int base=1,ans_n,ans_d;
    for(i++;i<len;i++){
      C.n=C.n*10+s[i]-'0',C.d*=10;
      base*=10;
    }
    if(base==1)ans_n=A.n,ans_d=A.d;
    else ans_n=A.n*C.d*(base-1)+A.d*C.n*base,ans_d=A.d*C.d*(base-1);
    int gcd=__gcd(ans_n,ans_d);
    cout<<ans_n/gcd<<'/'<<ans_d/gcd<<endl;
  }
  return 0;
}
```

��������$1.2,1.2\dot 3\dot 4$��$0.\dot 3$������ó�����ȷ�Ľ��������$1.\dot 28571\dot4$�ó��Ĵ𰸷�ĸ�Ǹ�����

��Ȼ����ˡ�`int`ȫ�ĳ�`long long`�ͺ��ˣ��Ͼ����������û�н���Լ�֡���`main`������Ȼ����Ҫ����`int`��

### ˵�ں���

֮�����ýṹ��`fract`����ʵ���������������ֱ�ӶԷ���$A,B,C$�������㣨������ĳ����ѧ��������д���������Ǻ��񵱳�д��������ֱ���÷��ӷ�ĸ���㡣

�º����������д���Ĵ��룺

```c++
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

���⣬yuzan1830ʮ��������û�г��ر��������ݣ����±��X�m�ڴ�����ǰû�ж�ʧ���档

����лyuzan1830 ~~�˷�~~��ʵ���ҽ�һ��Сʱ��ʱ�䡣