---
title: LeetCode 6.Z字形变换
date: 2018-04-24 23:13:30
categories: [LeetCode]
---


## 题意

将字符串 "PAYPALISHIRING" 以Z字形排列成给定的行数：
```
P   A   H   N
A P L S I I G
Y   I   R
```
之后从左往右，逐行读取字符：`"PAHNAPLSIIGYIR"`

实现一个将字符串进行指定行数变换的函数:
```
string convert(string s, int numRows);
```
示例 1:

输入:` s = "PAYPALISHIRING", numRows = 3`
输出: `"PAHNAPLSIIGYIR"`
示例 2:

输入: `s = "PAYPALISHIRING", numRows = 4`
输出: `"PINALSIGYAHRPI"`
解释:
```
P     I    N
A   L S  I G
Y A   H R
P     I
```

## 解题思路

数学题，规律题，可以发现如下规律
```
--行数-------第一次增加的值-------第二次增加的值---
---1----------2(n-1)
---2----------2(n-2)----------------2*1---------
---3----------2(n-3)----------------2*2---------
---4----------2(n-4)----------------2*3---------
...
---n-------------------------------2*(n-1)------
```
这里需要注意当n为1时，增加的值都是0，不符合这个规律，需要加一个特判。

## 参考代码

```
//java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows==1)
            return s;
        String ans = "";
        int len = s.length();
        for (int i=0;i<numRows;i++){
            int pos = i;
            if (pos>=len) break;
            ans = ans + s.charAt(pos);
            for (int j=0;pos<len&&j<len;j++){
            	if (i<numRows-1) {
            		pos += 2*(numRows-i-1);
            		if (pos>=len) break;
            		ans = ans + s.charAt(pos);
            	}
                if (i>0){
                    pos += 2*i;
                    if (pos>=len) break;
                    ans = ans + s.charAt(pos);
                }
            }
        }
        return ans;
    }
}
```