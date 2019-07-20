---
title: SnackDown Online Qualifier 2017 | Same Snake
date: 2017-05-22 16:15:31
categories: [CodeChef]
---
##题意
具体题意：[Same Snake](https://s3.amazonaws.com/codechef_shared/download/translated/SNCKQL17/mandarin/SAMESNAK.pdf)（需要翻墙）

考虑一块二维网格。我们用 (i, j) 代表一个格子。你收到了宣称在网格上出现了蛇的两份目击报告，你需要判断他们目击到的是两条蛇，还是同一条蛇。每份目击报告对应网格中的一条指定起止位置的线段，即目击者在网格上线段所覆盖的格子上看到了蛇。考虑一张图，其节点为网格中的每个格子。两个节点之间有边相连，当且仅当其对
应的格子相邻，且被同一份目击记录中的蛇占据。
如果下面的两个条件均满足，那么我们认为两次目击到的是同一条蛇：
• 图中连有边的所有节点连通；
• 每个节点的度数不超过 2。
换言之，图中有边相连的所有节点构成一条链。每份目击报告的线段的起止节点位于同一行或同一列中
如果目击到的是同一条蛇，则输出一行“yes”；否则输出一行“no”。

##解题思路
题目保证了每份目击报告的线段的起止节点位于同一行或同一列中，则存在下列三种情况
![](http://7xwian.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20170522161024.png)
同时也存在，当某份目击报告的线段是一个点时的情况
![](http://7xwian.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20170522161326.png)

注意：给出的坐标不一定是从小到大的，需要自己调整。
##参考代码
```
#include <iostream>
using namespace std;
struct Node{
	int x1,y1,x2,y2;
}S,T;
bool f(int a,int b,int c,int d){
	if ((a<=c&&c<=b)||(a<=d&&d<=b)||(c<=a&&a<=d)||(c<=b&&b<=d))
		return 1;
	return 0;
}
void swap(int &a,int &b){
	int tmp=a;
	a=b;
	b=tmp;
}
int main(){
	int t;
	cin>>t;
	while (t--){
		cin>>S.x1>>S.y1>>S.x2>>S.y2;
		cin>>T.x1>>T.y1>>T.x2>>T.y2;
		if (S.x1>=S.x2&&S.y1>=S.y2){
			swap(S.x1,S.x2);
			swap(S.y1,S.y2);
		}
		if (T.x1>=T.x2&&T.y1>=T.y2){
			swap(T.x1,T.x2);
			swap(T.y1,T.y2);
		}
		int sl=0,tl=0;
		if (S.x1==S.x2) sl=0;
		else sl=1;
		if (T.x1==T.x2) tl=0;
		else tl=1;
		if (S.x1==S.x2&&S.y1==S.y2){
			if (T.x1<=S.x1&&S.x1<=T.x2 && T.y1<=S.y1&&S.y1<=T.y2)
				cout<<"yes"<<endl;
			else cout<<"no"<<endl;
			continue;
		}
		if (T.x1==T.x2&&T.y1==T.y2){
			if (S.x1<=T.x1&&T.x1<=S.x2 && S.y1<=T.y1&&T.y1<=S.y2)
				cout<<"yes"<<endl;
			else cout<<"no"<<endl;
			continue;
		}
		if (sl+tl==0){ //竖+竖
			if (S.x1==T.x1 && f(S.y1,S.y2,T.y1,T.y2)==1)
				cout<<"yes"<<endl;
			else cout<<"no"<<endl;
		}
		if (sl+tl==2){ //横+横
			if (S.y1==T.y1 && f(S.x1,S.x2,T.x1,T.x2)==1)
				cout<<"yes"<<endl;
			else cout<<"no"<<endl;
		}

		if (sl+tl==1){ //竖+横
			if ((S.x1==T.x1&&S.y1==T.y1)||(S.x1==T.x2&&S.y1==T.y2)||(S.x2==T.x1&&S.y2==T.y1)||(S.x2==T.x2&&S.y2==T.y2))
				cout<<"yes"<<endl;
			else cout<<"no"<<endl;
		}
	}
	return 0;
}
```