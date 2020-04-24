---
title: 位运算入门
author: P-Henning
permalink: /bit-operation
key: bit-operation
tags: [笔记, OI]
---

## 整数的存储

```cpp
short y=-25;
```

**原码**就是这个数字原始的二进制形式，只不过最高位表示符号。

    1000 0000 0001 1001

**反码**：将原码除符号位外其余的按位取反。

    1111 1111 1110 0110

**补码**：反码加$1$。

    1111 1111 1110 0111

<!--more-->

有符号类型使用补码表示法存储整数，正数和$0$用原码，负数用补码。

有符号类型的最左边的二进制位是符号位。

$n$位有符号类型的表示范围是$[-2^{n-1},2^{n-1}-1]$，可以看作是模$2^n$意义下的数。

## 位运算

### 与

两个整数作与运算，对于每个二进制位，都是$1$才取$1$。即若$x\land y=z$，则$x,y,z$第$i$个二进制位$x_i,y_i,z_i$之间存在如下关系：

$$z_i=\begin{cases}
1&x_i=1\land y_i=1\\
0&\text{otherwise}
\end{cases}$$

- $0,1\land 1$等于它本身，$0,1\land 0$等于$0$。
- $x$为奇数时，$x\land 1=1$；$x$为偶数时，$x\land 1=0$。

与在代码中用`&`表示。

### 或

两个整数作或运算，对于每个二进制位，只要有$1$就取$1$。即若$x\lor y=z$，则$x,y,z$第$i$个二进制位$x_i,y_i,z_i$之间存在如下关系：

$$z_i=\begin{cases}
1&x_i=1\lor y_i=1\\
0&\text{otherwise}
\end{cases}$$

- $0,1\lor 1$等于$1$，$0,1\lor 0$等于它本身。

或在代码中用`|`表示。

### 异或

两个整数作异或运算，对于每个二进制位，同为$0$，异为$1$。即若$x\oplus y=z$，则$x,y,z$第$i$个二进制位$x_i,y_i,z_i$之间存在如下关系：

$$z_i=\begin{cases}
1&x_i\neq y_i\\
0&x_i=y_i
\end{cases}$$

- $0,1\oplus 1$等于本身取反，$0,1\oplus 0$等于它本身。
- $x\oplus 1$将偶数的最后一位强制变为$1$，也就是变为与它相邻的奇数；将奇数的最后一位强制变为$0$，也就是变为与它相邻的偶数。

异或在代码中用`^`表示。

### 取反

一个数取反就是这个数的每一个二进制位取反。即若$\lnot x=y$，则$x,y$第$i$个二进制位$x_i,y_i$之间有如下关系：

$$y_i=\begin{cases}
1&x_i=0\\
0&x_i=1
\end{cases}$$

一般地，有

$$\lnot x=-x-1$$

取反在代码中用`~`表示。

### 移位

$x\ll y$表示把$x$的每个二进制位向左移动$y$位，移动造成的最右边的空位由$0$补足，最左边的数溢出。

$x\gg y$表示把$x$的每个二进制位向右移动$y$位，移动造成的最左边的空位由$0$补足，最右边的数溢出。

有符号类型右移，最左边补符号位。

左移、右移在代码中分别用`<<`、`>>`表示。

## 优先级

取反比乘、除、取模的优先级高。

移位比比较运算优先级高，比加减优先级低。

与、或、异或优先级由高到低，比比较运算优先级低，比逻辑运算优先级高。

## 综合应用

### 数字变换

针对$x$二进制的第$i$位（从第$0$位开始）：

- 获取，得到$0$或$1$：$(x\gg i)\land 1$
- 获取，得到$2^i$：$x\land(1\ll i)$
- $i=0$时判断奇偶：$x\land 1$
- 取反：$x\oplus(1\ll i)$
- 置为$1$：$x\lor(1\ll i)$
- 置为$0$：$x\land\lnot(1\ll i)$

针对$x$二进制的前$i$位（不包括第$i$位）：

- 获取：$x\land((1\ll i)-1)$
- 取反：$x\oplus((1\ll i)-1)$
- 置为$1$：$x\lor((1\ll i)-1)$
- 置为$0$：$x\land\lnot((1\ll i)-1)$

把$x$二进制低位的连续的$0$都变成$1$：$x\lor(x-1)$

