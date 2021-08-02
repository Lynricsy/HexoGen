## 最大流&最小割

### 割

对于一个网络流图 $G=(V,E)$，其割的定义为一种 **点的划分方式**：将所有的点划分为 $S$ 和 $T=V-S$ 两个集合，其中源点 $s\in S$，汇点 $t\in T$。

### 割的容量

我们的定义割 $(S,T)$ 的容量 $c(S,T)$ 表示所有从 $S$ 到 $T$ 的边的容量之和，即 $c(S,T)=\sum_{u\in S,v\in T}c(u,v)$。当然我们也可以用 $c(s,t)$ 表示 $c(S,T)$。

### 最小割

最小割就是求得一个割 $(S,T)$ 使得割的容量 $c(S,T)$ 最小。

### 最大流

学了好久了，这里就不写了。总体来说也就这点东西：

 - 一个有向图，存在源点 S 和汇点 T，每条边有一个流量，求从 S 到 T 最多能经过多少流量。

 - 最大流-最小割定理：最大流=最小割。

### 代码

```cpp
/**************************************************************
 * Problem: Luogu P3376 
 * Author: 芊枫
 * Date: 2021 Aug 02
 * Algorithm: 费用流
**************************************************************/
#include <bits/stdc++.h>
#define INF 0x3f3f3f3f3f3f3f3f
#define IINF 0x3f3f3f3f

using namespace std;

inline long long read()
{
	long long x = 0;
	int f = 1;
	char ch = getchar();
	while (ch < '0' || ch > '9')
	{
		if (ch == '-')
			f = -1;
		ch = getchar();
	}
	while (ch >= '0' && ch <= '9')
	{
		x = (x << 1) + (x << 3) + (ch ^ 48);
		ch = getchar();
	}
	return x * f;
}
void write(const long long &x)
{
	if (!x)
	{
		putchar('0');
		return;
	}
	char f[100];
	long long tmp = x;
	if (tmp < 0)
	{
		tmp = -tmp;
		putchar('-');
	}
	long long s = 0;
	while (tmp > 0)
	{
		f[s++] = tmp % 10 + '0';
		tmp /= 10;
	}
	while (s > 0)
	{
		putchar(f[--s]);
	}
}

const long long maxN = 100090;
long long totN;
long long totM;
long long totT;
long long totS;
long long DIS[maxN];
long long now[maxN];
long long maxFLOW;

struct Edge
{
	long long to;
	long long nxt;
	long long flow;
} edges[maxN];
long long head[maxN];
long long cnt_edges = 1;

void add_edge(long long x, long long y, long long flow)
{
	++cnt_edges;
	edges[cnt_edges].nxt = head[x];
	head[x] = cnt_edges;
	edges[cnt_edges].to = y;
	edges[cnt_edges].flow = flow;

	++cnt_edges;
	edges[cnt_edges].nxt = head[y];
	head[y] = cnt_edges;
	edges[cnt_edges].to = x;
	edges[cnt_edges].flow = 0;
}

bool BFS()
{
	memset(DIS, 0x3f, sizeof DIS);
	queue<long long> Q;
	DIS[totS] = 0;
	now[totS] = head[totS];
	Q.push(totS);
	while (!Q.empty())
	{
		long long nowX = Q.front();
		Q.pop();
		for (int i = head[nowX]; i; i = edges[i].nxt)
		{
			long long vir = edges[i].to;
			if ((DIS[vir] != INF) || (!edges[i].flow))
			{
				continue;
			}
			Q.push(vir);
			DIS[vir] = DIS[nowX] + 1;
			now[vir] = head[vir];
			if (vir == totT)
			{
				return true;
			}
		}
	}
	return false;
}

long long DFS(long long nowX, long long inFLOW)
{
	if ((nowX == totT) || (!inFLOW))
	{
		return inFLOW;
	}
	long long outFLOW = 0;
	long long nowOUT;
	for (int i = now[nowX]; i && inFLOW; i = edges[i].nxt)
	{
		long long vir = edges[i].to;
		now[nowX] = i;
		if ((DIS[vir] != DIS[nowX] + 1) || (!edges[i].flow))
		{
			continue;
		}
		nowOUT = DFS(vir, min(edges[i].flow, inFLOW));
		inFLOW -= nowOUT;
		outFLOW += nowOUT;
		edges[i].flow -= nowOUT;
		edges[i ^ 1].flow += nowOUT;
	}
	return outFLOW;
}

int main()
{
	totN = read();
	totM = read();
	totS = read();
	totT = read();
	for (int i = 1, x, y, z; i <= totM; ++i)
	{
		x = read();
		y = read();
		z = read();
		add_edge(x, y, z);
	}
	while (BFS())
	{
		maxFLOW += DFS(totS, INF);
	}
	write(maxFLOW);
	return 0;
} //Thomitics Code
```

