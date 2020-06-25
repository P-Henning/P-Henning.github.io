---
title: 挑战三：Picture Puzzle of Chino (II)
author: P-Henning
date: 2019-08-31
permalink: /challenge-3-II
key: challenge-3-II
sharing: true
license: true
aside:
  toc: true
show_edit_on_github: true
show_subscribe: true
pageview: true
---

### 说在前面

虽然最近有些自闭，但是该来的总是要来的。

yuzan1830给出了一个$n\times n$的网格，每个格子内填了$[1,n^2]$内的一个数字，且每个数字恰好被填一次。现在有两种类型的操作可以进行：

- 任选一个数字，再从上下左右与它相邻的数字中选一个，两个数字进行交换。即两个数字所在的行数相差$1$、列数相同，或列数相差$1$、行数相同。
- 任选一个数字，再从左上、左下、右上、右下与它相邻的数字中选一个，两个数字进行交换。即两个数字所在的行数和列数都相差$1$。

规定每一次操作不能和上一次操作属于同一种类型，即两种操作必须交替进行。而第一次操作可以从两种类型中任选一种。

现在需要让网格中的数字从上到下、从左到右按$1,2,\dots,n^2$的顺序排列，求所需的最少步数，并输出任意一种方案。输出的第$i$行应包括第$i$次操作交换的两个数字。步数为$0$输出"No Need"，无解输出"No Solution"。

**样例输入**

    1 2 9
    4 6 3
    7 8 5

**样例输出**

    Step #1: 3 9
    Step #2: 5 6
    Step #3: 6 9

### 接受挑战

限于本蒟蒻的水平，这里只考虑$n=3$的情况。把$3\times 3$的网格展开成一个长为$9$的序列，由于$9!=362880$，网格中数字的摆放顺序不会超过$362880$种，宽搜表示毫无压力。

每一次操作前网格中数字的摆放顺序定义成一个状态，作为图中的结点；能够相互转化的顺序之间连边以转移状态。图中每个结点第一次到达时遍历的层数一定是最少的，若遇到已经访问过的结点应该停止往下搜索。标记已经访问过的结点，康托展开表示毫无压力。

状态数：

```cpp
const int maxn=362880+5;
```

一个状态应该包括$9$个格子内的数字，图方便的话可以这样写：

```cpp
typedef int state[10];
```

但是这样写起来方便用起来不方便，我们最好还是装一下逼：

```cpp
struct state{
  int st[10];
  state operator=(int arr[10]){
    memcpy(st,arr,10);return *this;
  }
  int &operator[](int x){return st[x];}
};
```

然后是康托展开部分。这样就有$\text{encode}(\lbrace 6,5,2,8,7,9,1,2,4\rbrace)=223051$，$\text{decode}(223051)=\lbrace 6,5,2,8,7,9,1,2,4\rbrace$之类的映射关系了。

```cpp
int fact[]={1,1,2,6,24,120,720,5040,40320,362880};
int encode(state st){
  int cnt=(st[1]-1)*fact[8];
  for(int i=2;i<=9;i++){
    int tmp=0;
    for(int j=1;j<i;j++)tmp+=st[j]<st[i];
    cnt+=(st[i]-tmp-1)*fact[9-i];
  }
  return cnt+1;
}
state decode(int c){
  state ans;int vis=0;c--;
  for(int i=1;i<=9;i++){
    int j,t=c/fact[9-i];
    for(j=1;j<=9;j++)if(!(vis>>j&1)&&!t--)break;
    ans[i]=j,vis|=1<<j,c%=fact[9-i];
  }
  return ans;
}
```

然后是核心部分：宽搜。由于第一步操作有两种选择，因此要搜两次，而在两次都搜完之前是不确定最优解的，因此要把两次搜索的结果都存下来，本人偷懒用了一个类。

有关宽搜的声明：

- $vis[x]$标记状态$x$（$x$是$\text{encode}$值）是否被访问过。
- $fa[x]$记录状态$x$上一步的状态。
- $step[x]$记录从初始状态到达状态$x$所需的步数。
- $type[x]$记录从状态$fa[x]$转移到状态$x$选择的操作类型。
- $opt[x]$记录从状态$fa[x]$转移到状态$x$交换的数字对。
- $\text{bfs}(st,type_{st})$：从状态$st$搜索，且第一步的操作类型为$type_{st}$。两次搜索的$type_{st}$值不同。
- $\text{print\_path}(u)$：递归输出从初始状态到状态$u$的解。

