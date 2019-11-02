# *Generate Parentheses*

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

For example, given n = 3, a solution set is:

[

  "((()))",
  
  "(()())",
  
  "(())()",
  
  "()(())",
  
  "()()()"
  
]

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/generate-parentheses

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## my answer

### 动态规划

1.自顶向下：递归调用，时间换空间，时间比较久

Java code from：https://leetcode-cn.com/problems/generate-parentheses/solution/gua-hao-sheng-cheng-by-leetcode/
```Java code
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> ans = new ArrayList();
        if (n == 0) {
            ans.add("");
        } else {
            for (int c = 0; c < n; ++c)
                for (String left: generateParenthesis(c))
                    for (String right: generateParenthesis(n-1-c))
                        ans.add("(" + left + ")" + right);
        }
        return ans;
    }
}
```
时间和空间复杂度：O(4^n/sqrt{n})


2.自底向上：数组存储，空间换时间，空间比较大

原文链接：https://leetcode-cn.com/problems/generate-parentheses/solution/zui-jian-dan-yi-dong-de-dong-tai-gui-hua-bu-lun-da/

"(" + 【i=p时所有括号的排列组合】 + ")" + 【i=q时所有括号的排列组合】

其中 p + q = n-1，且 p q 均为非负整数。

事实上，当上述 p 从 0 取到 n-1，q 从 n-1 取到 0 后，所有情况就遍历完了。

my C++ code

```C++ code
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> tempres;
        if(n==0) return tempres;
        vector<vector<string>> res;
        tempres.push_back("");
        res.push_back(tempres);
        for (int len=1;len<=n;++len)
        {
            tempres.clear();
            for(int left=0;left<len;++left)
            {
                int right=len-left-1;
                for(int i=0;i<res[left].size();++i)
                {
                    for(int j=0;j<res[right].size();++j)
                    {
                        tempres.push_back("("+res[left][i]+")"+res[right][j]);
                    }
                }
            }
            res.push_back(tempres);
        }
        return tempres;
    }
};
```
执行用时 :8 ms, 在所有 cpp 提交中击败了82.82%的用户
内存消耗 :9.7 MB, 在所有 cpp 提交中击败了97.75%的用户

## solutions

### 回溯法

Java code from: https://leetcode-cn.com/problems/generate-parentheses/solution/gua-hao-sheng-cheng-by-leetcode/

我们可以通过跟踪到目前为止放置的左括号和右括号的数目来做到这一点，

距离总的括号对数n，计数当前左括号数open和右括号数close，

如果我们没有放完左括号，我们可以开始放一个左括号。 如果右括号不超过左括号的数量，我们可以放一个右括号。

Java code from link above

```Java code
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> ans = new ArrayList();
        backtrack(ans, "", 0, 0, n);
        return ans;
    }

    public void backtrack(List<String> ans, String cur, int open, int close, int max){
        if (cur.length() == max * 2) {
            ans.add(cur);
            return;
        }

        if (open < max)
            backtrack(ans, cur+"(", open+1, close, max);
        if (close < open)
            backtrack(ans, cur+")", open, close+1, max);
    }
}
```

我们的复杂度分析依赖于理解 generateParenthesis(n) 中有多少个元素。这个分析超出了本文的范畴，但事实证明这是第 n 个卡塔兰数 

时间复杂度：O(4^n/sqrt{n})，在回溯过程中，每个有效序列最多需要 n 步。

空间复杂度：O(4^n/sqrt{n})，如上所述，并使用 O(n) 的空间来存储序列。
