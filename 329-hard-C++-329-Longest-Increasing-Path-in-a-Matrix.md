# solutions

## peel onion变形

优先队列实现小顶堆/大顶堆

初始化longest[][] = 0

按顺序pop，longest = max（四个邻居的longest）+1

```
class Solution {
public:
    struct Node {
        int val, x, y;
        bool operator< (const Node& a) const { //小顶堆
            return a.val<val;
        }
        Node(int v, int x0, int y0) {val = v; x = x0; y = y0;}
    };
    
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        priority_queue<Node> pq;
        int m = matrix.size();
        if (m==0) return 0;
        int n = matrix[0].size();
        for (int i = 0; i < m; ++ i) {
            for (int j = 0; j < n; ++ j) {
                Node temp(matrix[i][j], i, j);
                pq.push(temp);
            }
        }
        int length[m][n];
        for (int i = 0; i < m; ++ i) {
            for (int j = 0; j < n; ++ j) {
                length[i][j] = 0;
            }
        }
        int longest = 0;
        while (!pq.empty()) {
            Node temp = pq.top();
            int x = temp.x;
            int y = temp.y;
            pq.pop();
            length[x][y] = max(max(max(((x > 0 && matrix[x-1][y] < matrix[x][y]) ? length[x - 1][y] : 0), ((x < m - 1 && matrix[x+1][y] < matrix[x][y]) ? length[x + 1][y] : 0)), ((y > 0 && matrix[x][y-1] < matrix[x][y]) ? length[x][y - 1] : 0)), ((y < n - 1 && matrix[x][y+1] < matrix[x][y]) ? length[x][y + 1] : 0)) + 1;
            longest = max(longest, length[x][y]);
        }
        return longest;
    }
};
```
Runtime: 212 ms, faster than 24.07% of C++ online submissions for Longest Increasing Path in a Matrix.

Memory Usage: 20.7 MB, less than 29.15% of C++ online submissions for Longest Increasing Path in a Matrix.
