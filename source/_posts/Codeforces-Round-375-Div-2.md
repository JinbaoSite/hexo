---
title: 'Codeforces Round #375 (Div. 2)'
date: 2016-10-04 23:29:14
categories: [Codeforces]
---
## A、The New Year: Meeting Friends

### 题意

在x轴上有a，b，c三个点，请你在x轴上找一个点使得a，b，c到这个点的距离之和最短，求这个最短的距离。

### 解题思路

将a，b，c排序，中位数即为我们需要寻找的点，最短距离为最大值减最小值。

<!--more-->

### 参考代码
```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int a[10];
int main(){
	cin>>a[0]>>a[1]>>a[2];
	sort(a,a+3);
	cout<<a[2]-a[0]<<endl;
	return 0;
}
```

## B、Text Document Analysis

### 题意

由下划线、括号、大小写字母组成的字符串，单词只是包含大小写字母，求在所有括号外单词的长度的最大值，所有括号内单词的个数。

### 解题思路

将括号内的字符串和括号外的字符串分割出来，对于括号内的字符串统计单词个数，对于括号外的字符串统计单词长度的最大值。

### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
char s[300];
bool isLetter(char x){
	if (('a'<=x && x<='z')||('A'<=x && x<='Z')){
		return true;
	}
	return false;
}
int f(int l,int r){	//计算个数
	//cout<<l<<" "<<r<<endl;
	int cnt=0;
	for (int i=l;i<=r;i++){
		if (isLetter(s[i])==true){
			int j;
			for (j=i+1;j<=r;j++){
				if (isLetter(s[j])==false) break;
			}
			i=j-1;
			cnt++;
		}
	}
	return cnt;
}
int g(int l,int r){	//计算长度
	int cnt=0;
	for (int i=l;i<=r;i++){
		if (isLetter(s[i])==true){
			int j=0;
			int temp=0;
			for (j=i;j<=r;j++){
				if (isLetter(s[j])==false) break;
				temp++;
			}
			cnt=max(cnt,temp);
		}
	}
	return cnt;
}
int main(){
	int n;
	while (cin>>n>>s){
		int len=strlen(s);
		int ans=0,cnt=0;
		for (int i=0;i<len;i++){
			if (s[i]=='('){
				int j=0;
				for (j=i+1;j<len;j++)
					if (s[j]==')') 
						break;
				cnt+=f(i,j-1);
				i=j;
			}
			else{
				int j=0;
				for (j=i;j<len;j++)
					if (s[j]=='(')
						break;
				ans=max(ans,g(i,j-1));
				i=j-1;
			}
		}
		cout<<ans<<" "<<cnt<<endl;
	}
	return 0;
}
```

## C、Polycarp at the Radio

### 题意

n个数，用最少的次数来改变数字，使得1到m出现的次数的最小值最大。输出最小值和改变次数以及改变后的数组。

### 解题思路

最小值最大一定是n/m，然后把可以改变的位置上的数变为需要的数。

### 参考代码
```
#include <bits/stdc++.h>
using namespace std;
int a[2005], b[2005], c[2005];
bool used[2005];
int n, m;
void print(int k1,int k2){
	cout<<k1<<" "<<k2<<endl;
    for (int i=1;i<=n;i++)
    	cout<<a[i]<<" ";
    cout<<endl;
}
int main() {
    while (cin>>n>>m){
    	queue <int> q;
    	for (int i = 1; i <= n; i++) {
        	cin>>a[i];
        	if (a[i]>m){
        		q.push(i);
        		used[i]=true;
        	}
        	else{
        		b[a[i]]++;
        	}
    	}
    	int ii=1;
    	while (ii<=m){
    		int jj=1;
    		while (jj<=n){
    			if (c[ii]+1<=n/m){
        			if (a[jj]==ii){
        				used[jj]=true;
        				c[ii]++;
        			}
        		}
    			jj++;
    		}
    		ii++;
    	}
    	int cnt = 0,t;
    	for (int i = 1; i <= m; i++) {
       		while (!q.empty() && b[i] + 1 <= n / m) {
            		t = q.front(); 
            		q.pop();
            		a[t] = i;
            		b[i]++;
            		++cnt;
        	}
        	int j=1;
        	while (j<=n){
            	if (b[i] + 1 <= n / m){
            		if (!used[j]){
            			b[i]++;
                		a[j] = i;
                		used[j] = true;
                		++cnt;
            		}
                	
            	}
            	j++;
        	}
    	}
    	print(n/m,cnt);
	}
    return 0;
}
```

## D、Lakes in Berland

### 题意

海洋包围的小岛，岛内的有湖，`“.”`代表水，`“*”`代表陆地，给出的`n*m`的地图里至少有k个湖，求填掉面积尽量少的水，使得湖的数量正好为k。

### 解题思路

利用dfs我们将湖其标记为第几个湖，并记录湖的大小，如果某个湖在小岛的边缘，那么它不在是湖，我们要将其重新置于0，最后对湖的大小排序，将湖的面积小的先填为陆地，直到最好剩下k个湖。

### 参考代码
```C++
#include <bits/stdc++.h>
using namespace std;
int n,m,k;
char s[100][100];
int a[100][100];
bool used[100][100];
int ar[]={-1,0,1,0};
int br[]={0,1,0,-1};
int ans[3600];
struct Node{
	int index,v;
}P[3600];
int dfs(int x,int y,int flag){
	if (s[x][y]!='.') return 0;
	int cnt=1;
	a[x][y]=flag;
	used[x][y]=true;
	for (int i=0;i<4;i++){
		int xx=x+ar[i],yy=y+br[i];
		if (0<=xx && xx<n && 0<=yy && yy<m && used[xx][yy]==false)
			cnt+=dfs(xx,yy,flag);
	}
	return cnt;
}
bool cmp(Node xx,Node yy){
	return xx.v<yy.v;
}
int main(){
	while (cin>>n>>m>>k){
		for (int i=0;i<n;i++) cin>>s[i];
		memset(a,0,sizeof(a));
		memset(used,false,sizeof(used));
		int cnt=1;
		for (int i=0;i<n;i++){
			for (int j=0;j<m;j++){
				if (used[i][j]==false && s[i][j]=='.'){
					int k=dfs(i,j,cnt++);
					ans[cnt-1]=k;
				}
			}
		}
		for (int i=0;i<n;i++){
			if (a[i][0]!=0){
				ans[a[i][0]]=0;
			}
			if (a[i][m-1]){
				ans[a[i][m-1]]=0;
			}
		}
		for (int j=0;j<m;j++){
			if (a[0][j]){
				ans[a[0][j]]=0;
			}
			if (a[n-1][j]){
				ans[a[n-1][j]]=0;
			}
		}
		int count=0;
		for (int i=1;i<cnt;i++){
			if (ans[i]){
				P[count].index=i;
				P[count++].v=ans[i];
			}
		}
		sort(P,P+count,cmp);
		int inp=0;
		for (int ii=0;ii<count-k;ii++){
			
			for (int i=0;i<n;i++){
				for (int j=0;j<m;j++){
					if (a[i][j]==P[ii].index){
						s[i][j]='*';
						inp++;
					}
				}
			}
		}
		cout<<inp<<endl;
		for (int i=0;i<n;i++){
			cout<<s[i]<<endl;
		}
	}
	return 0;
}
```
