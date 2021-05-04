---
title: 树状数组
date: 2021-05-04
tags:
 - 数据结构
categories: 数据结构
description: 关于一种简单的数据结构——树状数组。
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxkcmckquj31kw0w0e5l.jpg
---

<h1>前言</h1>
树状数组，又称二叉索引树，是一种代码简单，应用广泛的神奇数据结构！今天我来带大家详细的了解一下这个神奇的数据结构！！！超级详细，不看后悔哦！

话不多说，我们开始内容！

<h1>普通树状数组</h1>
<h2><strong>概念普及：树状数组的原理</strong></h2>
<img src="https://cdn.luogu.com.cn/upload/image_hosting/rjksv5dw.png" alt="" />
设黑色框内数组为 $A[1]\to A[8]$

那么可以得到以下式子：

$C[1] = A[1];$

$C[2] = A[1] + A[2];$

$C[3] = A[3];$

$C[4] = A[1] + A[2] + A[3] + A[4];$

$C[5] = A[5];$

$C[6] = A[5] + A[6];$

$C[7] = A[7];$

$C[8] = A[1] + A[2] + A[3] + A[4] + A[5] + A[6] + A[7] + A[8];$

我们便可以得到 $C[i] = A[i - 2^k+1] + A[i - 2^k+2] + ... + A[i];$

在这里， $k$为 $i$的二进制中从最低位到高位连续零的长度

那么，如何求出二进制中从最低位到高位连续零的长度呢？

我们需要找最低位的1！！！

如何找最低位的1呢？

我们需要引入<strong>lowbit</strong>

<strong>lowbit</strong> 

<pre><code class="language-cpp">inline int lowbit(int x)
{
    return x&amp;(-x);
}</code></pre>
&amp;运算，即与运算，即按位比较都是1则为1，否则为0。
<strong>lowbit的原理简单说一下：</strong>
在计算机中二进制是以补码存储的。对于 $x(x&gt;0)$,他的补码就是他的本身.
而 $[−x]补为[x]$补连同符号位取反加一之后的结果
所以[-x]补&amp;[x]补刚好就是最低位1的结果
<strong>总结一下规律</strong>：x&amp;(-x)，当x为0时结果为0；x为奇数时，结果为1；x为偶数时，结果为x中2的最大次方的因子。用处呢就是<strong>找最低位的1的位置。</strong>
那么我们树状数组的雏形就搭建好了，看看几个基础函数的用法吧。
（其实我们发现，树状数组其实就是个特殊的前缀和数组！）
<strong>add</strong>
<pre><code class="language-cpp">inline void adds(int x,int y)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    t[i]+=y;
    return ;
}</code></pre>
<strong>query</strong>
<pre><code class="language-cpp">inline int query(int x)
{
    int tot=0;
    for(fint i=x;i;i-=lowbit(i))
    tot+=t[i];
    return tot;
}</code></pre>
<h2>具体用法</h2>
有了上面的模板，我们可以解决基础的实际性问题：
<h3><strong>单点修改+区间查询</strong></h3>
<code>adds(x,y);</code>//在第x个数上加y
<code>query(y)-query(x-1);</code>//求区间x~y的和（<strong>前缀和</strong>思想）。

<a href="https://www.luogu.com.cn/problem/P3374">【模板】树状数组 1</a>
代码：
<pre><code class="language-cpp">signed main()
{
    n=read(),m=read();
    nn=(n&lt;&lt;1);
    for(fint i=1;i&lt;=n;i++)
    a[i]=read(),adda(i,a[i]),addb(i,a[i]*i);
    for(fint i=1;i&lt;=m;i++)
    {
        int x,y;
        scanf("%s",s+1);
        if(s[1]=='Q')
        x=read(),cout&lt;&lt;((x+1)*get_tota(x)-get_totb(x))&lt;&lt;endl;
        else
        x=read(),y=read(),adda(x,y-a[x]),addb(x,(y-a[x])*x),a[x]=y;
    }
    return 0;
}

inline int lowbit(int x)
{
    return x&amp;(-x);
}

inline void adda(int x,int y)
{
    for(fint i=x;i&lt;=p;i+=lowbit(i))
    trea[i]+=y;
    return ;
}

inline void addb(int x,int y)
{
    for(fint i=x;i&lt;=p;i+=lowbit(i))
    treb[i]+=y;
    return ;
}

inline int get_tota(int x)
{
    int ans=0;
    for(fint i=x;i;i-=lowbit(i))
    ans+=trea[i];
    return ans;
}

