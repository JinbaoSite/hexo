---
title: ' June Challenge 2017 | A Good Set'
date: 2017-06-07 16:31:50
categories: [CodeChef]
---
##题意
如果一个整数集合中不存在三个不同的元素 a、b 和 c，满足 a + b = c，那么我们称这个集合为好集合。
输入一个n（1 ≤ n ≤ 100），只要输出任意大小为 n 的好集合。集合中的元素应当两两不同，且取值在 [1, 500] 之间。
##解题思路
因为a，b，c都在 [1, 500] 之间，所以要使得a+b不等于c，只需要a+b大于500即可，所以，a，b，c从250开始一直到500，即[250,251,252,...,500]这个集合是好集合。我们输出n个就符合要求。
##参考代码
```
#include <iostream>
using namespace std;
int main(){
	int t,n;
	cin>>t;
	while (t--){
		cin>>n;
		for (int i=0;i<n;i++){
			if (i==0) cout<<i+300;
			else cout<<" "<<i+300; 
		}
		cout<<endl;
	}
	return 0;
}
```
