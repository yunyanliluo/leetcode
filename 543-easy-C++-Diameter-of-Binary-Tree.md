# Diameter of Binary Tree
二叉树节点之间的最长距离
Given a binary tree, you need to compute the length of the diameter of the tree. The diameter of a binary tree is the length of the longest path between any two nodes in a tree. This path may or may not pass through the root.

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/diameter-of-binary-tree

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## my answer
### 递归调用。过根节点的最长路径，等于左子树的高度+右子树的高度。然后递归求过左右子树根节点的最长路径。
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
    int diameterOfBinaryTree(TreeNode* root) {
        if(root==NULL) return 0;
        int leftHeight=findMaxHeight(root->left)+1;
        int rightHeight=findMaxHeight(root->right)+1;
        maxLen=max(maxLen, leftHeight+rightHeight);
        if(leftHeight-rightHeight>2) diameterOfBinaryTree(root->left);
        else if(rightHeight-leftHeight>2) diameterOfBinaryTree(root->right);
        return maxLen;
    }
    int findMaxHeight(TreeNode* root)
    {
        if(root==NULL) return -1;
        return max(findMaxHeight(root->left), findMaxHeight(root->right))+1;
    }
private:
    int maxLen=0;
};
```
执行用时 :64 ms, 在所有 cpp 提交中击败了9.64%的用户

内存消耗 :20.1 MB, 在所有 cpp 提交中击败了66.18%的用户

时间复杂度太高，原因是diameterOfBinaryTree遍历和findMaxHeight遍历的重复，可以在findMaxHeight合并成为一次遍历。

## solutions
### improvement
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
    int diameterOfBinaryTree(TreeNode* root) {
        if(root==NULL) return 0;
        int leftHeight=findMaxHeight(root->left)+1;
        int rightHeight=findMaxHeight(root->right)+1;
        maxLen=max(maxLen, leftHeight+rightHeight);
        return maxLen;
    }
    int findMaxHeight(TreeNode* root)
    {
        if(root==NULL) return -1;
        int leftHeight=findMaxHeight(root->left)+1;
        int rightHeight=findMaxHeight(root->right)+1;
        maxLen=max(maxLen, leftHeight+rightHeight);
        return max(leftHeight, rightHeight);
    }
private:
    int maxLen=0;
};
```
可以将diameterOfBinaryTree一段简化，直接在root节点递归调用：
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
    int diameterOfBinaryTree(TreeNode* root) {
        if(root==NULL) return 0;
        findMaxHeight(root);
        return maxLen;
    }
    int findMaxHeight(TreeNode* root)
    {
        if(root==NULL) return -1;
        int leftHeight=findMaxHeight(root->left)+1;
        int rightHeight=findMaxHeight(root->right)+1;
        maxLen=max(maxLen, leftHeight+rightHeight);
        return max(leftHeight, rightHeight);
    }
private:
    int maxLen=0;
};
```
执行用时 :16 ms, 在所有 cpp 提交中击败了72.20%的用户
内存消耗 :19.7 MB, 在所有 cpp 提交中击败了83.82%的用户
