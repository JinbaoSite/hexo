---
title: SnackDown Online Qualifier 2017 | Temple Land
date: 2017-05-22 15:55:35
categories: [CodeChef]
---
##题意
具体题意[Temple Land](https://s3.amazonaws.com/codechef_shared/download/translated/SNCKQL17/mandarin/TEMPLELA.pdf)（需要翻墙）

每块地有其长度。设第 i 块地的长度为 $N_i$，则我们用 $N_i$ 个整数 $H_{i1}, H_{i2}, . . . , H_{iN_i}$描述这块地不同位置的宽度。即，我们将这块地划分成了 $N_i$ 个部分，并用 $H_i$ 序列顺次描述其每个部分的宽度。适合建造神殿的土地应当满足以下条件：
• 应当存在一个唯一的“中央”部分，神殿将建造这这一部分上。中央的含义为，其左右两侧的
部分数相等；
• $H_{i1}$ = 1；
• 从最左侧的部分开始到中央部分，每一部分比前一部分的宽度多 1；
• 从中央部分开始到最右侧的部分，每一部分比前一部分的宽度少 1；这意味着 $H_{iN_i}$ = 1。
你的任务即判断每块土地是否符合条件。

##解题思路
由题意可知，土地符合要求的模型是：
1,2,3,4,.....,x-1,x,x-1,...,4,3,2,1
所以土地的块数必须是奇数，而且每一块的值应符合上面的模型。
##参考代码
```
#include <iostream>
using namespace std;
int main(){
	int t,n,x;
	cin>>t;
	while (t--){
		cin>>n;
		int k=1,flag=0;
		if (n%2==0) flag=1;
		for (int i=0;i<n;i++){
			cin>>x;
			if (x!=k) flag=1;
			if (i<n/2) k++;
			else k--;
		}
		if (flag==1) cout<<"no"<<endl;
		else cout<<"yes"<<endl;
	}
	return 0;
}
```