inline int get_totb(int x)
{
    int ans=0;
    for(fint i=x;i;i-=lowbit(i))
    ans+=treb[i];
    return ans;
}</code></pre>
知道这个，我们就可以求逆序对了，先卖个关子，后面部分有，接着看吧！
<h1>差分树状数组</h1>
<h2><strong>概念普及：一维差分</strong></h2>
差分就是<strong>将数列中的每一项分别与前一项数做差</strong>，例如：
<table>
<thead>
<tr>
<th>原序列</th>
<th>1</th>
<th>2</th>
<th>5</th>
<th>4</th>
<th>7</th>
<th>3</th>
<th>\</th>
</tr>
</thead>
<tbody>
<tr>
<td>差分序列</td>
<td>1</td>
<td>1</td>
<td>3</td>
<td>-1</td>
<td>3</td>
<td>-4</td>
<td>-3</td>
</tr>
</tbody>
</table>
观察表格，我们可以得到以下<strong>规律</strong>：
<table>
<thead>
<tr>
<th><strong>差分序列第一个数和原来的第一个数一样</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>差分序列最后比原序列多一个数，即最后一个数的相反数</strong></td>
</tr>
<tr>
<td><strong>差分序列求前缀和可得原序列</strong></td>
</tr>
<tr>
<td><strong>将原序列区间[L,R]中的元素全部+1，可以转化操作为差分序列L处+1，R+1处-1</strong></td>
</tr>
</tbody>
</table>
根据<strong>规律3</strong>我们可以得到( $s$为差分前缀和数组, $a$为原数组， $c$为差分数组)
<pre><code class="language-cpp">for(fint i=1;i&lt;=n;i++)
s[i]=s[i-1]+c[i];
s[i]=a[i];</code></pre>
<h2>具体实现</h2>
<h3><strong>区间修改+单点查询</strong></h3>
根据上文<strong>规律4</strong>我们可以得到区间修改代码(设区间为 $l，r$)
<pre><code class="language-cpp">adds(l,x),adds(r+1,-x);</code></pre>
<code>query(x)</code>/​/求出第x个数的值

我们设树状数组 $t[i]$表示 $i$是否在序列中出现过(0为没出现过，1为出现过)。

那么我们的 $adds$函数就是把 $i$加入到序列中。

$query$函数就是统计序列中值小于等于 $i$的个数，因为树状数组 $t$维护的是数组 $a$的值，则该求和函数即是用于求下标小于等于 $ i $的数组 $a$的和，而数组 $a$中元素的值要么是 $0$要么是 $1$，所以最后求出来的就是小于等于 $i$的元素的个数。

<a href="https://www.luogu.com.cn/problem/P3368">【模板】树状数组 2</a>
代码:
<pre><code class="language-cpp">signed main()
{
    n=read();
    m=read();
    for(fint i=1;i&lt;=n;i++)
    {
        a[i]=read();
        adds(i,a[i]-a[i-1]);
    }
    int num; 
    int c,d,e;
    for(int i=1;i&lt;=m;i++)
    {
        num=read();
        if(num==1)
        {
            c=read();
            d=read();
            e=read();
            adds(c,e);
            adds(d+1,-e);
        }
        else
        if(num==2)
        {
            c=read();
            cout&lt;&lt;tot(c)&lt;&lt;endl;
        }
    }
    exit(0);
}
inline void adds(int x,int y)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    tree[i]+=y;
}
inline int lowbit(int x)
{
    return x&amp;(-x);
}
inline int tot(int x)
{
    int t=0;
    for(fint i=x;i;i-=lowbit(i))
    t+=tree[i];
    return t;
}</code></pre>
<h3>区间修改+区间查询</h3>
<strong>先来乱搞一下试试</strong>
使用差分数组进行建树,可以轻松进行区间修改
<pre><code class="language-cpp">for(fint i=1;i&lt;=n;i++)
a[i]=read(),adds(i,a[i]-a[i-1]);
int x,y,z;
for(fint i=1;i&lt;=m;i++)
x=read(),y=read(),z=read(),adds(x,z),adds(y+1,-z);</code></pre>
所以我们效仿此前直接对每个点进行求和
<pre><code>for(fint i=1;i&lt;=n;i++)
ans+=query(r)-query(l-1);</code></pre>
$lowbit,adds,query$的函数不变。
<strong>那么，思考一下，这样做对吗？</strong>
答案显然是否定的！！！
由于我们维护的是差分序列，那么意味着 $query$函数在这里维护的是差分序列的前缀和，也就是说<code>query(r)query(l-1)</code>此时求得是 $\sum _{i=1}^r c[i]-\sum _{i=1}^{l-1}c[i]$(c[i]表示差分数组)，根据此前说到的差分数组求前缀和即为原数组，那么此时该式子化为了 $a[r]-a[l]$恭喜你成功求得两点之差！，此时我们如果想求区间加和，显然需要在套一层前缀和，达到差分的前缀和的前缀和=原数组的前缀和（doge）这显然会让复杂度！BOOM！
那么我们不禁会想，如果能把这二次前缀和的操作也放入树状数组中那该多好呀！ 

<strong>到底应该如何完成呢？</strong>

别急，我们拆一下式子看看：
$∑^n_{i=1}a[i]$
$=a[1]+a[2]+a[3]...a[n]$
$=c[1]+c[1]+c[2]+c[1]+c[2]+c[3]...c[1]+c[2]...+c[n−1]+c[n]$
$=n∗(c[1]+c[2]+...+c[n])-(c[2]+c[3]+....c[n]+c[3]+...c[n]+...+c[n]+c[n])$
$=n∗(c[1]+c[2]+...+c[n])−(c[2]+c[3]+....c[n]+c[3]+...c[n]+...+c[n]+c[n])$
$=\sum_{i=1}^n\ (n-i+1)×c_i$
$=n⋅∑^n_{i=1}c[i]−∑^n_{i=1}c[i]∗(i−1)$
$=∑^n_{i=1}c[i]∗(n−i+1)$
这样的话，我们维护两个数组-&gt;
ta数组维护a的前缀和，tb数组维护 $ta×(i-1)$
<pre><code class="language-cpp">ta[i]=a[i]-a[i-1];
tb[i]=ta[i]*(i-1);</code></pre>
<strong>adds</strong>
$ta[i]$还是与原来的树状数组一样更新， $ta[i]+x$即可

