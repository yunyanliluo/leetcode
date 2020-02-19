# 最长有效括号
Given a string containing just the characters '(' and ')', find the length of the longest valid (well-formed) parentheses substring.

Example 1:

Input: "(()"

Output: 2

Explanation: The longest valid parentheses substring is "()"

Example 2:

Input: ")()())"

Output: 4

Explanation: The longest valid parentheses substring is "()()"

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/longest-valid-parentheses

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## DP
十分钟AC一道hard：）

C++ code
```
class Solution {
public:
    int longestValidParentheses(string s) {
        int len=s.size();
        if(len<2) return 0;
        int maxLen=0;
        for(int i=0;i<len-1;++i) {
            if(s[i]=='(') {
                int sum=1;
                for(int j=i+1;j<len;++j) {
                    if(s[j]=='(') ++sum;
                    else --sum;
                    if(sum==0) maxLen=max(maxLen, j-i+1); //有效括号
                    if(sum<0) break; //‘)’多于’(‘
                }
            }
        }
        return maxLen;
    }
};
```
执行用时 :
1084 ms
, 在所有 C++ 提交中击败了
5.02%
的用户

内存消耗 :
9.2 MB
, 在所有 C++ 提交中击败了
77.25%
的用户

时间On^2，空间O1

时间能够提高

## DP - 遍历两遍

参考：https://leetcode-cn.com/problems/longest-valid-parentheses/solution/zui-chang-you-xiao-gua-hao-by-leetcode/ 方法四

从左到右遍历一次，从右向左再遍历一次

left记录(个数，right记录)个数

- left==right时 更新串长度

left>right时 left，right清零

时间On，空间O1

C++ code
```
class Solution {
public:
    int longestValidParentheses(string s) {
        int len=s.size();
        if(len<2) return 0;
        int maxLen=0;
        int left=0, right=0;
        for(int i=0;i<len;++i) {
            if(s[i]=='(') ++left;
            else ++right;
            if(left==right) maxLen=max(maxLen, left+left);
            else if(left<right) {
                left=0;right=0;
            }
        }
        left=0;right=0;
        for(int i=len-1;i>=0;--i) {
            if(s[i]=='(') ++left;
            else ++right;
            if(left==right) maxLen=max(maxLen, left+left);
            else if(left>right) {
                left=0;right=0;
            }
        }
        return maxLen;
    }
};
```
执行用时 :
4 ms
, 在所有 C++ 提交中击败了
95.59%
的用户

内存消耗 :
9.1 MB
, 在所有 C++ 提交中击败了
84.85%
的用户

## 使用栈

一次遍历

首先-1入栈

遍历到(，坐标入栈

遍历到)，栈顶元素出栈，更新串长度（当前遍历到的坐标-出栈后新栈顶元素坐标，倘若出栈后栈空，则将遍历到的坐标入栈）

数归可以证明，起始栈中有一个元素，每一次pop后，若栈空则push一次，从而可以保证栈内pop前永远有至少一个元素在。

时间On，空间On

C++ code
```
class Solution {
public:
    int longestValidParentheses(string s) {
        int len=s.size();
        if(len<2) return 0;
        int maxLen=0;
        stack<int> stk;
        stk.push(-1);
        for(int i=0;i<len;++i) {
            if(s[i]=='(') stk.push(i);
            else {
                // if(stk.empty()) {
                //     stk.push(i);
                //     continue;
                // } 这里实际上不可能存在栈空的情况。栈内总有一个先前的坐标。通过数归可以证明。
                stk.pop();
                if(stk.empty()) stk.push(i);
                else maxLen=max(maxLen, i-stk.top());
            }
        }
        return maxLen;
    }
};
```
执行用时 :
4 ms
, 在所有 C++ 提交中击败了
95.59%
的用户

内存消耗 :
9.7 MB
, 在所有 C++ 提交中击败了
17.92%
的用户


## 另一种DP
时间On，空间On

参考：https://leetcode-cn.com/problems/longest-valid-parentheses/solution/zui-chang-you-xiao-gua-hao-by-leetcode/ 方法二

遍历一遍

遍历到(，跳过（dp初始化默认为0）

遍历到)，

- 如果前一个是(，那么dp值等于(的前一个的dp值+2

如果前一个是)，那么这可能是个串A的末尾位置，根据这个串A末尾位置的dp值，可以找到这个串A的串左面挨着的一个位置，

1.如果这个位置是)，跳过

2.如果这个位置是(，那么我们找到了一个更新长度（等于中间串A+头尾2），这个位置左面可能还有一串B，事实上可以考虑可能有效串最多分为三段：左边一段B，(，中间一串A，)

其中，左边段和中间段的长度都可以调dp获得。

另外，需要考虑鲁棒性，左边段不存在的情况、 )所在串左面挨着的一个位置不存在的情况。

时间On，空间On

Java code
```
public class Solution {
    public int longestValidParentheses(String s) {
        int maxans = 0;
        int dp[] = new int[s.length()];
        for (int i = 1; i < s.length(); i++) { //从1开始遍历即可，因为dp[0]默认是0
            if (s.charAt(i) == ')') {
                if (s.charAt(i - 1) == '(') {
                    dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
                } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                    dp[i] = dp[i - 1] + ((i - dp[i - 1]) >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
                }
                maxans = Math.max(maxans, dp[i]);
            }
        }
        return maxans;
    }
}

//作者：LeetCode
//链接：https://leetcode-cn.com/problems/longest-valid-parentheses/solution/zui-chang-you-xiao-gua-hao-by-leetcode/
//来源：力扣（LeetCode）
//著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
