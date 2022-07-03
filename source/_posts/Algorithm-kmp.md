---
title: 基础字符串模板复习
tags:
	- 算法模板
  - KMP
  - Manacher
categories: 模板
mathjax: true
---

做了 NOIP2002TG 子串变换 之后来复习KMP..

两年没搞果真是肾摸都不记得了

<!-- more -->

## KMP

KMP 线性时间内判定字符串A[1-N]是否为字符串B[1-M]的子串 并求出字符串A在字符串B中每次出现的位置

首先是一个正常匹配的思路

```cpp
void Check(string a,string b) {
  if (b.size() < a.size()) return;
  for (int i = 0; i <= b.size() - a.size(); i++) {
    if (b[i] == a[0]) {
      bool flag = true;
      for (int j = 0; j < a.size(); j++)
        if (a[j] != b[i+j]) {flag = false; break;}
      if (flag) cout << i + 1 << ' ';
    }
  }
}
```

这样是O(nm)的暴力匹配 改一改就能求匹配的最长前缀之类的信息...

### KMP

#### 构造next数组

对模式串A进行自我匹配 定义

next[i] = max{j | j < i, A[i-j+1 \~ i] = A[1 \~ j]}

即对于A的子串A[1 ~ i]部分 定义next[i]为满足长为j的前缀和后缀相等的j最大值

根据定义 有next[1]=0

**引理: 如果$j_0$是集合$j$里面的一个元素 那么小于$j_0$的最大的元素就是next[$j_0$]**

根据定义可以很容易得到

我们发现next[i]找了一个最大的子串 满足一个又是前缀又是后缀的性质

那么如果我们想要找到小于$j_0$的最大元素 当前重合部分子串长度为$j_0$

满足A[i-$j_0$+1 ~ i] = A[1 ~ $j_0$] 我们想要得到一个$j_1$

有A[i-$j_1$+1 ~ i] = A[1 ~ $j_1$] 并且$j_1$<$j_0$

但作为第二长的公共部分 其实是有A[$j_0$-$j_1$+1 ~ $j_0$] = A[i-$j_1$+1 ~ i] = A[1 ~ $j_1$]

因为子串A[1~ i]的后$j_1$部分就是子串A[1 ~$j_0$]的后半部分 $j_1$作为第二长的前缀|后缀 是被覆盖了的

那么根据定义 $j_1$就是next[$j_0$] 画画图很容易就发现了

算法竞赛进阶指南上用反证法 也是一个道理

如果有$j_0$>$j_2$>$j_1$ 那么$j_2$才是next[$j_0$]

#### 用引理求解next

如果j是next[i]的一个集合元素 那么j-1就是next[i-1]的一个集合元素

反过来，我们在求next[i]的时候 就可以用next[i-1]+1 next[next[i-1]]+1作为集合j的元素

实际上就是不断的缩短前缀长度 达到匹配的效果

而每次可能的选择都是当前能取到的最大值 自然就维护了next最长子串的性质

#### 求模式串的next

```cpp
string pattern, target;
cin >> target >> pattern;
target='$'+target;
pattern='$'+pattern;
int n=pattern.size(), m=target.size();
n--, m--;
nxt[1]=0;
for (int i = 2, j = 0; i <= n; i++) {
  while (j > 0 && pattern[i] != pattern[j+1]) j = nxt[j];
  if (pattern[i] == pattern[j+1]) j++;
  nxt[i] = j;
}
```

#### 匹配目标串

对模式串pattern和目标串target进行匹配 求出一个数组f f[i]表示B中以i为结尾的子串 和 A的前缀能够匹配的最长长度

f[i] = max{j | j < i, B[i-j+1 \~ i] = A[1 \~ j]}

考虑已经满足条件的情况下 匹配下一位target[i] 现在手上的是j

如果target[i]==j+1 那么j++ 表示匹配的长度增加了一位

不然的话意味着模式串的第j+1位失配 自然用到了next数组

```cpp
for (int i = 1, j = 0; i <= m; i++) {
  while (j > 0 && target[i] != pattern[j+1]) j=nxt[j];
  if (target[i]==pattern[j+1]) j++;
  if (j == n) { // 这个时候就匹配到了
    /* Code */
  }
}
```

#### [洛谷P3375[模板]KMP字符串匹配](https://www.luogu.com.cn/problem/P3375)
{% fold code %}
```cpp
#include <bits/stdc++.h>
using namespace std;
int nxt[1000005];

int main() {
  string pattern, target;
  cin >> target >> pattern;
  target='$'+target;
  pattern='$'+pattern;
  int n=pattern.size(), m=target.size();
  n--, m--;
  nxt[1]=0;
  for (int i = 2, j = 0; i <= n; i++) {
    while (j > 0 && pattern[i] != pattern[j+1]) j = nxt[j];
    if (pattern[i] == pattern[j+1]) j++;
    nxt[i] = j;
  }
  for (int i = 1, j = 0; i <= m; i++) {
    while (j > 0 && target[i] != pattern[j+1]) j=nxt[j];
    if (target[i]==pattern[j+1]) j++;
    if (j == n) { // 这个时候就匹配到了
      cout << i - n + 1 << endl; // 输出模式串的匹配到的第一个位置
    }
  }
  for (int i = 1; i <= n; i++) cout << nxt[i] << ' ';
  return 0;
}
```
{% endfold %}
观察一下 发现整个过程j=nxt[j]的变化幅度不超过j增加的幅度总和

j每次循环至多增加1 所以总体复杂度为O(N+M)

这个在notepad里面有很多应用 不过用的更多是BM?

不太清楚 matrix回复恢复里看到是这么个说法

水B站的时候发现的 [皮套人现在都这么卷了吗](https://www.bilibili.com/video/BV1234y1y7pm) 麻了