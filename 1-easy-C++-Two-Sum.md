# solutions

```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> dict;
        int n = nums.size();
        // vector<int> res;
        for (int i = 0; i < n; ++ i) {
            int need = target - nums[i];
            if (dict.find(need) != dict.end()) {
                vector<int> res = {dict[need], i};  // vector<int>的初始化方法
                // res.push_back(dict[need]);
                // res.push_back(i);
                return res;
            }
            dict[nums[i]] = i;
        }
        return {};
    }
};
```
Runtime: 8 ms, faster than 89.31% of C++ online submissions for Two Sum.

Memory Usage: 9.4 MB, less than 55.14% of C++ online submissions for Two Sum.
