---
title: ' Intel Code Challenge Final Round (Div. 1 + Div. 2, Combined)'
date: 2016-10-10 18:30:11
categories: [Codeforces]
---
## A. Checking the Calendar

### 题意

给定两个星期几（星期A和星期B），是否存在一个非闰年的相邻两个月，前一个月的第一天是星期A，后一个月的第一天是星期B？

### 解题思路

星期A到星期B的天数等于前一个月的天数mod7。

### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int a[]={31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
char day[7][15]={"monday", "tuesday", "wednesday", "thursday", "friday", "saturday", "sunday"};
int main(){
	char s1[15],s2[15];
	while (cin>>s1>>s2){
		int x,y;
		for (int i=0;i<7;i++){
			if (strcmp(s1,day[i])==0)	x=i;
			if (strcmp(s2,day[i])==0)	y=i;
		}
		int k=y-x;;
		if (y<x){
			k+=7;
		}
		//cout<<k<<endl;
		int flag=0;
		for (int i=0;i<12;i++){
			if (a[i]%7==k){
				flag=1;
				break;
			}
			
		}
		if (flag) cout<<"YES"<<endl;
		else cout<<"NO"<<endl;
	}
	return 0;
}
```

## B. Batch Sort

### 题意

给你一个n*m的数组，数组的每一行都是1~m的一个排列，现在你有两种操作：
（1）交换同一行的两个数，每一行最多交换一次
（2）交换两个不同的列，最多交换一次
这两种操作总共加起来不超过n+1次
问你能不能把数组进行上诉的操作后，数组的每一行都从1到n的排列？

### 解题思路

模拟所有情况

### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
int n,m;
int a[25][25];
int b[25][25];
bool Change(int c1,int c2,bool flag){
	for (int i=0;i<n;i++)
		for (int j=0;j<m;j++)
			b[i][j]=a[i][j];
	if (!flag){
		for (int i=0;i<n;i++)
			swap(b[i][c1],b[i][c2]);
	}
	for (int i=0;i<n;i++){
		int cnt=0;
		for (int j=0;j<m;j++)
			if (b[i][j]!=j+1)
				cnt++;
		if (cnt>2){
			return false;
		}
	}

}
bool judge(){
	if (Change(0,0,true)) return true;
	for (int i=0;i<m;i++){
		for (int j=i+1;j<m;j++){
			if (Change(i,j,false)==true)
				return true;
		}
	}
	return false;
}
int main(){
	while (cin>>n>>m){
		for (int i=0;i<n;i++)
			for (int j=0;j<m;j++)
				cin>>a[i][j];
		if (judge()) cout<<"YES"<<endl;
		else cout<<"NO"<<endl;
	}
	return 0;
}
```