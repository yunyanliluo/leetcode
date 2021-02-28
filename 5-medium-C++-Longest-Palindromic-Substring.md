Longest Palindromic Substring

# corner case:

1. "ccc" 
- output: "cc" 
- expectation: "ccc"

2. "bananas"
- output: "ana"
- expectation: "anana"


# my answer
dp[i][j] = length of the longest approproate substring of s[i][j]
runtime error


# solutions

1.dp - true or false instead of length, 时间复杂度O(n^2)，空间复杂度O(n^2)
```
class Solution {
public:
    string longestPalindrome(string s) {
        int n=s.size();
        if(n==0) return "";
        string resstr=s.substr(0,1);
        int maxlen=1;
        bool dp[1000][1000];
        for (int i=n-1; i>=0; --i)
        {
            dp[i][i]=1;
            for (int j=i+1; j<n; ++j)
            {
                if(s[i]==s[j]&&(i+1==j||dp[i+1][j-1]))//特殊情况就是i+1==j，dp值未定义
                {
                    dp[i][j]=1;
                    if(j-i+1>maxlen)
                    {
                        maxlen=j-i+1;
                        resstr=s.substr(i, j-i+1);
                    }    
                }
                else
                {
                    dp[i][j]=0;
                }
            }
        }
        return resstr;
    }
};
```
执行用时 :124 ms, 在所有 C++ 提交中击败了49.33%的用户

内存消耗 :17.4 MB, 在所有 C++ 提交中击败了40.35%的用户

2.中心扩展法

子串的对称中心总共有2*n-1个，包含n个奇数长度和n-1个偶数长度，分别求一下最长子串

时间复杂度O(n^2)，空间复杂度O(1)
```
class Solution {
public:
    string longestPalindrome(string s) {
        int n=s.size();
        if(n==0) return "";
        string resstr=s.substr(0,1);
        int maxlen=1;
        for (int i=1; i<n-1; ++i)
        {
            int j;
            for(j=1;j<=i&&i+j<n;++j)
            {
                if(s[i-j]!=s[i+j])
                {
                    if(2*j-1>maxlen)
                    {
                        maxlen=2*j-1;
                        resstr=s.substr(i-j+1, 2*j-1);
                    }
                    break;
                }
            }
            if(j>i||i+j>=n)//假如不是s[i-j]!=s[i+j]退出上一个循环的
            {
                if(2*j-1>maxlen)
                {
                    maxlen=2*j-1;
                    resstr=s.substr(i-j+1, 2*j-1);
                }
            }
        }
        for (int i=0; i<n-1; ++i)
        {
            int j;
            for(j=0;j<=i&&i+j+1<n;++j)
            {
                if(s[i-j]!=s[i+j+1])
                {
                    if(2*j>maxlen)
                    {
                        maxlen=2*j;
                        resstr=s.substr(i-j+1, 2*j);
                    }
                    break;
                }
            }
            if(j>i||i+j+1>=n)
            {
                if(2*j>maxlen)
                {
                    maxlen=2*j;
                    resstr=s.substr(i-j+1, 2*j);
                }
            }
        }
        return resstr;
    }
};
```
执行用时 :20 ms, 在所有 C++ 提交中击败了89.92%的用户

内存消耗 :12.6 MB, 在所有 C++ 提交中击败了53.36%的用户

3. dp[n], 时间复杂度O(n^2)，空间复杂度O(n)

先考虑examples,

corner cases,
```
class Solution {
public:
    string longestPalindrome(string s) {
        // ab b
        // abab bab
        // dabacaba 
        // cbb bb
        // dabaaba
        // dabaabaa
        // aaaaaaaaa
        if (s.size() == 0) return s;
        vector<int> dp(s.size(), 1);
        int maxLen = 1;
        string res = s.substr(0, 1);
        for (int i = 1; i < s.size(); ++ i) {
            if (s[i] == s[i - 1]) {
                dp[i] = max(dp[i], i > 1 && dp[i - 1] == dp[i - 2] + 1 ? dp[i - 1] + 1 : 2);
            }
            int temp = i - 1 - dp[i - 1];
            if (temp >= 0 && s[temp] == s[i]) {
                dp[i] = max(dp[i], dp[i - 1] + 2);
            } else if (dp[i] < dp[i - 1] + 1){
                for (int j = temp + 1; j < i - 1; ++ j) {
                    if (s[j] == s[i]) {
                        int i_ptr = i, j_ptr = j;
                        while (j_ptr < i_ptr) {
                            if (s[j_ptr] != s[i_ptr]) break;
                            -- i_ptr;
                            ++ j_ptr;
                        }
                        if (j_ptr >= i_ptr) {
                            dp[i] = max(dp[i], i - j + 1);
                            break;
                        }
                    }
                }
            }
            if (maxLen < dp[i]) {
                maxLen = dp[i];
                res = s.substr(i - dp[i] + 1, dp[i]);
            }
        }
        return res;
    }
};
```
执行用时：
24 ms
, 在所有 C++ 提交中击败了
87.26%
的用户

内存消耗：
24.5 MB
, 在所有 C++ 提交中击败了
55.05%
的用户

4. manacher算法，tricky，On
