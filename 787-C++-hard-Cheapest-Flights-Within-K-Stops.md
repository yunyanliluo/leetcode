# Cheapest Flights Within K Stops

难度 medium -> hard

```
There are n cities connected by some number of flights. You are given an array flights where flights[i] = [fromi, toi, pricei] indicates that there is a flight from city fromi to city toi with cost pricei.

You are also given three integers src, dst, and k, return the cheapest price from src to dst with at most k stops. If there is no such route, return -1.

 

Example 1:


Input: n = 3, flights = [[0,1,100],[1,2,100],[0,2,500]], src = 0, dst = 2, k = 1
Output: 200
Explanation: The graph is shown.
The cheapest price from city 0 to city 2 with at most 1 stop costs 200, as marked red in the picture.
Example 2:


Input: n = 3, flights = [[0,1,100],[1,2,100],[0,2,500]], src = 0, dst = 2, k = 0
Output: 500
Explanation: The graph is shown.
The cheapest price from city 0 to city 2 with at most 0 stop costs 500, as marked blue in the picture.
 

Constraints:

1 <= n <= 100
0 <= flights.length <= (n * (n - 1) / 2)
flights[i].length == 3
0 <= fromi, toi < n
fromi != toi
1 <= pricei <= 104
There will not be any multiple flights between two cities.
0 <= src, dst, k < n
src != dst

```

## my attempt
Dijkstra, WA

my code
```C++
class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        // 17:28 - 17:54, 17:54 - 18:13
        unordered_set<int> s_set, d_set;
        s_set.insert(src);
        for (int i = 0; i < n; ++i) {
            if (i != src) {
                d_set.insert(i);
            }
        }
        vector<vector<int>> dist, step;
        vector<int> temp(n, pow(10, 4) + 1);
        for (int i = 0; i < n; ++i) {
            dist.push_back(temp);
        }
        vector<int> temp2(n, 0);
        for (int i = 0; i < n; ++i) {
            step.push_back(temp2);
        }
        for (auto & flight : flights) {
            dist[flight[0]][flight[1]] = flight[2];
        }
        while (d_set.find(dst) != d_set.end()) {
            int minprice = pow(10, 4) + 1;
            int mincity = -1;
            for (auto & d : d_set) {
                if (dist[src][d] < minprice) {
                    minprice = dist[src][d];
                    mincity = d;
                }
            }
            if (mincity == -1) {
                break;
            }
            if (mincity == dst) {
                return dist[src][dst];
            }
            s_set.insert(mincity);
            d_set.erase(mincity);
            for (auto & d : d_set) {
if (dist[src][mincity] + dist[mincity][d] < dist[src][d]) {
                    if (d == dst && step[src][mincity] + step[mincity][d] + 1 > k) {
                        continue;
                    } else {
                        dist[src][d] = dist[src][mincity] + dist[mincity][d];
                        step[src][d] = step[src][mincity] + step[mincity][d] + 1;
                    }
                }
            }
        }
        // if (steps <= k && dist[src][dst] != pow(10, 4) + 1) {
        //     return dist[src][dst];
        // }
        return -1;
    }
};
```

## DFS with memorization
递推公式： A->B(k stops) = A->C + C->B(k-1 stops)

二维dp[v][k] 表示起点和stops数（终点固定）

```C++
class Solution {
public:
    int helper(int s, int d, int k, int n, vector<vector<int>>& adja, vector<vector<int>>& memo) {
        if (s == d) return 0;
        if (k < 0) return INT_MAX;
        if (k == 0) return adja[s][d];
        if (memo[s][k] != -1) return memo[s][k];
        int minprice = INT_MAX;
        for (int i = 0; i < n; ++i) {
            if (i != s && adja[s][i] != INT_MAX) {
                int temp = helper(i, d, k - 1, n, adja, memo);
                if (temp != INT_MAX)
                    minprice = min(minprice, temp + adja[s][i]);  // 这里提防相加溢出
            }
        }
        memo[s][k] = minprice;
        return minprice;
    }
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        int res;
        vector<vector<int>> adja;
        vector<int> temp1(n, INT_MAX);
        for (int i = 0; i < n; ++i) {
            adja.push_back(temp1);
        }
        for (auto & flight : flights) {
            adja[flight[0]][flight[1]] = flight[2];
        }
        vector<vector<int>> memo; // memo[i][j] represents min price from a city i to dst stopped j stops
        vector<int> temp(k + 1, -1);
        for (int i = 0; i < n; ++i) {
            memo.push_back(temp);
        }
        res = helper(src, dst, k, n, adja, memo);
        return res == INT_MAX ? -1 : res;
    }
};
Runtime: 32 ms, faster than 47.04% of C++ online submissions for Cheapest Flights Within K Stops.
Memory Usage: 12.9 MB, less than 43.05% of C++ online submissions for Cheapest Flights Within K Stops.
```

