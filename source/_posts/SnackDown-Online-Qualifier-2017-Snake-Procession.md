---
title: SnackDown Online Qualifier 2017 | Snake Procession
date: 2017-05-22 15:45:40
categories: [CodeChef]
---
##题意
具体题意：[Snake Procession](https://s3.amazonaws.com/codechef_shared/download/translated/SNCKQL17/mandarin/SNAKPROC.pdf)（需要翻墙）
一条蛇由‘H’开头、‘T’结尾，中间可以有任意数量（可以为零）的‘.’。
合法的报道：由任意数量（可以为零）的‘.’开头，后接任意数量（可以为零）的蛇，中间穿插任意数量（可以为零）的‘.’，最后以任意数量（可以为零）的‘.’结尾。
请你帮忙检验报道是否合法。
##解体思路
由题意可知，蛇是完整出现的，那么每个H后面一定要有一个T，而且中间不能有H，所以判断H后面是否出现H或者没有出现T就可以了。
##参考代码
```
#include <iostream>
#include <string.h>
using namespace std;
#define MAXN 505
char str[MAXN];
int main(){
	int t,n;
	cin>>t;
	while (t--){
		cin>>n>>str;
		int flag=0;
		for (int i=0;i<n;i++){
			if (flag==1) break;
			if (str[i]=='T') flag=1;
			if (str[i]=='H'){
				int gl=0;
				for (int j=i+1;j<n;j++){
					if (str[j]=='T'){
						i=j;
						gl=1;
						break;
					}
					if (str[j]=='H')
						break;
				}
				if (gl==0) flag=1;
			}
			
		}
		if (flag) cout<<"Invalid"<<endl;
		else cout<<"Valid"<<endl;
	}
	return 0;
}
```