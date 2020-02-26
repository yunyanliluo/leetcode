# 打家劫舍III

## 递归
一个节点分两种情况考虑：
- 父亲robbed：则当前节点unrobbed，+左+右最优结果（递归调用）
- 父亲unrobbed：则当前节点可以robbed，+左+右最优，当前节点也可以unrobbed+左+右最优，取两种情况之中sum最大者
返回此sum，作为当前节点做根时返回的最大抢劫费用

目前通过123/124testcases，最后一个runtime error

C++ code
```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int rob(TreeNode* root) {
        if(root==NULL) return 0;
        return dfs(root, true);
    }
    int dfs(TreeNode* &r, bool fatherUnrobbed) {
        int sum=0, leftsum=0, rightsum=0;
        bool unrobbed=true;
        if(r->left) leftsum=dfs(r->left, unrobbed);
        if(r->right) rightsum=dfs(r->right, unrobbed);
        sum=leftsum+rightsum;
        if(fatherUnrobbed) {
            unrobbed=false;
            leftsum=(r->left)?dfs(r->left, unrobbed):0;
            rightsum=(r->right)?dfs(r->right, unrobbed):0;
            sum = max(sum, r->val+leftsum+rightsum);
        }
        return sum;
    }
};
```

        bool unrobbed
