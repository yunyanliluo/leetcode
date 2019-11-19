# *Binary Tree Level Order Traversal*
二叉树的层次遍历

## my answer

### queue*2+迭代
C++
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
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(root==NULL) return res;
        vector<int> tempres;
        queue<TreeNode*> q1, q2;
        q1.push(root);
        while(!q1.empty()||!q2.empty())
        {
            tempres.clear();
            while(!q1.empty())
            {
                TreeNode* p=q1.front();
                q1.pop();
                tempres.push_back(p->val);
                if(p->left!=NULL) q2.push(p->left);
                if(p->right!=NULL) q2.push(p->right);
            }
            if(!tempres.empty()) res.push_back(tempres);
            tempres.clear();
            while(!q2.empty())
            {
                TreeNode* p=q2.front();
                q2.pop();
                tempres.push_back(p->val);
                if(p->left!=NULL) q1.push(p->left);
                if(p->right!=NULL) q1.push(p->right);
            }
            if(!tempres.empty()) res.push_back(tempres);
        }
        return res;
    }
};
```
执行用时 :4 ms, 在所有 cpp 提交中击败了96.06%的用户

内存消耗 :13.9 MB, 在所有 cpp 提交中击败了50.53%的用户
## solutions

### DFS+递归
原文链接：https://leetcode-cn.com/problems/binary-tree-level-order-traversal/solution/er-cha-shu-de-ceng-ci-bian-li-by-leetcode/
Python
```Python
class Solution:
    def levelOrder(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        levels = []
        if not root:
            return levels
        
        def helper(node, level):
            # start the current level
            if len(levels) == level:
                levels.append([])

            # append the current node value
            levels[level].append(node.val)

            # process child nodes for the next level
            if node.left:
                helper(node.left, level + 1)
            if node.right:
                helper(node.right, level + 1)
            
        helper(root, 0)
        return levels
```
空间更少
