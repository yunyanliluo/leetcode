# 最大子序列和

例如
```
Example 1:

Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
Example 2:

Input: nums = [1]
Output: 1
Example 3:

Input: nums = [0]
Output: 0
Example 4:

Input: nums = [-1]
Output: -1
Example 5:

Input: nums = [-100000]
Output: -100000
 

Constraints:

1 <= nums.length <= 3 * 104
-105 <= nums[i] <= 105


来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/maximum-subarray
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
```

## DP
```
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.size()==0)
            return 0;
        int i;
        int res=nums[0];
        int sum = 0;
        for (i=0; i<nums.size(); ++i) {
            if (sum > 0)
                sum += nums[i];
            else
                sum = nums[i];
            res = max(res, sum);
        }
        return res;
    }
};

或

class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int res = nums[0];
        int sum = 0;
        for (auto num : nums) {
            sum = max(sum, 0) + num;
            res = max(res, sum);
        }
        return res;
    }
};
```
执行用时：
4 ms
, 在所有 C++ 提交中击败了
97.74%
的用户

内存消耗：
12.8 MB
, 在所有 C++ 提交中击败了
77.57%
的用户
