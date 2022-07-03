---
title: 数论笔记
categories: 模板
tags:
    - 算法模板
    - 线性筛
    - ExGCD
    - 乘法逆元
---

卷王xy在CF1581的一道800分题中用了逆元，导致我被迫复习数学..

从老博客搬运过来...

<!-- more -->

upd: 2022.6

学数学得到的启发：废话不用写上来

之后的内容会尽可能精确，标准就是以我的角度能让零基础的人看懂即可

# 数论基础

## 素数筛法

不超过N的质数大约有N/lnN个，即每lnN个数中大约有一个质数

给定一个整数N，求出1-N中的所有质数，称为质数筛选问题

### Erathothenes筛法
埃筛基于的想法非常朴素，对于任意整数x，它的倍数2x,3x,...都不是质数，我们可以从2开始扫描，将扫描的数标记为合数，当扫描到一个数时，如果它没有被标记，那么这个数就是质数

容易发现6会被2和3重复标记，再次发现小于$x^2$的x的倍数在扫描更小的数的时候就已经被标记过了，因此有这么一个小优化

{% fold code %}
```cpp
void primes(int n) {
    memset(v, 0, sizeof(v));
    for (int i=2; i<=n; i++) {
        if (v[i]) continue;
        prime[++length]=i;
        for (int j=i; j<=n/i;j++) v[i*j]=1;
    }
}
```
{% endfold %}

埃筛的复杂度可以达到O(NloglogN)

