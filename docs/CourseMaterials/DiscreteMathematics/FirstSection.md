---
layout: default
title: "DiscreteMathematics | First Section"
description: "第一大章"
---

## 欧几里得算法求最大公约数

```c++
int getGreatestCommonDivisor(int a, int b) {
    if (a % b == 0) return b;
    else return getGreatestCommonDivisor(b, a % b);
}
```

## 最小公倍数

(a,b)最大公因数 × (a,b)最小公倍数 = a×b

```c++
int getLeastCommonMutiple(int a, int b) {
    return (a * b) / getGreatestCommonDivisor(a, b);
}
```

## 裴蜀等式

> 对于不全为0的整数 a，b 和 d，方程sa + tb = d存在整数解s和t 
> 当且仅当 GCD(a, b)|d 存在
> 方程 sa+tb=d 称作裴蜀等式

## 同余

> 设n是正整数，a和n是整数，如果`n|(a-b)`,则`a ≡ b (mod n)` 

## 加法法则和乘法法则

> A,B,C是3个城市，从A到B有4条道路，从B到C有2条道路，从A直接到C有3条道路，则从A到C共有4×2+3=11种不同的方式。（先分类，类内再分步）

> 某种样式的套装上装有T恤和衬衫两种，下装为长裤。T恤可选红色、蓝色和橙色，衬衫可选白色、黄色和粉色，长裤可选黑色、棕色，则共有(3+3)×2=12种着色方案。（先分步，每步再分类）

## 排列

$P(n, r) = \frac{n!}{(n - r)!}$，如果n = r，就是全排列n!

> 一个社团共有 10 名成员，从中选出一名主席、一名副主席、一名书记，则共有 P(10, 3)=720 种方法

## 阻合

$C(n, r) = \frac{P(n, r)}{r!} = \frac{n!}{(n - r)! × r!}$

> 一个社团共有10名成员，从中选出3人组成指导委员会，则共有C(10, 3)=120种方法。