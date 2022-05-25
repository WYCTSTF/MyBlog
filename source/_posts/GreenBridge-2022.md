---
title: 备战2022绿桥杯国赛
categories: OI/ACM
---

有一说一咸鱼摸了好久的时间，并没有什么备战想法

就是花一两天把dyy整理的题目全部做一下，暂时想的是这样

就今年那个省赛A，难度上天，C的最后一题好像也是个莫队，不知道今年国赛B难度如何

<!-- more -->

## 2021国赛 - Java B组 H 巧克力

[原题链接](https://www.lanqiao.cn/problems/1596/learning/)

按过期时间排个序，先正着考虑时间，发现如果选一个价格最小的，可能会拿一个保质期比较大的，导致后面选不了，因而本来是有解的，算成了无解

那么只要倒着考虑，天数从x往前即可

{% fold code %}
```cpp
#include <bits/stdc++.h>
using namespace std;
struct Node {
  int a, b, c;
  bool operator<(const Node &A) const {
    return a > A.a;
  }
};
bool cmp(const Node &x, const Node &y) {
  return x.b > y.b;
}
int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(nullptr);
  int x, n;
  cin >> x >> n;
  vector<Node> a(n);
  for (int i = 0; i < n; i++)
    cin >> a[i].a >> a[i].b >> a[i].c;
  sort(a.begin(), a.end(), cmp);
  long long ans = 0; int st = 1;
  priority_queue<Node>q;
  q.push(a[0]);
  while (!q.empty() && x) {
    while (st < n && a[st].b >= x) q.push(a[st]), st++;
    auto it = q.top(); q.pop();
    it.c--; ans += it.a; x--;
    if (it.c>0) q.push(it);
  }
  if (x) puts("-1");
  else cout<<ans;
  return 0;
}
```
{% endfold %}

这题跟那个super market应该一样，有个并查集做法，我感觉是这样

Supermarket那个题是每个商品有利润 $p_i$ 和过期时间 $d_i$，然后是优先卖出利润大的商品，并且在过期之前尽可能晚的卖出去

用堆做，是先按过期时间排序，然后扫描每个商品，满足过期时间大于等于商品数量的同时把总价值提高

这个题从要求上来看不太明显，但感觉和supermarket就是一样的，supermarket还有一种做法是按利润排序，然后每天构成一个并查集，对于当前在 $d$ 过期的商品，找到 $d$ 天的根 $r$，合并 $r$ 和 $r-1$，加上这个利润

这里也一样，可以用并查集来满足限制条件，先按照价格排个序，然后对于当前过期时间为 $d_i$ 的巧克力，维护一个并查集，去找 $d_i$ 的父节点 $r$，合并 $r$ 和 $r-1$

{% fold code %}
```cpp
#include <bits/stdc++.h>
using namespace std;
struct Node {
  int a,b,c;
  bool operator<(const Node &X) const {
    return a<X.a;
  }
}d[100010];
int f[100010];
int find(int o) {return o==f[o]?o:f[o]=find(f[o]);}
int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(nullptr);
  int x,n;
  cin>>x>>n;
  for(int i=1;i<=n;i++) cin>>d[i].a>>d[i].b>>d[i].c;
  for(int i=1;i<=x;i++) f[i]=i;
  sort(d+1,d+1+n);
  long long ans=0;
  int r,nxt,tot;
  for(int i=1;i<=n;i++){
    tot=0; r=find(d[i].b);
    while (r && tot<d[i].c) {
      tot++;
      nxt=find(r-1); f[r]=nxt; r=nxt;
    }
    ans+=1ll*tot*d[i].a;
  }
  if(find(n)==0) cout<<ans;
  else puts("-1");
  return 0;
}
```
{% endfold %}

太久没写题了，这么点东西磨蹭了好久..

## 2021国赛 I 反转括号序列

给定长为 $n$ 的由 $($ 和 $)$ 构成的序列，$m$ 个操作

操作1是选择一个[L, R]反转里面的括号，左变右，右变左

2是给定L，要求找出最大的R，使得[L, R]是一个合法的序列

$n \leq 10^6, m \leq 2*10^5$，给了10s。。