# 课程表
示例 1:

输入: 2, [[1,0]] 

输出: true

解释: 总共有 2 门课程。学习课程 1 之前，你需要完成课程 0。所以这是可能的。

示例 2:

输入: 2, [[1,0],[0,1]]

输出: false

解释: 总共有 2 门课程。学习课程 1 之前，你需要先完成​课程 0；并且学习课程 0 之前，你还应先完成课程 1。这是不可能的。

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/course-schedule

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## DFS
在有向图中寻找环，存在环则返回false，否则返回true。

C++ code
```
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        if(numCourses<=1) return true;
        unordered_map<int, vector<int>> precondition;
        for(int i=0;i<numCourses;++i)
            precondition[i]={};
        for(auto& prerequisite:prerequisites) {
            precondition[prerequisite[0]].push_back(prerequisite[1]);
        }
        vector<int> visitLevel(numCourses, 0);
        int level=1;
        for(int i=0;i<numCourses;++i) {
            if(visitLevel[i]==INT_MAX) continue;
            if(dfs(precondition, visitLevel, numCourses, level, i)==false) return false;
        }
        return true;
    }
    bool dfs(unordered_map<int, vector<int>>& precondition, vector<int>& visitLevel, int& numCourses, int& level, int CourseID) {
        if(visitLevel[CourseID]==INT_MAX) return true;
        else if(visitLevel[CourseID]!=level) {
            visitLevel[CourseID]=level;
            for(int& nextCourseID:precondition[CourseID]) {
                if(dfs(precondition, visitLevel, numCourses, level, nextCourseID)==false) return false;
            }
            visitLevel[CourseID]=INT_MAX;
            return true;
        }
        else return false;
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
94.52%
的用户

内存消耗 :
15.9 MB
, 在所有 C++ 提交中击败了
23.13%
的用户

上面的map可以优化为vector，因为key是按顺序的，可以做vector下标。


## 入度表（广度优先遍历）
可以欣赏BFS+队列的方法

参考链接：https://leetcode-cn.com/problems/course-schedule/solution/course-schedule-tuo-bu-pai-xu-bfsdfsliang-chong-fa/

算法流程：

统计课程安排图中每个节点的入度，生成 入度表 indegrees。

借助一个队列 queue，将所有入度为 
0 的节点入队。

当 queue 非空时，依次将队首节点出队，在课程安排图中删除此节点 pre：

并不是真正从邻接表中删除此节点 pre，而是将此节点对应所有邻接节点 cur 的入度 
−1，即 indegrees[cur] -= 1。

当入度 
−1后邻接节点 cur 的入度为 
0，说明 cur 所有的前驱节点已经被 “删除”，此时将 cur 入队。

在每次 pre 出队时，执行 numCourses--；

若整个课程安排图是有向无环图（即可以安排），则所有节点一定都入队并出队过，即完成拓扑排序。换个角度说，若课程安排图中存在环，一定有节点的入度始终不为 
0。

因此，拓扑排序出队次数等于课程个数，返回 numCourses == 0 判断课程是否可以成功安排。

复杂度分析：

时间复杂度 
O(N+M)，遍历一个图需要访问所有节点和所有临边，
N 和 
M 分别为节点数量和临边数量；

空间复杂度 
O(N)，为建立邻接矩阵所需额外空间。

Python code
```
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        indegrees = [0 for _ in range(numCourses)]
        adjacency = [[] for _ in range(numCourses)]
        queue = []
        # Get the indegree and adjacency of every course.
        for cur, pre in prerequisites:
            indegrees[cur] += 1
            adjacency[pre].append(cur)
        # Get all the courses with the indegree of 0.
        for i in range(len(indegrees)):
            if not indegrees[i]: queue.append(i)
        # BFS TopSort.
        while queue:
            pre = queue.pop(0)
            numCourses -= 1
            for cur in adjacency[pre]:
                indegrees[cur] -= 1
                if not indegrees[cur]: queue.append(cur)
        return not numCourses

作者：jyd
链接：https://leetcode-cn.com/problems/course-schedule/solution/course-schedule-tuo-bu-pai-xu-bfsdfsliang-chong-fa/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
