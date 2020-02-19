# 二叉树中的最大路径和
给定一个非空二叉树，返回其最大路径和。

本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径至少包含一个节点，且不一定经过根节点。

## 递归
参考：https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/solution/er-cha-shu-zhong-de-zui-da-lu-jing-he-by-ikaruga/

对于A（父）-B（左）-C（右）

考虑一个子树必须经过根的路径，只有两种可能

1.最长路径是B-A-C

2.最长路径是B/C-A-A的父亲

（不考虑不经过A的情况，假如不经过A，我们把它放到以B/C为根的子树中去考虑）

对于1.更新最长路径（对应lmr）

对于2.需要递归，通过返回值传递上去（对应ret）

使用一个引用变量val更新最长路径长度

使用max（0，x）来包含路径中不选择x的情况

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
    int maxPathSum(TreeNode* root) {
        int val=root->val;
        maxPathSum(root, val);
        return val;
    }
    int maxPathSum(TreeNode* root, int& val) {
        if(root==NULL) return 0;
        int left=maxPathSum(root->left, val);
        int right=maxPathSum(root->right, val);
        int lmr=root->val+max(0, left)+max(0, right);
        int ret=root->val+max(0, max(left, right));
        val=max(val, lmr); //lmr一定>=ret。这里更新最长路径。
        return ret;
    }
};
```
执行用时 :
36 ms
, 在所有 C++ 提交中击败了
55.49%
的用户

内存消耗 :
25.3 MB
, 在所有 C++ 提交中击败了
25.40%
的用户

时间On，空间Ologn
