## 题目大意
> &ensp;&ensp;&ensp;&ensp;给出一个$N$个点的树，找出一个点来，以这个点为根的树时，所有点的深度之和最大。  

-----
## 题目分析
假设当前答案在结点$u$，尝试将答案转移到子结点$v$。  
![](/img/billyang-1.png)  
这样有$n-Size[v]$个结点的深度$+1$，有$Size[v]$个结点的深度$-1$。  

那么带来的变化就是$n-2Size[v]$的深度总值。  

从上到下Dp一次即可。  

-----
## 代码
```cpp
#include<algorithm>
#include<iostream>
#include<iomanip>
#include<cstring>
#include<cstdlib>
#include<vector>
#include<cstdio>
#include<cmath>
#include<queue>
using namespace std;
typedef long long LL;
inline const int Get_Int() {
	int num=0,bj=1;
	char x=getchar();
	while(x<'0'||x>'9') {
		if(x=='-')bj=-1;
		x=getchar();
	}
	while(x>='0'&&x<='9') {
		num=num*10+x-'0';
		x=getchar();
	}
	return num*bj;
}
const int maxn=1000005;
int n,Size[maxn],Ans2=1;
LL Ans=0;
vector<int>edges[maxn];
void AddEdge(int x,int y) {
	edges[x].push_back(y);
}
void Dfs(int Now,int father,int depth) {
	Size[Now]=1;
	Ans+=depth;
	for(int& Next:edges[Now]) {
		if(Next==father)continue;
		Dfs(Next,Now,depth+1);
		Size[Now]+=Size[Next];
	}
}
void TreeDp(int Now,int father,LL ans) {
	if(Now!=1) {
		ans=ans+n-2*Size[Now];
		if(ans>Ans) {
			Ans=ans;
			Ans2=Now;
		} else if(ans==Ans&&Now<Ans2)Ans2=Now;
	}
	for(int& Next:edges[Now]) {
		if(Next==father)continue;
		TreeDp(Next,Now,ans);
	}
}
int main() {
	n=Get_Int();
	for(int i=1; i<n; i++) {
		int x=Get_Int(),y=Get_Int();
		AddEdge(x,y);
		AddEdge(y,x);
	}
	Dfs(1,0,1);
	TreeDp(1,0,Ans);
	printf("%d\n",Ans2);
	return 0;
}
```

## 本题解由Bill Yang制作
您可以访问[我的博客](https://blog.bill.moe/bzoj1131-Sta/)阅读本题解。  