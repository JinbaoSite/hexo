---
title: June Challenge 2017 | Chef and the Feast
date: 2017-06-08 20:40:56
categories: [CodeChef]
---
##题意
厨师为您准备了N个菜肴。 你喜欢厨师的烹饪，所以你想吃所有为你准备的菜肴。 你还会得到一个大小为N的数组A，其中Ai代表你吃的第i道菜所获得的幸福值。你将以一系列步骤吃所有的菜肴。 在每个步骤中，您可以选择剩余菜肴的一个子集，然后吃掉它们。 从这些菜中获得的幸福值是子集的大小乘以子集中菜肴的个人幸福总和。 你想要最大限度地发挥整个节日所获得的快乐，这是每一步中幸福的总和。
例如：
1
4
-1 -2 -3 6
你可以选择{-3}，{-1，-2，6}两个子集，那么幸福值之和为
-3+(-1+-2+6)*3=6
##解题思路
将数组排序，记有k个大于0的值，所有大于0的数值为x，所有小于0的数组为y，那么最后得到的幸福总和为ans=x*(n-k)+y。
为了使得幸福值总和最大，我们需要改变x，y，k之间的值，所以先将小的数从y中减去，在x中加入，计算幸福总和，并与原先的幸福总和相比，如果更大，那么更新x，y，k，直到不能更新为止。
##参考代码
```
#include <iostream>
#include <algorithm>
using namespace std;
int a[100000+5];
typedef long long ll;
int main(){
	int t,n;
	cin>>t;
	while (t--){
		cin>>n;
		ll ans=0;
		for (int i=0;i<n;i++)
			cin>>a[i];
		sort(a,a+n);
		int k=-1;
		for (int i=0;i<n;i++){
			if (a[i]>0){
				k=i;
				break;
			}
		}
		if (k==-1){
			for (int i=0;i<n;i++)
				ans+=a[i];
		}
		else{
			ll x=0;
			for (int i=n-1;i>=k;i--)
				x+=a[i];
			ll y=0;
			for (int i=0;i<k;i++)
				y+=a[i];
			if (k==0){
				ans=x*n;
			}
			else{
				while (k){
					if ((x*(n-k)+y)<((x+a[k-1])*(n-k+1)+y-a[k-1])){
						k--;
						x+=a[k];
						y-=a[k];
					}
					else break;
				}
				ans=x*(n-k)+y;
			}
		}
		cout<<ans<<endl;
	}
	return 0;
}
```