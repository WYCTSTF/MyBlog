---
title: Codeforces Round 779 「B - F」
tags: 
	- Codeforces
	- Trie
	- DP
categories: OI/ACM
---

昨天凌晨发生了一件巨~~(好笑)~~丢人的事 整天处于呆滞状态 所以昨天咕了

D2的trie写了1H+ 人都麻了 现在只想给自己来两个大嘴巴子

页面最好刷新一下 不然有的提示直接显示出来就挺草的

<!-- more -->

## [B. Marin and Anti-coprime Permutation](https://codeforces.com/contest/1658/problem/B)

尝试构造这个gcd的排列 发现系数是奇数的时候 必须填一个偶数

那么n%2==1的时候 偶数数量就会不够 如果n是偶数 那么位置随便 所以答案$(\frac{n}{2})^2$

{% fold code %}
```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int mod=998244353;
signed main() {
	auto fac=[](int x){
		long long ans=1;
		while(x>0){
			ans=ans*x%mod;
			x--;
		}
		return ans;
	};
	int tt;cin>>tt;
	while(tt--){
		int n;cin>>n;
		if(n%2==1) puts("0");
		else cout<<fac(n/2)*fac(n/2)%mod<<endl;
	}
	return 0;
}
```
{% endfold %}

## [C. Shinju and the Lost Permutation](https://codeforces.com/contest/1658/problem/C)

这题我考虑c[]下降的时候傻逼了 wyl给的思路

因为一次右移最多让prefix maximum+1 所以c一次最多+1

如果下降的时候 只要不降到1 (降到1说明此时n在第一位) 那么在满足c上升都是+1上升的条件下

都是可以构造出符合条件的p的..

我比赛的时候尝试具体分析下降幅度 然后考虑p[i]和前几个大小关系 用并查集维护传递关系

只能说做题做傻了..

{% fold code %}
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
	int tt;cin>>tt;
	while(tt--){
		int n; cin>>n;
		vector<int>c(n+1);
		int now=0;
		for(int i=1;i<=n;i++) {cin>>c[i];if(c[i]==1) now=i;}
		vector<int>a;
		for(int i=now;i<=n;i++) a.push_back(c[i]);
		for(int i=1;i<now;i++) a.push_back(c[i]);
		bool flag=true;
		for(int i=1;i<n;i++){
			if(a[i]-a[i-1]>1) {flag=false;break;}
			else if(a[i]==1) {flag=false; break;}
		}
		if(now==0) puts("NO");
		else if(flag) puts("YES");
		else puts("NO");
	}
	return 0;
}
```
{% endfold %}

## [D1. 388535 (Easy Version)](https://codeforces.com/contest/1658/problem/D1)

给定l=0, r [0,r] 异或一个x 变换到给定的数组a[] 求x

{% fold Hint1 %}
不同的数^x得到的答案都不同
{% endfold %}

{% fold solution %}
求一个x 使得a[] xor x得到的答案总和最小，既是[0,r]
{% endfold %}

{% fold code %}
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
	ios_base::sync_with_stdio(false);
	cin.tie(nullptr);
	int tt;cin>>tt;
	for(;tt;tt--){
		vector tot(17,0);
		int l,r,tem; cin>>l>>r;
		for(int i=0;i<=r;i++){
			cin>>tem;
			for(int j=0;j<17;j++) if(tem&(1<<j)) tot[j]++;
		}
		int ans=0;
		for(int i=0;i<17;i++) if(tot[i]>r+1-tot[i]) ans+=(1<<i);
		cout<<ans<<endl;
	}
	return 0;
}
```
{% endfold %}

## [D2. 388535 (Hard Version)](https://codeforces.com/contest/1658/problem/D2)

l没有=0的限制

D1一开始我看到异或就觉得是Trie

但是后来又sb的"专下判"觉得Trie做这个会T..

因为我想的找x是把所有的x放到Trie里面 然后所有a[i]统计一遍 然后计算每个x符合的数量 如果是r-l+1就OK

