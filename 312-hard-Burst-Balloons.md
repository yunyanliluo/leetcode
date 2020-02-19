# 戳气球

## DP
参考：https://leetcode-cn.com/problems/burst-balloons/solution/chao-xiang-xi-hui-su-dao-fen-zhi-dao-dp-by-niu-you/

算法示例：

计算3 5 8

1*3*5*8*1

8    1    0 （这种dp[i][i+1]=0，因为根本不符合实际，不可能只有两个数，左右至少应该补1）
5    8    0
5 8 1    40
3    5    0
3 5 8    120——回溯点
//3 5 1    40+15
3 8 1    120+24
1    3     0
1 3 5    15
1 3 8    120+24——回溯点
//1 5 8    15+40
//1 3 1    144+3
//1 5 1    15+40+5
1 8 1    144+8——回溯

最佳方案 ->    120(3 5 8)+24(1 3 8)+8(1 8 1)


Java code
```
 /**
     * @Author Nyr
     * @Date 2019/11/30 01:43
     * @Param
     * @Return
     * @Exception
     * @Description 动态规划解法
     */
    public static int maxCoins4DP(int[] nums) {
        //避免空指针异常
        if (nums == null) {
            return 0;
        }

        //创建虚拟边界。这一步是方法的最精妙之处
        int length = nums.length;
        int[] nums2 = new int[length + 2];
        System.arraycopy(nums, 0, nums2, 1, length);
        nums2[0] = 1;
        nums2[length + 1] = 1;
        length = nums2.length;

        //创建dp表
        length = nums2.length;
        int[][] dp = new int[length][length];

        //开始dp：i为begin，j为end，k为在i、j区间划分子问题时的边界
        for (int i = length - 2; i > -1; i--) {
            for (int j = i + 2; j < length; j++) {
                //维护一个最大值；如果i、j相邻，值为0
                int max = 0;
                for (int k = i + 1; k < j; k++) {
                    int temp = dp[i][k] + dp[k][j] + nums2[i] * nums2[k] * nums2[j];
                    if (temp > max) {
                        max = temp;
                    }
                }
                dp[i][j] = max;
            }
        }
        return dp[0][length-1];
    }


作者：niu-you-rou
链接：https://leetcode-cn.com/problems/burst-balloons/solution/chao-xiang-xi-hui-su-dao-fen-zhi-dao-dp-by-niu-you/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

C++ code
```
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int n=nums.size();
        if(n==0) return 0;
        vector<int> newnums(nums);
        newnums.insert(newnums.begin(), 1);
        newnums.push_back(1);
        int dp[n][n];
        dp[n-2][n-1]=0;
        for(int i=n-3;i>=0;--i) {
            dp[i][i+1]=0;
            for(int j=i+2;j<n;++j) {
                int max=0;
                for(int k=i+1;k<j;++k) {
                    int temp=dp[i][k]+dp[k][j]+newnums[i]*newnums[k]*newnums[j];
                    if(temp>max) max=temp;
                }
                dp[i][j]=max;
            }
        }
        return dp[0][n-1];
    }
};
```
执行用时 :
24 ms
, 在所有 C++ 提交中击败了
60.13%
的用户

内存消耗 :
9 MB
, 在所有 C++ 提交中击败了
85.54%
的用户
