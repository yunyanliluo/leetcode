# 树上最长路径

## dfs
难点：

1. root点的选取：任意点，于是取0

2. 多叉树，等于是个图，一次遍历避免走回头路：记录parent，来时的路

Tips：

1. build tree能封装独立函数就独立

2. 沿用的是二叉树最长路径的思想
```C++
class Solution {
public:
    /**
     * @param n: The number of nodes
     * @param starts: One point of the edge
     * @param ends: Another point of the edge
     * @param lens: The length of the edge
     * @return: Return the length of longest path on the tree.
     */
    unordered_map<int, unordered_map<int, int>> build_tree(vector<int> &starts, vector<int> &ends, vector<int> &lens) {
        unordered_map<int, unordered_map<int, int>> tree;
        int n = starts.size();
        if (ends.size() != n || lens.size() != n) return tree;
        for (int i = 0; i < n; ++i) {
            tree[starts[i]][ends[i]] = lens[i];
            tree[ends[i]][starts[i]] = lens[i];
        }
        return tree;
    }
    int helper(int root, int parent, unordered_map<int, unordered_map<int, int>>& tree, int &res) {
        int height = 0, second_height = 0;
        unordered_map<int, int> neighbors = tree[root];
        unordered_map<int, int>::iterator iter;
        for (iter = neighbors.begin(); iter != neighbors.end(); ++iter) {
            if (iter->first == parent) continue;
            int child_height = helper(iter->first, root, tree, res);
            int curr_height = child_height + iter->second;
            if (curr_height > height) {
                second_height = height;
                height = curr_height;
            } else if (curr_height > second_height) {
                second_height = curr_height;
            }
        }
        res = max(res, height + second_height);
        return height;
    }
    int longestPath(int n, vector<int> &starts, vector<int> &ends, vector<int> &lens) {
        unordered_map<int, unordered_map<int, int>> tree = build_tree(starts, ends, lens);
        int res;
        helper(0, -1, tree, res);
        return res;
    }
};
```
49.82MB空间消耗
·
2615 ms时间消耗
·
超过了14.36 %的提交


## bfs
trick

两次bfs

第一次从root（任意点）出发，找到最远点，作为start

第二次从start出发，找到最远点，作为end

则start到end是最长路径

直观理解：最长路径一定可以将一个端点作为root抖一下，然后另一个端点是最远点
