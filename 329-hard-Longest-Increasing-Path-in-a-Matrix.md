# 矩阵中的最长递增路径
https://leetcode-cn.com/problems/longest-increasing-path-in-a-matrix/

## DP

improved code - C++

```
class Solution {
public:
    int directions[4][2] = {{-1,0},{1,0},{0,-1},{0,1}}; //使用类变量，比传引用参数更快
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        if(matrix.empty()||matrix[0].empty()) return 0;
        int m=matrix.size();
        int n=matrix[0].size();
        vector<vector<int>> dp(m, vector<int>(n,0));
        // vector<vector<int>> directions = {{0,1},{0,-1},{1,0},{-1,0}};
        int maxLen = 1;
        for(int i=0;i<m;++i) {
            for(int j=0;j<n;++j) {
                maxLen = max(maxLen, longestIncreasingPath2(matrix, m, n, dp, i, j)); //在函数外面maxLen，参数列表可以少写一个maxLen
            }
        }
        return maxLen;
    }
    int longestIncreasingPath2(vector<vector<int>>& matrix, int& m, int& n, vector<vector<int>>& dp, int& i, int& j) {
        if(dp[i][j]) return dp[i][j]; //dp初始值0，每一次计算以后+1，由于计算过后至少1，因此初始值0可以替代visited数组的功能
        for(auto& direction:directions) {
            int x = i+direction[0];
            int y = j+direction[1];
            if(x>=0 && x<m && y>=0 && y<n) {
                if(matrix[x][y]>matrix[i][j]) {
                    dp[i][j] = max(dp[i][j], longestIncreasingPath2(matrix, m, n, dp, x, y)); //函数有返回值，比函数返回再调一次dp存储快
                }
            }
        }
        ++dp[i][j];
        // maxLen = max(maxLen, dp[i][j]);
        return dp[i][j];
    }
};

//88 ms	14.1 MB
//百分之35.5, 百分之50
```

我的答案 - C++
```
class Solution {
public:
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        int m=matrix.size();
        int n=(m==0)?0:matrix[0].size();
        if(n==0) return 0;
        vector<int> temp(n,1);
        vector<vector<int>> dp(m, temp);
        vector<bool> temp2(n,false);
        vector<vector<bool>> visited(m, temp2);
        vector<vector<int>> directions = {{0,1},{0,-1},{1,0},{-1,0}};
        int maxLen = 1;
        for(int i=0;i<m;++i) {
            for(int j=0;j<n;++j) {
                if(visited[i][j]) continue;
                longestIncreasingPath2(matrix, m, n, dp, visited, directions, i, j, maxLen);
            }
        }
        return maxLen;
    }
    void longestIncreasingPath2(vector<vector<int>>& matrix, int& m, int& n, vector<vector<int>>& dp, vector<vector<bool>>& visited, vector<vector<int>>& directions, int& i, int& j, int& maxLen) {
        for(auto& direction:directions) {
            int x = i+direction[0];
            int y = j+direction[1];
            if(x>=0 && x<m && y>=0 && y<n) {
                if(matrix[x][y]>matrix[i][j]) {
                    if(!visited[x][y]) {
                        longestIncreasingPath2(matrix, m, n, dp, visited, directions, x, y, maxLen);
                    }
                    dp[i][j] = max(dp[i][j], dp[x][y]+1);
                    visited[i][j] = true;
                }
            }
        }
        maxLen = max(maxLen, dp[i][j]);
        return;
    }
};
//136 ms	15.7 MB
//百分之25, 百分之50
```
