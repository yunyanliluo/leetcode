# solutions
## hashmap

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

## sort and two ptrs, O(n)(n*2)
```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> sortednums = nums;
        sort(sortednums.begin(), sortednums.end());
        int i = 0, j = nums.size() - 1;
        int sum = sortednums[i] + sortednums[j];
        while (sum != target) {
            if (sum > target) {
                -- j;
            } else {
                ++ i;
            }
            sum = sortednums[i] + sortednums[j];
        }
        bool flagi = false, flagj = false;
        for (int k = 0; k < nums.size() && (!flagi || !flagj); ++ k) {
            if (flagi == false && nums[k] == sortednums[i]) {
                i = k;
                flagi = true;
            } else if (flagj == false && nums[k] == sortednums[j]) {
                j = k;
                flagj = true;
            }
        }
        return {i, j};
    }
};
```
执行用时：
0 ms
, 在所有 C++ 提交中击败了
100.00%
的用户

内存消耗：
8.7 MB
, 在所有 C++ 提交中击败了
82.36%
的用户
