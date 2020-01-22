# Number of Islands
Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

Example 1:

Input:

11110

11010

11000

00000

Output: 1

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/number-of-islands

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 一种可复用的并查集代码
感谢：https://leetcode-cn.com/problems/number-of-islands/solution/dfs-bfs-bing-cha-ji-python-dai-ma-java-dai-ma-by-l/
C++ code
```
class Solution {
public:
    class UnionFind {
    public:
        UnionFind(int n) {
            parent=new int [n];
            rank=new int [n];
            for(int i=0;i<n;++i) {
                parent[i]=i;
                rank[i]=1;
            }
            count=n;
        }
        int getCount() {
            return count;
        }
        int find(int x) {
            while(parent[x]!=x) {
                x=parent[x];
            }
            return x;
        }
        bool isConnected(int p, int q) {
            return find(p)==find(q);
        }
        void join(int p, int q) {
            int p_root=find(p);
            int q_root=find(q);
            if(p_root!=q_root) {
                if(rank[q_root]>rank[p_root]) parent[p_root]=q_root;
                else if(rank[q_root]<rank[p_root]) parent[q_root]=p_root;
                else {
                    parent[p_root]=q_root;
                    ++rank[q_root];
                }
                --count;
            }
        }
    private:
        int* parent;
        int* rank;
        int count;
    };
    int getIndex(int i, int j) {
        return i*c+j;
    }
    int numIslands(vector<vector<char>>& grid) {
        r=grid.size();
        if(r==0) return 0;
        c=grid[0].size();
        
        vector<vector<int>> directions={{0,1},{1,0}};
        int area=r*c;
        UnionFind* uf=new UnionFind(area+1);
        for(int i=0;i<r;++i) {
            for(int j=0;j<c;++j) {
                if(grid[i][j]=='0') uf->join(getIndex(i, j), area);
                else {
                    for(const auto& direction: directions) {
                        int new_x=i+direction[0];
                        int new_y=j+direction[1];
                        if(new_x<r&&new_y<c&&grid[new_x][new_y]=='1') {
                            uf->join(getIndex(i, j), getIndex(new_x, new_y));
                        }
                    }
                }
            }
        }
        return uf->getCount()-1;
    }
private:
    int r, c;
};
```
执行用时 :16 ms, 在所有 C++ 提交中击败了59.71%的用户

内存消耗 :11.6 MB, 在所有 C++ 提交中击败了18.84%的用户

## 进一步改进
C++ code，感谢评论中的改进：https://leetcode-cn.com/problems/number-of-islands/solution/dfs-bfs-bing-cha-ji-python-dai-ma-java-dai-ma-by-l/185896
```
class Solution {
public:
    class UnionFind {
    public:
        UnionFind(int n) {
            parent=new int [n];
            rank=new int [n];
            for(int i=0;i<n;++i) {
                parent[i]=i;
                rank[i]=1;
            }
            count=n;
        }
        int getCount() {
            return count;
        }
        int find(int x) {
            while(parent[x]!=x) {
                parent[x]=parent[parent[x]]; //优化,这一句可以去掉，只添加这一句使得rank不准啦，同时时间从百分之60提升到百分之88，空间增加百分之2
                x=parent[x];
            }
            return x;
        }
        //bool isConnected(int p, int q) { 
        //    return find(p)==find(q);
        //}
        void join(int p, int q) {
            int p_root=find(p);
            int q_root=find(q);
            if(p_root!=q_root) {
                if(rank[q_root]>rank[p_root]) { //优化，此时rank是一种估计量------------
                    parent[p_root]=q_root;
                    ++rank[q_root];
                }
                else { //优化------------
                    parent[q_root]=p_root;
                    ++rank[p_root];
                }
                --count;
            }
        }
    private:
        int* parent;
        int* rank;
        int count;
    };
    int getIndex(int i, int j) {
        return i*c+j;
    }
    int numIslands(vector<vector<char>>& grid) {
        r=grid.size();
        if(r==0) return 0;
        c=grid[0].size();
        int waterNum=0; //记录水的数量-------------
        int area=r*c;
        UnionFind* uf=new UnionFind(area);
        for(int i=0;i<r;++i) {
            for(int j=0;j<c;++j) {
                if(grid[i][j]=='0') ++waterNum; //优化，加法比join运算更快-----------
                else {                   
                        int new_x=i+1; //优化，相当于可以拆分成两个if，进行更少的比较，去掉循环------------
                        int new_y=j+1;
                        if(new_x<r&&grid[new_x][j]=='1') {
                            uf->join(getIndex(i, j), getIndex(new_x, j));
                        }
                        if(new_y<c&&grid[i][new_y]=='1') {
                            uf->join(getIndex(i, j), getIndex(i, new_y));
                        }
                }
            }
        }
        return uf->getCount()-waterNum; //减去值改变---------------
    }
private:
    int r, c;
};
```
执行用时 :8 ms, 在所有 C++ 提交中击败了98.72%的用户

内存消耗 :11.3 MB, 在所有 C++ 提交中击败了26.81%的用户

## 一份清爽的并查集代码，速度超过百分之百
C++ code，感谢力扣的百分之百答案
```
static const auto _____ = []()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return nullptr;
}();
/*
    查并集的方式，前面一个12ms的是DFS
*/
class UnionSet {
public:
    UnionSet(int cnt) :count(cnt) {
        for(int i = 0; i < count; ++i) {
            parent.push_back(i);
        }
    }

    int find(int i) {
        if(parent[i] != i) return find(parent[i]);
        else return i;
    }
    void uni(int i, int j) {
        int x = find(i);
        int y = find(j);
        if(x != y) {
            parent[x] = y;
            --count;
        }
    }
    int get_cnt() const {return count;}
private:
    int count;
    vector<int> parent;
};

class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        if(grid.size() == 0) return 0;
        int cnt = 0, n = grid.size(), m = grid[0].size();
        UnionSet uSet(n * m);
        for(int i = 0; i < n; ++i) {
            for(int j = 0; j <m; ++j) {
                if(grid[i][j] == '1') {
                    grid[i][j] = 0;
                    ++cnt;
                    if(j + 1 < m && grid[i][j+1] == '1') uSet.uni(i*m+j, i*m+j+1);
                    if(i + 1 < n && grid[i+1][j] == '1') uSet.uni(i*m+j, (i+1)*m+j);
                }
            }
        }
        return uSet.get_cnt() - n*m + cnt;
    }
};
```
执行用时 :
0 ms
, 在所有 C++ 提交中击败了
100.00%
的用户
内存消耗 :
11.8 MB
, 在所有 C++ 提交中击败了
14.00%
的用户
