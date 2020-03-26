---
layout: article
title: 计算几何
permalink: /codes/maths/geometry
aside:
  toc: true
sidebar:
  nav: codes
show_edit_on_github: true
---

## 平面向量

```cpp
template<typename Tp>
struct point{
  Tp x,y;
  point(Tp x=0,Tp y=0):x(x),y(y){}
  bool operator<(point rhs)const{
    return x<rhs.x||x==rhs.x&&y<rhs.y;
  }
  point operator+(point b){return point(x+b.x,y+b.y);}
  point operator-(point b){return point(x-b.x,y-b.y);}
  point operator*(Tp b){return point(x*b,y*b);}
  point operator/(Tp b){return point(x/b,y/b);}
  Tp operator^(point b){return x*b.x+y*b.y;}
  Tp operator*(point b){return x*b.y-y*b.x;}
  friend Tp len(point a){return sqrt(a.x*a.x+a.y*a.y);}
  friend Tp dist(point a,point b){return len(a-b);}
};
```

---

## Graham扫描法

```cpp
const int maxn=1e5+5;

point<double> st[maxn];int top;
#define cross(a,b,c) (((b)-(a))*((c)-(a)))
template<typename Tp>
void graham(point<Tp> *a,int n){
  sort(a+1,a+1+n);top=0;
  for(int i=1;i<=n;i++){
    while(top>1&&cross(st[top-1],st[top],a[i])<=0)top--;
    st[++top]=a[i];
  }
  int tmp=top;
  for(int i=n-1;i>=1;i--){
    while(top>tmp&&cross(st[top-1],st[top],a[i])<=0)top--;
    st[++top]=a[i];
  }
}
#undef cross
```

---

## 旋转卡壳

```cpp
#define cross(a,b,c) (((b)-(a))*((c)-(a)))
template<typename Tp>
Tp caliper_rotate(point<Tp> *h,int tot){
  Tp ans=0;
  for(int i=1,j=2;i<=tot;i++){
    while(cross(h[i],h[i%tot+1],h[j+1])>cross(h[i],h[i%tot+1],h[j]))j=j%tot+1;
    ans=max(ans,max(dist(h[i],h[j]),dist(h[i%tot+1],h[j])));
  }
  return ans;
}
#undef cross
```
