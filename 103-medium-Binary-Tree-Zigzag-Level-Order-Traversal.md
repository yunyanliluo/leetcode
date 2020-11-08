# 二叉树的锯齿形遍历

执行用时：
0 ms
, 在所有 C++ 提交中击败了
100.00%
的用户

内存消耗：
11.5 MB
, 在所有 C++ 提交中击败了
9.52%
的用户

## 思路

层次遍历维护队列

z字形遍历意味着先存入result数组的，后存入他们的子孙

使用一个空节点（9999）来标记一层的遍历结束，顺便作为层与层之间的分隔节点

使用双向队列，每次取到9999时，反向一下，开始取下一层

每一个节点先存入temp，再压入队列

```C++
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
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (root == NULL)  return res;

        vector<int> temp;
        temp.push_back(root->val);
        res.push_back(temp);

        deque<TreeNode*> dq;
        dq.push_front(root);
        dq.push_front(new TreeNode(99999)); //  0 15 7
        bool popFrontBack = true;
        while(dq.size() > 1) {
            if (popFrontBack) {
                vector<int> temp;
                TreeNode* curr = dq.back();
                while(curr->val != 99999) {
                    dq.pop_back();
                    if(curr->right) {
                        temp.push_back(curr->right->val);
                        dq.push_front(curr->right);
                    } 
                    if(curr->left) {
                        temp.push_back(curr->left->val);
                        dq.push_front(curr->left);
                    }
                    curr = dq.back();
                }
                if (curr->val == 99999) {
                    if(!temp.empty())  res.push_back(temp);
                    popFrontBack = false;
                    continue;
                }
            } else {
                vector<int> temp;
                TreeNode* curr = dq.front();
                while(curr->val != 99999) {
                    dq.pop_front();
                    if(curr->left) {
                        temp.push_back(curr->left->val);
                        dq.push_back(curr->left);
                    }
                    if(curr->right) {
                        temp.push_back(curr->right->val);
                        dq.push_back(curr->right);
                    } 
                    curr = dq.front();
                }
                if (curr->val == 99999) {
                    if(!temp.empty())  res.push_back(temp);
                    popFrontBack = true;
                    continue;
                }
            }
        }
        return res;
    }
};
```