$tb[i]=ta[i]* (x-1)$

那么 $tb[i]=(ta[i]+x)* (i-1)$

即 $tb[i]=(ta[i])* (i-1)+x* (i-1)$

我们更新 $x*(i-1)$即可
<pre><code class="language-cpp">inline void adds(int x,int y)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    ta[i]+=y,tb[i]+=y*(x-1);
    return ;
}</code></pre>
<strong>query</strong>

求和公式 $∑^n_{i=1}a[i]=n∗∑^n_{i=1}c[i]−∑^n_{i=1}b[i]$
<pre><code class="language-cpp">inline int query(int x)
{
    int tot=0;
    for(fint i=x;i;i-=lowbit(i))
    tot+=x*ta[i]-tb[i];
    return tot;
}</code></pre>
<strong>如此状况下</strong>
单点修改：
$adds(i,x);$

区间修改（l,r）

$adds(l,x),adds(r+1,-x)$

单点查询：

$query(x)$

区间查询

$query(r)-query(l-1)$

<a href="https://www.luogu.com.cn/problem/P3372">【模板】线段树 1</a>

树状数组版代码：

<pre><code class="language-cpp">signed main()
{
    n=read(),m=read();
    for(fint i=1;i&lt;=n;i++)
        a[i]=read(),adds(i,a[i]-a[i-1]);
        for(fint i=1;i&lt;=m;i++)
        {
            int b,c,d,e;
            b=read();
            if(b==1)
            c=read(),d=read(),e=read(),adds(c,e),adds(d+1,-e);
            else
            if(b==2)
            c=read(),d=read(),cout&lt;&lt;query(d)-query(c-1)&lt;&lt;endl;
        }
    return 0;
}

inline int lowbit(int x)
{
    return x&amp;(-x);
}

inline void adds(int x,int y)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    ta[i]+=y,tb[i]+=y*(x-1);
    return ;
}

inline int query(int x)
{
    int tot=0;
    for(fint i=x;i;i-=lowbit(i))
    tot+=x*ta[i]-tb[i];
    return tot;
}</code></pre>
<h1>二维树状数组</h1>
<h2>概念普及：二维差分和二维前缀和</h2>
回顾一下之前说到的一维差分：

差分与前缀和互为逆运算,即<strong>差分数组的前缀和数组为原数组</strong>,<strong>前缀和数组的差分数组为原数组</strong>.二者都利用了容斥原理.

<strong>那么二维差分呢？</strong>

我们先来看看二维差分的逆运算二维前缀和。
我们定义为以二维数组的首行首列(即左上角)元素为左上角,当前位置元素为右下角的矩阵的元素和.
<img src="https://cdn.luogu.com.cn/upload/image_hosting/wmli1zl9.png" alt="" />

我们要求黑色部分的和其实就是红色部分(当前位置值)加上绿色部分和紫色部分【 $(i-1,j)$和 $(i,j-1)$的前缀和】，中间橙色部分【 $(i-1,j-1)$的前缀和】被减了两次，我们再加回来。
根据容斥原理，我们可以得到二维前缀和的式子：

$f_{i,j}=f_{i-1,j}+f_{i,j-1}-f_{i-1,j-1}+a_{i,j}$

如果我们想通过前缀和求得原数组（单个元素的值），就做差即可：
<img src="https://cdn.luogu.com.cn/upload/image_hosting/jvtzsk58.png" alt="" />

黑色块的值=红色块的值减去绿色和黄色块的值，橙色部分被减了两次，再加回来。

得到柿子：

$a_{i,j}=f_{i,j}-f_{i-1,j}-f_{i,j-1}+f_{i-1,j-1}$

众所周知，<strong>前缀数组还原成原数组需要的操作正是差分！</strong>

那么，推广到二维的容斥上，可得类似推论
$f_{i,j}=a_{i,j}-a_{i-1,j}-a_{i,j-1}+a_{i-1,j-1}$
<img src="https://cdn.luogu.com.cn/upload/image_hosting/408xe87s.png" alt="" />

那么修改其实也一样。如果想对黑色部分全部+1，那么在 $(x_1,y_1)$位置＋1，此时受影响的是整个大矩阵，我们只想让小矩阵收到影响，所以我们要减去绿色部分和红色部分，载吧多减了一次的橙色部分加上，这样就在 $O(1)$的时间内完成了单次修改。

看看代码：

如果想做这样的区间加减

<pre><code class="language-cpp">for(fint i=la;i&lt;=ra;i++)
for(fint j=lb;j&lt;=rb;j++)
a[i][j]+=x;    </code></pre>

使用差分的话，就是

<pre><code class="language-cpp">a[la][ra]++,a[lb][rb]++;
a[la][rb+1]--,a[lb+1][ra]--;</code></pre>

也是个多退少补吧！

是不是很像，恰好是二维前缀和反过来了，这就是逆运算吧，i了i了。

<h2>具体实现</h2>
<ul>
<li>
lowbit不变
</li>
<li>
单差分修改,查询变为：
</li>
</ul>
<pre><code class="language-cpp">inline void adds(int x,int y,int z)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    for(fint j=y;j&lt;=m;j+=lowbit(j))
    t[i][j]+=z;
    return ;
}

inline int query(int x,int y)
{
    int tot=0;
    for(fint i=x;i;i-=lowbit(i))
    for(fint j=y;j;j-=lowbit(j))
    tot+=t[i][j];
    return tot;
}</code></pre>
讲真，除了多出来一维之外和一维树状数组没毛区别。

