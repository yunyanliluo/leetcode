# 环形链表II

找到一个链表中环的起始位置，返回这个节点指针。无环返回NULL。

# 哈希集合
遍历节点，放入哈希集合。当哈希集合已经拥有某一节点时，这一节点就是环形入口。

时间O（n），空间O（n）。

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
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> st;
        ListNode* p=head;
        while(p!=NULL) {
            if(st.find(p)!=st.end()) return p;
            st.insert(p);
            p=p->next;
        }
        return (ListNode *)NULL;
    }
};
```
执行用时 :
12 ms
, 在所有 C++ 提交中击败了
84.58%
的用户

内存消耗 :
12.2 MB
, 在所有 C++ 提交中击败了
5.11%
的用户

## 快慢指针
一个环形链表，快慢指针必在环上相遇，而且第一次相遇必定有快指针比慢指针多走了环的周长C。

假设从head经f步走到环形入口，那么第一次相遇时，假设慢指针走了f+a，则慢指针走了f+a+C。并且有2（f+a）=f+a+C。

解得a=C-f。

因此环的后半部分长度等于f（从a走到环形入口的长度）。

设计两个阶段：

1.快慢指针相遇，相遇于a。

2.ptr1位于head，ptr2位于a，两指针每次前进一步，直到相遇。必经过f步相遇，相遇于环形入口。

若第一阶段不能相遇，说明无环，返回NULL。

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
    ListNode *getIntersect(ListNode *head) {
        ListNode *tortoise=head, *hare=head;
        while(hare!=NULL&&hare->next!=NULL) {
            tortoise=tortoise->next;
            hare=hare->next->next;
            if(hare==tortoise) return hare;
        }
        return NULL;
    }
    ListNode *detectCycle(ListNode *head) {
        if(head==NULL) return head;
        ListNode *ptr1=head;
        ListNode *ptr2=getIntersect(head);
        if(ptr2==NULL) return NULL;
        while(ptr1!=ptr2) {
            ptr1=ptr1->next;
            ptr2=ptr2->next;
        }
        return ptr1;
    }
};
```
执行用时 :
8 ms
, 在所有 C++ 提交中击败了
97.51%
的用户

内存消耗 :
9.8 MB
, 在所有 C++ 提交中击败了
44.93%
的用户
