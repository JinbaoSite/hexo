---
title: July Challenge 2017 | Calculator
date: 2017-07-11 21:28:00
categories: [CodeChef]
---
## 题意

大厨有一个计算器，计算器上有两个屏幕和两个按钮。初始时每个屏幕上显示的都是 0。没按一次第一个按钮，就会让第一个屏幕上显示的数字加 1，同时消耗 1 单位的能量。每按一次第二个按钮，会让第二个屏幕上显示的数字加上第一个屏幕上显示的数字，同时消耗 B 单位的能量。
初始时，计算器有 N 单位的能量。大厨想知道在能量限制下，第二个屏幕上最大可以出现的数字是多少？

## 解题思路

贪心思想，先按第一个按钮，再按第二按钮。假设x次按第一个按钮，y次按第二个按钮，则必须满足

$$
\begin{cases}
N=x+By \newline
M=xy
\end{cases}
$$

为了求M的最大值，我们解这个方程。

## 参考代码

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int main(){
	int t;
	ll n,b;
	cin>>t;
	while (t--){
		cin>>n>>b;
		ll k=n/(2*b);
		ll ans1=-b*k*k+n*k;
		ll ans2=-b*(k+1)*(k+1)+n*(k+1);
		if (ans1>ans2) cout<<ans1<<endl;
		else cout<<ans2<<endl;
	}
	return 0;
}
```