<h3>单点修改+区间查询</h3>

就是把一维前缀和求差分变成了二维前缀和求差分(还原数组)
<pre><code class="language-cpp">while(cin&gt;&gt;op)
{
    int x,y,k;
    if(op==1)
    cin&gt;&gt;x&gt;&gt;y&gt;&gt;k,adds(x,y,k);
    int a,b,c,d;
    if(op==2)
    cin&gt;&gt;a&gt;&gt;b&gt;&gt;c&gt;&gt;d,cout&lt;&lt;query(c,d)-query(a-1,d)-query(c,b-1)+query(a-1,b-1)&lt;&lt;endl;
}</code></pre>
<h3>区间修改+单点查询</h3>

其实就是个对差分数组求前缀和的操作，修改变得复杂，查询变得简单

<pre><code class="language-cpp">while(cin&gt;&gt;op)
{
    int a,b,c,d,k;
    if(op==1)
    cin&gt;&gt;a&gt;&gt;b&gt;&gt;c&gt;&gt;d&gt;&gt;k,adds(a,b,k),adds(a,d+1,-k),adds(c+1,b,-k),adds(c+1,d+1,k);
    int x,y;
    if(op==2)
    cin&gt;&gt;x&gt;&gt;y,cout&lt;&lt;query(x,y)&lt;&lt;endl;
}</code></pre>
<h3>区间修改+区间查询</h3>

修改和查询都变得复杂，本质上是差分求前缀和再求前缀和的操作

先看公式推导
<img src="https://cdn.luogu.com.cn/upload/image_hosting/l25mc017.png" alt="" />

<img src="https://cdn.luogu.com.cn/upload/image_hosting/wf1ezqk6.png" alt="" />

<img src="https://cdn.luogu.com.cn/upload/image_hosting/0e3rqqo2.png" alt="" />

此时需要开4个操作数组：
<pre><code class="language-cpp">inline void adds(int x,int y,int z)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    for(fint j=y;j&lt;=m;j+=lowbit(j))
    ta[i][j]+=z,tb[i][j]+=(x-1)*z,tc[i][j]+=(y-1)*z,td[i][j]+=(x-1)*(y-1)*z;
    return ;
}

inline int query(int x,int y)
{
    int tot=0;
    for(fint i=x;i;i-=lowbit(i))
    for(fint j=y;j;j-=lowbit(j))
    tot+=ta[i][j]*x*y-tb[i][j]*y-tc[i][j]*x+td[i][j];
    return tot;
}</code></pre>
查询修改代码：

<pre><code class="language-cpp">while(cin&gt;&gt;op)
{
    int a,b,c,d,k;
    if(op==1)
    cin&gt;&gt;a&gt;&gt;b&gt;&gt;c&gt;&gt;d&gt;&gt;k,adds(a,b,k),adds(a,d+1,-k),adds(c+1,b,-k),adds(c+1,d+1,k);
    int x,y;
    if(op==2)
    cin&gt;&gt;x&gt;&gt;y,cout&lt;&lt;query(x,y)&lt;&lt;endl;
}</code></pre>
<h1>树上的树状数组</h1>
树状数组本身就是一种类似树形的数据结构，一般来说只适合维护线性序列区间(或者矩阵区间)。对于树形结构我们有办法维护吗？

答案是肯定的！
但在此之前，我想先请大家了解几个小的前置知识。


<h2>概念普及:树上差分和DFS序</h2>
<h3>树的差分</h3>
<h4>前置知识</h4>
首先请确保你已经完全理解了一维差分的内容和LCA的知识，知道差分是如何对区间进行修改的，并会使用倍增or树链剖分求LCA。如果不知道，请先仔细学习。

话不多说，开始讲述。

<strong>什么是树上差分？</strong>

树上差分就是对一条树链进行 $O(1)$的修改，需要LCA来维护。

树上差分有点差分和边差分两种
<h4>点差分</h4>
对于一条路径 $(s,t)$所有点权全部加上 $1$

为了方便设 $ a[x]$是 $x$的点权, $LCA(s,t)$为公共祖先， $Fa(x)$为x的父亲。
<img src="https://cdn.luogu.com.cn/upload/image_hosting/yyv2tikx.png" alt="" />

我们把链拆分成两部分 $s\to LCA,t\to LCA$.我们要把这两条路径上分别进行修改，显然是 $s+1,t+1,Fa(LCA)-1$.可是，对于这两条路径有一个重复的点也就是他们的公共祖先 $LCA$被经过了两次，所以应该让 $LCA-1$.

代码：
<pre><code class="language-cpp">ans[s]++,ans[t]++;
ans[LCA(s,t)--],ans[Fa[LCA(s,t)]]--;</code></pre>
<h4>边差分</h4>

对于一条路径 $(s,t)$所有边权全部加上 $1$

设 $a[x]$是x到 $fa[x]$的边权。
<img src="https://cdn.luogu.com.cn/upload/image_hosting/6ld9lwqb.png" alt="" />

如果我们还按照点差分的方法，那就会使回溯的步骤经过 $Fa_{LCA}\to LCA$这条路。这是不行的。所以我们应该不经过LCA点进行差分。

我们分别对图中 $s\to Leftson_{LCA}$和 $t\to Rightson_{LCA}$
也就是 $s+1,t+1,LCA-1,LCA-1$

