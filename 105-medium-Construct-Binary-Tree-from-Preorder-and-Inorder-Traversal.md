# 从前序和中序遍历构造二叉树

## 递归
我开始的想法 也是最经典解法
C+
+code
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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if(preorder.empty()) return NULL;
        if(preorder.size()==1) return new TreeNode(preorder[0]);
        int rootval=preorder[0];
        TreeNode* root=new TreeNode(rootval);
        int i=0;
        while(inorder[i]!=rootval) ++i;
        vector<int> forepre(preorder.begin()+1, preorder.begin()+i+1);
        vector<int> backpre(preorder.begin()+i+1, preorder.end());
        vector<int> forein(inorder.begin(), inorder.begin()+i);
        vector<int> backin(inorder.begin()+i+1, inorder.end());
        root->left=buildTree(forepre, forein);
        root->right=buildTree(backpre, backin);
        return root;
    }
};
```
执行用时 :
52 ms
, 在所有 C++ 提交中击败了
24.18%
的用户

内存消耗 :
87.9 MB
, 在所有 C++ 提交中击败了
15.31%
的用户

## improvement - 使用hashmap存储中序遍历序列
避免了每次遍历寻找preorder[0]的过程

## 栈
这个解法不容易想到，但是思路可以欣赏一下。

C++ code
```
public TreeNode buildTree(int[] preorder, int[] inorder) {
    if (preorder.length == 0) {
        return null;
    }
    Stack<TreeNode> roots = new Stack<TreeNode>();
    int pre = 0;
    int in = 0;
    //先序遍历第一个值作为根节点
    TreeNode curRoot = new TreeNode(preorder[pre]);
    TreeNode root = curRoot;
    roots.push(curRoot);
    pre++;
    //遍历前序遍历的数组
    while (pre < preorder.length) {
        //出现了当前节点的值和中序遍历数组的值相等，寻找是谁的右子树
        if (curRoot.val == inorder[in]) {
            //每次进行出栈，实现倒着遍历
            while (!roots.isEmpty() && roots.peek().val == inorder[in]) {
                curRoot = roots.peek();
                roots.pop();
                in++;
            }
            //设为当前的右孩子
            curRoot.right = new TreeNode(preorder[pre]);
            //更新 curRoot
            curRoot = curRoot.right;
            roots.push(curRoot);
            pre++;
        } else {
            //否则的话就一直作为左子树
            curRoot.left = new TreeNode(preorder[pre]);
            curRoot = curRoot.left;
            roots.push(curRoot);
            pre++;
        }
    }
    return root;
}


作者：windliang
链接：https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--22/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