发现这个是O(N^2)的假思路

正确做法上是把a[]插入到Trie里面 然后枚举$x=l\ xor\ a[i]$ 找x能异或出来的最值边界 恰好是[l,r]就符合条件

然后T了 因为t组数据多测的时候memset炸时间了 看了知乎上严格鸽写的多测时候初始化Trie的方法

写的要脑淤血了 才发现我的tot计数方式是++tot 所以清空tot的时候要清空trie[tot+1][0/1] 麻了

{% fold code %}
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=150010;
int l,r,trie[N*32][2],a[N],tot;
void add(int x){
	int p=0;
	for(int i=16;i>=0;i--){
		int ch=(x>>i)&1;
		if(!trie[p][ch]){
			trie[tot+1][0]=trie[tot+1][1]=0;
			trie[p][ch]=++tot;
		}
		p=trie[p][ch];
	}
}
int qryMx(int x) {
	int ans=0,p=0;
	for(int i=16;i>=0;i--){
		int ch=(x>>i)&1;
		if(trie[p][ch^1]) p=trie[p][ch^1],ans+=(1<<i);
		else p=trie[p][ch];
	}
	return ans;
}
int qryMn(int x){
	int ans=0,p=0;
	for(int i=16;i>=0;i--){
		int ch=(x>>i)&1;
		if(trie[p][ch]) p=trie[p][ch];
		else p=trie[p][ch^1],ans+=(1<<i);
	}
	return ans;
}
void solve(){
	cin>>l>>r;
	tot=0; trie[0][0]=trie[0][1]=0;
	for(int i=1;i<=r-l+1;i++){cin>>a[i],add(a[i]);}
	for(int i=1;i<=r-l+1;i++){
		int x=a[i]^l;
		if(qryMn(x)==l&&qryMx(x)==r){
			cout<<x<<endl;
			return ;
		}
	}
}
int main() {
	int tt;cin>>tt;
	while(tt--){
		solve();
	}
	return 0;
}
```
{% endfold %}

还有个做法是运用成对变换

如果a[i]是偶数 那么a[i]^1=a[i]+1 奇数则是-1

把[l,r] 和 a[]里面的对全部删掉 因为他们可以一一对应起来

那么如果l是奇数 l就没有匹配的东西 如果r是偶数 r没有匹配的项

这时候就可以和剩下来的a[]配对 检查一下

如果l是偶数 r是奇数 那么把所有的值缩小一倍 返回x的时候*2即可

{% fold code %}
```cpp
#include <bits/stdc++.h>
using namespace std;
int solve() {
  int l, r;
  cin >> l >> r;
  vector a(r - l + 1, 0);
  set<int> s;
  for (int i = 0; i < r - l + 1; i++) {
    int tem;
    cin >> tem;
    s.insert(tem);
  }
  int f = 1;
  auto check = [&](int x) {
    for (auto it : s)
      if ((it ^ x) < l || (it ^ x) > r)
        return false;
    return true;
  };
  while (1) {
    set<int> t = s;
    for (auto it : s)
      if (t.find(it ^ 1) != t.end())
        t.erase(it ^ 1);
    if (l % 2 == 1 && r % 2 == 1)
      return f * (*t.begin() ^ l);
    if (l % 2 == 0 && r % 2 == 0)
      return f * (*t.begin() ^ r);
    if (l % 2 == 1 && r % 2 == 0) {
      if (check(*t.begin() ^ l))
        return f * (*t.begin() ^ l);
      else
        return f * (*t.begin() ^ r);
    }
    t.clear();
    for (auto it : s)
      t.insert(it / 2);
    s = t;
    l /= 2;
    r /= 2;
    f *= 2;
  }
}
int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(nullptr);
  int tt;
  cin >> tt;
  while (tt--) {
		cout<< solve()<<endl;
  }
  return 0;
}
```
{% endfold %}

tourist这题写的DP 看不懂...

## [E. Gojou and Matrix Game](https://codeforces.com/contest/1658/problem/E)

从起点开始 每次需要移动到一个权值更大的点 谁走到最后一步 谁就赢了 (废话..)

然后就不会了.. 没学过博弈论 不清楚是不是

{% fold hint %}
题解说是DP
{% endfold %}

发现v[i][j]都是不同的 设dp[a][b]=1表示选择(a,b)的人会赢

显然如果v[a][b]=n 那么dp[a][b]=1

对于dp[i][j] = 1

if for all (i',j') $\in |i-i'|+|j-j'|>k$ 都有 dp[i'][j']=0

if for all (i',j') $\in dp[i'][j']=1$ 都有$|i-i'|+|j-j'|<=k$

那么做法就是维护一个集合S $(i',j' | \in dp[i'][j']=1)$

1. S中加入点(i,j)
2. 检查所有的(i',j')是否满足$|i-i'|+|j-j'|<=k$

这些都很容易想到，， 关键的一个trick是关于绝对值不等式的变换

$$|i-i'|+|j-j'|<=k \iff max(|i+j-(i'+j')|,|i-j-(i'-j')|)\leq k \tag{1}$$

当$i\geq i'$时 有

$$|i-i'|+|j-j'|=max[i-i'+j-j',i-i'+j'-j]=max[(i+j)-(i'+j'),(i-j)-(i'-j')]\tag{2}$$

当$i<i'$时 同理 整理一下就得到了$(1)$式

然后就好处理了.. 只要管一下最大最小的(i'+j') (i'-j') 我是呆逼..

{% fold code %}
```cpp
#include <bits/stdc++.h>
#define rep(i,a,n) for(int i=a;i<=n;i++)
#define per(i,a,n) for(int i=n;i>=a;i--)
#define max(a,b) (a>b?a:b)
#define min(a,b) (a<b?a:b)
#define mp make_pair
#define pb push_back
#define fi first
#define se second
#define il inline
#define rg register
#define SZ(x) ((int)(x).size())
#define all(x) x.begin(),x.end()
using namespace std;
typedef vector<int> VI;
typedef pair<int,int> PII;
typedef long long ll;
typedef double db;
const int INF=0x7fffffff;
const int inf=0x3f3f3f3f;
const int MOD=998244353;
const int mod=1e9+7;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a; }
// head
int main(){
	ios_base::sync_with_stdio(false);
	cin.tie(nullptr);
	int n,k;
	cin>>n>>k;
	vector v(n,vector<int>(n));
	vector<array<int,2>>pos(n*n);
	rep(i,0,n-1){
		rep(j,0,n-1){
			cin>>v[i][j];
			v[i][j]--;
			pos[v[i][j]]={i,j};
		}
	}
	int p1=-inf,p2=-inf,p3=-inf,p4=-inf;
	vector<string>ans(n,string(n,'G'));
	per(i,0,n*n-1){
		auto [x,y]=pos[i];
		if(p1>x+y+k||p2>-x-y+k||p3>x-y+k||p4>y-x+k) continue;
		ans[x][y]='M';
		p1=max(p1,x+y);
		p2=max(p2,-x-y);
		p3=max(p3,x-y);
		p4=max(p4,y-x);
	}
	rep(i,0,n-1) cout<<ans[i]<<'\n';
}
```
{% endfold %}

## [F. Juju and Binary String](https://codeforces.com/contest/1658/problem/F)

题意转化成 给定一个长为n的01串 选出最少的子区间使得0 1的数量恰好达到要求

0和1的数量求个gcd 处理一下至少的值和m是否成倍数关系 判断-1还是k 这个很好搞..

到这里我就无了，， 想了20min 没啥好的区间处理 也想不到DP怎么设 

甚至想过类似环形DP的拓展空间 因为对顺序没什么要求 于是点开tutorial..

看了Hint1 就是判断m啥时候行啥时候不行 看了Hint2.. 开 幕 雷 击

{% fold Hint2 %}
We don't need more than 2 parts, or to say $𝑘\leq2$ is needed in this problem.
{% endfold %}

未曾设想过的道路.. 虽然想了一个情况类似前面一个1中间一堆0然后后半段01均匀分布

发现不能很贪心的取0取够 然后取1 或者说别的一些贪心思路 但是没具体算过..

{% fold Hint2证明1%}
设$c_i$表示[i,i+m-1]内1的数量 令s[i+n]=s[i]故c[i+n]=c[i]

因为$|c_i-c_{i-1}|\leq 1$ 所以 $\forall y\in\ [min(c_i),\ max(c_i)]$ 都有 $c[i]=y$
{% endfold %}

然后思考一下这个每次上升下降至多1导致的连续c[i] 和 k$\leq 2$有什么关系 可能要感性理解(糊)一下..

题解这部分没讲清楚 这个点还是很值得思考的(在很显然的看出来之前) 评论区也有个红名smax讲的很详细了

{% fold Hint2证明2 %}
在s[]扩展了一倍之后 有个感觉就是存在一段[i,i+m-1]的区间满足条件

证明显然是正难则反的方法

1. $\forall c[i], c[i]\leq 所m需要的1数量$
2. $\forall c[i], c[i]\geq 所需要的1数量$

发现这两种都很轻松的违背$m$ is a multiple of $\frac{n}{gcd(w,b)}$

嗯 c[i]虽然可以有大有小 但是因为单次增减变化都在1 所以恰好存在一个点满足条件

如果这个选择的范围超过了n 也就是后半段要从原s[]的前面取 那么k=2
{% endfold %}

感觉这个+-1的思想和之前的#757 最后一题很像 不过那个维护要用动态开点权值树 这个明显好写很多

{% fold code%}
```cpp
#include <bits/stdc++.h>
#define rep(i,a,n) for(int i=a;i<=n;i++)
#define per(i,a,n) for(int i=n;i>=a;i--)
#define max(a,b) (a>b?a:b)
#define min(a,b) (a<b?a:b)
#define mp make_pair
#define pb push_back
#define fi first
#define se second
#define il inline
#define rg register
#define SZ(x) ((int)(x).size())
#define all(x) x.begin(),x.end()
using namespace std;
typedef vector<int> VI;
typedef pair<int,int> PII;
typedef long long ll;
typedef double db;
const int INF=0x7fffffff;
const int inf=0x3f3f3f3f;
const int MOD=998244353;
const int mod=1e9+7;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a; }
// head
int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(nullptr);;
	int tt; cin>>tt;
	while(tt--){
		int n,m; cin>>n>>m;
		string s; cin>>s;
		int one=count_if(all(s),[](char c){return c=='1';});
		if(1LL*one*m%n!=0) {cout<<"-1\n"; continue;}
		one=1LL*one*m/n;
		vector c(2*n+1,0);
		s="#"+s+s;
		for(int i=1;i<=m;i++) c[1]+=((s[i]=='1')?1:0);
		for(int i=2;i<=2*n-m+1;i++) {
			c[i]=c[i-1];
			if(s[i-1]=='1') c[i]--;
			if(s[i+m-1]=='1') c[i]++;
		}
		int now;
		for(int i=1;i<=2*n-m+1;i++)
			if(c[i]==one) {now=i; break;}
		if(now>n){
			cout<<"1\n";
			cout<<now%n<<' '<<(now+m-1)%n<<'\n';
		} else {
			if(now+m-1>n) {
				cout<<"2\n";
				cout<<"1 "<<m-(n-now+1)<<'\n';
				cout<<now<<' '<<n<<endl;
			} else{
				cout<<"1\n";
				cout<<now<<' '<<now+m-1<<endl;
			}
		}
	}
	return 0;
}
```
{% endfold %}
---

评论区说这是PermutationForces.. 属实是整把结论梭哈到底了

---

昨天的事情也是因为自己自行脑补 搞得别人误会了一些事情

我和w🐶说了之后 w🐶因为这事儿在凌晨4点的寝室笑的吵醒室友..

嗯 至于是什么事情 还是gone with the wind吧 思沁都无语了 只能说要更稳重一点