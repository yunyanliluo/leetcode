# 正则表达式匹配
Given an input string (s) and a pattern (p), implement regular expression matching with support for '.' and '*'.

'.' Matches any single character.

'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

Note:

s could be empty and contains only lowercase letters a-z.

p could be empty and contains only lowercase letters a-z, and characters like . or *.

Example 1:

Input:
s = "aa"
p = "a"

Output: false

Explanation: "a" does not match the entire string "aa".

Example 2:

Input:
s = "aa"
p = "a*"

Output: true

Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".

Example 3:

Input:
s = "ab"
p = ".*"

Output: true

Explanation: ".*" means "zero or more (*) of any character (.)".

Example 4:

Input:
s = "aab"
p = "c*a*b"

Output: true

Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore, it matches "aab".

Example 5:

Input:
s = "mississippi"
p = "mis*is*p*."

Output: false

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/regular-expression-matching

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## DP

注意题意 *的功能是前一个字符0次或重复若干次

参考：https://leetcode-cn.com/problems/regular-expression-matching/solution/dong-tai-gui-hua-zen-yao-cong-0kai-shi-si-kao-da-b/

推理dp[i][j]：

- 如果 p.charAt(j) == s.charAt(i) : dp[i][j] = dp[i-1][j-1]；
- 如果 p.charAt(j) == '.' : dp[i][j] = dp[i-1][j-1]；
- 如果 p.charAt(j) == '*'：
其中，

1.如果 p.charAt(j-1) != s.charAt(i) : dp[i][j] = dp[i][j-2] //in this case, a* only counts as empty

2.如果 p.charAt(i-1) == s.charAt(i) or p.charAt(i-1) == '.'：
dp[i][j] = dp[i-1][j] //in this case, a* counts as multiple/single a
or dp[i][j] = dp[i][j-2] // in this case, a* counts as empty

C++ code
```
class Solution {
public:
    bool isMatch(string s, string p) {
        if(p.size()==0) return s=="";
        if(s.size()==0&&p.size()==1) return false;
        int nrol=s.size()+1, ncol=p.size()+1;
        vector<vector<bool>> dp;
        vector<bool> dpTEMP(ncol, false);
        dpTEMP[0]=true;
        dpTEMP[1]=false;
        for(int c=2;c<ncol;++c) {
            if(p[c-1]=='*') dpTEMP[c]=dpTEMP[c-2];
        }
        dp.push_back(dpTEMP);
        for(int r=1;r<nrol;++r) {
            dpTEMP[0]=false;
            int i=r-1;
            for(int c=1;c<ncol;++c) {
                int j=c-1;
                if(s[i]==p[j]||p[j]=='.') dpTEMP[c]=dp[r-1][c-1];
                else if(p[j]=='*') {
                    if(s[i]==p[j-1]||p[j-1]=='.') dpTEMP[c]=(dp[r-1][c]||dpTEMP[c-2]); //这里容易忘记’.’
                    else dpTEMP[c]=dpTEMP[c-2];
                }
                else {
                    dpTEMP[c]=false;
                }
            }
            dp.push_back(dpTEMP);
        }
        return dp[nrol-1][ncol-1];
    }
};
```
执行用时 :
12 ms
, 在所有 C++ 提交中击败了
62.63%
的用户

内存消耗 :
9.6 MB
, 在所有 C++ 提交中击败了
38.26%
的用户

时间Omn，空间Omn

能够优化为空间On（只需要一行），使用prev记录dp[r-1][c-1]即可，在此不予以实现了。
