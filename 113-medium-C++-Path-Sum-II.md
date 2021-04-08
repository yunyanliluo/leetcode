# 递归

使用自己的递归模板

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void dfs(TreeNode* curr, int targetSum, int tempsum, vector<int>& tempres, vector<vector<int>>& res) {
        if (curr == NULL) return;
        int sum = tempsum + curr->val;
        tempres.push_back(curr->val);
        if (curr->left == NULL && curr->right == NULL) {
            if (sum == targetSum) {
                res.push_back(tempres);
            }
        }
        if (curr->left) dfs(curr->left, targetSum, sum, tempres, res);
        if (curr->right) dfs(curr->right, targetSum, sum, tempres, res);
        tempres.pop_back();
        return;
    }
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        vector<vector<int>> res;
        vector<int> tempres;
        int tempsum = 0;
        dfs(root, targetSum, tempsum, tempres, res);
        return res;
    }
};
```

执行用时：
8 ms
, 在所有 C++ 提交中击败了
87.75%
的用户

内存消耗：
19.3 MB
, 在所有 C++ 提交中击败了
75.24%
的用户