这样就不会经过 $Fa_{LCA}\to LCA$这条路啦！

此时代码
<pre><code class="language-cpp">ans[s]++,ans[t]++;
ans[LCA(s,t)--],ans[Fa[LCA(s,t)]]--;</code></pre>
那么我们就掌握了最基础的树上差分啦
<h3>DFS序</h3>
<h4>基础概念</h4>

dfs序，顾名思义就是dfs的顺序，在一棵树中我们记录它第一次被访问的时间和被回溯后的时间，这两者差的时间就是访问子树的时间，也可以说就是子树的大小啦。在DFS序的帮助下，我们可以将树转化为线性结构。然后使用其他数据结构（如树状数组维护）达到修改查询的目的。

<img src="https://cdn.luogu.com.cn/upload/image_hosting/ktjk4ici.png" alt="" />

我们将这棵树进行了DFS的处理操作，就使得其变成了如上的线性结构。

知道了这些，我们就可以往树上套树状数组了！

<h2>具体实现</h2>
<h4>单点修改+子树求和</h4>

我们用dfs序记录时间戳，根据访问时间让结构变成线性的，然后在dfs序的位置是维护一个树状数组，设这个记录访问时间的数组为 $dfn$，那么单点修改操作就是：

<code>addup(dfn[a],x)</code>

而我们根据dfs序的性质可以知道初次访问时间-回溯时间=子树大小。我们可以记录回溯过来的时间，也可以简单dp一下记录子树和。这里先介绍一下第二种方法：

<pre><code class="language-cpp">inline void dfs(int x,int fa)
{
    dfn[x]=++tim;
    siz[x]=1;
    for(fint i=head[x];i;i=e[i].nxt)
    if(e[i].to!=fa)
    dfs(e[i].to,x),siz[x]+=siz[e[i].to];
    return ;
}</code></pre>
那么查询很简单了
<pre><code class="language-cpp">cout&lt;&lt;query(dfn[a]+siz[a]-1)-query(dfn[a]-1)&lt;&lt;endl</code></pre>
其他部分套树状数组模板即可

<h4>子树修改+子树求和</h4>

既然要修改一棵子树，那我们自然需要用到差分，在树上的一条链差分并不是<code>v[i]-v[i-1]</code>那么简单。我们用 $viss$数组记录时间对应的节点，用 $dfn$数组记录节点对应时间，用 $ou$数组记录回溯的时间。
跑出dfs序
<pre><code class="language-cpp">inline void dfs(int x,int fa)
{
    dfn[x]=++tim;
    siz[x]=1;
    for(fint i=head[x];i;i=e[i].nxt)
    if(e[i].to!=fa)
    dfs(e[i].to,x),siz[x]+=siz[e[i].to];
    return ;
}</code></pre>
由于我们在树状数组中维护的是 $dfs$序，我们应该将当前的i节点的权值和比他早一个时间节点访问的元素进行差分。

这是对树状数组的初始元素添加：
<pre><code class="language-cpp">addup(dfn[i],v[i]-v[viss[dfn[i]-1]]);</code></pre>

求和的话就直接用树状数组维护回溯时间减去初次访问时间的权值即可

<pre><code class="language-cpp">cout&lt;&lt;query(ou[a])-query(dfn[a]-1)&lt;&lt;endl;</code></pre>
<h4>链修改+单点查询+子树求和</h4>

<strong>这是这一部分的重难点！！！</strong>

首先链修改，修改的是点权，那么我们之前介绍的点差分就派上了用场，此时我们用<strong>差分树状数组维护一个点差分数组</strong>（好像在套娃）。大家应该还没忘记点差分的做法吧？！
<pre><code class="language-cpp">addup(dfn[x],val,1),addup(dfn[lca],-val,1);
addup(dfn[y],val,1),addup(dfn[f[lca][0]],-val,1);</code></pre>

此时如果只有单点查询，那么树状数组直接求前缀和就可以帮助我们了：

只需要将回溯时间减去初次访问时间即可
<pre><code class="language-cpp">query(ou[a],1)-query(dfn[a]-1,1)</code></pre>
目前这部分看起来就是将上一部分内容再差分了一步。
<strong>但是还有子树查询，这可怎么办？</strong>

有人可能会觉得用之前差分树状数组中的区间求和操作即可。但子树可能含有很多条链，这对于维护线性序列的树状数组来说十分困难。所以我们需要<strong>结合dfs序来处理。</strong>

我们可以考虑子树中节点对整个子树的贡献，画个图看一下

<img src="https://cdn.luogu.com.cn/upload/image_hosting/ir0c1lvy.png" alt="" />

不难发现，同一子树中深度大的节点对深度浅的节点的子树和有贡献，贡献就是 $val_v*(dep_v-dep_u+1)$,我们再用一棵点差分线段树，不储存点权，而是储存子树的差分贡献
上个代码：

<pre><code class="language-cpp">addup(dfn[x],val*dep[x],2),addup(dfn[lca],-val*dep[lca],2);
addup(dfn[y],val*dep[y],2),addup(dfn[f[lca][0]],-val*dep[f[lca][0]],2);</code></pre>

那么查询就不难了

我们对以 $a$为根子树差分贡献求一个前缀和再减去其中 $a$的点权的贡献就是 $a$子树的权值和了
<pre><code class="language-cpp">(query(ou[a],2)-query(dfn[a]-1,2))-(query(ou[a],1)-query(dfn[a]-1,1))*(dep[x]-1)</code></pre>

