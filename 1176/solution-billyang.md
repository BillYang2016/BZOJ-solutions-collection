## 题目大意
> &ensp;&ensp;&ensp;&ensp;维护一个W*W的矩阵，初始值均为S.每次操作可以增加某格子的权值,或询问某子矩阵的总权值.修改操作数M<=160000,询问数Q<=10000,W<=2000000.

-----
## 题目分析

这道题其实也是三维偏序的变种。  
关于三维偏序可以看这两篇文章：[传送门1](https:/bill.blog.moe/fjwc2014-3Dtable/)、[传送门2](https:/bill.blog.moe/bzoj3262-flower/)。  

首先矩阵可以被拆分为四条线段。  
![](/img/mokia1.png)  
看似是一个二维偏序。  
而二维偏序我们可以按照$x$维排序后$y$维用树状数组进行维护。  
具体方案是：**全部减掉比$x1$小的且在$y$范围内点的权值，全部加上比$x2$小且在$y$范围内点的权值。（前缀和）**  

然而本题有修改操作，这导致我们暗中添加了一个维度：时间。  
因为修改、询问与时间有关，这就将问题变为了一个三维偏序。  
我们可以按照$x$维排序然后cdq分治时间维，用树状数组维护$y$维解决本问题。  

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
const int maxn=2000005,maxq=200005*4;
int n;
struct BIT {
	int c[maxn];
	#define lowbit(x) x&(-x)
	void add(int x,int v) {
		for(int i=x; i<=n; i+=lowbit(i))c[i]+=v;
	}
	void clear(int x,int v) {
		for(int i=x; i<=n; i+=lowbit(i))c[i]=0;
	}
	int sum(int x) {
		int sum=0;
		for(int i=x; i>=1; i-=lowbit(i))sum+=c[i];
		return sum;
	}
} bit; 
struct Point {
	int x,y,v,id,time,opt;
	Point() {}
	Point(int _x,int _y,int _v,int _i,int _t,int _o):x(_x),y(_y),v(_v),id(_i),time(_t),opt(_o) {}
	bool operator < (const Point& b) const {
		return x<b.x||(x==b.x&&y<b.y)||(x==b.x&&y==b.y&&time<b.time);
	}
} q[maxq],tmp[maxq];
int Ans[maxq];
void CDQBinary(int Left,int Right) {
	if(Left==Right)return;
	int mid=(Left+Right)>>1;
	int lst=Left,rst=mid+1;
	for(int i=Left; i<=Right; i++)
		if(q[i].time<=mid)tmp[lst++]=q[i];
		else tmp[rst++]=q[i];
	for(int i=Left; i<=Right; i++)q[i]=tmp[i];
	CDQBinary(Left,mid);
	int j=Left;
	for(int i=mid+1; i<=Right; i++) {
		while(j<=mid&&q[j]<q[i]) {
			if(q[j].opt==1)bit.add(q[j].y,q[j].v);
			j++;
		}
		if(q[i].opt==2)Ans[q[i].id]+=q[i].v*bit.sum(q[i].y);
	}
	for(int i=Left; i<=mid; i++)
		if(q[i].opt==1)bit.clear(q[i].y,0);
	CDQBinary(mid+1,Right);
	merge(q+Left,q+mid+1,q+mid+1,q+Right+1,tmp);
	int tot=0;
	for(int i=Left; i<=Right; i++)q[i]=tmp[tot++];
}
int k,m=0,opt[maxq],s[maxq],cnt=0;
int main() {
	k=Get_Int();
	n=Get_Int();
	while(true) {
		opt[++m]=Get_Int();
		if(opt[m]==1) {
			int x=Get_Int(),y=Get_Int(),a=Get_Int();
			q[++cnt]=Point(x,y,a,m,cnt,1);
		} else if(opt[m]==2) {
			int x1=Get_Int(),y1=Get_Int(),x2=Get_Int(),y2=Get_Int();
			q[++cnt]=Point(x2,y2,1,m,cnt,2);
			q[++cnt]=Point(x1-1,y2,-1,m,cnt,2);
			q[++cnt]=Point(x2,y1-1,-1,m,cnt,2);
			q[++cnt]=Point(x1-1,y1-1,1,m,cnt,2);
			s[m]=(x2-x1+1)*(y2-y1+1);
		} else break;
	}
	sort(q+1,q+cnt+1);
	CDQBinary(1,cnt);
	for(int i=1; i<=m; i++)
		if(opt[i]==2)printf("%d\n",Ans[i]+s[i]*k);
	return 0;
}
```
更简洁的写法：  
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
const int maxn=2000005,maxq=200005*4;
int n;
struct BIT {
	int c[maxn];
	#define lowbit(x) x&(-x)
	void add(int x,int v) {
		for(int i=x; i<=n; i+=lowbit(i))c[i]+=v;
	}
	int sum(int x) {
		int sum=0;
		for(int i=x; i>=1; i-=lowbit(i))sum+=c[i];
		return sum;
	}
} bit; 
struct Point {
	int x,y,v,id,time,opt;
	Point() {}
	Point(int _x,int _y,int _v,int _i,int _t,int _o):x(_x),y(_y),v(_v),id(_i),time(_t),opt(_o) {}
	bool operator < (const Point& b) const {
		return x<b.x||(x==b.x&&y<b.y)||(x==b.x&&y==b.y&&time<b.time);
	}
} q[maxq],tmp[maxq];
int Ans[maxq];
void CDQBinary(int Left,int Right) {
	if(Left==Right)return;
	int mid=(Left+Right)>>1;
	for(int i=Left; i<=Right; i++)
		if(q[i].time<=mid&&q[i].opt==1)bit.add(q[i].y,q[i].v);
		else if(q[i].time>mid&&q[i].opt==2)Ans[q[i].id]+=q[i].v*bit.sum(q[i].y);
	for(int i=Left; i<=Right; i++)
		if(q[i].time<=mid&&q[i].opt==1)bit.add(q[i].y,-q[i].v);
	int lst=Left,rst=mid+1;
	for(int i=Left; i<=Right; i++)
		if(q[i].time<=mid)tmp[lst++]=q[i];
		else tmp[rst++]=q[i];
	for(int i=Left; i<=Right; i++)q[i]=tmp[i];
	CDQBinary(Left,mid);
	CDQBinary(mid+1,Right);
}
int k,m=0,opt[maxq],s[maxq],cnt=0;
int main() {
	k=Get_Int();
	n=Get_Int();
	while(true) {
		opt[++m]=Get_Int();
		if(opt[m]==1) {
			int x=Get_Int(),y=Get_Int(),a=Get_Int();
			q[++cnt]=Point(x,y,a,m,cnt,1);
		} else if(opt[m]==2) {
			int x1=Get_Int(),y1=Get_Int(),x2=Get_Int(),y2=Get_Int();
			q[++cnt]=Point(x2,y2,1,m,cnt,2);
			q[++cnt]=Point(x1-1,y2,-1,m,cnt,2);
			q[++cnt]=Point(x2,y1-1,-1,m,cnt,2);
			q[++cnt]=Point(x1-1,y1-1,1,m,cnt,2);
			s[m]=(x2-x1+1)*(y2-y1+1);
		} else break;
	}
	sort(q+1,q+cnt+1);
	CDQBinary(1,cnt);
	for(int i=1; i<=m; i++)
		if(opt[i]==2)printf("%d\n",Ans[i]+s[i]*k);
	return 0;
}
```

## 本题解由Bill Yang制作
您可以访问[我的博客](https://blog.bill.moe/bzoj1176-mokia/)阅读本题解。  