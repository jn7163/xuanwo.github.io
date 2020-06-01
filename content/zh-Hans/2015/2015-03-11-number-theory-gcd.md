---
categories: Code
date: 2015-03-11T10:32:31Z
title: 数论——欧几里得算法
toc: true
url: /2015/03/11/number-theory-gcd/
---

# 欧几里得算法
## 介绍
欧几里得算法，又名辗转相除法，是求最大公约数的算法。两个整数的最大公约数是能够同时整除它们的最大的正整数。辗转相除法基于如下原理：两个整数的最大公约数等于其中较小的数和两数的差的最大公约数。例如，252和105的最大公约数是21（252 = 21 × 12；105 = 21 × 5）；因为252 − 105 = 147，所以147和105的最大公约数也是21。在这个过程中，较大的数缩小了，所以继续进行同样的计算可以不断缩小这两个数直至其中一个变成零。这时，所剩下的还没有变成零的数就是两数的最大公约数。
![辗转相除法的演示动画](/imgs/summary/Euclidean_algorithm_252_105_animation_flipped.gif)
参考辗转相除法的演示动画：两条线段分别表示252和105，其中每一段表示21。动画演示了循环从大数中减去小数，直到其中一段的长度为0，此时剩下的一条线段的长度就是252和105的最大公约数。

<!--more-->

## 关于最大公约数

- **基础定义**

欧几里得的辗转相除法计算的是两个自然数a和b的最大公约数g，意思是能够同时整除a和b的自然数中最大的一个。两个数的最大公约数通常写成gcd(a, b)，如果有gcd(a, b)==1，则有a，b互质。
![最大公约数](/imgs/summary/150px-Square_tiling_24x60.svg.png)
参考最大公约数的演示动画：一个24×60的长方形正好被十个12×12的方格填满，其中12是24和60的最大公约数。一般地，当且仅当c是a和b的公约数时，a×b尺寸的长方形可被边长c的正方形正好填满。

- **环论定义**

在数学中，尤其是高等数学的环论中，最大公约数有一个更加巧妙的定义：a和b的最大公约数g是a和b的线性和中（绝对值）最小的一个，即所有形如ua + vb（其中u和v是整数）的数中（绝对值）最小的数。所有ua + vb都是g的整数倍（ua + vb = mg，其中m是整数）。
## 举例
例如，计算a = 1071和b = 462的最大公约数的过程如下：从1071中不断减去462直到小于462（可以减2次，即商q0 = 2），余数是147：
1071 = 2 × 462 + 147.
然后从462中不断减去147直到小于147（可以减3次，即q1 = 3），余数是21：
462 = 3 × 147 + 21.
再从147中不断减去21直到小于21（可以减7次，即q2 = 7），没有余数：
147 = 7 × 21 + 0.
此时，余数是0，所以1071和462的最大公约数是21。

## 算法
### 递归

```
int Gcd(int a, int b)
{
    if(b == 0)
        return a;
    return Gcd(b, a % b);
}

```
### 迭代

```
int Gcd(int a, int b)
{
    while(b != 0)
    {
        int r = b;
        b = a % b;
        a = r;
    }
    return a;
}

```

# 扩展欧几里得算法
## 介绍
扩展欧几里德算法是用来在已知a, b求解一组p，q使得`p*a+q*b=Gcd(a,b)`(根据数论中的相关定理解一定存在，不展开叙述)。扩展欧几里德常用在求解模线性方程及方程组中。
## 算法

```
int exGcd(int a, int b, int &x, int &y)
{
    if(b == 0)
    {
        x = 1;
        y = 0;
        return a;
    }
    int r = exGcd(b, a % b, x, y);
    int t = x;
    x = y;
    y = t - a / b * y;

    return r;
}

```
## 理解
把这个实现和Gcd的递归实现相比，发现多了下面的x,y赋值过程，这就是扩展欧几里德算法的精髓。
可以这样思考:
对于`a' =b` , `b' =a%b` 而言，我们求得x, y使得`a' x+b' y=Gcd(a', b')`
由于`b' = a % b = a - a / b * b`
那么可以得到:

```
a' x + b' y = Gcd(a' , b')
===>
bx + (a - a/b *b)y = Gcd(a' , b') = Gcd(a, b)  //注意到这里的/是C语言中的出发
===>
ay + b(x- a/b *y) = Gcd(a, b)

```
因此对于a和b而言，他们的相对应的p，q分别是 y和(x-a/b*y)

