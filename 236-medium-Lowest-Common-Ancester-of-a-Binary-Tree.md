# 二叉树的公共祖先

## dfs+剪枝
参考：https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/solution/java-dfs-jian-zhi-9ms9244-by-lava-4/

我们使用DFS搜索每一个节点的左右子树：

1、若子树上存在p和q的公共节点，返回此公共节点

2、若不存在公共节点，但是存在p或q任意一个节点，返回此节点

3、若不存在公共、p、q节点，则返回null。

那么，有以下几个结论：

0、若当前节点为null、p、q之一，直接返回当前节点

1、若左子树上存在公共节点（返回值非p、q），则函数返回值为左子树返回值，不需再遍历右子树

2、若左子树返回null，则函数返回值为右子树返回值

3、若左子树、右子树返回值均为非null，则肯定为一个p，一个q，则公共节点为当前节点

4、最后一种情况，即左子树返回非null，右子树返回null，则函数返回值为左子树返回值

Java code
```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        //结论0
        if (root == null || root == p || root == q)
            return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);

        //结论1
        if (left != null && left != q && left != p)
            return left;
        TreeNode right = lowestCommonAncestor(root.right, p, q);

        //结论3
        if (left != null && right != null)
            return root;

        //结论2 4
        else
            return left == null ? right : left;
    }
}

作者：lava-4
链接：https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/solution/java-dfs-jian-zhi-9ms9244-by-lava-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root==NULL||root==p||root==q) return root;
        TreeNode* l=lowestCommonAncestor(root->left, p, q);
        if(l!=NULL&&l!=p&&l!=q) return l; //剪枝
        TreeNode* r=lowestCommonAncestor(root->right, p, q);
        if(l==NULL) return r;
        if(r==NULL) return l;
        return root;
    }
};
```
执行用时 :
24 ms
, 在所有 C++ 提交中击败了
61.71%
的用户

内存消耗 :
16.8 MB
, 在所有 C++ 提交中击败了
33.81%
的用户


## 递归dfs

注意到，

A/B最小公共祖先p满足，

p, p->left子树, p->right子树有两个返回true（找到一个节点A/B），

并且唯一。

用一个引用变量记录结果。

On，On(递归斜二叉树空间On).

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root==NULL) return NULL;
        if(root==p||root==q) return root;
        TreeNode* res=NULL;
        dfs(root, p, q, res);
        return res;
    }
    bool dfs(TreeNode*& r, TreeNode*& p, TreeNode*& q, TreeNode*& res) {
        if(r==NULL) return NULL;
        bool curr=(r==p||r==q)?1:0;
        bool left=dfs(r->left, p, q, res);
        bool right=dfs(r->right, p, q, res);
        int all=curr+left+right;
        if(all>=2) {
            res=r;
            return true;
        }
        if(all>=1) return true;
        return false;
    }
};
```
执行用时 :
24 ms
, 在所有 C++ 提交中击败了
61.71%
的用户

内存消耗 :
17 MB
, 在所有 C++ 提交中击败了
28.89%
的用户

## 递归
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
    int recursive(TreeNode* curr, TreeNode* p, TreeNode* q, TreeNode*& res) {
        if (curr == NULL) return 0;
        if (curr == p || curr == q) {
            if (recursive(curr->left, p, q, res) == 1 || recursive(curr->right, p, q, res) == 1) {
                res = curr;
                return 2;
            } else {
                return 1;
            }
        } else {
            int left = 0, right = 0;
            left = recursive(curr->left, p, q, res);
            right = recursive(curr->right, p, q, res);
            if (left == 1 && right == 1) {
                res = curr;
            }
            return left + right;
        }
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode* res = NULL;
        recursive(root, p, q, res);
        return res;
    }
};
```
执行用时：
16 ms
, 在所有 C++ 提交中击败了
89.92%
的用户

内存消耗：
14.1 MB
, 在所有 C++ 提交中击败了
30.55%
的用户

## 暴力

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root==NULL) return NULL;
        if(root==p||root==q) return root;
        if(containNode(root->left, p)&&containNode(root->left, q)) return lowestCommonAncestor(root->left, p, q);
        if(containNode(root->right, p)&&containNode(root->right, q)) return lowestCommonAncestor(root->right, p, q);
        return root;
    }
    bool containNode(TreeNode* r, TreeNode* p) {
        if(r==NULL) return false;
        if(r==p) return true;
        if(containNode(r->left, p)||containNode(r->right, p)) return true;
        return false;
    }
};
```
执行用时 :
2452 ms
, 在所有 C++ 提交中击败了
5.00%
的用户

内存消耗 :
17.5 MB
, 在所有 C++ 提交中击败了
24.32%
的用户
