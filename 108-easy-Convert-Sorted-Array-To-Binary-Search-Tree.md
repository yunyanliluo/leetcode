# 有序列表->高度平衡BST

高度平衡的含义：*任意*节点的左右子树高度差至多为1

## 分治法

执行用时：
40 ms
, 在所有 C++ 提交中击败了
16.03%
的用户

内存消耗：
24 MB
, 在所有 C++ 提交中击败了
15.27%
的用户

分治，递归函数

"这种区间分治类的题目，用左闭右闭就好，左闭右开不方便。

另外需要注意的是，求中点不要用 int mid = (l + r)/2，有溢出风险，稳妥的方法是 int mid = l + (r-l)/2。"

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
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return sortedArrayToBST(nums, 0, nums.size()-1);
    }
    TreeNode* sortedArrayToBST(vector<int>& nums, int l, int r) {
        if(l>r)  return NULL;
        int mid = l + (r-l)/2;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = sortedArrayToBST(nums, l, mid-1);
        root->right = sortedArrayToBST(nums, mid+1, r);
        return root;
    }
};
```
