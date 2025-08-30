## section A
**数位DP**用于数字的数位统计，是一种比较简单的DP套路题。
一个数字的数位有个位，十位，百位等，如果题目与数位统计有关，那么可以用DP思想，把低位的统计结果记录下来，在高位计算时直接使用低位的结果，从而提高搜索效率。
下面用一道模板题来讲解数位DP的实现：[洛谷P2602 [ZJOI2010] 数字计数](https://www.luogu.com.cn/problem/P2602) 。

首先区间 [a,b] 内的数量可转化为 [1,b]-[1,a] 的数量,把问题转化成在**区间 [1,x]** 内，统计 0-9个出现了几次。

以 x=324为例，把区间[0,324]分解为4个区间：000-099、100-199、200-299、300-324。其中，000-099、100-199、200-299 能够沿用 00-99 的计算结果。至于 300-324，如果最高位3不是要找的数位，等同于计算 00-24.

## section B
下面设计dp状态。编码时，用变量now表示正在统计的数字，下面的解释以now=2为例。

定义状态 dp[pos][sum] 表示最后pos为范围是[0...0(pos个),99...9(pos个)],
前面2的个数为sum时，数字2的总个数。例如，dp[2][2]=220 表示 2200-2299 区间内2的个数为220。

其次，用lead标识是否有前导0(为假标识有前导0，为真则反之)，这个标识的意义在于在统计有前导0的数时，前导0不计入0的个数。

用limit标识当前最高位数为限制的情况(为真表示有数位限制，为假则反之)。例如，计算324的最高位时，范围是 0-3，limit=true。再如，递归计算到 00-99 时，最高位范围是 0-9，limit=false。

## section C
如果还是不太理解，请结合代码进行理解。
```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N=15;

ll now,dp[N][N],num[N];

ll dfs(int pos,int sum,bool lead,bool limit)
{
	ll ans=0;
	if(pos==0)return sum;
	if(!lead&&!limit&&dp[pos][sum]!=-1)return dp[pos][sum];
	int up=(limit?num[pos]:9);
	for(int i=0;i<=up;i++){
		if(i==0&&lead)ans+=dfs(pos-1,sum,true,limit&&i==up);
		else if(i==now)ans+=dfs(pos-1,sum+1,false,limit&&i==up);
		else if(i!=now)ans+=dfs(pos-1,sum,false,limit&&i==up);
	}
	if(!lead&&!limit)dp[pos][sum]=ans;
	return ans;
}

ll solve(ll x)
{
	int len=0;
	while(x){
		num[++len]=x%10;
		x/=10;
	}
	memset(dp,-1,sizeof(dp));
	return dfs(len,0,true,true);
}

signed main()
{
	ll a,b;cin>>a>>b;
	for(int i=0;i<10;i++){
		now=i; 
		cout<<solve(b)-solve(a-1)<<" ";		
	} 
	
	return 0;
} 
```