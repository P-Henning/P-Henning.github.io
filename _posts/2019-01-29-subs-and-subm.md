---
title: 子序列与子矩阵问题
author: P-Henning
permalink: /subs-and-subm
key: subs-and-subm
tags: [笔记, OI]
---

## 子序列

### 最大连续子序列

$f[i]$表示以第$i$个数字结尾能够得到的最大连续子序列的元素和。可以将$a[i]$单独作为一个子序列，也可以把它加入以前一个数结尾的连续子序列中，形成一个新的连续子序列。

$$f[i]=\max\lbrace f[i-1],0\rbrace+a[i]$$

答案为$\max\limits_i\lbrace f[i]\rbrace$。

时间复杂度$O(n)$。

<!--more-->

### 最长上升子序列

$f[i]$表示以第$i$个数字结尾能够得到的最长上升子序列的长度。枚举该序列中前一个数字可能的位置即可。

$$f[i]=\max_{j=1}^{i-1}\lbrace f[j]\vert a[j]\leqslant a[i]\rbrace+1$$

答案为$\max\limits_i\lbrace f[i]\rbrace$。

时间复杂度$O\left(n^2\right)$。

**优化：**从左往右讨论每一个数字，$f[i]$表示当前已讨论的部分中长度为$i$的上升子序列最后一个数的最小值。于是$f[i]$是单调递增的。对于当前的数字$a[k]$，只需要二分查找找到恰比$a[k]$小的一个$f[i]$，把$f[i+1]$改成$a[k]$就行了。

答案为所有被修改过的$f[i]$中最大的$i$。

时间复杂度$O(n\log n)$。

```cpp
for(int i=1;i<=n;i++)f[i]=INF;
for(int i=1;i<=n;i++){
  int t=lower_bound(f+1,f+1+n,a[i])-f;
  ans=max(ans,t),f[t]=a[i];
}
```

### 最长公共子序列

设$x,y$两个序列的长度分别为$n,m$。$f[i][j]$表示$x$序列的前$i$项与$y$序列的前$j$项能构成的最长公共子序列的长度。

- $x[i]=y[j]$时，只需找出$x[1,i-1]$和$y[1,j-1]$的最长公共子序列，然后加上该项。
- $x[1]\neq y[j]$时，找出$x[1,i-1]$和$y[1,j]$的最长公共子序列与$x[1,i]$和$y[1,j-1]$的最长公共子序列中的较长者。

$$f[i][j]=\begin{cases}
f[i-1][j-1]+1&x[i]=y[j]\\
\max\lbrace f[i-1][j],f[i][j-1]\rbrace&x[i]\neq y[j]
\end{cases}$$

答案为$f[n][m]$。

时间复杂度$O\left(n^2\right)$。

### 最长公共连续子序列

$f[i][j]$表示以$x$序列的第$i$个数字与$y$序列的第$j$个数字结尾能得到的最长公共连续子序列的长度。若当前讨论的两个数字相等，可以成为新的合法子序列的结尾。

$$f[i][j]=\begin{cases}
f[i-1][j-1]+1&x[i]=y[i]\\
0&x[i]\neq y[i]
\end{cases}$$

答案为$\max\limits_{i,j}\lbrace f[i][j]\rbrace$。

时间复杂度$O\left(n^2\right)$。

### 最长公共上升子序列

将最长公共子序列和最长上升子序列的求法合并得到。设$x,y$两个序列的长度分别为$n,m$。$f[i][j]$表示$x$序列的前$i$项与$y$序列的前$j$项且包括$y[j]$能构成的最长公共上升子序列的长度。

$$f[i][j]=\max\begin{cases}
f[i-1][j]\\
\max\limits_{k=1}^{j-1}\lbrace f[i-1][k]\vert y[k]\leqslant y[j]\rbrace+1&x[i]=y[j]
\end{cases}$$

答案为$\max\limits_j\lbrace f[n][j]\rbrace$。

时间复杂度$O\left(n^3\right)$。用前缀和优化可以优化至$O\left(n^2\right)$。

## 子矩阵

### 最大全1子方阵

$f[i][j]$表示把点$(i,j)$当作子方阵的右下角端点，能够得到的最大的全$1$的子方阵的边长。

$$f[i][j]=\begin{cases}
0&a[i][j]=0\\
\min\lbrace f[i-1][j-1],f[i][j-1],f[i-1][j]\rbrace+1&a[i][j]=1
\end{cases}$$

答案为$\max\limits_{i,j}\lbrace f[i][j]\rbrace$。

时间复杂度$O\left(n^2\right)$。

### 最大全1子矩阵
$f[i][j]$表示在第$j$列的数字中，以第$i$行的数字为结尾的最长连续$1$的个数。

$$f[i][j]=\begin{cases}
0&a[i][j]=0\\
f[i-1][j]+1&a[i][j]=1
\end{cases}$$

然后$f[i][j]$的每一行就成了一个广告印刷模型，用单调队列/单调栈解决。

时间复杂度$O\left(n^2\right)$。

```cpp
for(int i=1;i<=n;i++){
  for(int j=1;j<=m;j++){
    cur=0;
    while(top&&st[top].h>=f[i][j]){
      cur+=st[top].len;
      ans=max(ans,st[top--].h*cur);
    }
    st[++top]=node(f[i][j],cur+1);
  }
  cur=0;
  while(top){
    cur+=st[top].len;
    ans=max(ans,st[top--].h*cur);
  }
}
```

### 最大子矩阵

$f[i][j][k]$表示以第$i,j$行为上下边界、第$k$列为左右边界的子矩阵的最大元素和。预处理第$j$列中前$i$行数字的前缀和$sum[i][j]$。对于每一对上下边界$(i,j)$，把每一列中$i$到$j$行的数字总和放入一个新序列，对这个序列求最大连续子序列即可。

时间复杂度$O\left(n^3\right)$。
