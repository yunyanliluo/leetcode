Maximal Square
最大正方形
在一个由 0 和 1 组成的二维矩阵内，找到只包含 1 的最大正方形，并返回其面积。

#solutions
#dp - 使用二维数组
#时间复杂度O（mn），空间复杂度O（mn）
链接：https://leetcode-cn.com/problems/maximal-square/solution/zui-da-zheng-fang-xing-by-leetcode/
dp(i,j) 表示的是由 1 组成的最大正方形的边长；
从 (0,0) 开始，对原始矩阵中的每一个 1，我们将当前元素的值更新为
dp(i, j)=min(dp(i−1, j), dp(i−1, j−1), dp(i, j−1))+1
我们还用一个变量记录当前出现的最大边长，这样遍历一次找到最大的正方形边长 maxsqlenmaxsqlen，那么结果就是 maxsqlen^2。

#dp - 只使用一维数组
#时间复杂度O（mn），空间复杂度O（n）
链接：https://leetcode-cn.com/problems/maximal-square/solution/zui-da-zheng-fang-xing-by-leetcode/
在前面的动态规划解法中，计算 i th  行（row）的 dp 方法中，我们只使用了上一个元素和第 (i−1) th行，因此我们不需要二维 dp 矩阵，因为一维 dp 足以满足。
我们扫描一行原始矩阵元素时，我们根据公式：dp[j]=min(dp[j−1],dp[j],prev) 更新数组 dp,其中 prev 指的是 dp[j-1]。对于每一行，我们重复相同过程并在 dp 矩阵中更新元素。
#my C++ code
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int m=matrix.size(), n=m>0 ? matrix[0].size() : 0; //假如matrix==[],matrix[0]不存在出现runtime error
        int maxsqlen=0;
        if (n==0) return 0;
        int dp[n]={0};
        int prev=0;
        for (int i=0; i<m; ++i)
        for (int j=0; j<n; ++j)
        {
            int temp=dp[j];
            if (matrix[i][j]=='0') dp[j]=0; 
            else if (i==0||j==0)  
            {
                dp[j]=1; 
                maxsqlen=max(maxsqlen, 1); 
            } //原代码漏过了遍历到边界的1没更新maxsqlen，结果仅在边界有1的情况
            else
            {
                dp[j]=min(min(dp[j-1], dp[j]),prev)+1; //min()的参数列表只有两个参数，若处理多于两个需要迭代
                if (dp[j]>maxsqlen) maxsqlen=dp[j];
            }
            prev=temp;
        }
        return maxsqlen*maxsqlen;
    }
};
执行用时 :20 ms, 在所有 C++ 提交中击败了87.45%的用户
内存消耗 :10.4 MB, 在所有 C++ 提交中击败了99.17%的用户

#dp - 不使用额外的dp数组，只在原数组上修改
#时间复杂度O（mn），空间复杂度O（1）
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int m=matrix.size(), n=m>0 ? matrix[0].size() : 0; //假如matrix==[],matrix[0]不存在出现runtime error
        int maxsqlen=0;
        for (int i=0; i<m; ++i)
        for (int j=0; j<n; ++j)
        {
            if (matrix[i][j]=='0') continue;
            if (i==0||j==0)  {maxsqlen=max(maxsqlen, 1); continue;} //原代码漏过了遍历到边界的1没更新maxsqlen，结果仅在边界有1的情况
            matrix[i][j]=min(min(matrix[i-1][j-1], matrix[i-1][j]), matrix[i][j-1])+1; //min()的参数列表只有两个参数，若处理多于两个需要迭代
            if (matrix[i][j]-'0'>maxsqlen) maxsqlen=matrix[i][j]-'0';
        }
        return maxsqlen*maxsqlen;
    }
};
执行用时 :20 ms, 在所有 C++ 提交中击败了87.45%的用户
内存消耗 :10.4 MB, 在所有 C++ 提交中击败了99.72%的用户

