---
title: UOJ T1~T15 题解
date: 2020-11-26 16:33:08
top_img: https://uploadbeta.com/api/pictures/random/?key=BingEverydayWallpaperPicture
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxk3mu0ztj31hc0u07m5.jpg
tags: 
    - 字符串
    - 排序
    - 校内事务
hidden: true
description: 校内UOJ的简单题Python板题解。
---

## T1 
```python
a=int(input())
b=int(input())
print(a+b)
```
## T2
```python
print('Hello,World!')
```
## T3
```python
a=int(input())
b=int(input())
c=int(input())
print(max(a,b,c))
```
## T4
```python
a=int(input())
b=int(input())
c=int(a/b)
print(int(a/b))
print(a-b*c)
```
## T5
```python
fahrenheit = float(input())
celsius = (fahrenheit - 32) / 1.8

print('%0.5f ' %(celsius))
```
## T6
```python
PI=3.14
R=float(input())
print('%.2f',%(R*R*R*PI*4/3))
```
## T7
```python
a1=int(input())
a2=int(input())
n=int(input())
d=a2-a1;
an=a1+(n-1)*d
print(an)
```
## T8
```python
a=int(input())
if a>0:
	print('positive')
elif a=0:
	print('zero')
else:
	print('negative')
```
## T9
```python
a=int(input())
if a&1:
	print('odd')
else:
	print('even')
```
## T10
```python
a=int(input())
if (a%3==0)&&(a%5==0):
	print('YES')
else:
	print('NO')
```
## T11
```python
a=int(input())
b=int(input())
c=int(input())
print(max(a,b,c))
```
## T12
```python
n=int(input())
tot=0
for i in range(n):
	tot=tot+int(input())
print('%.2f',%(float(tot)/n))
```









