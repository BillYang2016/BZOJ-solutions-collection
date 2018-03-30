## 题目大意
> &ensp;&ensp;&ensp;&ensp;一个长度为$n$的序列$a$，设其排过序之后为$b$，其中位数定义为$b[n/2]$，其中$a,b$从$0$开始标号,除法取下整。  
> &ensp;&ensp;&ensp;&ensp;给你一个长度为$n$的序列$s$。  
> &ensp;&ensp;&ensp;&ensp;回答$Q$个这样的询问：$s$的左端点在$[a,b]$之间,右端点在$[c,d]$之间的子序列中，最大的中位数。  
> &ensp;&ensp;&ensp;&ensp;其中$a\lt b\lt c\lt d$。  
> &ensp;&ensp;&ensp;&ensp;位置也从$0$开始标号。  
> &ensp;&ensp;&ensp;&ensp;我会使用一些方式强制你在线。

<!--more-->

-----
## 题目分析
对于一个询问$(a,b,c,d)$，显然$[b+1,c-1]$是必选区间，现在我们要尽量得添加较大的数使得答案变大。  

不妨假设答案为$p$，若我们统计出有$num_1$个数比$p$小，有$num_2$个数比$p$大，若$num_1\ge num_2$，则说明答案$p$可以变大，否则需要变小。  

那么现在问题转化为了，对于一个数$v$，在区间$[a,b]$求出最大的右区间$[l,b]$，满足$num_1-num_2$尽量地大，对$[c,d]$则要求左区间。   

不妨将比数$v$大的记为$+1$，比$v$小的记为$-1$，则我们仅需要求出$[a,b]$的靠右最大子段和与$[c,d]$的靠左最大子段和。  

对这些$+1,-1$位置建立线段树，那么我们就可以方便地统计出最大子段和了，目前的问题是如何快速、省空间地对每一个数都建立线段树。  

注意到仅有$n$个数，每个数的$+1,-1$标记只可能从比其小的数所建立的线段树上更改一处，因此我们可以使用可持久化线段树维护。  

对位置建立按数可持久化的线段树，每次询问二分猜测答案，并用对应的线段树验证即可。

-----
## 代码
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

const int maxn=20005;

struct Tree {
	int l,r;
	int sum,lmax,rmax;
};

struct President_Tree {
	Tree tree[maxn*50];
	int size;
#define lm(x) tree[x].lmax
#define rm(x) tree[x].rmax
#define sum(x) tree[x].sum
	void push_up(int index) {
		int l=tree[index].l,r=tree[index].r;
		sum(index)=sum(l)+sum(r);
		lm(index)=max(lm(l),sum(l)+lm(r));
		rm(index)=max(rm(r),rm(l)+sum(r));
	}
	int build(int Left,int Right) {
		int now=++size;
		if(Left==Right) {
			sum(now)=lm(now)=rm(now)=1;
			return now;
		}
		int mid=(Left+Right)>>1;
		tree[now].l=build(Left,mid);
		tree[now].r=build(mid+1,Right);
		push_up(now);
		return now;
	}
	int insert(int Left,int Right,int pre,int v) {
		int now=++size;
		tree[now]=tree[pre];
		if(Left==Right) {
			sum(now)=lm(now)=rm(now)=-1;
			return now;
		}
		int mid=(Left+Right)>>1;
		if(v<=mid)tree[now].l=insert(Left,mid,tree[pre].l,v);
		else tree[now].r=insert(mid+1,Right,tree[pre].r,v);
		push_up(now);
		return now;
	}
	int query_sum(int root,int Left,int Right,int left,int right) {
		if(right<Left||left>Right)return 0;
		if(left<=Left&&right>=Right)return sum(root);
		int mid=(Left+Right)>>1;
		return query_sum(tree[root].l,Left,mid,left,right)+query_sum(tree[root].r,mid+1,Right,left,right);
	}
	int query_left(int root,int Left,int Right,int left,int right) {
		if(right<Left||left>Right)return -INT_MAX/2;
		if(left<=Left&&right>=Right)return lm(root);
		int mid=(Left+Right)>>1;
		return max(query_left(tree[root].l,Left,mid,left,right),query_sum(tree[root].l,Left,mid,left,right)+query_left(tree[root].r,mid+1,Right,left,right));
	}
	int query_right(int root,int Left,int Right,int left,int right) {
		if(right<Left||left>Right)return -INT_MAX/2;
		if(left<=Left&&right>=Right)return rm(root);
		int mid=(Left+Right)>>1;
		return max(query_right(tree[root].r,mid+1,Right,left,right),query_sum(tree[root].r,mid+1,Right,left,right)+query_right(tree[root].l,Left,mid,left,right));
	}
} pt;

int n,q,root[maxn],lastans;

struct node {
	int v,pos;
	bool operator < (const node& b) const {
		return v<b.v;
	}
} a[maxn];

bool Check(int v,int a,int b,int c,int d) {
	int ans=0;
	if(c-b>1)ans=pt.query_sum(root[v],1,n,b+1,c-1);
	ans+=pt.query_right(root[v],1,n,a,b)+pt.query_left(root[v],1,n,c,d);
	return ans>=0;
}

int main() {
	n=Get_Int();
	for(int i=1; i<=n; i++) {
		a[i].v=Get_Int();
		a[i].pos=i;
	}
	sort(a+1,a+n+1);
	root[1]=pt.build(1,n);
	for(int i=2; i<=n; i++)root[i]=pt.insert(1,n,root[i-1],a[i-1].pos);
	q=Get_Int();
	while(q--) {
		int tmp[4];
		tmp[0]=Get_Int(),tmp[1]=Get_Int(),tmp[2]=Get_Int(),tmp[3]=Get_Int();
		for(int i=0; i<4; i++)tmp[i]=(tmp[i]+lastans)%n+1;
		sort(tmp,tmp+4);
		int Left=1,Right=n;
		while(Left<=Right) {
			int mid=(Left+Right)>>1;
			if(Check(mid,tmp[0],tmp[1],tmp[2],tmp[3]))Left=mid+1;
			else Right=mid-1;
		}
		lastans=a[Right].v;
		printf("%d\n",lastans);
	}
	return 0;
}
```

## 本题解由Bill Yang制作
您可以访问[我的博客](https://blog.bill.moe/bzoj2653-middle/)阅读本题解。  