---
title: Technocup 2017 - Elimination Round 1
date: 2016-10-18 13:19:58
categories: [Codeforces]
---
## A. Transformation: from A to B

### 题意

对A的操作有：
（1）将A乘于2
（2）在A后面补充一个1
你可以执行上诉操作若干次，问能否得到B？如果能输出操作过程的数。

### 解题思路

逆向思考，从B出发，如果B最后一位是1，那么逆向执行（2）操作，如果是偶数，则逆向执行（1）。

### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
ll ans[80000];
int main(){
	ll a,b;
	while (cin>>a>>b){
		int k=0;
		ans[k++]=b;
		while (b>a){
			if (b%10==1)	b/=10;
			else if (b%2==0) b/=2;
			else{
				cout<<"NO"<<endl;
				return 0;
			}
			ans[k++]=b;
		}
		if (b<a){
			cout<<"NO"<<endl;
		}
		else{
			cout<<"YES"<<endl;
			cout<<k<<endl;
			for (int i=k-1;i>=0;i--) cout<<ans[i]<<" ";
			cout<<endl;
		}
	}
	return 0;
}
```

## B. Bill Total Value

### 题意

在一个字符串里面有商品的名字跟价格，需要你从这个字符串里面提取出价格，然后求出所有的价格之和。

### 解题思路

将价格分割成美元和美分，分别求和。

### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
char str[1005];
bool isNum(char x){
	if ('0'<=x && x<='9') return true;
	return false;
}
int x=0,y=0;
void Add(int l,int r){
	int r2=r;
	if (str[r-2]=='.'){
		r2=r-3;
		int temp=(str[r-1]-'0')*10+str[r]-'0';
		y+=temp;
	}
	int temp=0;
	for (int i=l;i<=r2;i++){
		if (str[i]=='.') continue;
		temp=temp*10+(str[i]-'0');
	}
	x+=temp;
}
int ans[10];
int main(){
	while (cin>>str){
		x=0,y=0;
		for (int i=0;str[i];i++){
			if (isNum(str[i])==true){
				int j;
				for (j=i;str[j];j++)
					if (isNum(str[j])==false && str[j]!='.') 
						break;
				Add(i,j-1);
				i=j;
			}
		}
		if (y>=100){
			x+=y/100;
			y%=100;
		}
		int k=0;
		int temp=x;
		while (temp){
			ans[k++]=temp%1000;
			temp/=1000;
		}
		if (k>0)
			cout<<ans[k-1];
		for (int i=k-2;i>=0;i--)
			printf(".%03d",ans[i]);
		if (k==0) cout<<0;
		if (y) printf(".%02d",y);
		cout<<endl;
	}
	return 0;
}
```

## C. Guess the Array

### 题意

给出一个数组中任意两个数之和，让你猜这个数组

### 题意

将输入的看成是
（1）1和2
（2）1和3
（3）1和4
...
（4）1和n
（5）2和3
由（1）、（2）、（5）求出1、2、3的值，然后求出剩下的值。

### 参考代码

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int a[50005];
int b[50005];
int main(){
	int n;
	cin>>n;
	for (int i=0;i<n-1;i++){
		cout<<"? 1 "<<i+2<<endl;
		cin>>a[i];
	}
	cout<<"? 2 3"<<endl;
	cin>>a[n-1];
	b[1]=(a[0]-a[1]+a[n-1])/2;
	b[0]=a[0]-b[1];
	for (int i=1;i<n-1;i++)
		b[i+1]=a[i]-b[0];
	cout<<"! ";
	for (int i=0;i<n;i++)
		cout<<b[i]<<" ";
	cout<<endl;
	return 0;
}
```