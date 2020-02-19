# 合并区间
Example 1:

Input: [[1,3],[2,6],[8,10],[15,18]]

Output: [[1,6],[8,10],[15,18]]

Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].

Example 2:

Input: [[1,4],[4,5]]

Output: [[1,5]]

Explanation: Intervals [1,4] and [4,5] are considered overlapping.

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/merge-intervals

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
## 排序+贪心

C++ code
```
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> res;
        if(intervals.size()==0) return res;
        sort(intervals.begin(), intervals.end(), [](vector<int>& a, vector<int>& b){return a[0]<b[0];}); //注意这里只能用<.改进见下面
        vector<int> temp(intervals[0]);
        for(int i=1;i<intervals.size();++i) {
            if(intervals[i][0]>temp[1]) {
                res.push_back(temp);
                temp.assign(intervals[i].begin(), intervals[i].end());
            }
            else {
                temp[1]=max(temp[1], intervals[i][1]); //max的必要性：[[1,4],[2,3]] 注意区间端点应该比较
            }
        }
        res.push_back(temp);
        return res;
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
87.37%
的用户

内存消耗 :
12.9 MB
, 在所有 C++ 提交中击败了
27.56%
的用户

注意：compare内部只能使用<，不能使用<=，

并且我还尝试了a[0]<b[0]||a[0]==b[0]&&a[1]<b[1],(这是多余的，因为a[1],b[1]之后会经过max比较一次)：

使用这种写法快了不少。

C++ code
```
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> res;
        if(intervals.size()==0) return res;
        sort(intervals.begin(), intervals.end(), [](vector<int>& a, vector<int>& b){return a[0]<b[0]||a[0]==b[0]&&a[1]<b[1];}); //唯一只改了这一行。//注意这里只能用<.
        vector<int> temp(intervals[0]);
        for(int i=1;i<intervals.size();++i) {
            if(intervals[i][0]>temp[1]) {
                res.push_back(temp);
                temp.assign(intervals[i].begin(), intervals[i].end());
            }
            else {
                temp[1]=max(temp[1], intervals[i][1]); //max的必要性：[[1,4],[2,3]] 注意区间端点应该比较
            }
        }
        res.push_back(temp);
        return res;
    }
};
```

执行用时 :
16 ms
, 在所有 C++ 提交中击败了
97.06%
的用户

内存消耗 :
12.8 MB
, 在所有 C++ 提交中击败了
29.31%
的用户
