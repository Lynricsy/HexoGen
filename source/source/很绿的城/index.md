---
title: 很绿的城
date: 2020-07-26 17:54:55
description: 一道非常非常水的图论题。
keywords: 我出的题
cover: https://acg.xydwz.cn/api/api.php  
top_img: http://ww1.sinaimg.cn/large/0075LAc9ly1gh59qvg8wsj33z41a0aib.jpg
---

<div class='tip warning'><p>此题目考试前禁止外泄！！！</p></div>

<div class='tip'><p>本题题目提供者：ZKW</p></div>

<div class='tip success'><p>时间限制：1000ms</p></div>

<div class='tip success'><p>空间限制：64MB</p></div>

## 题目描述

<p class='div-border red'>某个城市中有多个小区，有的小区之间有道路（只允许单向通行）。为了使城市更加美丽，所以要在道路旁种植树木。若从某个小区出发经过若干条道路又回到了这个小区（即为一个环），则在这个环所围成的封闭图形内建造一个公园，并且要求公园周围的道路旁的所有树不止有一个品种。求最少需要多少种树。<br>确保每一个节点最多在一个环内。</p>

## 输入格式

<p class='div-border green'>第一行 一个数T；代表你有T个城市需要规划；
    <br>接下来有T组输入，每组第一行输入两个数 $n$和$m$；代表这个城市有$n$个小区，$m$条道路；
    <br>接下来$m$行每行输入两个数$s$和$t$，代表$s$号小区可以通向$t$号小区。</p>

## 输出格式

<p class='div-border yellow'>对于每组数据输出一个数$k$，代表最少需要$k$种树，一个一行。</p>

## 输入输出样例

**输入#1**

```zkw
2
4 3
1 3
2 3
2 4

4 5
1 2
1 3
2 4
4 3
3 2
```

**输出#1**

```zkw
1
2
```

## 数据范围

<p class='div-border red'>对于$10％$的数据$T=1，m=0$
    <br>对于$20％$的数据$T=1$
    <br>对于$50％$的数据$n≤500，m≤500$
    <br>对于$100％$的数据$n≤100000，m≤100000，T≤10$
    <br>所有数据保证没有自环与重边。


