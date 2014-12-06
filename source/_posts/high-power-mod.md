title: 高次幂取模
tags:
  - 数论
id: 37
categories:
  - 数据结构与算法
date: 2014-06-13 14:00:09
---

## 几个概念

*   同余
$a$、$b$ 为整数，$m$ 为正整数，若 $m$ 整除 $a-b$ 就说 $a$ 模 $m$ 同余 $b$ ，用  $a\equiv b\pmod m$表示。
此时，$a\equiv b\pmod n$ 等价于 $a\mod m =b\mod m$ 。
还可以这么说：$a=b+km$

*   最大公约数和最小公倍数
$a、b$ 为不全为 $0$ 的两个整数，能使 $d\ |\ a和d\ |\ b$ 的最大整数 $d$ 称为 $a和b$ 的最大公约数。用 $gcd(a,b)$ 表示。
正整数 $a$和$b$ 最小公倍数则是能被$a$和$b$整除的最小整数。用 $lcm(a,b)$ 表示。
且，$gcd(a,b)\cdot lcm(a,b)=ab$

<!--more-->

## 求解同余幂

如何有效地求解 $b^n\mod m$（$m\ge2、n\ge0、1\le b\le m$）?
当 $b,n,m$ 都是大整数时，先求 $b^n$ ，再求 $b^n$ 除以 $m$ 的余数，这显然是不可行的。

先介绍一个公式：

$a\cdot b \mod m=((a \mod m)\cdot(b \mod m))\mod m$

首先，我们遇到的问题就是，如何求解在精度和效率的要求下求解 $b^n$
那么，有一种可行的方法是利用指数 $n$ 的二进制展开：$n=(a_{k-1}\cdots a_1a_0)_2$。

$!b^n=b^{a_{k-1}\cdot 2^{k-1}+\cdots+a_1\cdot2+a_0}=b^{a_{k-1}\cdot2^{k-1}}\cdots b^{a_1\cdot2}\cdot b^{a_0}$

为了计算 $b^n$ ，我们要先计算 $b,b^2,(b^2)^2=b^4,(b^4)^2=b^8,\cdots,b^{2^k}$,再把 $a_j=1$（二进制位为1）的那些项乘起来，就得到了 $b^n$ 。

比如我们要算 $3^{11}$，先把 11 转化为二进制：$11=(1011)_2$，因此 
$!3^{11}=3^{8+2+1}=3^83^23^1=6561\cdot9\cdot3=177147$

那么求解 $b^n\mod m$ 的方法就是，依次求 $b\mod m$，$b^2\mod m$，$b^4\mod m$，$\cdots$，$b^{2^{k-1}}\mod m$，把其中 $a_j=1$（二进制位为1） 的那些项 $b^{2^j} \mod m$ 乘起来，每次乘法后再 $\mod m$ 。

* * *

**上面一段没看懂的看这里：**
假设按照二进制的方法得到： $b^n=a\cdot b\cdot c\cdot d$
那我们就把 $a\mod m、b\mod m、c\mod m、d\mod m$ 求出来，开始推了：
$(a\mod m)\cdot (b\mod m) \mod m = ab \mod m$
$(ab \mod m)\cdot (c\mod m) \mod m = abc \mod m$
$(abc \mod m)\cdot (d\mod m) \mod m = abcd \mod m$

* * *

**举个栗子**

求 $3^{644} \mod 625$

* * *

$(644)_2= 1010000100$
$x=b^n \mod m$
$i:0$ to $k-1$

步骤：
初始 $x=b^0 \mod 625=1$，$power=3 \mod 645=3$，power的作用是实时更新 $b^n$的因子

$i=0,a_0=0,x=1,power=3^2 \mod 645=9 \mod 645=9$;
$i=1,a_1=0,x=1,power=9^2 \mod 645=81 \mod 645=81$;
$i=2,a_1=1,x=1 \cdot 81 \mod 645=81,power=81^2 \mod 645=6561 \mod 645=111$;
$i=3,a_3=0,x=81,power=111^2 \mod 645=12321 \mod 645=66$;
$i=4,a_1=0,x=81,power=66^2 \mod 645=4356 \mod 645=486$;
$i=5,a_1=0,x=81,power=486^2 \mod 645=236196 \mod 645=126$;
$i=6,a_1=0,x=81,power=126^2 \mod 645=15876 \mod 645=396$;
$i=7,a_1=1,x=(81 \cdot 396)\mod 645=471,power=396^2 \mod 645=156816 \mod 645=81$;
$i=8,a_1=0,x=471,power=81^2 \mod 645=6561 \mod 645=111$;
$i=9,a_1=1,x=(471 \cdot 111) \mod 645=6561 \mod 645=36$;

* * *

## 代码实现

```C
// C语言实现
int high_power_mod(int b,int n,int m){
    if(n==0) return 1%m;
    if(n==1) return b%m;
    int result=1;
    int power=b;
    while(n>0){
        if(n & 1==1){
            result = (result*power) % m;
        }
        power = (power*power) % m;
        n >>= 1;
    }
    return result;
}
```

## 递归方法

在我们这种情况下（$1\le b\lt m$），下面这个式子是成立的：

$b^n \mod m$
$=b \cdot b^{n-1} \mod m$
$=((b \mod m)\cdot(b^{n-1} \mod m))\mod m$
$=(b \cdot(b^{n-1} \mod m)) \mod m$

结合一下我们的初始设定 $b^0 \mod m=1$，很容易就想到利用递归求解了。

```C
int high_power_mod(int b,int n,int m){
    if(n==0){
        return 1;
    }
    int result = mod(b,n-1,m);
    return b*result%m;
}
```

这个算法的复杂度应该是 O(n)，n 就是幂次。

## 改进

其实，还有一个式子也是成立的：
$a^b \mod m=(a \mod m)^b \mod m$

那么利用这个式子，就可以把递归次数减少了：
$b^n \mod m=(b^{n/2} \mod m)^2 \mod m$

实现的时候要注意：
**当 n 为奇数时，最后一次要再乘一个 b 。**

代码：

```C
int high_power_mod(int b,int n,int m){
    if(n==0){
        return 1;
    }
    int result = high_power_mod(b,n/2,m);
    if(n%2)
        return b*result*result%m;
    else
        return result*result%m;
}
```

同理，不用递归也是可以的：

```C
int high_power_mod(int b,int n,int m){
    if(n==0) return 1%m;
    if(n==1) return b%m;
    int result=1;
    int power=b;
    while(n){
        if(n%2)
            result=result*b%m;
        b = b*b%m;
        n /= 2;
    }
    return result;
}
```    