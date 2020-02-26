# 打家劫舍III
一根二叉树，不能偷直接相连的房子，求能偷的最大和。

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
一个依然超出时间限制的更新

C++
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
        return dfs(root);
    }
    int dfs(TreeNode* &r) {
        if(r==NULL) return 0;
        int sum = r->val;
        if(r->left) sum += dfs(r->left->left)+dfs(r->left->right);
        if(r->right) sum += dfs(r->right->left)+dfs(r->right->right);
        return max(sum, dfs(r->left)+dfs(r->right));
    }
};
```

## 递归->dp

弄个hashmap存每个节点为根的子树的最大rob和

之后不需要重复计算，直接调用即可

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
        unordered_map<TreeNode*, int> dp;
        return dfs(root, dp);
    }
    int dfs(TreeNode* &r, unordered_map<TreeNode*, int>& dp) {
        if(r==NULL) return 0;
        if(dp.find(r)!=dp.end()) return dp[r];
        int sum = r->val;
        if(r->left) sum += dfs(r->left->left, dp)+dfs(r->left->right, dp);
        if(r->right) sum += dfs(r->right->left, dp)+dfs(r->right->right, dp);
        int sum2=dfs(r->left, dp)+dfs(r->right, dp);
        sum=max(sum, sum2);
        dp[r]=sum;
        return sum;
    }
};
```
执行用时 :
28 ms
, 在所有 C++ 提交中击败了
57.28%
的用户

内存消耗 :
27.4 MB
, 在所有 C++ 提交中击败了
25.71%
的用户

## 哈希表->变量存储
节省了哈希表的时间+空间开销

Oms的样例：（有dp的影子，跟上一个思路一个意思）

C++
```
class Solution {
public:
    void rob_task(TreeNode* root,int *val1,int *val2)
    {
        if(root == NULL){
            *val1 = 0;
            *val2 = 0;
            return;
        }
        int result1,result2,result3,result4;

        rob_task(root->right,&result1,&result2);
        rob_task(root->left,&result3,&result4);

        *val1 = root->val + result2+result4;
        *val2 = max(result1,result2) +  max(result3,result4);
    }
    int rob(TreeNode* root) {
        int res1,res2;
        rob_task(root,&res1,&res2);
        return max(res1,res2);
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
80.68%
的用户

内存消耗 :
24.7 MB
, 在所有 C++ 提交中击败了
29.99%
的用户