## 最小费用最大流

### 代码

```cpp
#include <bits/stdc++.h>
#define INF 999999999

using namespace std;

inline long long read()
{
	long long x = 0;
	int f = 1;
	char ch = getchar();
	while (ch < '0' || ch > '9')
	{
		if (ch == '-')
			f = -1;
		ch = getchar();
	}
	while (ch >= '0' && ch <= '9')
	{
		x = (x << 1) + (x << 3) + (ch ^ 48);
		ch = getchar();
	}
	return x * f;
}
void write(const long long &x)
{
	if (!x)
	{
		putchar('0');
		return;
	}
	char f[100];
	long long tmp = x;
	if (tmp < 0)
	{
		tmp = -tmp;
		putchar('-');
	}
	long long s = 0;
	while (tmp > 0)
	{
		f[s++] = tmp % 10 + '0';
		tmp /= 10;
	}
	while (s > 0)
	{
		putchar(f[--s]);
	}
}
inline double dread()
{
	double r;
	double x = 0, t = 0;
	int s = 0, f = 1;
	char c = getchar();
	for (; !isdigit(c); c = getchar())
	{
		if (c == '-')
		{
			f = -1;
		}
		if (c == '.')
		{
			goto readt;
		}
	}
	for (; isdigit(c) && c != '.'; c = getchar())
	{
		x = x * 10 + c - '0';
	}
	readt:
	for (; c == '.'; c = getchar())
		;
	for (; isdigit(c); c = getchar())
	{
		t = t * 10 + c - '0';
		++s;
	}
	r = (x + t / pow(10, s)) * f;
	return r;
}

inline void dwrite(long long x)
{
	if (x == 0)
	{
		putchar(48);
		return;
	}
	int bit[20], p = 0, i;
	for (; x; x /= 10)
		bit[++p] = x % 10;
	for (i = p; i > 0; --i)
		putchar(bit[i] + 48);
}
inline void write(double x, int k)
{
	static int n = pow(10, k);
	if (x == 0)
	{
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
long long totN;
long long totM;
long long totT;
long long totS;
long long head[maxN];
long long cnt_edges = 1;
long long DIS[maxN];
bool vis[maxN];
long long incf[maxN];
long long pre[maxN];
long long maxFLOW;
long long minCOST;

struct Edge
{
	long long nxt;
	long long to;
	long long flow;
	long long val;
}edges[maxN*3];

inline void add_edge(long long x,long long y,long long f,long long w)
{
	++cnt_edges;
	edges[cnt_edges].nxt = head[x];
	head[x] = cnt_edges;
	edges[cnt_edges].to = y;
	edges[cnt_edges].val = w;
	edges[cnt_edges].flow = f;

	++cnt_edges;
	edges[cnt_edges].nxt = head[y];
	head[y] = cnt_edges;
	edges[cnt_edges].to = x;
	edges[cnt_edges].val = -w;
	edges[cnt_edges].flow = 0;
}

inline bool SPFA()
{
	memset(vis, 0, sizeof vis);
	memset(DIS, 0x3f, sizeof DIS);
	queue<long long> Q;
	vis[totS] = true;
	DIS[totS] = 0;
	incf[totS] = 0x3f3f3f3f3f3f3f3f;
	Q.push(totS);
	while (!Q.empty())
	{
		int x = Q.front();
		Q.pop();
		vis[x] = 0;
		for (int i = head[x]; i; i = edges[i].nxt)
		{
			if (!edges[i].flow)
			{
				continue;
			}
			long long ver = edges[i].to;
			if (DIS[ver] > edges[i].val + DIS[x])
			{
				DIS[ver] = edges[i].val + DIS[x];
				pre[ver] = i;
				incf[ver] = min(incf[x], edges[i].flow);
				if (!vis[ver])
				{
					Q.push(ver);
				}
			}
		}
	}
	if (DIS[totT] == 0x3f3f3f3f3f3f3f3f)
	{
		return false;
	}
	return true;
}

void MCMF()
{
	while (SPFA())
	{
		long long ti = totT;
		maxFLOW += incf[totT];
		minCOST += incf[totT] * DIS[totT];
		int nowEDGE;
		while (ti != totS)
		{
			nowEDGE = pre[ti];
			edges[nowEDGE].flow -= incf[totT];
			edges[nowEDGE ^ 1].flow += incf[totT];
			ti = edges[nowEDGE ^ 1].to;
		}
	}
}

int main()
{
	totN = read();
	totM = read();
	totS = read();
	totT = read();
	for (int i = 1, x, y, w, f; i <= totM; ++i)
	{
		x = read();
		y = read();
		f = read();
		w = read();
		add_edge(x, y, f, w);
	}
	MCMF();
	write(maxFLOW);
	putchar(' ');
	write(minCOST);
	return 0;
} //Thomitics Code
```

