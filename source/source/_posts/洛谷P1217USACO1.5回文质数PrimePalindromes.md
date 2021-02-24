---
title: 洛谷P1217USACO1.5回文质数PrimePalindromes
date: 2020-02-06
top_img: https://api.r10086.com/%E9%A3%8E%E6%99%AF%E7%B3%BB%E5%88%9710.php
cover: https://api.r10086.com/%E5%8A%A8%E6%BC%AB%E7%BB%BC%E5%90%889.php   
tags: 
    - 洛谷
    - 数论
---
# P1217 [USACO1.5]回文质数 Prime Palindromes

## 题目描述

因为 151 既是一个质数又是一个回文数（从左到右和从右到左是看一样的），所以 151 是回文质数。

写一个程序来找出范围 \[a,b\](5≤a<b≤100,000,000)( 一亿)间的所有回文质数。

## 输入格式

第 1 行: 二个整数 `a `和 `b` .

## 输出格式

输出一个回文质数的列表，一行一个。

## 输入输出样例

```
5 500
```

```cpp
5
7
11
101
131
151
181
191
313
353
373
383
```

## 说明/提示

Hint 1: Generate the palindromes and see if they are prime.

提示 1: 找出所有的回文数再判断它们是不是质数（素数）.

Hint 2: Generate palindromes by combining digits properly. You might need more than one of the loops like below.

提示 2: 要产生正确的回文数，你可能需要几个像下面这样的循环。

题目翻译来自NOCOW。

USACO Training Section 1.5

产生长度为5的回文数:

```
1 for (d1 = 1; d1 <= 9; d1+=2) {    // 只有奇数才会是素数
2      for (d2 = 0; d2 <= 9; d2++) {
3          for (d3 = 0; d3 <= 9; d3++) {
4            palindrome = 10000*d1 + 1000*d2 +100*d3 + 10*d2 + d1;//(处理回文数...)
5          }
6      }
7  }
```

 **首先，我看了题下面的提示，就想按照提示的方法解这道题。因为右边界的取值范围最大到100000000，所以我考虑到位数可以有以下几种情况：1,3,5,7,9.分类讨论分别写循环就可以列举出这里所有回文数了。**

**然后因为题目要的是回文质数，所以再加上一个质数的判断函数，是质数就输出就可以了：**

**按照提示，列举回文数的方法是这样的：**

**比如说我们需要列举所有九位的回文数，我们需要循环5个数：a,b,c,d,e，把这五个数从0`9进行循环，我们的回文数是这样的：**

<img src="https://img2018.cnblogs.com/common/1924270/202002/1924270-20200206094520257-1296305125.png" alt="" />

**那就以此类推，写出其他位数的情况：**

```cpp
int l,r,num,abc=0;
cin >> l >> r;
int res[10000];
    for (int a = 1; a <= 9; a += 2)//一位
    {
        num =a;
        if (num >= l && num <= r && isp(num))
        {
            res[abc] = num;
            abc++;
        }
    }
for (int a = 0; a <= 9; a++)//三位
{
        for (int b = 1; b <= 9; b += 2)
        {
            num =b * 100 + a * 10 + b;
            if (num >= l && num <= r && isp(num))
            {
                res[abc] = num;
                abc++;
            }
        }
}
for (int a = 0; a <= 9; a++)//五位
{
    for (int b = 0; b <= 9; b++)
    {
            for (int c = 1; c <= 9; c += 2)
            {
                num = c * 10000 + b * 1000 + a * 100 + b * 10 + c;
                if (num >= l && num <= r && isp(num))
                {
                    res[abc] = num;
                    abc++;
                }
            }
    }
}
for (int a = 0; a <= 9; a++)//七位
{
    for (int b = 0; b <= 9; b++)
    {
        for (int c = 0; c <= 9; c++)
        {
            for (int d = 1; d <= 9; d+=2)
            {
                    num =   d * 1000000 + c * 100000 + b * 10000 + a * 1000 + b * 100 + c * 10 + d ;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
            }
        }
    }
}
for (int a = 0; a <= 9; a++)//九位
{
    for (int b = 0; b <= 9; b++)
    {
        for (int c = 0; c <= 9; c++)
        {
            for (int d = 0; d <= 9; d++)
            {
                for (int e = 1; e <= 9; e+=2)
                {
                    num = e * 100000000 + d * 10000000 + c * 1000000 + b * 100000 + a * 10000 + b * 1000 + c * 100 + d * 10 + e;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
                }
            }
        }
    }
}
```

质数判断函数：

```cpp
int isp(int a)
{
    for (int i = 2; i <= floor(sqrt(a));i++)
    {
        if (a % i == 0)
        {
            return 0;//能被整除说明不是质数
        }
    }
    return 1;
}
```

但是这样子储存在数组中的数据顺序有些乱，因为我是按照它中间那一位数的顺序循环的。所以需要排一下序：

```cpp
sort(res, res + abc);
```

然后就珂以输出了：

```cpp
for (int z = 0; z <= abc-1; z++)
{
    cout << res[z] << endl;
}
```

最后提交上去一看：

WA，WA，WA

仔细研究程序发现：

回文数不止有奇数位的，还有偶数位的！！！（真是一个惊天动地的珂学大发现呢！）

赶紧加上：

```cpp
for (int b = 0; b <= 9; b++)//六位
        {
            for (int c = 0; c <= 9; c++)
            {
                for (int d = 1; d <= 9; d += 2)
                {
                    num = d * 100000 + c * 10000 + b * 1000 +b * 100 + c * 10 + d;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
                }
            }
        }
            for (int c = 0; c <= 9; c++)//四位
            {
                for (int d = 1; d <= 9; d += 2)
                {
                    num = d * 1000 + c * 100 + c * 10 + d;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
                }
            }
                for (int d = 1; d <= 9; d += 2)//两位
                {
                    num = d * 10 +d;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
                }