这时的树状数组需要两个，分别维护两种操作。

<pre><code class="language-cpp">inline void addup(int x,int y,int id)
{
    for(fint i=x;i&lt;=p;i+=lowbit(i))
    t[i][id]+=y;
    return ; 
}

inline int query(int x,int id)
{
    int tot=0;
    for(fint i=x;i;i-=lowbit(i))
    tot+=t[i][id];
    return tot;
}

inline void dfs(int x,int fa)
{
    dfn[x]=++tim;
    for(fint i=head[x];i;i=e[i].nxt)
    if(e[i].to!=fa)
    dfs(e[i].to,x);
    ou[x]=tim;
    return ; 
}

inline void add(int x,int y,int val)
{
    int lca=LCA(x,y);
    addup(dfn[x],val,1),addup(dfn[lca],-val,1);
    addup(dfn[y],val,1),addup(dfn[f[lca][0]],-val,1);
    addup(dfn[x],val*dep[x],2),addup(dfn[lca],-val*dep[lca],2);
    addup(dfn[y],val*dep[y],2),addup(dfn[f[lca][0]],-val*dep[f[lca][0]],2);
    return ; 
}</code></pre>
好了，这样我们就可以用树状数组实现链修改+单点查询+子树求和啦！是不是很神奇呢？

<h1>树状数组的延申应用</h1>
你可能想不到，树状数组还有这么多功能！快来学习一下吧！

<h2>求逆序对</h2>

我们设树状数组 $t[i]$表示 $i$是否在序列中出现过(0为没出现过，1为出现过)。

那么我们的 $adds$函数就是把 $i$加入到序列中。

$query$函数就是统计序列中值小于等于 $i$的个数，因为树状数组 $t$维护的是数组 $a$的值，则该求和函数即是用于求下标小于等于 $ i $的数组 $a$的和，而数组 $a$中元素的值要么是 $0$要么是 $1$，所以最后求出来的就是小于等于 $i$的元素的个数。

<strong>如何求逆序对个数呢？</strong>

我们从左往右依次将给定的序列输入，每次输入一个数时，就将当前序列中大于这个数的元素的个数计算出来，并累加到答案，最后的答案就是这个序列的逆序数个数。

代码：
<pre><code class="language-cpp">for(fint i=1;i&lt;=n;i++)
{
    cin&gt;&gt;a;
    adds(a,1);
    ans+=i-query(a);
}
cout&lt;&lt;ans;
return 0;</code></pre>
如果数据范围大呢？（如<a href="https://www.luogu.com.cn/problem/P1966">火柴排队</a>）

我们需要考虑<strong>离散化</strong>。

正常的离散化只需要记录他是第几大的数即可。

记录 $a$数组的id和 $b$数组的id，然后排序。

如：
$a:1\ 3\ 4\ 2$

$b:1\ 7\ 2\ 4$
排序后：

$a_{val}=1\ 2\ 3\ 4$

$a_{id}=1\ 4\ 2\ 3$

$b_{val}=1\ 2\ 4\ 7$

$b_{id}=1\ 3\ 4\ 2$

用 $c$数组记录 $a$的id对 $b$的id的映射,即：

$c[1]=1,c[4]=3,c[2]=4,c[3]=2$

此时c数组的下标代表着a数组的大小关系

通俗点说就是： $c[2]=4$表示第二小的数初始位置在第四个。

这样的话， $a,b$数组已经完成了匹配。这时候，看 $c$有几组逆序对即可

<pre><code class="language-cpp">for(fint i=1;i&lt;=n;i++)
adds(c[i],1),ans+=i-query(c[i]);</code></pre>
来道例题吧：

<a href="https://www.luogu.com.cn/problem/P1774">最接近神的人</a>

求逆序对，要先对数据进行离散化

如样例：

2,8,0,3序号为1，2，3，4

排序后序号为3，1，4，2

离散化后 $a_3=1,a_1=2,a_4=3,a_2=4$

而我们要求的排序后序列为： $a_1=1,a_2=2,a_3=3,a_4=4$

加入树状数组后即可求逆序对。
<pre><code class="language-cpp">signed main()
{
    cin&gt;&gt;n;
    for(fint i=1;i&lt;=n;i++)
    cin&gt;&gt;a[i],num[i]=i;
    stable_sort(num+1,num+n+1,cmp);
    for(fint i=1;i&lt;=n;i++)
    a[num[i]]=i;
    int ans=0;
    for(fint i=1;i&lt;=n;i++)
    adds(a[i],1),ans+=(i-query(a[i])); 
    cout&lt;&lt;ans;
    return 0;
}

inline int lowbit(int x)
{
    return x&amp;(-x);
}

inline void adds(int x,int y)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    t[i]+=y;
    return ;
}

inline int query(int x)
{
    int tot=0;
    for(fint i=x;i;i-=lowbit(i))
    tot+=t[i];
    return tot;
}

inline int cmp(int x,int y)
{
    return a[x]&lt;a[y];
}</code></pre>

可能你已经看出来了，树状数组离散化后求逆序对其实正是找区间 $[1,now]$内有几个小于等于 $now$的元素。那我们也可以有许多不少新的延申应用

<h2>求三元逆序对</h2>
<a href="https://www.luogu.com.cn/problem/CF61E">Enemy is weak</a>

