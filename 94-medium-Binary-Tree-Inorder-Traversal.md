# 二叉树中序遍历

## 递归
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
    vector<int> inorderTraversal(TreeNode* root) {
        if(root!=NULL) {
            inorderTraversal(root->left);
            res.push_back(root->val);
            inorderTraversal(root->right);
        }
        return res;
    }
private:
    vector<int> res;
};
```
执行用时 :
0 ms
, 在所有 C++ 提交中击败了
100.00%
的用户

内存消耗 :
11.6 MB
, 在所有 C++ 提交中击败了
5.07%
的用户

## 迭代
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
    vector<int> inorderTraversal(TreeNode* root) {
        if(root==NULL) return res;
        stack<TreeNode*> stk;
        TreeNode* curr=root;
        while(curr!=NULL||!stk.empty()) {
            while(curr!=NULL) {
                stk.push(curr);
                curr=curr->left;
            }
            curr=stk.top();
            stk.pop();
            res.push_back(curr->val);
            curr=curr->right;
        }
        return res;
    }
private:
    vector<int> res;
};
```
执行用时 :
0 ms
, 在所有 C++ 提交中击败了
100.00%
的用户

内存消耗 :
9.5 MB
, 在所有 C++ 提交中击败了
15.97%
的用户

another C++ code
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
    vector<int> inorderTraversal(TreeNode* root) {
        stack<TreeNode*> stk;
        TreeNode* curr=root;
        while(curr||!stk.empty()) {
            while(curr&&curr->left) {
                stk.push(curr);
                curr=curr->left;
            }
            if(curr==NULL) {
                curr=stk.top();
                stk.pop();
            }
            res.push_back(curr->val);
            curr=curr->right;
        }
        return res;
    }
private:
    vector<int> res;
};
```
执行用时 :
4 ms
, 在所有 C++ 提交中击败了
72.67%
的用户

内存消耗 :
9.6 MB
, 在所有 C++ 提交中击败了
14.09%
的用户
