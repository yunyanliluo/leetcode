# *Queue Reconstruction by Height*

Suppose you have a random list of people standing in a queue. Each person is described by a pair of integers (h, k), where h is the height of the person and k is the number of people in front of this person who have a height greater than or equal to h. Write an algorithm to reconstruct the queue.

Note:
The number of people is less than 1,100.

 
Example

Input:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]

Output:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/queue-reconstruction-by-height

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## solutions

### 贪心

排序，然后插入。

假设候选队列为 A，已经站好队的队列为 B.

从 A 里挑身高最高的人 x 出来，插入到 B. 因为 B 中每个人的身高都比 x 要高，因此 x 插入的位置，就是看 x 前面应该有多少人就行了。比如 x 前面有 5 个人，那 x 就插入到队列 B 的第 5 个位置。

原文链接：https://leetcode-cn.com/problems/queue-reconstruction-by-height/solution/406-gen-ju-shen-gao-zhong-jian-dui-lie-pai-xu-hou-/

一个例子
        // 先排序
        // [7,0], [7,1], [6,1], [5,0], [5,2], [4,4]
        
        // 再一个一个插入。
        // [7,0]
        // [7,0], [7,1]
        // [7,0], [6,1], [7,1]
        // [5,0], [7,0], [6,1], [7,1]
        // [5,0], [7,0], [5,2], [6,1], [7,1]
        // [5,0], [7,0], [5,2], [6,1], [4,4], [7,1]

my C++ code:
```C++
class Solution {
public:
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        sort(people.begin(), people.end(), [](const vector<int>& people1, const vector<int>& people2) //C++11中sort内自定义compare的写法
        {
            return (people1[0]>people2[0]||people1[0]==people2[0]&&people1[1]<people2[1]);//让[7,0]在[7,1]前面的原因是第一个插入队列的元素一定是[k,0]
        });
        vector<vector<int>> sortpp;
        for(auto iter=people.begin();iter!=people.end();++iter)
        {
            sortpp.insert(sortpp.begin()+((*iter)[1]), *iter);
        }
        return sortpp;
    }
};
```
执行用时 :100 ms, 在所有 cpp 提交中击败了87.93%的用户

内存消耗 :12.6 MB, 在所有 cpp 提交中击败了5.29%的用户