我们用两个树状数组，分别求从 $1$到 $i-1$中有多少个数比他大，从 $i-1$到 $n$中有多少个数比他小即可。
代码：
<pre><code class="language-cpp">signed main()
{
    cin&gt;&gt;n;
    for(fint i=1;i&lt;=n;i++)
    cin&gt;&gt;a[i];
    for(fint i=1;i&lt;=n;i++)
    b[i]=a[i];
    sort(b+1,b+n+1);
    int len=unique(b+1,b+n+1)-b-1;
    for(fint i=1;i&lt;=n;i++)
    a[i]=lower_bound(b+1,b+len+1,a[i])-b;
    for(fint i=n;i&gt;=1;i--)
    l[i]+=query_a(a[i]-1),adds_a(a[i],1);
    for(fint i=1;i&lt;=n;i++)
    r[i]+=query_b(n)-query_b(a[i]-1),adds_b(a[i],1);
    int ans=0;
    for(fint i=1;i&lt;=n;i++)
    ans+=l[i]*r[i];
    cout&lt;&lt;ans;
    return 0;
}</code></pre>
延申，再延申！
<h2>求排名</h2>

我们通过查找知道左边有几个比自己大的和右边有几个比自己大的，不就知道了排名吗？有平衡树那味儿了，hhh。

代码：
<pre><code class="language-cpp">signed main()
{
    cin&gt;&gt;n;
    for(fint i=1;i&lt;=n;i++)
    cin&gt;&gt;a[i];
    for(fint i=1;i&lt;=n;i++)
    b[i]=a[i];
    sort(b+1,b+n+1);
    int len=unique(b+1,b+n+1)-b-1;
    for(fint i=1;i&lt;=n;i++)
    a[i]=lower_bound(b+1,b+len+1,a[i])-b;
    for(fint i=n;i&gt;=1;i--)
    l[i]+=query_a(a[i]-1),adds_a(a[i],1);
    for(fint i=1;i&lt;=n;i++)
    r[i]+=query_b(a[i]-1),adds_b(a[i],1);
    int ans=0;
    for(fint i=1;i&lt;=n;i++)
    rank[i]=l[i]+r[i]+1,cout&lt;&lt;rank[i]&lt;&lt;" ";
    return 0;
}</code></pre>
<h2>求前驱后继</h2>
再离散化后的有序数组内，知道了排名就相当于知道了前驱后继。

假如我们要查询第 $x$名的前驱与后继，先用 $map$映射出第x名的元素
<pre><code class="language-cpp">mp[rank[x]]=i;</code></pre>
然后在 $map$中找到 $rank[x]$的前一名即可

当然也可以像之前luogu日报里一样用倍增解决，这里不再赘述。

<h2>拉格朗日恒等式</h2>
拉格朗日恒等式是18世纪由法国数学家<a href="https://baike.baidu.com/item/约瑟夫·路易斯·拉格朗日/7070424">约瑟夫·路易斯·拉格朗日</a>提出的数学恒等式。
<strong>内容</strong>
<img src="https://cdn.luogu.com.cn/upload/image_hosting/st83vkvj.png" alt="" />
也就是说，我们随便乱拆就可以把式子化简为：

$\sum_{i=1}^{n} a_{i}^{2} b_{i}^{2}+\sum_{1 \leq i&lt;j \leq n} a_{i}^{2} b_{j}^{2}+\sum_{1 \leq i&lt;j \leq n} a_{j}^{2} b_{i}^{2}$

<strong>这很显然啊！</strong>
我们可以倒推来证明嘛

$\begin{array}{l}
A=\sum_{i=1}^{n} a_{i}^{2} b_{i}^{2}+\sum_{1 \leq i&lt;j \leq n} a_{i}^{2} b_{j}^{2}+\sum_{1 \leq i&lt;j \leq n} a_{j}^{2} b_{i}^{2} \\
B=\sum_{i=1}^{n} a_{i}^{2} b_{i}^{2}+2 \sum_{1 \leq i&lt;j \leq n} a_{i} b_{i} a_{j} b_{j}+\sum_{1 \leq i&lt;j \leq n} a_{i}^{2} b_{j}^{2}-2 \sum_{1 \leq i&lt;j \leq n} a_{i} b_{j} a_{j} b_{i}+\sum_{1 \leq i&lt;j \leq n} a_{j}^{2} b_{i}^{2} \\
=\sum_{i=1}^{n} a_{i}^{2} b_{i}^{2}+\sum_{1 \leq j \leq n} a_{i}^{2} b_{j}^{2}+\sum_{1 \leq i&lt;j \leq n} a_{j}^{2} b_{i}^{2} \\
\therefore A=B
\end{array}$

所以呢，由此可知

$\left(\sum_{i=1}^{n} a_{i}^{2}\right)\left(\sum_{i=1}^{n} b_{i}^{2}\right)=\left(\sum_{i=1}^{n} a_{i} b_{i}\right)^{2}+\sum_{1 \leq i&lt;j \leq n}\left(a_{i} b_{j}-a_{j} b_{i}\right)^{2}$

这就是著名的<strong>拉格朗日恒等式</strong>的内容.

那么对于式子：
$\sum_{i=1}^{n}\sum_{j=1}^{n}(a_ib_j-a_jb_i)^2$
其实就等于 $\left(\sum_{i=1}^{n} a_{i}^{2}\right)\left(\sum_{i=1}^{n} b_{i}^{2}\right)\left(\sum_{i=1}^{n} a_{i} b_{i}\right)^{2}$

