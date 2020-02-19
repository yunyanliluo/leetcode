# 二叉树的序列化与反序列化

序列化：二叉树->字符串
反序列化：字符串->二叉树
谷歌2019冬令营OA

https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/

当时自己不会做，但是有了方法之后写起来并不难

## 队列（层次遍历）

序列化：节点入队 

null不入队仅更新序列

最后将序列尾部null依次去除

反序列化：每一次分析字符串中一项，依然是节点入队

永远有一个parent指针，和一个bool变量isLeft（左/右孩子）

字符串中非null的 建造节点并入队

连接上parent的左/右位置

每一次isLeft要更新，在右变左的时候要pop队列，更新parent

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
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        string data="[";
        queue<TreeNode*> q;
        if(root!=NULL) {
            q.push(root);
        } 
        while(!q.empty()) {
            while(!q.empty()&&q.front()==NULL) {
                q.pop();
                if(!q.empty()) data+=",null";
            }
            if(q.empty()) break;
            TreeNode* curr=q.front();
            q.pop();
            string currStr;
            if(curr!=root) currStr=","+to_string(curr->val);
            else currStr=to_string(curr->val);
            data+=currStr;
            q.push(curr->left);
            q.push(curr->right);
        }
        while(data[data.size()-2]=='l') data.erase(data.size()-5, 5);
        return (data+"]");
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        if(data=="[]") return NULL;
        TreeNode* root=NULL;
        int i=1;
        int temp;
        if(data[i]=='-') temp=0-data[++i]+'0';
        else temp=data[i]-'0';
        while(((++i)<data.size())&&(data[i]!=',')&&(data[i]!=']')) temp=temp*10+data[i]-'0';
        root=new TreeNode(temp);
        queue<TreeNode*> q;
        TreeNode* parent=root;
        bool isLeft=true;
        while(((++i)<data.size())&&(data[i]!=']')) {
            TreeNode* p;
            if(data.substr(i, 4)=="null") {
                p=NULL;
                i+=4;
            }
            else {
                if(data[i]=='-') temp=0-data[++i]+'0';
                else temp=data[i]-'0';
                while(((++i)<data.size())&&(data[i]!=',')&&(data[i]!=']')) temp=temp*10+data[i]-'0';
                p=new TreeNode(temp);
                q.push(p);
            }
            if(isLeft) {
                parent->left=p;
            }
            else {
                parent->right=p;
                parent=q.front();
                q.pop();
            }
            isLeft=(isLeft==false);
        }
        return root;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```
执行用时 :
32 ms
, 在所有 C++ 提交中击败了
93.33%
的用户

内存消耗 :
24.8 MB
, 在所有 C++ 提交中击败了
80.25%
的用户