复杂度 O(Vk)

## Bellman Ford

- 不需要将原始输入整理成邻接矩阵
- k stops，最多经历k+1条边，于是迭代k+1次，0～k，每一次迭代表示经过0～kstops的最优解
- 当前迭代，用到上一轮迭代的结果，要避免使用到本轮刚刚更新过的结果（否则经过stops数无法保证），于是使用两个数组，分别记录当前轮和上一轮迭代的结果

```C++
class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<vector<int>> prices;
        vector<int> temp(n, INT_MAX);
        prices.push_back(temp);
        prices.push_back(temp);
        prices[0][src] = 0;
        prices[1][src] = 0;
        
        for (int i = 0; i <= k; ++i) {
            for (auto & flt : flights) {
                int u = flt[0], v = flt[1], duv = flt[2];
                int du = prices[1 - i&1][u], dv = prices[i&1][v];  // 为什么用dv = prices[i&1][v]而不是prices[1 - i&1][u]来比较？
                if (du == INT_MAX) continue;
                if (du + duv < dv) {
                    prices[i&1][v] = du + duv;
                }
            }
        }
        return prices[k&1][dst] == INT_MAX ? -1 : prices[k&1][dst];  // 相邻两轮迭代结果不同步，为什么不需要取min
    }
};
Runtime: 28 ms, faster than 50.16% of C++ online submissions for Cheapest Flights Within K Stops.
Memory Usage: 12.5 MB, less than 49.01% of C++ online submissions for Cheapest Flights Within K Stops.
```

复杂度O（kE）

## BFS queue
- prices 二维 v，k
- 把src压入queue，后续每轮循环弹出queue元素，将更新值的后继结点压入queue，stops数+1
- 终止条件是queue空或者k stops

```C++
class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        if (k < 0) return -1;
        
        vector<vector<int>> adja;
        vector<int> temp1(n, INT_MAX);
        for (int i = 0; i < n; ++i) {
            adja.push_back(temp1);
        }
        for (auto & flight : flights) {
            adja[flight[0]][flight[1]] = flight[2];
        }
        if (k == 0) return adja[src][dst] == INT_MAX ? -1 : adja[src][dst];
        
        vector<vector<int>> prices;
        vector<int> price(n, INT_MAX);
        prices.push_back(price);
        prices.push_back(price);
        prices[0][src] = 0;
        prices[1][src] = 0;
        queue<int> que;
        que.push(src);
        int stops = 0;
        while (!que.empty() && stops < k + 1) {
            int len = que.size();
            for (int i = 0; i < len; ++i) {
                int top = que.front();
                que.pop();
                for (int j = 0; j < n; ++j) {
                    if (top == j) continue;
                    if (adja[top][j] == INT_MAX) continue;
                    int du = prices[1 - stops&1][top], dv = prices[stops&1][j];
                    if (du == INT_MAX) continue;
                    int wUV = adja[top][j];
                    if (du + wUV < dv) {
                        prices[stops&1][j] = du + wUV;
                        que.push(j);
                    }
                }
            }
            stops++;
        }
        int res = min(prices[k&1][dst], prices[1 - k&1][dst]); // 相邻两轮迭代结果不同步，需要取min
        if (res == INT_MAX) return -1;
        return res;
    }
};
```
复杂度O（Ek）