# Stein算法
## 介绍
Stein算法由J. Stein 1961年提出，这个方法也是计算两个数的最大公约数。和欧几里德算法 算法不同的是，Stein算法只有整数的移位和加减法，因此对于大素数Stein将更有优势。
## 描述
注意到如下结论：
1. 如果A=0，B是最大公约数，算法结束
2. 如果B=0，A是最大公约数，算法结束
3. 设置A1=A、B1=B和C1=1
4. 如果An和Bn都是偶数，则An+1=An>>1，Bn+1=Bn>>1，Cn+1=Cn<<1
5. 如果An是偶数，Bn不是偶数，则An+1=An>>1，Bn+1=Bn，Cn+1=Cn(很显然，2不是奇数的约数)
6. 如果Bn是偶数，An不是偶数，则Bn+1=B>>1，An+1=An，Cn+1=Cn(很显然，2不是奇数的约数)
7. 如果An和Bn都不是偶数，则An+1=|An-Bn|>>1，Bn+1=min(An,Bn)，Cn+1=Cn
8. n加1，转1

## 算法

```
int Gcd(int a, int b)
{
    if(a == 0) return b;
    if(b == 0) return a;
    if(a % 2 == 0 && b % 2 == 0) return 2 * gcd(a >> 1, b >> 1);
    else if(a % 2 == 0)  return gcd(a >> 1, b);
    else if(b % 2 == 0) return gcd(a, b >> 1);
    else return gcd(abs(a - b), Min(a, b));
}

```

# 应用
- **求解不定方程**

对于不定整数方程pa+qb=c，若 c mod Gcd(p, q)=0,则该方程存在整数解，否则不存在整数解。
上面已经列出找一个整数解的方法，在找到p * a+q * b = Gcd(p, q)的一组解p0,q0后，p * a+q * b = Gcd(p, q)的其他整数解满足：

1. p = p0 + b/Gcd(p, q) * t
2. q = q0 - a/Gcd(p, q) * t(其中t为任意整数)

至于pa+qb=c的整数解，只需将p * a+q * b = Gcd(p, q)的每个解乘上 c/Gcd(p, q) 即可。
在找到p * a+q * b = Gcd(a, b)的一组解p0,q0后，应该是得到p * a+q * b = c的一组解p1 = p0*(c/Gcd(a,b)),q1 = q0*(c/Gcd(a,b))，p * a+q * b = c的其他整数解满足：

1. p = p1 + b/Gcd(a, b) * t
2. q = q1 - a/Gcd(a, b) * t(其中t为任意整数)

此处的p 、q就是p * a+q * b = c的所有整数解。

- **求解模线性方程（线性同余方程）**

同余方程 ax≡b (mod n)对于未知数 x 有解，当且仅当 gcd(a,n) | b。且方程有解时，方程有 gcd(a,n) 个解。
求解方程 ax≡b (mod n) 相当于求解方程 ax+ ny= b, (x, y为整数)。
设 d= gcd(a,n)，假如整数 x 和 y，满足 d= ax+ ny(用扩展欧几里德得出)。如果 d| b，则方程a* x0+ n* y0= d， 方程两边乘以 b/ d，(因为 d|b，所以能够整除)，得到 a* x0* b/ d+ n* y0* b/ d= b。
所以 x= x0* b/ d，y= y0* b/ d 为 ax+ ny= b 的一个解，所以 x= x0* b/ d 为 ax= b (mod n ) 的解。
ax≡b (mod n)的一个解为 x0= x* (b/ d ) mod n，且方程的 d 个解分别为 xi= (x0+ i* (n/ d ))mod n {i= 0... d-1}。
设ans=x*(b/d),s=n/d;
方程ax≡b (mod n)的最小整数解为：(ans%s+s)%s;

- **求解模的逆元**

同余方程ax≡b (mod n)，如果 gcd(a,n)== 1，则方程只有唯一解。
在这种情况下，如果 b== 1，同余方程就是 ax=1 (mod n ),gcd(a,n)= 1。这时称求出的 x 为 a 的对模 n 乘法的逆元。
对于同余方程 ax= 1(mod n )， gcd(a,n)= 1 的求解就是求解方程ax+ ny= 1，x, y 为整数。这个可用扩展欧几里德算法求出，原同余方程的唯一解就是用扩展欧几里德算法得出的 x 。

# 引用
- [最大公约数(Gcd)两种算法(Euclid && Stein) [整理]](http://www.cnblogs.com/drizzlecrj/archive/2007/09/14/892340.html)
- [辗转相除法](http://zh.wikipedia.org/wiki/%E8%BC%BE%E8%BD%89%E7%9B%B8%E9%99%A4%E6%B3%95)
- [ACM 进阶学习第一课----同余相关之欧几里得算法及其扩展（2）](http://blog.csdn.net/suool/article/details/14094255)

# 更新日志
- 2015年03月18日 拖了这么久，总算写完了= =。
- 2015年03月19日 补充了扩展欧几里得算法的一些应用。