```cpp
int dx1[]={-1,1,0,0},dy1[]={0,0,-1,1};
int dx2[]={-1,-1,1,1},dy2[]={-1,1,-1,1};
class solver{
public:
  bool vis[maxn];
  int fa[maxn],step[maxn],type[maxn];
  pair<int,int> opt[maxn];
  void bfs(state st,int type_st){
    memset(vis,0,sizeof(vis));
    int u=encode(st),v;
    state cur,next;
    vis[u]=1,fa[u]=-1,step[u]=0;
    type[u]=type_st^1;
    queue<int> q;q.push(u);
    while(!q.empty()){
      u=q.front();q.pop();
      if(u==1)return;
      cur=decode(u);
      for(int x=1;x<=3;x++){
        for(int y=1;y<=3;y++){
          int pos=(x-1)*3+y;
          for(int i=0;i<4;i++){
            int tx,ty;
            if(type[u])tx=x+dx2[i],ty=y+dy2[i];
            else tx=x+dx1[i],ty=y+dy1[i];
            int newpos=(tx-1)*3+ty;
            if(tx>0&&tx<=3&&ty>0&&ty<=3){
              next=cur;swap(next[pos],next[newpos]);
              v=encode(next);
              if(!vis[v]){
                vis[v]=1,fa[v]=u,step[v]=step[u]+1;
                type[v]=type[u]^1;
                opt[v]=make_pair(next[pos],next[newpos]);
                q.push(v);
              }
            }
          }
        }
      }
    }
  }
  void print_path(int u){
    if(fa[u]<0)return;
    print_path(fa[u]);
    printf("Step #%d: %d %d\n",step[u],opt[u].first,opt[u].second);
  }
};
solver g1,g2;
```

主函数就比较显然了：

```cpp
int main(){
  state st;
  for(int i=1;i<=9;i++)scanf("%d",&st[i]);
  g1.bfs(st,0);
  g2.bfs(st,1);
  if(!g1.vis[1]&&!g2.vis[1])printf("No Solution\n");
  else if(!g2.vis[1]||g1.step[1]<g2.step[1]){
    if(!g1.step[1])printf("No Need\n");
    else g1.print_path(1);
  }
  else{
    if(!g2.step[1])printf("No Need\n");
    else g2.print_path(1);
  }
  return 0;
}
```

可能有多解的情况。比如一开始的样例的另一个解为：

    Step #1: 6 9
    Step #2: 3 6
    Step #3: 5 9

分析一下时间复杂度：对于边长为$n$的网格，一共有不超过$\left(n^2\right)!$种状态，每种状态可以选择$n^2$个位置，每个位置又可以选择另外$4$个位置的数来交换，而一次康托展开或逆康托展开的复杂度为$O\left(n^4\right)$，因此一次搜索的时间复杂度为$O\left(\left(n^2\right)!\cdot 4n^6\right)$。看起来很可怕，但在实际情况下很多状态都是达不到的，因此这个上界特别松。

### 说在后面

并不是所有的数据宽搜都跑得特别快。比如下面这组数据，上面的代码跑了8.2秒！

**样例输入**

    9 8 7
    6 5 4
    3 2 1

**样例输出**

    Step #1: 6 9
    Step #2: 5 6
    Step #3: 6 8
    Step #4: 4 6
    Step #5: 2 8
    Step #6: 2 7
    Step #7: 7 9
    Step #8: 1 9
    Step #9: 3 7
    Step #10: 3 4
    Step #11: 2 3
    Step #12: 1 5

我们不能忘了A\*搜索。本蒟蒻用A\*代替BFS之后，上面这组数据只跑了0.4秒。不过它求出的是另一组解：

    Step #1: 8 9
    Step #2: 5 7
    Step #3: 7 6
    Step #4: 1 6
    Step #5: 3 7
    Step #6: 1 8
    Step #7: 2 8
    Step #8: 4 9
    Step #9: 6 9
    Step #10: 3 4
    Step #11: 2 3
    Step #12: 3 5

为什么A\*搜索这么快？宽搜是从起点向各个方向搜索，而A\*可以说是直接朝着目标搜索的。

A\*搜索最关键的部分是估价函数$h(x)$，它表示从当前状态$x$到目标状态所需步数的估计值。这个估计值应该小于实际所需步数，且要尽可能接近。下面是本蒟蒻针对yuzan1830的这个问题设计的估价函数（设目标状态为$y$，$x_{i_1j_1}=y_{i_2j_2}=k$），也许是个假的估价函数。

$$h(x)=\left\lfloor\dfrac 13\sum_{k=1}^9\vert i_1-i_2\vert+\vert j_1-j_2\vert\right\rfloor$$

```cpp
int h(state st){
  int hv=0;
  for(int x=1;x<=3;x++){
    for(int y=1;y<=3;y++){
      int pos=(x-1)*3+y;
      int tx=(st[pos]-1)/3+1,ty=(st[pos]-1)%3+1;
      hv+=abs(x-tx)+abs(y-ty);
    }
  }
  return hv/3;
}
```

### 在线运行

宽搜+康托展开：

<div data-pym-src="https://www.jdoodle.com/embed/v0/1VtM"></div>

A\*+康托展开：

<div data-pym-src="https://www.jdoodle.com/embed/v0/1VtQ"></div>

<script src="https://www.jdoodle.com/assets/jdoodle-pym.min.js" type="text/javascript"></script>
