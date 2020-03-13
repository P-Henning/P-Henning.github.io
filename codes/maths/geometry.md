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
struct vect{
  double x,y;
  vect(double x=0,double y=0):x(x),y(y){}
  bool operator<(vect rhs)const{
    return x<rhs.x||x==rhs.x&&y<rhs.y;
  }
  friend double len(vect a){return sqrt(a.x*a.x+a.y*a.y);}
  friend double dist(vect a,vect b){return len(a-b);}
  friend vect operator+(vect a,vect b){
    return vect(a.x+b.x,a.y+b.y);
  }
  friend vect operator-(vect a,vect b){
    return vect(a.x-b.x,a.y-b.y);
  }
  friend vect operator*(vect a,double b){
    return vect(a.x*b,a.y*b);
  }
  friend vect operator*(double b,vect a){
    return vect(a.x*b,a.y*b);
  }
  friend vect operator/(vect a,double b){
    return vect(a.x/b,a.y/b);
  }
  friend double operator^(vect a,vect b){
    return a.x*b.x+a.y*b.y;
  }
  friend double operator*(vect a,vect b){
    return a.x*b.y-a.y*b.x;
  }
};
typedef vect point;
```

---

## Graham扫描法

```cpp
const int maxn=1e5+5;

point st[maxn];int stktop;
double cross(point a,point b,point c){return (b-a)*(c-a);}
void graham(point *a,int n){
  sort(a+1,a+1+n);stktop=0;
  for(int i=1;i<=n;i++){
    while(stktop>1&&cross(st[stktop-1],st[stktop],a[i])<=0)stktop--;
    st[++stktop]=a[i];
  }
  int tmp=stktop;
  for(int i=n-1;i>=1;i--){
    while(stktop>tmp&&cross(st[stktop-1],st[stktop],a[i])<=0)stktop--;
    st[++stktop]=a[i];
  }
}
```

---

## 旋转卡壳

```cpp
double cross(point a,point b,point c){return (b-a)*(c-a);}
double caliper_rotate(point *h,int tot){
  double ans=0;
  for(int i=1,j=2;i<=tot;i++){
    while(cross(h[i],h[i%tot+1],h[j+1])>cross(h[i],h[i%tot+1],h[j]))j=j%tot+1;
    ans=max(ans,max(dist(h[i],h[j]),dist(h[i%tot+1],h[j])));
  }
  return ans;
}
```
