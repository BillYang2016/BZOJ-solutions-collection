## 题目大意
> &ensp;&ensp;&ensp;&ensp;没有得到激光武器的苏联十分生气，他们决定派遣一支特种部队强行登陆美国并造成一定的袭击。Reddington得到的情报是他们将在佛罗里达海岸登陆，他决定派遣他的手下去阻击他们。可惜的是，Reddington由于不听从总统的意见，手中的部队只剩下了$N$个人。人与人之间会有一定的矛盾值，第$i$个人与第$j$个人的矛盾值为$T_{i,j}$，并且有$T_{i,i}=0$，$T_{i,j}=T_{j,i}$。  
> &ensp;&ensp;&ensp;&ensp;Reddington希望将这$N$个人分为两支小分队，记为$A,B$，每个人要么属于分队$A$要么属于分队$B$。对于一支小分队$S$，其内部的不安值
$$D(S)=\max_{u\in S,v\in S}T_{u,v}$$
> &ensp;&ensp;&ensp;&ensp;显然的，假如一支分队的不安值很高，那么作战能力就会很差。现在给定你$N$以及一个$N\times N$的矩阵$T$，你需要告诉Reddington，最小的$D(A)+D(B)$是多少。  

<!--more-->

-----
## 题目分析
`%%%`用随机化算法AC的大佬。  

又是这种两个最值相加的最值。  
$D(A)+D(B)$是不具有可二分性的，而$D(A),D(B)$均具有可二分性。  
因此不妨枚举其中一个，二分另一个。  
不妨假设$D(A)\le D(B)$，因此枚举$D(B)$，二分$D(A)$，使得在$A$集合中不存在$\gt D(A)$的边，$B$集合中不存在$\gt D(B)$的边。  
因此现在要做的是判断是否存在这样一组方案，这是一个确定二分图的经典问题，可以使用2-sat解决。  

因为$D(B)$的取值有$O(n^2)$种，因此时间复杂度为$O(n^4\log n^2)$，只能拿到$40$分。  

考虑$D(B)$的取值，其实远远没有$O(n^2)$种，仅有$O(n)$种，下面我们证明一个结论：  
> $D(B)$要么是最大生成树上的一条边，要么将最大生成树黑白染色，$A,B$分别选择黑色与白色。  

**证明：**
考虑原图的最大生成树，在划分的时候，将最大生成树上的点分为两个集合选择，同一集合间的边一定会被选择。  
这相当于是在最大生成树上染色，要么一层一层地交替染黑白两色，否则一定包含最大生成树上的一条边。  
证毕。  

这样$D(B)$的范围就被降低到了$O(n)$。  
就可以通过本题了。  

本题还有一种$O(n^3)$的做法，考虑初始状态加对称边，然后双指针加减边维护，然而实测没有上一种方法快。  

-----
## 代码
注意细节，集合可以选空，而二分可能会忽略这一种情况，需要在边集里加入一条边权为$0$的边。  
```cpp
#include<algorithm>
#include<iostream>
#include<iomanip>
#include<cstring>
#include<cstdlib>
#include<climits>
#include<vector>
#include<cstdio>
#include<cmath>
#include<queue>
#include<stack>
using namespace std;

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

const int maxn=505;

struct Edge {
	int from,to,dist;
	bool operator < (const Edge& b) const {
		return dist>b.dist;
	}
};

stack<int> S;
vector<int> edges[maxn];
vector<Edge> edge;
int n,map[maxn][maxn],step=0,top=0,SCC=0,father[maxn],color[maxn],Dfn[maxn],Lowlink[maxn],Stack[maxn],Belong[maxn];
bool Instack[maxn];

void AddEdge(int x,int y) {
	edges[x].push_back(y);
}

int Get_Father(int x) {
	if(father[x]==x)return x;
	return father[x]=Get_Father(father[x]);
}

void Kruskal() {
	int id=0;
	sort(edge.begin(),edge.end());
	for(auto e:edge) {
		int fx=Get_Father(e.from),fy=Get_Father(e.to);
		if(fx!=fy) {
			father[fx]=fy;
			S.push(id);
			AddEdge(e.from,e.to);
			AddEdge(e.to,e.from);
		}
		id++;
	}
}

void Dfs(int Now,int father) {
	for(int Next:edges[Now]) {
		if(Next==father)continue;
		color[Next]=color[Now]^1;
		Dfs(Next,Now);
	}
}

void Tarjan(int Now) {
	Dfn[Now]=Lowlink[Now]=++step;
	Stack[++top]=Now;
	Instack[Now]=1;
	for(int Next:edges[Now]) {
		if(!Dfn[Next]) {
			Tarjan(Next);
			Lowlink[Now]=min(Lowlink[Now],Lowlink[Next]);
		} else if(Instack[Next])Lowlink[Now]=min(Lowlink[Now],Dfn[Next]);
	}
	if(Dfn[Now]==Lowlink[Now]) {
		SCC++;
		int y;
		do {
			y=Stack[top--];
			Instack[y]=0;
			Belong[y]=SCC;
		} while(y!=Now);
	}
}

bool Check(int A,int B) {
	step=SCC=0;
	for(int i=1; i<=2*n; i++) {
		edges[i].clear();
		Dfn[i]=0;
	}
	for(int i=1; i<n; i++)
		for(int j=i+1; j<=n; j++) {
			if(map[i][j]<=A)continue;
			else if(map[i][j]<=B) {
				AddEdge(i,n+j);
				AddEdge(j,n+i);
			} else {
				AddEdge(i,n+j);
				AddEdge(n+j,i);
				AddEdge(n+i,j);
				AddEdge(j,n+i);
			}
		}
	for(int i=1; i<=2*n; i++)if(!Dfn[i])Tarjan(i);
	for(int i=1; i<=n; i++)if(Belong[i]==Belong[n+i])return false;
	return true;
}

int main() {
	while(~scanf("%d",&n)) {
		edge.clear();
		while(!S.empty())S.pop();
		for(int i=1; i<n; i++)
			for(int j=i+1; j<=n; j++) {
				map[i][j]=Get_Int();
				edge.push_back((Edge) {i,j,map[i][j]});
			}
		for(int i=1; i<=n; i++) {
			father[i]=i;
			edges[i].clear();
		}
		Kruskal();
		color[1]=0;
		Dfs(1,0);
		int Max0=0,Max1=0;
		for(int i=1; i<=n; i++)
			for(int j=i+1; j<=n; j++)
				if(color[i]==color[j]) { //特判二分图
					if(!color[i])Max0=max(Max0,map[i][j]);
					else Max1=max(Max1,map[i][j]);
				}
		edge.push_back((Edge) {0,0,0});
		int ans=Max0+Max1;
		while(!S.empty()) {
			int id=S.top(),Left=id,Right=edge.size()-1,B=edge[id].dist;
			S.pop();
			if(B>ans)break;
			while(Left<=Right) {
				int mid=(Left+Right)>>1,A=edge[mid].dist;
				if(Check(A,B))Left=mid+1;
				else Right=mid-1;
			}
			ans=min(ans,edge[Right].dist+B);
		}
		printf("%d\n",ans);
	}
	return 0;
} 
```

## 本题解由Bill Yang制作
您可以访问[我的博客](https://blog.bill.moe/bzoj4670-florida/)阅读本题解。  