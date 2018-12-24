动态规划问题，在算法里面算是比较难的问题，关键是找到它的递归模型。

# 1. 规则表达式匹配（Regular Expression Matching）【hard】

输入字符串S和匹配模型串p，实现支持'.'和'*'的规则表达式匹配。<br>
'.' 匹配任意单个字符串<br>
'*' 匹配0个或者多个前面的字符。<br>
匹配模型串p需要匹配上完整字符串s。<br>
注意：<br>
1） s 可以为空，并且包括任意小写字符 a-z。<br>
2） p 可以为空，并且包括任意小写字符 a-z，和 字符 '.'和'*'。<br>

例如：
```
例子1
Input:
s = "aa"
p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".

例子2：
Input:
s = "aa"
p = "a*"
Output: true
Explanation: '*' means zero or more of the precedeng element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".

例子3：
Input:
s = "ab"
p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".

例子4:
Input:
s = "aab"
p = "c*a*b"
Output: true
Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore it matches "aab".

例子5：
Input:
s = "mississippi"
p = "mis*is*p*."
Output: false
```

## 解法一：

找到递归模型，
1) 普通字符匹配a-z，只需要判断是否相等就可以了。
2) p中的'.'可以匹配s中的任意字符。
3) p中的任意字符（A）后面加*，"A*"可以分成，A跟s对应字符匹配first_match和A跟s对应字符下一个匹配ismatch(s+1,p)
或者 s跟*后面的字符匹配 isMatch(s,p+2)。

``` c++
class Solution {
public:
    bool isMatch(string s, string p) {
        
        if(p.size() <= 0) 
            return s.size() <= 0;
        
        bool first_match = ( s.size() > 0) && 
            (s[0] == p[0] || p[0] == '.');
        
        
        if(p.size() >= 2 && p[1] == '*'){
            
            return (isMatch(s, p.substr(2))) || (first_match && isMatch(s.substr(1),p));
        }
        else
            return first_match && isMatch(s.substr(1), p.substr(1));
        
        
        
    }
};
```

## 解法二：

再递归的基础上，增加中间结果缓存，减少递归重复计算。

``` c++
class Solution {
public:
    
    int **inter;
    
    bool isMatch(string s, string p) {
        
        const int lenx = s.size() + 1;
        const int leny = p.size() + 1;
        inter = new int*[lenx];
        for(int i = 0; i < lenx; i++)
            inter[i] = new int[leny];
        
        for(int i = 0; i < lenx; i++)
            for(int j = 0; j < leny; j++)
                inter[i][j] = -1;
        
        return dp(0, 0, s, p);
        
        
    }
    
    bool dp(int i, int j, string s, string p){
        
        if(inter[i][j] != -1) 
            return inter[i][j] == 1;
        
        if(j >= p.size() && i >= s.size()) 
        {
            inter[i][j] = 1;
            return true;
        }
        
        
        bool first_match = ( i < s.size()) && 
            (s[i] == p[j] || p[j] == '.');
        
        
        bool bl;
        if(p.size() >= 2 + j && p[j + 1] == '*'){
            
            bl = (dp(i, j + 2, s, p)) || (first_match && dp(i+1,j, s,p));
        }
        else
            bl = first_match && dp(i+1, j+1, s, p);
        
       
        inter[i][j] = (bl == true) ? 1:0;
        
        return bl;
    }
};
```

# 2. 捕捉雨水（Trapping Rain Water）

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

![](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)<br>
The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. Thanks Marcos for contributing this image!

Example:

Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6

# 解法（技巧）
找数组中最大的值，再从这个数左右方向分别找对应的次最大值max，再求区间的各个差值之和。
代码如下：
``` c++
class Solution {
public:
    int trap(vector<int>& height) {
        
        int k = 0;
        //find max value
        int maxValue = -1;
        for(int i = 0; i < height.size(); i++){
            if(maxValue < height[i]){
                k = i;
                maxValue = height[i];
            }
        }
        //cout << k << "," << maxValue << endl;
        return trapij(height, 0, k -1, true) + trapij(height, k+1, height.size() -1, false);
        
    }
    
    
    int trapij(vector<int>& height, int i, int j, bool bl/*true left, false right*/){
        if(i >= j) return 0;
        //string str = bl?"左":"右";
        //cout << "pos: " << i << "," << j << "," << str << endl;
        
        int k = 0;
        //find max value
        int maxValue = -1;
        
        for(int ii = i; ii <= j; ii++){
            if(maxValue < height[ii]){
                k = ii;
                maxValue = height[ii];
            }
        }
        //cout << k << "," << maxValue << endl;
        
        int value = 0;
        if(bl)//left
        {
            for(int ii = k + 1; ii <= j; ii ++){
                value += maxValue - height[ii];
            }
            
            return trapij(height, i, k-1, true) + value;
        }
        else//right
        {
            for(int ii = i; ii < k; ii ++){
                value += maxValue - height[ii];
            }
            
            return trapij(height, k+1, j, false) + value;
        }
    }
};
```
