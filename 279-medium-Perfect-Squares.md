# 完全平方数
将输入的n拆解成完全平方数之和，输出最少的加数数量。

## DP
这种连续数字，拆解成数字之和的题目，应该想到DP。

C++ code
```
class Solution {
public:
    int numSquares(int n) {
        int dp[n+1]={0}; //dp[0]=0;
        for(int i=1;i<=n;++i) {
            dp[i]=i;
            for(int j=1;j*j<=i;++j) {
                dp[i]=min(dp[i], dp[i-j*j]+1);
            }
        }
        return dp[n];
    }
};
```
执行用时 :
140 ms
, 在所有 C++ 提交中击败了
50.33%
的用户

内存消耗 :
8.4 MB
, 在所有 C++ 提交中击败了
84.07%
的用户

## BFS
思路可以欣赏。

将n，按照BFS一层一层，每一次拆成一个加数和剩下的和，

用一个队列装，直到遇到第一个剩余为0，一定是层数最少的答案。

还有一个处理，因为可能遇到多个相同的数，用visited数组标记，避免重复计算。

例如将11分解，

第一层，11，减1，4，9，将10，7，2插入队列，

第二层，10，减1，4，9，将9，6，1插入队列，

       7，减1，4，……，直到减去一个数得到0.返回层数。
       
Python code
```
class Solution:
    def numSquares(self, n: int) -> int:
        from collections import deque
        if n == 0: return 0
        queue = deque([n])
        step = 0
        visited = set()
        while(queue): //循环条件是队列非空
            step+=1
            l=len(queue)
            for _ in range(l):
                tmp=queue.pop()
                for i in range(1,int(tmp**0.5)+1):
                    x=tmp-i**2
                    if(x==0):
                        return step
                    if(x not in visited):
                        queue.appendleft(x)
                        visited.add(x) //计算过的数不会重复计算
        return step

作者：zhu_shi_fu
链接：https://leetcode-cn.com/problems/perfect-squares/solution/dong-tai-gui-hua-bfs-zhu-xing-jie-shi-python3-by-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
