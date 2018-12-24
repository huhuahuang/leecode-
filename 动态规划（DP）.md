动态规划问题，在算法里面算是比较难的问题，关键是找到它的递归模型。

#1. 规则表达式匹配（Regular Expression Matching）【hard】
输入字符串S和匹配模型串p，实现支持'.'和'*'的规则表达式匹配。
'.' 匹配任意单个字符串
'*' 匹配0个或者多个前面的字符。
匹配模型串p需要匹配上完整字符串s。
注意：
1） s 可以为空，并且包括任意小写字符 a-z。
2） p 可以为空，并且包括任意小写字符 a-z，和 字符 '.'和'*'。

例如：
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

##解法一：
找到递归模型，
1) 普通字符匹配a-z，只需要判断是否相等就可以了。
2) p中的'.'可以匹配s中的任意字符。
3) p中的任意字符（A）后面加*，"A*"可以分成，A跟s对应字符匹配first_match和A跟s对应字符下一个匹配ismatch(s+1,p)
或者 s跟*后面的字符匹配 isMatch(s,p+2)
(```)
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
(```)

##解法二：
再递归的基础上，增加中间结果缓存，减少递归重复计算。
(```)
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
(```)
