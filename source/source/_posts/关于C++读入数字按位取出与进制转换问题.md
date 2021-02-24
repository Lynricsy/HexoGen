---
title: 关于C++读入数字按位取出与进制转换问题
date: 2020-01-27
top_img: https://api.r10086.com/%E9%A3%8E%E6%99%AF%E7%B3%BB%E5%88%9710.php
cover: https://api.r10086.com/%E5%8A%A8%E6%BC%AB%E7%BB%BC%E5%90%887.php   
tags: 
    - 数论
    - 算法概述
---
这一片博客我就不写具体的一个题了，只是总结一种典型问题读入数字按位取出。

就拿数字12345举例吧。

是首先，我们要取出个位。这样取出：

```cpp
12345/1=12345

12345%10=5.     //为了好发现规律
```

这样我们就有了它的个位。十位是这样：

```
12345/10=1234

1234%10=4.
```

同理，百位：

```cpp
12345/100=123

123%10=3.
```

于是可以发现，取出哪一位，就是要先将原数除以这一位的位名，再模10.

程序：

```cpp
#include<iostream>
#include<cmath>
using namespace std;
int main()
{
    int a[100];
    int wei = 0;
    int num;
    cin >> num;
    while ((num / (int)pow(10, wei)) != 0)        //循环终止条件是这个数的位数小于这一次要除以的数的位数
    {
        a[wei] =(num/(int)pow(10,wei))%10;        //根据刚才得出的结论，取出各位，存到数组中。
        wei++;    
    }
}
```

然后是进制转换问题。其实和取位问题差不多，只不过取出之后要乘上这一位对应的进制的次方数。

程序：

```cpp
long long to10(int jz,int num)//功能：将输入的数转换成十进制 
{    
    long long result=0;
    int wei=0;
    while(num/(int)pow(10,wei)!=0)//将输入的数按位取出 
    {
        result+=pow(jz,wei)*((int)(num/pow(10,wei))%10);//按数所在的位置乘上对应的进制的次方 
        wei++;
//        (num/1)%10
//        (num/10)%10
//        (num/100)%10
    }
    return result;
 } 
```

