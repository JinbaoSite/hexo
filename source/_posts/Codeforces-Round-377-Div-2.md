---
title: 'Codeforces Round #377 (Div. 2)'
date: 2016-10-18 13:04:48
categories: [Codeforces]
---
## A. Buy a Shovel

### 题意

你有若干个价值为10的钱币和一个价值为r的钱币，对于价值为k的商品，买几个可以使得你不需要找零。

### 解题思路

从买一个开始到买若干个，看是否有恰好有模10为0或者r的个数。


### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int main(){
	int k,r;
	while (cin>>k>>r){
		for (int i=1;;i++){
			if (k*i%10==r || k*i%10==0){
				cout<<i<<endl;
				break;
			}
		}
	}
	return 0;
}
```

## B. Cormen — The Best Friend Of a Man

### 题意

给你一个数组，你可以给数组中的某个元素加上一个任意值或者不加，使得任意两个相邻的元素之和大于k。

### 解题思路

贪心思路，从第二个元素开始，如果它和前一个元素之和小于k，那么将这个元素加上一个恰好可以使得它和前一个元素之和等于于k的值。

### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
int a[505];
int main(){
	int n,k;
	while (cin>>n>>k){
		int ans=0;
		for (int i=0;i<n;i++){
			cin>>a[i];
			if (i){
				if (a[i]+a[i-1]<k){
					ans+=k-(a[i]+a[i-1]);
					a[i]+=(k-(a[i]+a[i-1]));
				}
			}
		}
		cout<<ans<<endl;
		for (int i=0;i<n;i++)
			cout<<a[i]<<" ";
		cout<<endl;
	}
	return 0;
}
```

## C. Sanatorium

### 题意

一天有三餐，你可以选择在任意时刻到，也可以选择在任意时刻离开，现在告诉你吃了三餐各自的数量，问你空了几餐没吃？

### 解题思路

分类讨论，我们用A，B，C表示三个时间段到达，用D，E，F表示三个时间离开。
--A--早--B--中--C--晚--
--D--早--E--中--F--晚--

| --到达-- | --离开-- | --早餐数-- | --中餐数-- | --晚餐数-- |
| :----: |:----:| :-----:| :----: | :----: |
|  A   |   D  |   n    |    n   |    n   |
|  A   |   E  |   n+1  |    n   |    n   |
|  A   |   F  |   n+1  |    n+1 |    n   |
|  B   |   D  |   n    |    n+1 |    n+1 |
|  B   |   E  |   n    |    n   |    n   |
|  B   |   F  |   n    |    n+1 |    n   |
|  C   |   D  |   n    |    n   |    n+1 |
|  C   |   E  |   n+1  |    n   |    n+1 |
|  C   |   F  |   n    |    n   |    n   |

我们可以发现，如果三餐数都相当，那么说明没有空餐没吃，否则每餐至少有n-1餐（n来源三餐数的最大值）。如果某一餐数小于n-1，那么与n-1的差为没吃的餐数。

### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int main(){
	ll b,d,s;
	while (cin>>b>>d>>s){
		ll Max=max(b,max(d,s));
		ll ans=0;
		if (b==d && d==s) ans=0;
		else{
			if (Max>b) ans+=Max-1-b;
			if (Max>d) ans+=Max-1-d;
			if (Max>s) ans+=Max-1-s;
		}
		cout<<ans<<endl;
	}
	return 0;
}
```
## D. Exams

### 题意

在n天的时间内有m门考试，某一个可能是空闲的，也可能是某一门考试，你可以选择放弃考试用来复习，也可以参加考试，要想通过考试，必须要有足够多天的复习时间，问你最早在什么时候完成所有的考试。

### 解题思路

贪心，优先完成复习时间短的考试，如果遇到其他考试，那么将其放弃，用来复习。

### 参考代码

```
#include <bits/stdc++.h>
using namespace std;
const int N = 100000 + 100;
typedef long long ll;
ll d[N],a[N];
int main(){
    ll n,m;
    cin>>n>>m;
    for (int i=0;i<n;i++)
        cin>>d[i];
    for (int i=0;i<m;i++)
        cin>>a[i];
    sort(a,a+m);
    int cnt=0,k=0,ans=0;
    for (int i=0;i<n;i++){
        if (k==m){
            ans=i;
            brak;
        }
        if (d[i]){
            if (cnt<a[k]) cnt++;
            else{
                cnt-=a[k];
                k++;
            }
        }
        else cnt++;
    }
    if (k!=m) cout<<-1<<endl;
    else{
        if (ans) cout<<ans<<endl;
        else cout<<n<<endl;
    }
    return 0;
}
```