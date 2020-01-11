# 114. Flatten Binary Tree to Linked List
Given a binary tree, flatten it to a linked list in-place.

## 方法一

递归：flatten左子树，flatten右子树，把左子树挂到右子树，把右子树挂到左子树尽头

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
    void flatten(TreeNode* root) {
        if(root==NULL) return;
        flatten(root->left);
        flatten(root->right);
        if(root->left) 
        {
            TreeNode* q=root->right;
            root->right=root->left;
            root->left=NULL;
            TreeNode* p=root->right;
            while(p->right) p=p->right;
            p->right=q;
        }
        return;
    }
};
```
执行用时 :12 ms, 在所有 C++ 提交中击败了38.14%的用户

内存消耗 :9.9 MB, 在所有 C++ 提交中击败了74.81%的用户


## 方法二：参考了官方题解方法一
把左子树接到右边，右子树接到左子树最右，保证了root没有左子树了，然后递归处理root的右子树

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
    void flatten(TreeNode* root) {
        if(root==NULL) return;
        if(root->left)
        {
            TreeNode* r=root->right;
            root->right=root->left;
            root->left=NULL;
            TreeNode* p=root->right;
            while(p->right) p=p->right;
            p->right=r;
        }
        flatten(root->right);
    }
};
```
执行用时 :0 ms, 在所有 C++ 提交中击败了100.00%的用户

内存消耗 :9.9 MB, 在所有 C++ 提交中击败了75.00%的用户