```

大功告成！

附上全部的程序：

```cpp
#include <bits/stdc++.h>

using namespace std;
int isp(int a)
{
    for (int i = 2; i <= floor(sqrt(a));i++)
    {
        if (a % i == 0)
        {
            return 0;
        }
    }
    return 1;
}

int main()
{
    int l,r,num,abc=0;
    cin >> l >> r;
    int res[10000];
        for (int a = 1; a <= 9; a += 2)//一位
        {
            num =a;
            if (num >= l && num <= r && isp(num))
            {
                res[abc] = num;
                abc++;
            }
        }
    for (int a = 0; a <= 9; a++)//三位
    {
            for (int b = 1; b <= 9; b += 2)
            {
                num =b * 100 + a * 10 + b;
                if (num >= l && num <= r && isp(num))
                {
                    res[abc] = num;
                    abc++;
                }
            }
    }
    for (int a = 0; a <= 9; a++)//五位
    {
        for (int b = 0; b <= 9; b++)
        {
                for (int c = 1; c <= 9; c += 2)
                {
                    num = c * 10000 + b * 1000 + a * 100 + b * 10 + c;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
                }
        }
    }
    for (int a = 0; a <= 9; a++)//七位
    {
        for (int b = 0; b <= 9; b++)
        {
            for (int c = 0; c <= 9; c++)
            {
                for (int d = 1; d <= 9; d+=2)
                {
                        num =   d * 1000000 + c * 100000 + b * 10000 + a * 1000 + b * 100 + c * 10 + d ;
                        if (num >= l && num <= r && isp(num))
                        {
                            res[abc] = num;
                            abc++;
                        }
                }
            }
        }
    }
    for (int a = 0; a <= 9; a++)//九位
    {
        for (int b = 0; b <= 9; b++)
        {
            for (int c = 0; c <= 9; c++)
            {
                for (int d = 0; d <= 9; d++)
                {
                    for (int e = 1; e <= 9; e+=2)
                    {
                        num = e * 100000000 + d * 10000000 + c * 1000000 + b * 100000 + a * 10000 + b * 1000 + c * 100 + d * 10 + e;
                        if (num >= l && num <= r && isp(num))
                        {
                            res[abc] = num;
                            abc++;
                        }
                    }
                }
            }
        }
    }
        for (int b = 0; b <= 9; b++)//六位
        {
            for (int c = 0; c <= 9; c++)
            {
                for (int d = 1; d <= 9; d += 2)
                {
                    num = d * 100000 + c * 10000 + b * 1000 +b * 100 + c * 10 + d;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
                }
            }
        }
            for (int c = 0; c <= 9; c++)//四位
            {
                for (int d = 1; d <= 9; d += 2)
                {
                    num = d * 1000 + c * 100 + c * 10 + d;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
                }
            }
                for (int d = 1; d <= 9; d += 2)//两位
                {
                    num = d * 10 +d;
                    if (num >= l && num <= r && isp(num))
                    {
                        res[abc] = num;
                        abc++;
                    }
                }
    sort(res, res + abc);
    for (int z = 0; z <= abc-1; z++)
    {
        cout << res[z] << endl;
    }
    return 0;
}
```

还有一个方法。我们其实可以先列举出所有5~100000000的整数，然后分别判断是不是回文数，是不是质数，是不是在区间内。因为会超时，所以还是别试了78.42秒

程序：（其实可以直接用++来列举，我这样进行嵌套是为了能一眼看出效率有多么低）（5 100000000时）

```cpp
#include <bits/stdc++.h>

using namespace std;

int isp(int pr)
{
    for (int i = 2; i <= floor(sqrt(pr)); i++)
    {
        if (pr % i == 0)
        {
            return 0;
        }
    }
    return 1;
}
int ishui(int x)
{
    int s=0;
    if (x % 10 == 0 || x <= 0)
    {
        return 0;
    }
    while (x > s)
    {
        s = s * 10 + x % 10;
        x /= 10;
    }
    if (x == s || s / 10 == x)
    {
        return 1;
    }
    else
    {
        return 0;
    }
}
int main()
{
    int s = 0;
    int l=5, r=100000000;
    for (int a = 0; a <= 9; a++)
    {
        for (int b = 0; b <= 9; b++)
        {
            for (int c = 0; c <= 9; c++)
            {
                for (int d = 0; d <= 9; d++)
                {
                    for (int e = 0; e <= 9; e++)
                    {
                        for (int f = 0; f <= 9; f++)
                        {
                            for (int g = 0; g <= 9; g++)
                            {
                                for (int h = 0; h <= 9; h++)
                                {
                                    for (int i = 0; i <= 9; i++)
                                    {
                                        s = a * 100000000 + b * 10000000 + c * 1000000 + d * 100000 + e * 10000 + f * 1000 + g * 100 + h * 10 + i;
                                        if (isp(s) && ishui(s) && s >= l && s <= r)
                                        {
                                            cout << s << endl;
                                        }
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
    }
    return 0;
}
```
