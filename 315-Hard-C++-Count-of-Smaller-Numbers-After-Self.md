# solution

## BST
nlogn

逆序遍历，所至之处有序，插入和查找logn

用法：https://blog.csdn.net/jxhaha/article/details/70256642

```
class Node{  
public:
    int n;
    int smallCount;
    int afterCount;
    Node* lChild;
    Node* rChild;
    Node(int num,int c, Node* l, Node* r){
        n =num;
        lChild=l;
        rChild=r;
        smallCount=c; // 表示当前元素对应的smallCount
        afterCount=0; // 表示当前元素的左子树节点数目
    }
    
};

class Solution {
public:
    vector<int> countSmaller(vector<int>& nums) {
        
        //sort(nums);
        vector<int> res(nums);
        Node* root=NULL;
        for(int i=nums.size()-1;i>-1;i--){
            int c=0;
            Node* currNode=NULL;
            if(root==NULL){
                root=new Node(nums[i],c,NULL,NULL);
            }
            else{
                currNode=root;
                while(currNode!=NULL){
                    
                    if (nums[i]<=currNode->n){  // 小于等于：往左
                       currNode->afterCount++;  // 左子树节点数目增1
                        if(currNode->lChild==NULL){
                            currNode->lChild=new Node(nums[i],c,NULL,NULL);
                            currNode=currNode->lChild;  // 以便currNode置空，while停止
                        }
                        currNode=currNode->lChild;
                    
                    }
                    else{  // 大于：往右
                        c+=(currNode->afterCount+1); // currNode左子树数目+1，因为currNode及其左子树都小于当前nums[i]
                        
                        if(currNode->rChild==NULL){
                            //c+=currNode->smallCount;
                            currNode->rChild=new Node(nums[i],c,NULL,NULL);
                            currNode=currNode->rChild;  // 以便currNode置空，while停止
                        }
                        currNode=currNode->rChild;
                        
                    }
                    
                }
                
            }
           
            res[i]=c;
        }
        //res.push_back(0);
        return res;
    }
}; 
```

## 线段树/树状数组
TODO


## 正序遍历
n^2，TLE（太hard了）
复制一个数组进行sort，顺序遍历原始数组，每遍历一个位置，在有序数组里查找下标，然后remove掉

n^2的原因是vector的find和erase都是顺序遍历On（查看底层实现（CPLUSPLUS.com））

注意 vector的删除元素方式：erase（迭代器），remove（值）（参考：http://c.biancheng.net/view/6846.html）

```
class Solution {
public:
    vector<int> countSmaller(vector<int>& nums) {
        vector<int> sortednums(nums), res;
        sort(sortednums.begin(),sortednums.end());
        for (int num: nums) {
            vector<int>::iterator iter = find(sortednums.begin(), sortednums.end(), num);
            res.push_back(iter-sortednums.begin());
            sortednums.erase(iter);
        }
        return res;
    }
};
```

由于试图挑战时间复杂度，试图改成哈希表，让查找和删除变成O1，但没成功，尽力了
```
class Solution {
public:
    vector<int> countSmaller(vector<int>& nums) {
        vector<int> sortednums(nums), res, erasednums;
        sort(sortednums.begin(),sortednums.end());
        unordered_map<int, vector<int>> umap;
        for (int index = 0; index < nums.size(); ++ index ) {
            int num = sortednums[index];
            umap[num].push_back(index);
        }
        for (int num: nums) {
            int index = umap[num][0], ind = index;
            for (int ernum: erasednums) {
                if (ernum < index)  --ind;
            }
            vector<int>::iterator iter = sortednums.begin() + index;
            res.push_back(ind);
            erasednums.push_back(index);
            umap[num].erase(umap[num].begin());
        }
        return res;
    }
};
```

## 逆序插入法
逆序遍历数组，每遍历（On）到一个位置，让遍历过的临时数组有序，二分查找（Onlogn）找到插入位置，然后插入（On），这样时间负责度是n^2

太麻烦了，咩呀做