然而我们就会发现，还是过不了被修正并优化过的[线性筛素数](https://www.luogu.com.cn/problem/P3383)

### 欧拉筛
即使在优化后，埃筛还是会重复标记合数，根本原因是我们没有确定出唯一产生12的方式，例如12又会被2标记又会被3标记

线性筛法通过“从小到大累积质因子”的方式标记每个合数，即让12只有$3* 2 *2$的方式产生，设数组v记录每个数的最小质因子，我们按照以下步骤维护v
1. 一次考虑2-N之间的每一个数i
2. 若v[i]=i，说明i是质数，把它保存下来
3. 扫描不大于v[i]的每个质数p，令 $v[i * p]=p$ ，也就是在i的基础上积累一个质因子p。因为$p\leq v[i]$，所以p就是合数i*p的最小质因子

每个合数i*p只会被它的最小质因子p筛一次，时间复杂度为O(n)

线性筛给定n和q次询问，问第k小素数，范围小于n，这是代码

中途拿vector和静态数组存储答案比较了一下，vector确实略慢于静态数组

{% fold code %}
```cpp
#include <bits/stdc++.h>

const int N = 1e8+10;

int num;
int v[N], prime[N];
std::vector<int>ans;

inline int read() {
    int x=0,f=1;
    char ch=getchar();
    while (!isdigit(ch)) f=(ch=='-') ? -1 : 1, ch=getchar();
    while (isdigit(ch)) x=x*10+(ch-'0'), ch=getchar();
    return x * f;
}

void primes(int n) {
    for (int i = 2; i <= n; i++) {
        if (!v[i]) {
            v[i] = i;
            ans.push_back(i), num++;
        }
        for (int j = 0; j < num; j++) {
            if (ans[j] > v[i] || ans[j] > n / i) break;
            v[i * ans[j]] = ans[j];
        }
    }
}

int main() {
    int n, q;
    n = read(), q = read();
    primes(n);
    while (q--) {
        n = read();
        printf("%d\n", ans[n-1]);
    }
    return 0;
}
```
{% endfold %}

用bitset代替bool 时间从4.8->3s 空间开销从416M到46M 还行

{% fold code %}
```cpp
#include <bits/stdc++.h>

const int N=1e8+10;

std::bitset<N> num;
std::vector<int> ans;

inline int read() {
    int x = 0, f = 1;
    char ch = getchar();
    while (!isdigit(ch)) f=(ch=='-') ? -1 : 1, ch=getchar();
    while (isdigit(ch)) x=x*10+(ch-'0'), ch=getchar();
    return x*f;
}

void write(int f) {
    if (f > 9)
        write(f / 10);
    putchar(f % 10 + '0');
}

void primes(int n) {
    for (int i=2; i<=n; i++) {
        if (!num[i])
            ans.push_back(i);
        for (int j=0; j<ans.size(); j++) {
            if (i*ans[j]>n) break;
            if (i%ans[j]==0) break;
            num[i*ans[j]]=1;
        }
    }
}

int main() {
    int n = read(), q = read();
    primes(n);
    while (q--) {
        n = read();
        write(ans[n-1]);
        puts("");
    }
}
```
{% endfold %}
## GCD

已知两个数a,b 求gcd(a,b)

### 辗转相减
其实直接上辗转相除就好 但辗转相减有利于理解扩欧

假设a>b，则有gcd(a,b)=gcd(a-b,b)，不断地利用大的数减去小的数，就能的得到最大公约数

证明：
不妨设g为a，b的最大公约数
$g|a,g|b$，有$a= k_1 * g$,$b=k_2 * g$
显然对于a，b的任意公约数，a-b即$(k_1-k_2) * g$变化的只是两者不同的系数，且这两个系数很容易得出互质的结论，最后当一个数变为(1-1)*g=0时，剩下的数字就会是g，于是我们便得到了最大公约数

{% fold code %}
```cpp
while (a!=b) {
    a > b ? a-=b : b-=a;
}
```
{% endfold %}

### 辗转相除-欧几里得

我们一般说的GCD都是指欧几里得辗转相除法

在上述过程中 发现一直减的过程就是%

不妨设 $a=q* b+r$,$r\in [0,b)$, 显然$r=a\ mod\ b$，对于a，b的任意公约数d，因为d|a,d|b，所以d|(a-q*b)，因此d也是b，r的公约数

换而言之令d|b,d|r,易得d|a，因此a，b的公约数集合与b,a mod b的公约数集合相同，最大公约数自然也相同

{% fold code %}
```cpp
int gcd(int a, int b) {
    return b ? gcd(b, a%b) : a;
}

int main() {
    int a, b;
    scanf("%d%d", &a, &b);
    if (a < b) a^=b^=a^=b;
    printf("%d", gcd(a, b));
    return 0;
}
```
{% endfold %}
### 例题-[NOIP2009 Hankson的趣味题](https://www.luogu.com.cn/problem/P1072)
有n个询问，在每个讯问中，先给定四个自然数a,b,c,d，然后求有多少个x满足gcd(a,x)=c并且lcm(b,m)=d

有一个很奇怪的基本0思维难度以至于都不会选择的方法可以直接过掉

判断b1的所有约数，统计答案，居然能过？？？
这个的复杂度在$O(n\sqrt{d}logd)$的算法，本来只应该有90分，居然能过题
{% fold code %}
```cpp
#include<bits/stdc++.h>
#define int long long

inline int read() {
    int x=0,f=1; char ch=getchar();
    while (!isdigit(ch)) f=(ch=='-')?-1:1,ch=getchar();
    while (isdigit(ch)) x=x*10+(ch-'0'),ch=getchar();
    return x*f;
}

int gcd(int a,int b) {
    return b?gcd(b,a%b):a;
}

int lcm(int a,int b) {
    return a*b/gcd(a,b);
}

signed main() {
    int n; n=read();
    int a0,a1,b0,b1,x,ans,t;
    while (n--) {
        ans=0;
        a0=read(),a1=read(),b0=read(),b1=read();
        for (int i=1;i*i<b1;i++) {
            if(b1%i==0) {
                t=b1/i;
                if(gcd(a0,i)==a1&&lcm(b0,i)==b1)
                    ans++;
                if(gcd(a0,t)==a1&&lcm(b0,t)==b1)
                    ans++;
            }
        }
        int tem=sqrt(b1);
        if (tem*tem==b1)
            if(gcd(a0,tem)==a1&&lcm(b0,tem)==b1)
                ans++;
        printf("%d\n",ans);
    }
}
```
{% endfold %}
看了一圈貌似大部分都这么写，严格的来讲不打一个素数表是过不了的

## ExGCD
### 根的存在性定理
扩展欧几里得算法，常用于求$a * x + b * y=c$的一组可行解

其实通过辗转相减就可以观察的比较明显，$a$,$b$辗转相减的时候相当于$a * x$+$b * y$=x中$x$,$y$不断变化的过程，我们知道这个过程一定可以得到最大公约数，所以$a * x + b * y=gcd(a,b)$这个方程是肯定有解的

那么，当c为gcd($a,b$)的倍数时，该方程有解，而且我们可以两边同时除以一个最大公约数得到一个新的式子：
$$k_1 * x + k_2 * y = c/g. gcd(k_1,k_2)=1$$

由辗转相减可得，$k_1 * x + k_2 * y = 1$肯定有解，所以$k_1 * x + k_2 * y = c/g$在c是最大公约数的倍数时肯定有解，这就是裴蜀定理

### 特解和通解
所以我们只要秋姐形如：
$$a * x+b * y=gcd(a,b)$$
然后扩大$c/gcd(a,b)$倍即可
观察这个式子，用辗转相减去理解，假设已经求出了
$$(a-b) * x_1+b * y_1=gcd(a,b)$$
的姐$x_1,y_1$，那么变换一下得到$a * x_1+b * (y_1-x_1)=gcd(a,b)$，可以得到
$$x=x_1,y=y_1-x_1$$
对于辗转相除也是同理，原问题就变成了秋姐
$$b * x_1+(a\ mod\ b) * y_1=gcd(a,b)$$

又
$$a\ mod\ b=a-a/b * b$$
则有
$$b * x_1 + (a-a/b * b) * y_1=gcd(a,b)$$
整理得到
$$x=y_1,y=x_1-a/b * y_1$$

因此我们只要一直缩小问题规模，一直得到$gcd(a,b) * x+0 * y=gcd(a,b)$，得到一组特解(1,0)，再将这组特解反推回去就知道原方程的一组特解了
{% fold code %}
```cpp
int ExGCD(int a, int b, int &x, int &y) {
    int g = a;
    if (g) {
        g = ExGCD(b, a % b, x, y);
        x -= a / b * y;
        std::swap(x, y);
    }
    else
        x = 1, y = 0;
    return g;
}
```
{% endfold %}
函数返回得到的(x,y)是方程的一组特解，通解的变化规律就是x和y的值往相反的方向变化，设这个变化的最小单位量是$d_1,d_2$
$$a * (x+d_1)+b * (y-d_2)=gcd(a,b)$$
$$a * d_1=b * d_2$$
两边同除$gcd(a,b)$令$k_1=a/gcd(a,b),k_2=b/gcd(a,b)$，这个时候$k_1,k_2$显然互质，即$k_1=d_2,k_2=d_1$
可得通解为$(x+k * d_1,y-k * d_2)$，即
$$(x+k * b/gcd(a,b), y-k * a/gcd(a,b))$$

## 欧拉函数
### 定义
欧拉函数$\varphi(n)$表示[1,n]中与n互质的个数，例如$\varphi(p)=p-1$,$\varphi(1)=1$

### 计算式
首先考虑一个质数$p$,先来证一下结论：
$$\varphi(p^c)=p^c-p^{c-1}$$
对于质数p而言，$[1，p^c]$中与$p^c$不互质的数有$p^{c-1}$个，即$p^k$,$k\in[1,c)$，相当于用总数减去p的倍数

推广而言，我们容易发现$\varphi(p_1^{k_1}* p_2^{k_2})=\varphi(p_1^{k_1})*\varphi(p_2^{k_2})$，容斥一下也就是总数-$p_1$的倍数-$p_2$的倍数，再加上$p_1$、$p_2$的倍数

$$N-\frac{N}{p_1}-\frac{N}{p_2}+\frac{N}{p_1p_2}=N(1-\frac{1}{p_1})(1-\frac{1}{p_2})$$

事实上这就是欧拉函数的积性性质

积性函数：
若n,m互质，有f(mn)=f(m)*f(n)，则称函数为积性函数
若此质因数分解之后，$\forall n=p_1^{k_1}p_2^{k_2}...p_c^{k^c}$，有
$$\varphi(n)=\prod_{i=1}^c{p_i^{k_i-1}(p_i-1)}= n * \prod_{i=1}^c{\frac{p_i-1}{p_i}}= n * \prod_{i=1}^{c}({1-\frac{1}{p_i}})$$

根据计算式，只要分解质因数，即可顺便求出欧拉函数
{% fold code%}
```cpp
int phi(int n) {
    int ans=0;
    for (int i=2;i*i<=n;i++) {
        if (n%i==0) {
            ans = ans / i * (i-1);
            while (n%i==0) n/=i;
        }
    }
    if (n>1) ans=ans/n*(n-1);
    return ans;
}
```
{% endfold %}
这个过程可以用Pollar Rho优化

### 欧拉函数的性质
1. 若$gcd(m,n)$，则有$\varphi(m * n)=\varphi(m) * \varphi(n)$
2. 当$n$为奇数时，$\varphi(2 * n)=\varphi(n)$
3. $\varphi(n)=\varphi(n/p)*p$，前提是 $(p|(n/p))$，可以用计算式推导，证明不难
4. $\varphi(n)=\varphi(n/p) * (p-1)$，前提是$gcd(p,(n/p))$互质，且$p$是质数，积性函数，证明不难
5. $\sum_{d|n}{\varphi(d)}=n$

性质5证明：设$f(n)=\sum_{d|n}\varphi(d)$，若$n,m$互质
$$f(nm)=\sum_{d|nm}{\varphi(d)}=(\sum_{d|n}{\varphi(d)}) * (\sum_{d|m}{\varphi(d)})=f(n) * f(m)$$
所以$f(n)$是积性函数
$$f(p^m)=\sum_{d|p^m}{\varphi(d)}=\varphi(1)+\varphi(p)+...+\varphi(p^m)=p^m$$
因此
$$f(n)=\prod_{i=1}^{m}{f(p_i^{c_i})} = \prod_{i=1}^{m}{p_i^{c_i}} =n$$

## 同余
### 定义
若整数a和整数b除以正整数m的余数相等，则称a,b模m同余，记为$a\equiv b$(mod m)

### 同余类和剩余系
对于$\forall a\in [0,m-1]$，集合${a+km}(k\in \mathbb{Z})$的所有数模m同余，余数都是a，该集合称为一个模m的**同余类**，记为$\bar{a}$

模m的同余类一共有m个，分别为$\bar{0},\bar{1},...\bar{m-1}$，他们构成m的完全剩余系

1-m中与m互质的数代表的同余类一共有$\varphi(m)$个，它们构成m的简化剩余系

简化剩余系关于模m乘法封闭。这是因为若a,b$(1\leq a,b \leq m)$与m互质，则 a * b 也不可能与m含有相同的质因子，即 a * b 也与m互质，即a * b mod m也属于m的简化剩余系

### 费马小定理
若p是质数，则对于任意整数a，有$a^p \equiv a$ (mod p)

### 欧拉定理
若正整数a,n互质，则$a^{\varphi(n)}\equiv 1\pmod n$，其中$\varphi(n)$为欧拉函数

### 证明
设n的简化剩余系为${\bar{a_1},\bar{a_2},...,\bar{a_{\varphi(n)}}}$。对$\forall a_i, a_j$

若$a*a_i \equiv a * a_j(mod\ n)$

则$a*(a_i - a_j)$ $\equiv 0$

因为a,n互质，所以$a_i - a_j \equiv 0$，即$a_i \equiv a_j$,故当$a_i \neq a_j$时，$aa_i$，$aa_j$也代表了不同的同余类

又因为简化剩余系关于模n乘法封闭，故$\bar{aa_i}$也在简化剩余系的集合中，因此，集合${\bar{a_1},\bar{a_2},...,\bar{a_{\varphi(n)}}}$与集合${\bar{aa_1},\bar{aa_2},...,\bar{aa_{\varphi(n)}}}$都能表示n的简化剩余系

综上所述
$$a^{\varphi(n)}a_1a_2...a_{\varphi(n)} \equiv (aa_1)(aa_2)...(aa_{\varphi(n)}) \equiv a_1a_2...a_{\varphi(n)} (mod \ n)$$

所以$a^{\varphi(n)} \equiv 1(mod \ n)$

当p是质数时，$\varphi(p)=p-1$，并且只有p的倍数与p不互质。所以，只要a不是p的倍数，就有$a^{p-1}\equiv 1(mod \ p)$，两边同乘a就是费马小定理，另外若a是p的倍数，费马小定理显然成立

### 扩展欧拉定理

1. $gcd(a,p)=1$，则有$a^b \equiv 1$

若正整数a,n互质，则对于任意正整数b，有$a^b \equiv a^{b\ mod \ \varphi(n)}(mod\ n)$

证明：
设$b \ = \ q*\varphi(n)+r$，其中$0 \leq r < \varphi(n)$，即$r=b\ mod\ \varphi(n)$
于是有：

$$a^b \equiv a^{q*\varphi(n)+r} \equiv (a^{\varphi(n)})^q\ *a^r \equiv\ 1^q\ *a^r \equiv a^r \equiv\ a^{b\ mod\ \varphi(n)}(mod\ n)$$

### 指数循环节
**当a,n不一定互质且b$\geq \varphi(n)$时，有$a^b\ \equiv\ a^{b\ mod\ \varphi(n) + \varphi(n)}$**

证明太难，咕了

## 线性同余方程
给定正数$a,b,m$，求一个整数$x$满足$a * x \equiv b(mod\ m)$，或者给出无解。因为未知数的指数为1，所以我们称之为一次同余方程，也称线性同余方程

$a * x\equiv b(mod\ m)$，等价于$a * x-b$是$m$的倍数，不妨设为$-y$倍，于是，该方程可以改写成$a * x+m * y = b$，根据裴蜀定理及其证明过程，线性同余方程有解当且仅当$gcd(a,m)|b$。

在有解时，先用扩欧求出一组整数$x_0,y_0$，满足$a * x_0+m * y_0=gcd(a,m)$然后扩大$b/gcd(a,m)$就是原线性同余方程的一个解

方程的通解则是所有模$m/gcd(a,m)$与$x$同余的整数

### [NOIP2021 同余方程](https://www.luogu.com.cn/problem/P1082)
求关于$x$的同余方程 $a x \equiv 1 \pmod {b}$ 的最小正整数解。`
{% fold code%}
```cpp
#include<bits/stdc++.h>
void exgcd(int a,int b,int &x,int &y){
    if (b) {
        exgcd(b,a%b,y,x);
        y-=a/b*x;
    }
    else
        x=1, y=0;
}
int main() {
    int a,b,x,y;
    scanf("%d%d",&a,&b);
    exgcd(a,b,x,y);
    printf("%d",((x%b)+b)%b);
    return 0;
}
```
{% endfold %}


### 乘法逆元
若整数$a,b$互质，并且$b|a$，则存在一个整数$x$，使得$a/b \equiv a * x \pmod m$，则称$x$为$b$的模$m$乘法逆元，记为$b^{-1}\pmod m$

因为$a/b \equiv a * b^{-1}\equiv a/b * b * b^{-1}\pmod m$，所以$b * b^{-1}\equiv 1\pmod m$

因为$m$是质数，

# 整除分块

# 狄利克雷卷积

Dilichlet Convolution

定义在数论函数间的一种二元运算

$f*g=\sum\limits_{d\mid{n}}f(d)g(\frac{n}{d})$

## 一些常用的数论函数

* $\varepsilon(n)=\begin{cases} 1,while\ n=1\\0,otherwise\end{cases}$
* 

# 莫比乌斯反演

## 定义：

常数函数 <b>$1$</b> 的 Dirichlet Inverse，我们将其称为莫比乌斯函数 $\mu$

$\displaystyle\mu(n) = \begin{cases}\frac{1}{1(n)} & while\ n =1 \\ -\frac{1}{1(1)}\sum\limits_{d\mid n,d>1}1(d)\mu(\frac{n}{d}) & otherwise\end{cases}$

$\forall$ 素数 $p$，有 $\mu(p) = -1, \mu(p^k)=0\ (k>1)$

即$\mu(n) = \begin{cases}1 & while\ n=1 \\ (-1) ^ m & while\ n=p_1p_2\dots p_m \\ 0 & otherwise \end{cases}$

其中 $p_1,\dots,p_m$ 都是质数

## 莫比乌斯反演公式

$\displaystyle g(n) = \sum\limits_{d\mid n}f(d) \iff f(n) = \sum\limits_{d\mid n}\mu(d)g(\frac{n}{d})$，卷积形式表示为 $\displaystyle g=f*1 \iff g * u = f$

## 线性筛 $O(N)$ 求 $\mu(n)$

```cpp
bitset<N> vis;
int mu[N];
vector<int> primes;
void init() {
  mu[1] = 1;
  for (int i = 2; i <= n; i++) {
    if (vis[i] == 0) {
      primes.push_back(i);
      mu[i] = -1;
    }
    for (auto p : primes) {
      if (p * i > n)
        break;
      vis[p * i] = 1;
      if (p % i == 0) {
        mu[i] = 0;
        break;
      } else
        mu[p * i] = mu[p] * mu[i];
    }
  }
}
```

## 性质

$\mu$ 满足

$$\sum\limits_{d\mid n}\mu(d) = \begin{cases} 1 & while\ n = 1 \\ 0 & otherwise \end{cases}$$

即 
$$\varepsilon = \mu * 1,\sum\limits_{d\mid n}\mu(d)=[n==1]=\varepsilon(n)$$

### 证明：

设 $n=\prod\limits_{i=1}^k{p_{i}^{c_i}}$, $n'=\prod\limits_{i=1}^k{p_i}$

有
$$\sum\limits_{d\mid n}{\mu(d)}=\sum\limits_{d\mid n'}\mu(d)=\sum\limits_{i=0}^k{C^i_k (-1)^i}=(1+(-1))^k$$

当 $k = 0$ 时，$n =1$，值为 $1$，其余为 $0$

二项式定理： $(x+y)^n=\sum\limits_{k=0}^n{\mathrm(_k^n)x^{n-k}y^k}$

## 莫比乌斯变换

## 莫比乌斯逆变换（反演）

设 $f$, $g$ 为两个数论函数

若 $$x = \prod\limits p_i^{c_i}$$