## 最大权闭合子图

### 问题

一个有向图，选择一个点就必须选择其后继点，且选择每个点有一个花费或者奖励。求总奖励最大值。

### 思路

其实仔细一观察就可以发现，这个跟最小割有着很大的关系。

建 s 连向所有正权点，负权点连向 t，假设初始全选所有正权点，减掉最小割就是答案。

## Hall定理

判定二分图是否存在完美匹配的定理。设 $S$ 表示左侧的一个点集，$N(S)$ 表示从 $S$ 连出的边另一端构成的点集。

Hall 定理：二分图存在完美匹配当且仅当对于任意 $S$，$|N(S)|>=|S|$。

## T1

### 题目

现在小朋友们最喜欢的&quot;喜羊羊与灰太狼&quot;,话说灰太狼抓羊不到，但抓兔子还是比较在行的，而且现在的兔子还比较笨，它们只有两个窝，现在你做为狼王，面对下面这样一个网格的地形：

 ![](https://cdn.luogu.com.cn/upload/pic/11942.png) 

左上角点为 $(1,1)$, 右下角点为 $(N,M)$ (上图中 $N=3$, $M=4$).有以下三种类型的道路:

1. $(x,y)\rightleftharpoons(x+1,y)$

2. $(x,y)\rightleftharpoons(x,y+1)$

3. $(x,y)\rightleftharpoons(x+1,y+1)$

道路上的权值表示这条路上最多能够通过的兔子数，道路是无向的。左上角和右下角为兔子的两个窝，开始时所有的兔子都聚集在左上角 $(1,1)$ 的窝里，现在它们要跑到右下角 $(N,M)$ 的窝中去，狼王开始伏击这些兔子。当然为了保险起见，如果一条道路上最多通过的兔子数为 $K$，狼王需要安排同样数量的 $K$ 只狼，才能完全封锁这条道路，你需要帮助狼王安排一个伏击方案，使得在将兔子一网打尽的前提下，参与的狼的数量要最小。因为狼还要去找喜羊羊麻烦。

### 思路

首先，我们看到这张图是一个平面图，也就是说这个图中的所有边两两不向交。这有什么用呢？我们继续想。他说要封锁道路，其实就是要在这个网格图上面画一条曲折的线，将它分成两部分。我们建出它的对偶图（就是把每个被分成的平面当做一个点，并把所有的点都连接起来，边权为这条边切到的原图边权），然后求最小割即可。

对偶图，平面图，最小割，最短路径

## T2

### 题目

W 教授正在为国家航天中心计划一系列的太空飞行。每次太空飞行可进行一系列商业性实验而获取利润。现已确定了一个可供选择的实验集合 $ E = \{ E_1, E_2, \cdots, E_m \} $，和进行这些实验需要使用的全部仪器的集合 $ I = \{ I_1, I_2, \cdots, I_n \} $。实验 $ E_j $ 需要用到的仪器是 $ I $ 的子集 $ R_j \subseteq I $。

配置仪器 $ I_k $ 的费用为 $ c_k $ 美元。实验 $ E_j $ 的赞助商已同意为该实验结果支付 $ p_j $ 美元。W 教授的任务是找出一个有效算法，确定在一次太空飞行中要进行哪些实验并因此而配置哪些仪器才能使太空飞行的净收益最大。这里净收益是指进行实验所获得的全部收入与配置仪器的全部费用的差额。

对于给定的实验和仪器配置情况，编程找出净收益最大的试验计划。

### 思路

看到题目可以感觉到有一种熟悉的感觉……选择一种实验，就要选择它使用的所有的仪器，这不就是最大权闭合子图嘛！

跑一个最小割即可。

## T3

### 题目

假设一个试题库中有 $n$ 道试题。每道试题都标明了所属类别。同一道题可能有多个类别属性。现要从题库中抽取 $m$ 道题组成试卷。并要求试卷包含指定类型的试题。试设计一个满足要求的组卷算法。

对于给定的组卷要求，计算满足要求的组卷方案。

### 思路

从 s 向每个试卷连容量为 1 的边，从每个试卷向所属类型连容量为 1 的边，从每个类型向 t 连所需要的个数。跑最大流。

## T4 

### 题目

给定有向图 $G=(V,E)$ 。设 $P$ 是 $G$ 的一个简单路(顶点不相交)的集合。如果 $V$ 中每个定点恰好在$P$的一条路上，则称 $P$ 是 $G$ 的一个路径覆盖。$P$中路径可以从 $V$ 的任何一个定点开始，长度也是任意的，特别地，可以为 $0$ 。$G$ 的最小路径覆盖是 $G$ 所含路径条数最少的路径覆盖。设计一个有效算法求一个 DAG (有向无环图) $G$ 的最小路径覆盖。

### 思路

这个题说要找到一些路径覆盖整个图。我们可以想到路径其实就是一个点连接着下一个点组成的。

我们可以想，先对于原图的每一个点，建立一条自己连向自己的边。此后，我们每启用一条边，就可以合并两个路径，使路径数-1。首先将每个点拆成出点和入点，我们进行按照原图上的边建边，二分图最大匹配即可，答案即为n减去拆点二分图最大匹配数。

## T5

### 题目

假设有 $n$ 根柱子，现要按下述规则在这 $n$ 根柱子中依次放入编号为 $1$，$2$，$3$，...的球“

1. 每次只能在某根柱子的最上面放球。

2. 同一根柱子中，任何 $2$ 个相邻球的编号之和为完全平方数。

试设计一个算法，计算出在 $n$ 根柱子上最多能放多少个球。例如，在 $4$ 根柱子上最多可放 $11$ 个球。

对于给定的 $n$，计算在 $n$ 根柱子上最多能放多少个球。

### 思路

观察本题，对于一个进来的编号的球，他有两种情况，

1.放在某个和他组成平方数的球的后面

2.独立门户

我们要使这两种情况都合法，这样我们可以发挥网络流调整的优势

对于两种情况，为了使结果合法，我们姑且先将它和t直接相连

2情况显然要和s相连，1情况要和前面的点相连

又和s相连，又和t相连，还要和别的点相连，显然一个点是不够的

我们把一个点分开来，分开来的两个点不能相连，否则最大流就没有意义了

直接s-u-u'-t显然没有调整的作用

那么我们将u和s相连，u’和t相连，为了满足第一种条件

满足关系的两个点u、v，建立v-u'

那么在图中跑最大流算法即可

## T6

### 题目

<p>限制条件是——如果要取某一个方格，那么禁止取相邻的<strong>四个</strong>方格，不限制<strong>其它所有</strong>方格。</p>
<p>所以猜测，从禁止的角度考虑才会更高效。也就是说，先选中所有方格，再想办法删去<strong>权值和</strong>尽量小的一批方格。</p>
<hr />
相邻的概念是，横坐标或纵坐标中的一个相差 $1$，所以两点的横纵坐标之和<strong>奇偶性不同</strong>。
<p>于是，横纵坐标和的奇偶性相同的两个点肯定不互斥（奇偶性不同的<strong>可能</strong>互斥）。把互斥的点连边的话，会形成一个二分图。</p>
<p>但先不管这个。</p>
<p>要想办法构造一个模型，它：</p>
<ul>
<li>
<p><strong>能删掉一个元素，表示不取这个方格；</strong></p>
</li>
<li>
<p><strong>删掉的代价为方格的权值；</strong></p>
</li>
<li>
<p><strong>要么删掉的总是保证策略最优的，要么能反悔；</strong></p>
</li>
<li>
<p><strong>最终状态为：没有互斥的方格了。</strong></p>
</li>
</ul>
<p>好像能发现对应的模型了，大概是<strong>图</strong>一类的东西：</p>
<ul>
<li>
<p>删掉连向方格的边</p>
</li>
<li>
<p>边权为方格的权值</p>
</li>
<li>
<p>网络流搞一搞</p>
</li>
<li>
<p><strong>割</strong></p>
</li>
</ul>
<hr />
<p>怎么构造一个合适的图呢？最好能利用上面的二分图。并不容易想到：</p>
<p>源点连向二分图的一个点集（横纵坐标之和为奇数的那些方格），边权为点权。<strong>删一条边表示不取这个方格。</strong></p>
<p>二分图的另一个点集连向超级汇，边权还是点权。删边也表示不取此点。</p>
二分图内部的边，连接着互斥的点。边权全部赋为 $inf$，以保证在最小割中不被删。啥意思？
<p>想象一下通过某种方式，求出了该图最小割。</p>

 - 因为是最小割，所以中间的 $inf$ 边没删，删掉的都是源点连出的边，或连入汇点的边。
 - <strong>因此这个割能够确切表示：不取某些方格。</strong>（删掉中间边本来就没有意义，不能表示对方格的操作；只有两侧的边具有意义）</p>


 - 因为是割，所以图不连通。<strong>不连通</strong>，就已经保证没有取到任何互斥的方格（假设图中还有互斥方格，也就是两者在图中各自所属的边还没删，再因为它们中间的 $inf$ 边也没删，所以图还是连通的）
  
<p>　　　　<img src="https://cdn.luogu.com.cn/upload/pic/47260.png
" alt="" /></p>
<p>最后只需知道<strong>最大流 = 最小割</strong>就好了。</p>

## 一个经典模型

### 问题

![](https://cdn.jsdelivr.net/gh/Thomitics/PicBed@master/pics/20210802210651.png)

### 思路



## T7

### 题目

![](https://cdn.jsdelivr.net/gh/Thomitics/PicBed@master/pics/20210802211731.png)

### 思路

先二分答案mid，将所有边的权值变成（mid*边权），然后相当于跑一个点权为正，边权为负的最大权闭合图，然后思考怎么建图

1.从S向所有点连一条容量为点权的边，这是由最大权闭合图的思想得到
2.从所有点向相邻的点连一条容量为（mid*边权）的边（具体地说，是两条有向边），这个在两个点都选或都不选的时候没什么用，但是如果一个选另一个不选，那么就相当于要付出这条边边权的代价
3.从所有边界上的点向T连一条容量为（mid*边权）的边，这个可以理解为在网格外面还有一圈的点，这些点必须不选，也就相当于这些点可以和T看成一个点。那么如果选了边界上的点，必须付出这些边界上的边权 的代价。

这样最优代价和就变成了（所有正权和-最小割），如果是正值，就调整l，否则调整r

如果我们选出了这样一些点，那么他们和相邻的不选的点（包括网格外的点）都要用边隔开，也就是说我们要付出这些边的代价，（2）（3）中连的边可以满足这个要求；此时，其余的点都不能选，意味着他们都要和S隔开，意味着我们要付出这些点点权的代价，（1）中连的边可以满足这些要求；又因为所有不选的点一定会间接的和网格外的点相连，那么它们自然地被划分到了T集合中，当然，选的点也已经被划分到了S集合中，此时我们已经成功的将所有代价都 割 掉了


## T8

### 题目

一个餐厅在相继的 $N$ 天里,每天需用的餐巾数不尽相同。假设第 $i$ 天需要 $r_i$块餐巾( i=1,2,...,N)。餐厅可以购买新的餐巾,每块餐巾的费用为 $p$ 分;或者把旧餐巾送到快洗部,洗一块需 m 天,其费用为 f 分;或者送到慢洗部,洗一块需 $n$ 天($n\geq m$),其费用为 $s$ 分($s\leq f$)。

每天结束时,餐厅必须决定将多少块脏的餐巾送到快洗部,多少块餐巾送到慢洗部,以及多少块保存起来延期送洗。但是每天洗好的餐巾和购买的新餐巾数之和,要满足当天的需求量。

试设计一个算法为餐厅合理地安排好 $N$ 天中餐巾使用计划,使总的花费最小。编程找出一个最佳餐巾使用计划。

### 思路

这是一道最小费用（费用指单价）最大流的题目。

首先，我们拆点，将一天拆成晚上和早上，每天晚上会受到脏餐巾（来源：当天早上用完的餐巾，在这道题中可理解为从原点获得），每天早上又有干净的餐巾（来源：购买、快洗店、慢洗店）。

1. 从原点向每一天晚上连一条流量为当天所用餐巾x，费用为0的边，表示每天晚上从起点获得x条脏餐巾。

2. 从每一天早上向汇点连一条流量为当天所用餐巾x，费用为0的边，每天白天,表示向汇点提供x条干净的餐巾,流满时表示第i天的餐巾够用 。 

3. 从每一天晚上向第二天晚上连一条流量为$INF$，费用为$0$的边，表示每天晚上可以将脏餐巾留到第二天晚上（注意不是早上，因为脏餐巾在早上不可以使用）。

4. 从每一天晚上向这一天+快洗所用天数$t1$的那一天早上连一条流量为$INF$，费用为快洗所用钱数的边，表示每天晚上可以送去快洗部,在地$i+t1$天早上收到餐巾 。

5. 同理，从每一天晚上向这一天+慢洗所用天数$t2$的那一天早上连一条流量为$INF$，费用为慢洗所用钱数的边，表示每天晚上可以送去慢洗部,在地$i+t2$天早上收到餐巾 。

6. 从起点向每一天早上连一条流量为$INF$，费用为购买餐巾所用钱数的边，表示每天早上可以购买餐巾 。 注意，以上6点需要建反向边！3~6点需要做判断（即连向的边必须$\leq n$）

## T9

### 题目

有无穷个硬币，初始有n个正面向上，其余均正面向下。你每次可以选择一个奇质数p，并将连续p个硬币都翻转。

问最小操作次数使得所有硬币均正面向下。

### 思路

考虑差分。差分后1的数量一定为偶数。

然后一次操作$[l,r]$会翻转两端$l-1$与$r$。现在问题变成两两配对使得操作次数尽量少。有三种情况：

1. $|i-j|$是奇质数，那么$1$步即可。

2. $|i-j|$是偶数，那么$2$步即可。（2可以5-3，4可以7-3，>=6的可以哥德巴赫猜想）

3. $|i-j|$是奇非质数，那么$3$步即可。（1可以7-3-3，然后最小的奇合数是9，>=9的可以拆分成3+一个>=6的偶数，可以哥德巴赫猜想）按奇偶变成二分图，希望1的情况尽量多（跑二分图匹配），剩余每边分别尽量用2的情况，最后如果两边没消完就补一次3的情况。

复杂度 $O(n^3)$。

## T10

### 题目 (题目没用，这里直接给输入格式就行)

 The first line of the input is a single integer $t~(t\le 10)$ which is the number of test cases.<br><br>Then $t$ test cases follow.<br>Each test case contains several lines.<br>The first line contains the integer $n~(1\le n\le 256)$ and $m~(1\le m\le 500)$.<br>Here, $n$ corresponds to the number of $8$-digit binary numbers which Fang Fang hates, and $m$ corresponds to the number of supernatural powers.<br>The second line contains $n$ integer numbers $a_1,a_2,\cdots,a_n$ where $0\le a_1,\cdots,a_n\le 255$, which are $8$-digit binary numbers written by decimal representation.<br>The following $m$ lines describe the supernatural powers one per line in two formats.<br>$\cdot$ $P~s~w$: you can eliminate all $8$-digit binary numbers by prefixing the string $s$, with $w~(1\le w\le 1000)$ units of IQ.<br>$\cdot$ $S~s~w$: you can eliminate all $8$-digit binary numbers by suffixing the string $s$, with $w~(1\le w\le 1000)$ units of IQ.

### 思路

考虑将所有讨厌的数建成一个 $\text{Trie}$ 树，然后再将它们的反串也建成一个 $\text{Trie}$ 树，将两个 $\text{Trie}$ 树的对应的叶子节点都连起来。

考虑一次操作是可以将某棵 $\text{Trie}$ 树上的一条边下面的所有数都 ban 掉，可以看成是将那条边断掉，最后要使得两棵 $\text{Trie}$ 树的根不连通。

发现就是个最小割问题，于是将源点和一棵 $\text{Trie}$ 树的根相连，汇点和另一棵 $\text{Trie}$ 树的根相连，每条边附上对应的权值，然后跑一个最小割即可。

## T11

### 题目

![](https://cdn.jsdelivr.net/gh/Thomitics/PicBed@master/pics/20210802220612.png)

### 思路

假设最后有 k 个人选不到，那么就说明任选 t 个人，设他们最大的 li 为 L，最小的 ri 为 R，则需要满足 L+n-R+1+k>=t。

考虑枚举 l，那么就变成在当前的人里选 t 个使得 n-r-t 最小，用线段树维护每个 r 对应的答案，那么加入一个人就相当于区间 -1，然后每次询问全局最小值即可。