把$x$二进制低位的连续的$1$都变成$0$：$x\land(x+1)$

### 字符变换

大小写转换：

- 大写变小写，小写变大写：$c\oplus 32$
- 全部变小写：$c\lor 32$
- 全部变大写：$c\land\lnot 32$

字符与数字转换：

- 字符变数字，数字变字符：$c\oplus 48$
- 全部变字符：$c\lor 48$
- 全部变数字：$c\land\lnot 48$

### 集合操作

- 判断$s$是否是$u$的子集：$s\land u=s$或$s\lor u=u$
- $s$关于$u$的补集：$s\oplus u$
- 判断$k$是否属于$s$：$s\land(1\ll(k-1))$
- 枚举$s$的子集$t$

  ```cpp
  for(int t=s;t;t=t-1&s);
  ```

- 枚举$s$的元素$x$

  ```cpp
  for(int i=s;i;i-=i&-i)int x=i&-i;
  ```

### 其它操作

- 代替乘除模

  $$\begin{aligned}
  2^ix&=x\ll i\\
  \left\lfloor\dfrac x{2^i}\right\rfloor&=x\gg i\\
  x\bmod 2^i&=x\land((1\ll i)-1)
  \end{aligned}$$
  
  其中除法无论正负都是向下取整，负数取模会得到非负的结果。
- 找右起第一个$1$：

  $$lowbit(x)=x\land-x$$

- 交换正整数

  ```cpp
  void swapint(int &a,int &b){a=a^b,b=a^b,a=a^b;}
  ```

  类似地，有

  ```cpp
  void swapint(int &a,int &b){a=a+b,b=a-b,a=a-b;}
  ```

- 构造常数
  - `unsigned int`的上限：`~0u` `-1u` `0xffffffff`
  - `int`的上限：`~0u>>1` `-1u>>1` `0x7fffffff`
  - `int`的下限：`1<<31` `0x80000000`
  - `long long`的上限：`0ull>>1` `-1ull>>1` `0x7fffffffffffffff`
  - `long long`的下限：`1ll<<63` `0x8000000000000000`

## 完备性

假设现在需要一种位运算$\otimes$，满足：

$$\begin{cases}
0\otimes 0&=a\\
0\otimes 1&=b\\
1\otimes 0&=c\\
1\otimes 1&=d
\end{cases}$$

其中$a,b,c,d$是$0$或者$1$。

对于任意一组$a,b,c,d$的值，能不能将表达式$x\otimes y$用$x,y$以及位运算组成的表达式来代替？

$$\begin{aligned}
x\otimes y&=(\lnot x\land\lnot y\land a)\lor(\lnot x\land y\land b)\lor(x\land\lnot y\land c)\lor(x\land y\land d)\\
&=(x\lor y\lor a)\land(x\lor\lnot y\lor b)\land(\lnot x\lor y\lor c)\land(\lnot x\lor\lnot y\lor d)
\end{aligned}$$

$x_0,x_1,\dots,x_{n-1}$都是$01$变量，$f(x_0,x_1,\dots,x_{n-1})$是一个函数，函数的输出也是$0$或$1$，对于任意的$n$和任意的函数$f$，都可以写出位运算表达式， 表达式中只包含某些位运算，那么称这些位运算具有完备性。

与、或、取反三种运算具有完备性。

由于

$$x\lor y=\lnot(\lnot x\land\lnot y)$$

所以与、取反两种运算具有完备性。

由于

$$x\land y=\lnot(\lnot x\lor\lnot y)$$

所以或、取反两种运算具有完备性。

### 与非

$$x\uparrow y=\lnot(x\land y)$$

由于

$$\begin{aligned}
\lnot x&=\lnot(x\land x)=x\uparrow x\\
x\land y&=\lnot(x\uparrow y)=(x\uparrow y)\uparrow (x\uparrow y)
\end{aligned}$$

所以与非运算具有完备性。

### 或非

$$x\downarrow y=\lnot(x\lor y)$$

由于

$$\begin{aligned}
\lnot x&=\lnot(x\lor x)=x\downarrow x\\
x\lor y&=\lnot (x\downarrow y)=(x\downarrow y)\downarrow (x\downarrow y)
\end{aligned}$$

所以或非运算具有完备性。