式子被分为三个部分，我们可以使用树状数组来维护。

代码：

<pre><code class="language-cpp">p=read(),l_a=read(),r_a=read(),l_b=read(),r_b=read();
cout&lt;&lt;((1LL*query(0,l_a,r_a)*query(1,l_a,r_a)-Pow(query(2,l_a,r_a)))%mods+mods)%mods;
inline int Pow(int x)
{
    return 1LL*x*x%mods;
}

inline int lowbit(int x)
{
    return x&amp;(-x);
}

inline void adds(int id,int x, int y)
{
    y=(y+mods)%mods;
    for(fint i=x,j=0;i&lt;=n;i+=lowbit(i))
    t[i][id]+=y,t[i][id]&gt;mods?t[i][id]-=mods:j++;
    return ;
}

inline int query(int id, int l, int r)
{
    return qry(id,r)-qry(id,l-1);
}

inline int qry(int id,int x)
{
    int tot=0;
    for(fint i=x,j=0;i;i-=lowbit(i))
    tot+=t[i][id],tot&gt;mods?tot-=mods:j++;
    return tot;
}
</code></pre>
非常合理
<h2>区间最值</h2>

神奇的树状数组也可以处理区间最值的问题

<strong>修改</strong>

浅显易懂，直接暴力求最值

<pre><code class="language-cpp">inline void adds(int x,int y)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    t[x]=max(t[x],y);
}</code></pre>

<strong>查询</strong>

若 $r-lowbit(r)&gt;l$，<strong>我们可以把求x,y区间的最大值拆分成两部分，[先求l,r-lowbit(r)]中最大值与[r-lowbit(r)+1,r]中的最大值，再求这两者的最大值。</strong>
我们可以发现<strong>[ r-lowbit(y)+1,r ]=tree[r]</strong>

否则的话<strong>原问就变成了：求 [x,y-1]中最大值 与 a[y] 的最大值。</strong>
<pre><code class="language-cpp">inline void findx(intl,int r)
{
    if(r-lowbit(r)&gt;l)
    return max(t[r],findx(l,y-lowbit(r)))
    return max(a[y],findx(x,y-1));
}</code></pre>

看看这道题

<a href="https://www.luogu.com.cn/problem/P2880">Balanced Lineup G</a>
<pre><code class="language-cpp">signed main()
{
    cin&gt;&gt;n&gt;&gt;q;
    int a,b,c;
    memset(treeb,0x7f,sizeof(treeb));
    for(fint i=1;i&lt;=n;i++)
    {
        cin&gt;&gt;a;
        f[i]=a;
        addup(i,a);
    }
    for(fint i=1;i&lt;=q;i++)
    {
        cin&gt;&gt;b&gt;&gt;c;
        cout&lt;&lt;totmax(b,c)-totmin(b,c)&lt;&lt;endl;
    }
    return 0;
}
int totmax(int x, int y)
{ 
    if(y&gt;x)
    {
        if(y-lowbit(y)&gt;x) 
        return max(treea[y],totmax(x,y-lowbit(y)));
        else 
        return max(f[y],totmax(x,y-1));
    }
    return f[x];
}
int totmin(int x, int y) 
{ 
    if(y&gt;x)
    {
        if(y-lowbit(y)&gt;x) 
        return min(treeb[y],totmin(x,y-lowbit(y)));
        else 
        return min(f[y],totmin(x, y-1));
    }
    return f[x];
}
inline void addup(int x,int y)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
treea[i]=max(treea[i],y),treeb[i]=min(treeb[i],y);
}
inline int lowbit(int x)
{
    return x&amp;(-x);
}</code></pre>
接着延申

<h2>ST表</h2>

<a href="https://www.luogu.com.cn/problem/P3865">ST表经典题——静态区间最大值</a>

会查区间最值了，自然就能解决ST表

直接上代码（尼古卡的严，只有92分）：
<pre><code class="language-cpp">signed main()
{
    scanf("%d%d",&amp;n,&amp;q);
    for(fint i=1;i&lt;=n;i++)
    scanf("%d",&amp;a[i]),adds(i,a[i]);
    int l,r;
    for(fint i=1;i&lt;=q;i++)
    scanf("%d%d",&amp;l,&amp;r),printf("%d\n",totmax(l,r));
    return 0;
}
inline int totmax(int x,int y)
{ 
    if(y&gt;x)
    {
        if(y-lowbit(y)&gt;x) 
        return max(t[y],totmax(x,y-lowbit(y)));
        else 
        return max(a[y],totmax(x,y-1));
    }
    return a[x];
}

inline void adds(int x,int y)
{
    for(fint i=x;i&lt;=n;i+=lowbit(i))
    t[i]=max(t[i],y);
}

inline int lowbit(int x)
{
    return x&amp;(-x);
}</code></pre>

<h1>后记</h1>
<h3>推荐题目</h3>
最后推荐几道简单的练习题给大家：
<a href="https://www.luogu.com.cn/problem/P2357">守墓人</a>

<a href="https://www.luogu.com.cn/problem/P4939">Agent2</a>

<a href="https://www.luogu.com.cn/problem/P6278">Haircut G</a>

<a href="https://www.luogu.com.cn/problem/P1908">逆序对</a>

<a href="https://www.luogu.com.cn/problem/P1966">火柴排队</a>

<a href="https://www.luogu.com.cn/problem/P3253">删除物品</a>