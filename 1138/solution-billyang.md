## 题目大意
> &ensp;&ensp;&ensp;&ensp;$N$个点用$M$条有向边连接，每条边标有一个小写字母。 对于一个长度为$D$的顶点序列，回答每对相邻顶点$S_i$到$S_{i+1}$的最短回文路径。如果没有，输出$-1$。如果有，输出最短长度以及这个字符串。  

<!--more-->

-----
## 题目分析
不难想到设置二维状态$f[i,j]$表示$i$到$j$的最短回文路径长度。  
![](/img/billyang-1.png)  
$$f[x,y]=f[a,b]+2\quad (x,a)=c,(b,y)=c$$
然而这样的转移数量过于庞大，特别是这样的菊花图转移会被卡成$O(n^2)$  
![](/img/billyang-2.png)  

不妨设置辅助数组$g[]$帮助转移，设$g[x,y,c]$表示$x\rightarrow y$的回文路径右侧多了一个字符$c$的最短长度。  

$$f[x,y]=g[a,y,c]+1\quad(x,a)=c$$
$$g[a,y,c]=f[a,b]+1\quad(b,y)=c$$

然后就可以采用记忆化宽搜解决了。  

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
const int maxn=405;
int n,m,f[maxn][maxn],g[maxn][maxn][26];
bool map[maxn][maxn][26];
queue<pair<pair<int,int>,int> >Q;
vector<int>from[maxn][26],to[maxn][26];
void AddEdge(int x,int y,int c) {
	from[y][c].push_back(x);
	to[x][c].push_back(y);
}
int main() {
	n=Get_Int();
	m=Get_Int();
	memset(f,-1,sizeof(f));
	memset(g,-1,sizeof(g));
	for(int i=1; i<=n; i++) {
		f[i][i]=0;
		Q.push(make_pair(make_pair(i,i),-1));
	}
	for(int i=1; i<=m; i++) {
		int x=Get_Int(),y=Get_Int();
		char c=' ';
		while(!isalpha(c))c=getchar();
		map[x][y][c-'a']=1;
	}
	for(int i=1; i<=n; i++)
		for(int j=1; j<=n; j++)
			if(i!=j) {
				bool bj=0;
				for(int c=0; c<26; c++)
					if(map[i][j][c]) {
						AddEdge(i,j,c);
						bj=1;
						break;
					}
				if(bj) {
					f[i][j]=1;
					Q.push(make_pair(make_pair(i,j),-1));
				}
			}
	while(!Q.empty()) {
		pair<pair<int,int>,int>Now=Q.front();
		Q.pop();
		int x=Now.first.first,y=Now.first.second,c=Now.second;
		if(c==-1) {
			for(int i=0; i<26; i++)
				for(int Next:to[y][i])
					if(g[x][Next][i]==-1) {
						g[x][Next][i]=f[x][y]+1;
						Q.push(make_pair(make_pair(x,Next),i));
					}
		} else for(int Next:from[x][c])
			if(f[Next][y]==-1) {
				f[Next][y]=g[x][y][c]+1;
				Q.push(make_pair(make_pair(Next,y),-1));
			}
	}
	int q=Get_Int(),last=Get_Int();
	for(int i=2; i<=q; i++) {
		int x=Get_Int();
		printf("%d\n",f[last][x]);
		last=x;
	}
	return 0;
}
```

## 本题解由Bill Yang制作
您可以访问[我的博客](https://blog.bill.moe/bzoj1138-path/)阅读本题解。  