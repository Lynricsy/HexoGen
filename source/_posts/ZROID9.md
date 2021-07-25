---
title: 正睿OI Day8 总结
date: 2021-07-25
tags:
 - 培训总结
 - 平衡树
categories: 培训总结
description: 正睿OI Day8总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxkihz4b4j31hc0u0wux.jpg
---

## Splay

### 基本

核心操作：splay(x)，把 x 换到根的位置上。

记录信息：

 - son[x][0/1]：左右儿子
 - f[x]：x 的父亲
 - key[x]：x 的数值
 - size[x]：x 的子树大小
 - recy[x]：x 的值重复的次数

### 旋转操作

![](https://i0.hdslb.com/bfs/album/3f03f2c33338b718e77e6bef1a22a761eea83499.png)

![](https://i0.hdslb.com/bfs/album/a134b7b99a31063ad26dc0181a6c6aaddcc20b5b.png)

### Splay操作

splay 操作通过双旋保证均摊复杂度。

具体证明较为复杂，有兴趣可以参见 tarjan 的论文，这里不再赘述。

过程如下：

设执行 splay(x)，y=f[x]，z=f[y]，那么若 x,y,z 在一条直线上，则分别执行 rotate(y),rotate(x)；否则执行 rotate(x),rotate(x)。

若 y 是根，显然执行一遍 rotate(x) 即可。


### 代码

```cpp
#include<iostream>
#include<cstdio>
using namespace std;
#define MAXN 1000000
int f[MAXN],cnt[MAXN],value[MAXN],sons[MAXN][2],sub_size[MAXN],whole_size,root;                 
inline int qread(){
    int res=0,k=1;
    char c=getchar();
    while(!isdigit(c)){
        if(c=='-')k=-1;
        c=getchar();
    }
    while(isdigit(c)){
        res=(res<<1)+(res<<3)+c-48;
        c=getchar();
    }
    return res*k;
}
inline void S_Clear(int x){
    sons[x][0]=sons[x][1]=f[x]=sub_size[x]=cnt[x]=value[x]=0; 
}
inline bool get_which(int x){
    return sons[f[x]][1]==x;
}
inline void update(int x){
    if (x){  
        sub_size[x]=cnt[x];  
        if (sons[x][0]) sub_size[x]+=sub_size[sons[x][0]];  
        if (sons[x][1]) sub_size[x]+=sub_size[sons[x][1]];  
    }  
    return ;
}
inline void rotate(int x){
    int father=f[x],g_father=f[father],which_son=get_which(x);
    sons[father][which_son]=sons[x][which_son^1];
    f[sons[father][which_son]]=father;
    sons[x][which_son^1]=father;
    f[father]=x;
    f[x]=g_father;
    if(g_father){
        sons[g_father][sons[g_father][1]==father]=x;
    }
    update(father);
    update(x);
}
inline void splay(int x){
    for (int fa;fa=f[x];rotate(x))  
      if (f[fa])  
        rotate((get_which(x)==get_which(fa))?fa:x);  
    root=x;  
}
inline void insert(int x){
    if(!root){
        whole_size++;
        sons[whole_size][0]=sons[whole_size][1]=f[whole_size]=0;
        root=whole_size;
        sub_size[whole_size]=cnt[whole_size]++;
        value[whole_size]=x;
        return ;
    } 
    int now=root,fa=0;
    while(1){
        if(x==value[now]){
            cnt[now]++;
            update(now);
            update(fa);
            splay(now);
            break;
        }
        fa=now;
        now=sons[now][value[now]<x];
        if(!now){
            whole_size++;
            sons[whole_size][0]=sons[whole_size][1]=0;
            f[whole_size]=fa;
            sub_size[whole_size]=cnt[whole_size]=1;
            sons[fa][value[fa]<x]=whole_size;
            value[whole_size]=x;
            update(fa);
            splay(whole_size);
            break; 
        }
    }
    
}
inline int find_num(int x){ 
    int now=root;
    while(1){
        if(sons[now][0]&&x<=sub_size[sons[now][0]])
        now=sons[now][0];
        else {
            int temp=(sons[now][0]?sub_size[sons[now][0]]:0)+cnt[now];
            if(x<=temp)return value[now];
            x-=temp;
            now=sons[now][1];
        }
    }
}

inline int find_rank(int x){
      int now=root,ans=0;  
    while(1){  
        if (x<value[now])  
          now=sons[now][0];  
        else{  
            ans+=(sons[now][0]?sub_size[sons[now][0]]:0);  
            if (x==value[now]){  
                splay(now); return ans+1;  
            }  
            ans+=cnt[now];  
            now=sons[now][1];  
        }  
    }  
}
inline int find_pre(){
    int now=sons[root][0];
    while(sons[now][1])now=sons[now][1];
    return now;
}
inline int find_suffix(){
    int now=sons[root][1];
    while(sons[now][0])now=sons[now][0];
    return now;
}
inline void my_delete(int x){
    int hhh=find_rank(x);
    if (cnt[root]>1){
    cnt[root]--; 
    update(root); 
    return;
    }  
    if (!sons[root][0]&&!sons[root][1]) {
    S_Clear(root);
    root=0;
    return;
    }  
    if (!sons[root][0]){  
        int old_root=root; 
        root=sons[root][1];
        f[root]=0; 
        S_Clear(old_root); 
        return;  
    }  
     
    else if (!sons[root][1]){  
        int old_root=root; 
        root=sons[root][0]; 
        f[root]=0; 
        S_Clear(old_root); 
        return;  
    } 
    int left_max=find_pre(),old_root=root;  
    splay(left_max);  
    sons[root][1]=sons[old_root][1];  
    f[sons[old_root][1]]=root;  
    S_Clear(old_root);  
    update(root);  
}

   
int main(){
    int m,num,be_dealt;
    cin>>m;
    for(int i=1;i<=m;i++){
       num=qread();
       be_dealt=qread();
        switch(num)
        {
            case 1:insert(be_dealt);break;
            case 2:my_delete(be_dealt);break;
            case 3:printf("%d\n",find_rank(be_dealt));break;
            case 4:printf("%d\n",find_num(be_dealt));break;
            case 5:insert(be_dealt);printf("%d\n",value[find_pre()]);my_delete(be_dealt);break;
            case 6:insert(be_dealt);printf("%d\n",value[find_suffix()]);my_delete(be_dealt);break;
        }
    }
    return 0;
}
```

## Treap

### 简介

不仅要记录之前的信息，还要记录一个随机值 rd[i]，表示该点的随机值，要求随机值排成一个大根堆，来保证树的深度。

不过可以选择不记录 father。

### 代码

```cpp
#include <algorithm>
#include <climits>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <queue>
typedef long long LL;
using namespace std;
int RD() {
  int out = 0, flag = 1;
  char c = getchar();
  while (c < '0' || c > '9') {
    if (c == '-')
      flag = -1;
    c = getchar();
  }
  while (c >= '0' && c <= '9') {
    out = out * 10 + c - '0';
    c = getchar();
  }
  return flag * out;
}
//第一次打treap，不压行写注释XD
const int maxn = 1000019, INF = 1e9;
//平衡树，利用BST性质查询和修改，利用随机和堆优先级来保持平衡，把树的深度控制在log
// N，保证了操作效率 基本平衡树有以下几个比较重要的函数：新建，插入，删除，旋转
//节点的基本属性有val(值)，dat(随机出来的优先级)
//通过增加属性，结合BST的性质可以达到一些效果，如size(子树大小，查询排名)，cnt(每个节点包含的副本数)等
int na;
int ch[maxn][2]; //[i][0]代表i左儿子，[i][1]代表i右儿子
int val[maxn], dat[maxn];
int size[maxn], cnt[maxn];
int tot, root;
int New(int v) {     //新增节点，
  val[++tot] = v;    //节点赋值
  dat[tot] = rand(); //随机优先级
  size[tot] = 1;     //目前是新建叶子节点，所以子树大小为1
  cnt[tot] = 1;      //新建节点同理副本数为1
  return tot;
}
void pushup(int id) { //和线段树的pushup更新一样
  size[id] =
      size[ch[id][0]] + size[ch[id][1]] +
      cnt[id]; //本节点子树大小 = 左儿子子树大小 + 右儿子子树大小 + 本节点副本数
}
void build() {
  root = New(-INF),
  ch[root][1] = New(INF); //先加入正无穷和负无穷，便于之后操作(貌似不加也行)
  pushup(root); //因为INF > -INF,所以是右子树，
}
void Rotate(int &id,
            int d) { // id是引用传递，d(irection)为旋转方向，0为左旋，1为右旋
  int temp = ch[id][d ^ 1]; //旋转理解：找个动图看一看就好(或参见其他OIer的blog)
  ch[id][d ^ 1] =
      ch[temp][d]; //这里讲一个记忆技巧，这些数据都是被记录后马上修改
  ch[temp][d] = id; //所以像“Z”一样
  id = temp; //比如这个id，在上一行才被记录过，ch[temp][d]、ch[id][d ^
             // 1]也是一样的
  pushup(ch[id][d]),
      pushup(
          id); //旋转以后size会改变，看图就会发现只更新自己和转上来的点，pushup一下,注意先子节点再父节点
} //旋转实质是({在满足BST的性质的基础上比较优先级}通过交换本节点和其某个叶子节点)把链叉开成二叉形状(从而控制深度)，可以看图理解一下
void insert(int &id, int v) { // id依然是引用，在新建节点时可以体现
  if (!id) {
    id = New(v); //若节点为空，则新建一个节点
    return;
  }
  if (v == val[id])
    cnt[id]++; //若节点已存在，则副本数++;
  else {       //要满足BST性质，小于插到左边，大于插到右边
    int d =
        v < val[id]
            ? 0
            : 1; //这个d是方向的意思，按照BST的性质，小于本节点则向左，大于向右
    insert(ch[id][d], v); //递归实现
    if (dat[id] < dat[ch[id][d]])
      Rotate(id, d ^ 1); //(参考一下图)与左节点交换右旋，与右节点交换左旋
  }
  pushup(id); //现在更新一下本节点的信息
}
void Remove(int &id, int v) { //最难de部分了
  if (!id)
    return; //到这了发现查不到这个节点，该点不存在，直接返回
  if (v == val[id]) { //检索到了这个值
    if (cnt[id] > 1) {
      cnt[id]--, pushup(id);
      return;
    } //若副本不止一个，减去一个就好
    if (ch[id][0] ||
        ch[id][1]) { //发现只有一个值，且有儿子节点,我们只能把值旋转到底部删除
      if (!ch[id][1] ||
          dat[ch[id][0]] >
              dat[ch[id]
                    [1]]) { //当前点被移走之后，会有一个新的点补上来(左儿子或右儿子)，按照优先级，优先级大的补上来
        Rotate(id, 1),
            Remove(
                ch[id][1],
                v); //我们会发现，右旋是与左儿子交换，当前点变成右节点；左旋则是与右儿子交换，当前点变为左节点
      } else
        Rotate(id, 0), Remove(ch[id][0], v);
      pushup(id);
    } else
      id = 0; //发现本节点是叶子节点，直接删除
    return;   //这个return对应的是检索到值de所有情况
  }
  v < val[id] ? Remove(ch[id][0], v) : Remove(ch[id][1], v); //继续BST性质
  pushup(id);
}
int get_rank(int id, int v) {
  if (!id)
    return -2; //若查询值不存在，返回；因为最后要加一排除哨兵节点，想要结果为-1这里就返回-2
  if (v == val[id])
    return size[ch[id][0]] +
           1; //查询到该值，由BST性质可知：该点左边值都比该点的值(查询值)小，故rank为左儿子大小
              //+ 1
  else if (v < val[id])
    return get_rank(ch[id][0], v); //发现需查询的点在该点左边，往左边递归查询
  else
    return size[ch[id][0]] + cnt[id] +
           get_rank(
               ch[id][1],
               v); //若查询值大于该点值。说明询问点在当前点的右侧，且此点的值都小于查询值，所以要加上cnt[id]
}
int get_val(int id, int rank) {
  if (!id)
    return INF; //一直向右找找不到，说明是正无穷
  if (rank <= size[ch[id][0]])
    return get_val(ch[id][0],
                   rank); //左边排名已经大于rank了，说明rank对应的值在左儿子那里
  else if (rank <= size[ch[id][0]] + cnt[id])
    return val
        [id]; //上一步排除了在左区间的情况，若是rank在左与中(目前节点)中，则直接返回目前节点(中区间)的值
  else
    return get_val(
        ch[id][1],
        rank - size[ch[id][0]] -
            cnt[id]); //剩下只能在右区间找了，rank减去左区间大小和中区间，继续递归
}
int get_pre(int v) {
  int id = root, pre; //递归不好返回，以循环求解
  while (id) {        //查到节点不存在为止
    if (val[id] < v)
      pre = val[id],
      id = ch[id][1]; //满足当前节点比目标小，往当前节点的右侧寻找最优值
    else
      id = ch
          [id]
          [0]; //无论是比目标节点大还是等于目标节点，都不满足前驱条件，应往更小处靠近
  }
  return pre;
}
int get_next(int v) {
  int id = root, next;
  while (id) {
    if (val[id] > v)
      next = val[id],
      id = ch[id][0]; //同理，满足条件向左寻找更小解(也就是最优解)
    else
      id = ch[id][1]; //与上方同理
  }
  return next;
}
int main() {
  build(); //不要忘记初始化[运行build()会连同root一并初始化，所以很重要]
  na = RD();
  for (int i = 1; i <= na; i++) {
    int cmd = RD(), x = RD();
    if (cmd == 1)
      insert(
          root,
          x); //函数都写好了，注意：需要递归的函数都从根开始，不需要递归的函数直接查询
    else if (cmd == 2)
      Remove(root, x);
    else if (cmd == 3)
      printf(
          "%d\n",
          get_rank(root, x) -
              1); //注意：因为初始化时插入了INF和-INF,所以查询排名时要减1(-INF不是第一小，是“第零小”)
    else if (cmd == 4)
      printf("%d\n", get_val(root, x + 1)); //同理，用排名查询值得时候要查x +
                                            // 1名，因为第一名(其实不是)是-INF
    else if (cmd == 5)
      printf("%d\n", get_pre(x));
    else if (cmd == 6)
      printf("%d\n", get_next(x));
  }
  return 0;
}
```

## FHQ-Treap

这个写过了，这里就不再写了。

## 文艺平衡树

很经典的一道平衡树模板题。

### 题目

维护一个序列，支持区间翻转，动态查询每个位置上的数字。

### 思路

打一个 reverse 区间标记即可。

在需要时下传，最后递归输出左子树->自己->右子树。

### 代码

```cpp
/**************************************************************
 * Problem: OJ ProID
 * Author: 芊枫
 * Date: 2021 Mon Date
 * Algorithm:
 **************************************************************/

#include <bits/stdc++.h>
#include <bitset>
#include <cstdlib>
#include <ctime>
#include <shared_mutex>

#define INF 0x3f3f3f3f3f3f3f3f
#define IINF 0x3f3f3f3f
#define arand() rand() % 114514
#define mrg() merge(x, y)

using namespace std;

inline long long read() {
  long long x = 0;
  int f = 1;
  char ch = getchar();
  while (ch < '0' || ch > '9') {
    if (ch == '-')
      f = -1;
    ch = getchar();
  }
  while (ch >= '0' && ch <= '9') {
    x = (x << 1) + (x << 3) + (ch ^ 48);
    ch = getchar();
  }
  return x * f;
}
void write(const long long &x) {
  if (!x) {
    putchar('0');
    return;
  }
  char f[100];
  long long tmp = x;
  if (tmp < 0) {
    tmp = -tmp;
    putchar('-');
  }
  long long s = 0;
  while (tmp > 0) {
    f[s++] = tmp % 10 + '0';
    tmp /= 10;
  }
  while (s > 0) {
    putchar(f[--s]);
  }
}

const long long maxN = 1000090;

struct Node {
  long long lch, rch;
  long long val, siz;
  long long rnd;
  bool rev;
} nodes[maxN];

long long X, Y, Z, W;
long long rot;
long long cnt;
long long totN;
long long totM;
long long raw[maxN];

void pushdown(long long x) {
  // if (!nodes[x].rev) {
  //   return;
  // }
  swap(nodes[x].lch, nodes[x].rch);
  if (nodes[x].lch) {
    nodes[nodes[x].lch].rev ^= true;
  }
  if (nodes[x].rch) {
    nodes[nodes[x].rch].rev ^= true;
  }
  nodes[x].rev = false;
}

void update(long long nowX) {
  nodes[nowX].siz = nodes[nodes[nowX].lch].siz + nodes[nodes[nowX].rch].siz + 1;
}

long long merge(long long Atree, long long Btree) {
  //	puts("qwq");
  if ((!Atree) || (!Btree)) {
    return Atree + Btree;
  } else if (nodes[Atree].rnd < nodes[Btree].rnd) {
    if (nodes[Atree].rev) {
      pushdown(Atree);
    }
    nodes[Atree].rch = merge(nodes[Atree].rch, Btree);
    update(Atree);
    return Atree;
  } else {
    if (nodes[Btree].rev) {
      pushdown(Btree);
    }
    nodes[Btree].lch = merge(Atree, nodes[Btree].lch);
    update(Btree);

    return Btree;
  }
}

void split(long long splitX, long long K, long long &Xtree, long long &Ytree) {
  //	printf("%lld\n",splitX);
  if (!splitX) {
    Xtree = Ytree = 0;
    return;
  }
  if (nodes[splitX].rev) {
    pushdown(splitX);
  }
  if (nodes[nodes[splitX].lch].siz < K) {
    Xtree = splitX;
    split(nodes[splitX].rch, K - nodes[nodes[splitX].lch].siz - 1,
          nodes[splitX].rch, Ytree);
  } else {
    Ytree = splitX;
    split(nodes[splitX].lch, K, Xtree, nodes[splitX].lch);
  }
  update(splitX);
}
long long Kth(long long KX, long long K) {
  while (1) {
    //		puts("1");
    if (K <= nodes[nodes[KX].lch].siz) {
      KX = nodes[KX].lch;
    } else if (K == nodes[nodes[KX].lch].siz + 1) {
      return KX;
    } else {
      K -= nodes[nodes[KX].lch].siz + 1;
      KX = nodes[KX].rch;
    }
  }
}

void del(long long delX) {
  split(rot, delX, X, Z);
  split(X, delX - 1, X, Y);
  Y = merge(nodes[Y].lch, nodes[Y].rch);
  rot = merge(merge(X, Y), Z);
}

void insert(long long nowX) {
  split(rot, nowX, X, Y);
  //	puts("qwq");
  ++cnt;
  nodes[cnt].lch = nodes[cnt].rch = 0;
  nodes[cnt].val = nowX;
  nodes[cnt].siz = 1;
  nodes[cnt].rnd = arand();
  rot = merge(merge(X, cnt), Y);
}

long long pre(long long nowX) {
  split(rot, nowX - 1, X, Y);
  return Kth(X, nodes[X].siz);
}
long long nxt(long long nowX) {
  split(rot, nowX, X, Y);
  return Kth(Y, 1);
}
long long thK(long long nowX) {
  split(rot, nowX - 1, X, Y);
  return nodes[X].siz + 1;
}

void out(long long x) {
  if (!x) {
    return;
  }
  if (nodes[x].rev) {
    pushdown(x);
  }
  out(nodes[x].lch);
  // write(raw[x]);
  write(x);
  putchar(' ');
  out(nodes[x].rch);
}

int main() {
  totN = read();
  totM = read();
  srand(time(0));
  for (int i = 1; i <= totN; ++i) {
    // long long x = read();
    // raw[i] = x;

    insert(i);
    // nodes[i].val = i;
  }
  for (int i = 1; i <= totM; ++i) {
    long long l, r;
    l = read();
    r = read();
    split(rot, l - 1, X, Y);
    split(Y, r - l + 1, W, Z);
    nodes[W].rev ^= true;
    rot = merge(X, merge(W, Z));
  }
  out(rot);
  return 0;
} // Thomitics Code
```

## [NOI2004] 郁闷的出纳员

### 题目

![](https://pic.imgdb.cn/item/60fd7aab5132923bf8d79c90.png)

![](https://pic.imgdb.cn/item/60fd7ab55132923bf8d7ca97.png)

### 思路

由于只有全局加减操作，可以考虑改为更改工资下限（当然新加入员工的时候要算一下他当前对应的工资）。

然后每次操作后找出所有 < 下限的直接扔掉即可。

对于FHQ来说很简单，只要拆开后取其中一遍就可以了。

### 代码

```cpp
#include <bits/stdc++.h>
#include <cstdio>
#define INF 999999999
#define arand() rand() % 114514
#define mrg() rot = merge(X, Y)

using namespace std;

inline long long read() {
  long long x = 0;
  int f = 1;
  char ch = getchar();
  while (ch < '0' || ch > '9') {
    if (ch == '-')
      f = -1;
    ch = getchar();
  }
  while (ch >= '0' && ch <= '9') {
    x = (x << 1) + (x << 3) + (ch ^ 48);
    ch = getchar();
  }
  return x * f;
}
void write(const long long &x) {
  if (!x) {
    putchar('0');
    return;
  }
  char f[100];
  long long tmp = x;
  if (tmp < 0) {
    tmp = -tmp;
    putchar('-');
  }
  long long s = 0;
  while (tmp > 0) {
    f[s++] = tmp % 10 + '0';
    tmp /= 10;
  }
  while (s > 0) {
    putchar(f[--s]);
  }
}
inline double dread() {
  double r;
  double x = 0, t = 0;
  int s = 0, f = 1;
  char c = getchar();
  for (; !isdigit(c); c = getchar()) {
    if (c == '-') {
      f = -1;
    }
    if (c == '.') {
      goto readt;
    }
  }
  for (; isdigit(c) && c != '.'; c = getchar()) {
    x = x * 10 + c - '0';
  }
readt:
  for (; c == '.'; c = getchar())
    ;
  for (; isdigit(c); c = getchar()) {
    t = t * 10 + c - '0';
    ++s;
  }
  r = (x + t / pow(10, s)) * f;
  return r;
}

inline void dwrite(long long x) {
  if (x == 0) {
    putchar(48);
    return;
  }
  int bit[20], p = 0, i;
  for (; x; x /= 10)
    bit[++p] = x % 10;
  for (i = p; i > 0; --i)
    putchar(bit[i] + 48);
}
inline void write(double x, int k) {
  static int n = pow(10, k);
  if (x == 0) {
    putchar('0');
    putchar('.');
    for (int i = 1; i <= k; ++i)
      putchar('0');
    return;
  }
  if (x < 0)
    putchar('-'), x = -x;
  long long y = (long long)(x * n) % n;
  x = (long long)x;
  dwrite(x), putchar('.');
  int bit[10], p = 0, i;
  for (; p < k; y /= 10)
    bit[++p] = y % 10;
  for (i = p; i > 0; i--)
    putchar(bit[i] + 48);
}

const long long maxN = 100090;

struct Node {
  long long lch, rch;
  long long val, siz;
  long long rnd;
} nodes[maxN];

long long X, Y, Z;
long long rot;
long long cnt;
long long totN;
long long totMIN;
long long totTAG;
long long cntL;

void update(long long nowX) {
  nodes[nowX].siz = nodes[nodes[nowX].lch].siz + nodes[nodes[nowX].rch].siz + 1;
}

long long merge(long long Atree, long long Btree) {
  //	puts("qwq");
  if ((!Atree) || (!Btree)) {
    return Atree + Btree;
  } else if (nodes[Atree].rnd < nodes[Btree].rnd) {
    nodes[Atree].rch = merge(nodes[Atree].rch, Btree);
    update(Atree);
    return Atree;
  } else {
    nodes[Btree].lch = merge(Atree, nodes[Btree].lch);
    update(Btree);
    return Btree;
  }
}

void split(long long splitX, long long K, long long &Xtree, long long &Ytree) {
  //	printf("%lld\n",splitX);
  if (!splitX) {
    Xtree = Ytree = 0;
    return;
  } else {
    if (nodes[splitX].val <= K) {
      Xtree = splitX;
      split(nodes[splitX].rch, K, nodes[splitX].rch, Ytree);
    } else {
      Ytree = splitX;
      split(nodes[splitX].lch, K, Xtree, nodes[splitX].lch);
    }
    update(splitX);
  }
}
long long Kth(long long KX, long long K) {
  while (1) {
    if (K <= nodes[nodes[KX].lch].siz) {
      KX = nodes[KX].lch;
    } else if (K == nodes[nodes[KX].lch].siz + 1) {
      return KX;
    } else {
      K -= nodes[nodes[KX].lch].siz + 1;
      KX = nodes[KX].rch;
    }
  }
}

void del(long long delX) {
  split(rot, delX, X, Z);
  split(X, delX - 1, X, Y);
  Y = merge(nodes[Y].lch, nodes[Y].rch);
  rot = merge(merge(X, Y), Z);
}

void insert(long long nowX) {
  split(rot, nowX, X, Y);
  ++cnt;
  nodes[cnt].lch = nodes[cnt].rch = 0;
  nodes[cnt].val = nowX;
  nodes[cnt].siz = 1;
  nodes[cnt].rnd = arand();
  rot = merge(merge(X, cnt), Y);
}

long long pre(long long nowX) {
  split(rot, nowX - 1, X, Y);
  return Kth(X, nodes[X].siz);
}
long long nxt(long long nowX) {
  split(rot, nowX, X, Y);
  return Kth(Y, 1);
}
long long thK(long long nowX) {
  split(rot, nowX - 1, X, Y);
  return nodes[X].siz + 1;
}
void checkoff() {
  split(rot, totMIN - 1 - totTAG, X, Y);
  rot = Y;
  cntL += nodes[X].siz;
}

int main() {
  totN = read();
  totMIN = read();
  char readX;
  long long readY;
  while (totN--) {
    readX = getchar();
    while (readX != 'I' && readX != 'A' && readX != 'S' && readX != 'F') {
      readX = getchar();
    }
    readY = read();
    if (readX == 'I') {
      if (readY < totMIN) {
        continue;
      }
      insert(readY - totTAG);
      X = Y = 0;
    } else if (readX == 'A') {
      totTAG += readY;
      checkoff();
      X = Y = 0;
    } else if (readX == 'S') {
      totTAG -= readY;
      checkoff();
      X = Y = 0;
    } else if (readX == 'F') {
      if (readY > nodes[rot].siz) {
        puts("-1");
        continue;
      }
      write(nodes[Kth(rot, nodes[rot].siz - readY + 1)].val + totTAG);
      putchar('\n');
    }
  }
  write(cntL);
  putchar('\n');
  return 0;
}
```

## [SHOI2009]会场预约

### 题目

![](https://pic.imgdb.cn/item/60fd7b9d5132923bf8db8e80.png)

![](https://pic.imgdb.cn/item/60fd7ba75132923bf8dbb824.png)

### 思路

题意就是叫你维护一条时间轴，支持2种操作:

1. 删除当前时间轴上所有与新区间有交的区间输出删除个数，然后插入新区间。

2. 查询当前时间轴上的区间个数。

很容易考虑到将所有的区间按照两个端点双关键字排序(先左还是右没有什么影响)。

当插入一个区间时，我们找到最后一个在新区间之前与新区间无交的区间与第一个在新区间后与新区间无交的区间，然后计算它们之间的区间个数，返回后删除，再插入新区间。

查询就直接查询时间轴上的区间个数。

无旋treap(fhq treap)实现方法
首先，我们需要支持查找前驱和查找后继的操作，这是平衡树基础操作，不多赘述。接下来对于找到的前驱与后继的位置，我们在此执行split操作，将整棵treap分为3部分，即为删去的各区间之前，删去的区间，删去的区间之后，然后返回的个数直接就是删去区间这一部分的$root$的$size$，然后再插入新区间后直接与删去区间之前与删去区间之后合并即可。

操作2就直接返回整棵treap的$root$的$size$即可。

### 代码

```cpp
#include <stdio.h>
#define getchar()                                                              \
  (S == TT && (TT = (S = BB) + fread(BB, 1, 1 << 15, stdin), TT == S) ? EOF    \
                                                                      : *S++)
char BB[1 << 15], *S = BB, *TT = BB;
inline int in() {
  int x = 0;
  bool f = 0;
  char c;
  for (; (c = getchar()) < '0' || c > '9'; f = c == '-')
    ;
  for (x = c - '0'; (c = getchar()) >= '0' && c <= '9';
       x = (x << 3) + (x << 1) + c - '0')
    ;
  return f ? -x : x;
}
namespace Treap {
inline int Rand() {
  static int x = 23333;
  return x ^= x << 13, x ^= x >> 17, x ^= x << 5;
}
struct task {
  int lt, rt;
};
struct node {
  node *ls, *rs;
  task v;
  int pri, sz;
  node(task a) : v(a) {
    ls = rs = NULL;
    pri = Rand();
    sz = 1;
  }
  inline void up() { sz = (ls ? ls->sz : 0) + (rs ? rs->sz : 0) + 1; }
} * root;
struct Droot {
  node *a, *b;
};
inline int Size(node *x) { return x ? x->sz : 0; }
node *merge(node *a, node *b) {
  if (!a)
    return b;
  if (!b)
    return a;
  if (a->pri < b->pri) {
    a->rs = merge(a->rs, b);
    a->up();
    return a;
  } else {
    b->ls = merge(a, b->ls);
    b->up();
    return b;
  }
}
Droot split(node *a, int k) {
  Droot y;
  if (Size(a->ls) >= k) {
    y = split(a->ls, k);
    a->ls = y.b;
    a->up();
    y.b = a;
  } else {
    y = split(a->rs, k - Size(a->ls) - 1);
    a->rs = y.a;
    a->up();
    y.a = a;
  }
  return y;
}
int find_pre(node *x, task a) {
  if (x->v.rt < a.lt)
    return find_pre(x->rs, a) + Size(x->ls) + 1;
  return find_pre(x->ls, a);
}
int find_nxt(node *x, task a) {
  if (x->v.lt > a.rt)
    return find_nxt(x->ls, a);
  return find_nxt(x->rs, a) + Size(x->ls) + 1;
}
inline int new_task() {
  task a;
  a.lt = in(), a.rt = in();
  int L = find_pre(root, a), R = find_nxt(root, a);
  Droot y = split(root, R);
  Droot x = split(y.a, L);
  int ans = Size(x.b);
  node *newd = new node(a);
  root = merge(merge(x.a, newd), y.b);
  return ans;
}
inline int Get_Ans() { return Size(root); }
} 
int main() {
  int q = in();
  while (q--) {
    char op = getchar();
    while (op != 'A' && op != 'B')
      op = getchar();
    if (op == 'A')
      printf("%d\n", Treap::new_task());
    else
      printf("%d\n", Treap::Get_Ans());
  }
}
```

## [HNOI2012]永无乡

### 题目

![](https://pic.imgdb.cn/item/60fd7f415132923bf8eb3e77.png)

![](https://pic.imgdb.cn/item/60fd7f495132923bf8eb6330.png)

### 思路

使用启发式合并即可。

启发式合并怎么写呢？

```cpp
void dfs(int x,int &y)
{
    if(!x)return ;
    dfs(son[x][0],y);
    dfs(son[x][1],y);
    son[x][0]=son[x][1]=0;
    insert(y,x);
}
```

这样就好了。

其他的和普通平衡树没什么区别了。

### 代码

```cpp
#include <algorithm>
#include <bitset>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>
#include <iostream>
#include <map>
#include <queue>
#define ll long long
#define maxn 100005
using namespace std;
int n, m, asd, cnt, rt[maxn], sz[maxn], siz[maxn], son[maxn][2], pri[maxn],
    val[maxn], ys[maxn];
int find(int x) {
  if (rt[x] == x)
    return x;
  rt[x] = find(rt[x]);
  return rt[x];
}
void upda(int x) { siz[x] = siz[son[x][0]] + siz[son[x][1]] + 1; }
void spli(int now, int k, int &x, int &y) {
  if (!now)
    x = y = 0;
  else {
    if (val[now] <= k)
      x = now, spli(son[now][1], k, son[now][1], y);
    else
      y = now, spli(son[now][0], k, x, son[now][0]);
    upda(now);
  }
}
int merge(int x, int y) {
  if (!x || !y)
    return x + y;
  if (pri[x] < pri[y]) {
    son[x][1] = merge(son[x][1], y);
    upda(x);
    return x;
  } else {
    son[y][0] = merge(x, son[y][0]);
    upda(y);
    return y;
  }
}
void insert(int &root, int x) {
  int a, b;
  int v = val[x];
  spli(root, v, a, b);
  root = merge(merge(a, x), b);
}
void dfs(int x, int &y) {
  if (!x)
    return;
  dfs(son[x][0], y);
  dfs(son[x][1], y);
  son[x][0] = son[x][1] = 0;
  insert(y, x);
}
int hebing(int x, int y) {
  if (siz[x] > siz[y])
    swap(x, y);
  dfs(x, y);
  return y;
}
int kth(int now, int k) {
  while (1) {
    if (k <= siz[son[now][0]])
      now = son[now][0];
    else if (k == siz[son[now][0]] + 1)
      return now;
    else
      k -= siz[son[now][0]] + 1, now = son[now][1];
  }
}
int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; ++i)
    scanf("%d", &val[i]), rt[i] = i, pri[i] = rand(), siz[i] = sz[i] = 1,
                          ys[val[i]] = i;
  cnt = n;
  for (int i = 1, a, b, c; i <= m; ++i) {
    scanf("%d%d", &a, &b);
    if (find(a) == find(b))
      continue;
    c = hebing(rt[a], rt[b]);
    rt[find(a)] = rt[find(b)] = c;
    rt[c] = c;
  }
  scanf("%d", &asd);
  char ch[5];
  for (int i = 1, a, b, c; i <= asd; ++i) {
    scanf("%s%d%d", ch, &a, &b);
    if (ch[0] == 'B') {
      if (find(a) == find(b))
        continue;
      c = hebing(rt[a], rt[b]);
      rt[find(a)] = rt[find(b)] = c;
      rt[c] = c;
    } else {
      if (siz[find(a)] < b) {
        printf("-1\n");
        continue;
      }
      printf("%d\n", ys[val[kth(find(a), b)]]);
    }
  }
  return 0;
}
```

## [NOI2005] 维护数列

### 题目

![](https://pic.imgdb.cn/item/60fd82225132923bf8f80a07.png)

……