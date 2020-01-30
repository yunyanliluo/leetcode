# 合并k个有序列表

## 分治 归并排序
每次取两个链表，合并成一个。在这里使用queue。合并好一个链表，插入队列尾部。当queue.size（）==1时停止。

链表只需修改指针，空间O（1）

注意：vector转队列需要先转双端队列。或者for循环push。

C++ code
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        int n=lists.size();
        if(n==0) return NULL;
        if(n==1) return lists[0];
        queue<ListNode*> q(deque<ListNode*>(lists.begin(), lists.end())); //注意：vector转队列需要先转双端队列。或者for循环push。
        while(q.size()>1) {
            ListNode* l1=q.front();
            q.pop();
            ListNode* l2=q.front();
            q.pop();
            q.push(merge(l1, l2)); //在这里，merge的结果不存，效果更好。
        }
        return q.front();
    }
    ListNode* merge(ListNode* l1, ListNode* l2) {
        ListNode* head=new ListNode(0);
        ListNode* p=head;
        while(l1&&l2) {
            if(l1->val<l2->val) {
                p->next=l1;
                p=p->next;
                l1=l1->next;
            }
            else {
                p->next=l2;
                p=p->next;
                l2=l2->next;
            }
        }
        p->next=l1?l1:l2;cout<<endl;
        return head->next;
    }
};
```
执行用时 :
24 ms
, 在所有 C++ 提交中击败了
94.30%
的用户

内存消耗 :
12.1 MB
, 在所有 C++ 提交中击败了
17.08%
的用户

复杂度分析

作者：LeetCode

链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/he-bing-kge-pai-xu-lian-biao-by-leetcode/

时间复杂度： 
O(Nlogk) ，其中 
k 是链表的数目。

我们可以在 
O(n) 的时间内合并两个有序链表，其中 
n 是两个链表中的总节点数。
将所有的合并进程加起来，我们可以得到：
O(∑ 
i=1
log 
2
​	
 k
​	
 N)=O(Nlogk) 。
 
空间复杂度：
O(1)

我们可以用 
O(1) 的空间实现两个有序链表的合并。

相比于第一次合并第1，2个，第二次合并第1，3个……时间更快（O（kN））
