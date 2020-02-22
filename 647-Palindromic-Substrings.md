# 回文子串
给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被计为是不同的子串。

示例 1:

输入: "abc"

输出: 3

解释: 三个回文子串: "a", "b", "c".

示例 2:

输入: "aaa"

输出: 6

说明: 6个回文子串: "a", "a", "a", "aa", "aa", "aaa".

注意:

输入的字符串长度不会超过1000。

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/palindromic-substrings

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## DP
时间On^2，空间On^2

相当于暴力解法，但是清晰易懂

然而testcases只过了128/130，

runtime error

这个没过：

"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"

C++ code
```
class Solution {
public:
    int countSubstrings(string s) {
        int n=s.size();
        if(n<=1) return n;
        int sum=n; 
        int dp[n][n];
        dp[0][0]=1;
        for(int i=1;i<n;++i) dp[i][0]=0;
        for(int j=1;j<n;++j) {
            dp[j][j]=1;
            if(s[j-1]==s[j]) {
                dp[j-1][j]=1;
                ++sum;
            }
            else dp[j-1][j]=0;
            for(int i=0;i<j-1;++i) {
                if(s[i]==s[j]&&dp[i+1][j-1]) {
                    dp[i][j]=1;
                    ++sum;
                }
                else dp[i][j]=0;
            }
        }
        return sum;
    }
};
 ```
 
 ## 中心扩展法
 
 这是回文字符串的经典解法,
 
 相比于dp，时间提升11倍
 
 思路：
 
 以每个字符为中心，向左右两边伸展，是回文串则伸展下去，不是回文串时结束，
 
 要分奇偶两种情况，偶数则以两个字符之间为中心。
 
 依然时间On^2，空间O1
 
 C++ code
 ```
 class Solution {
public:
    int countSubstrings(string s) {
        int n=s.size();
        if(n<=1) return n;
        int sum=n; 
        for(int i=1;i<n-1;++i) {
            int l=i-1, r=i+1;
            while(l>=0&&r<n&&s[l]==s[r]) {
                --l;++r;
            } 
            sum+=(i-l-1);
            l=i-1;r=i;
            while(l>=0&&r<n&&s[l]==s[r]) {
                --l;++r;
            }
            sum+=(i-l-1);
        }
        if(s[n-2]==s[n-1]) ++sum;
        return sum;
    }
};
```
执行用时 :
4 ms
, 在所有 C++ 提交中击败了
97.38%
的用户

内存消耗 :
8.8 MB
, 在所有 C++ 提交中击败了
73.98%
的用户
