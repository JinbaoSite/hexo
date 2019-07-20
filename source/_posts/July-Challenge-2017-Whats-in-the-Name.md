---
title: July Challenge 2017 | Whats in the Name
date: 2017-07-12 10:25:49
categories: [CodeChef]
---

## 题意
Nitika 读了一本历史书，想要理清其中的人物关系。因此她要她的哥哥把书中出现的历史人物全部列出来。哥哥把列好的人名给了 Nitika，但 Nitika 非常不满意，因为哥哥列出的这些人名格式非常不规范。处于强迫症，Nitika 决定自己来整理这些人名。
一个名字最多有三个部分：姓、名，和中间名，按照名、中间名、姓的顺序排列；最少也有姓这一个部分。Nitika 决定把人名写成下面的格式：
• 只有每个部分的首字母是大写；
• 除了姓之外，每个部分用其大写首字母加一个‘.’表示。
比如，按这个规则对一些名字进行改写：
• gandhi → Gandhi
• mahatma gandhI → M. Gandhi
• Mohndas KaramChand gandhi → M. K. Gandhi

## 解题思路

先将所有的字母转为小写，然后记录空格的位置，再将第一个字母、空格后的字母大写后加‘.'输出，将最后空格后面的字母全部输出。

## 参考代码
```
#include <bits/stdc++.h>
using namespace std;
char s[100];
char ans[100];
int tmp[100];
int main(){
	int t;
	cin>>t;
	cin.getline(s,100);
	while (t--){
		memset(ans,0,sizeof(ans));
		cin.getline(s,100);
		int len=strlen(s);
		int cnt=0;
		for (int i=0;i<len;i++){
			if ('A'<=s[i] && s[i]<='Z')
				s[i]=s[i]-'A'+'a';
			if (s[i]==' ')
				tmp[cnt++]=i;
		}
		if (!cnt){
			s[0]=s[0]-'a'+'A';
			cout<<s<<endl;
		}
		else{
			cout<<char(s[0]-'a'+'A')<<"."<<" ";
			for (int i=0;i<cnt-1;i++)
				cout<<char(s[tmp[i]+1]-'a'+'A')<<"."<<" ";
			cout<<char(s[tmp[cnt-1]+1]-'a'+'A');
			for (int i=tmp[cnt-1]+2;i<len;i++)
				cout<<s[i];
			cout<<endl;
		}
		
	}
	return 0;
}
```