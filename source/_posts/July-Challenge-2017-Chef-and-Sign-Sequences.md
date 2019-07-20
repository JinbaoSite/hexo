---
title: July Challenge 2017 | Chef and Sign Sequences
date: 2017-07-11 21:10:04
categories: [CodeChef]
---
## 题意
大厨昨天捡到了一个奇怪的字符串 s，这是一个仅包含‘<’、‘=’和‘>’三种比较符号的字符串。记字符串长度为 N，大厨想要在字符串的开头、结尾，和每两个字符之间插入一个正整数，共N + 1 个数。大厨希望插入数字之后，这些比较符号所表达的含义是正确的。举个例子，如果在‘<’前后分别插入 a 和 b，那么应当有 a < b。对于‘=’和‘>’也是类似的。大厨可以在 [1, P] 中任意选择数字插入，同一个数也可以被插入到多个位置。
请你帮大厨计算 P 的最小取值可以是多少。
## 解题思路
忽略"="，计算忽略"="后字符串中连续最长的"<"的长度或者连续最长的">"的长度。
## 参考代码
```
#include <bits/stdc++.h>
using namespace std;
char s[100000+5];
int main(){
	int t;
	cin>>t;
	while (t--){
		cin>>s;
		int x=0,y=0;
		int ans1=0;
		for (int i=0;s[i];i++){
			if (s[i]=='>') x++;
			if (s[i]=='<') x=0;
			if (x>ans1) ans1=x;
		}
		int ans2=0;
		for (int i=0;s[i];i++){
			if (s[i]=='<') y++;
			if (s[i]=='>') y=0;
			if (y>ans2) ans2=y;
		}
		if (ans1>ans2) cout<<ans1+1<<endl;
		else cout<<ans2+1<<endl;
	}
	return 0;
}
```
