# 题目
限制k=1~sizelist

## 链表
分成两部分来考虑：

头/尾 + 中段

需要记录上一节的尾，连接上当前一节的头

当前一节的尾，先连接NULL

使用快慢指针判断是否需要翻转当前一节（是否还有k个节点）

next last用于当前节中段翻转

tail start用于上一节末尾连接当前一节开头

corner case：

1.最后一节不需翻转，连接上一节的尾：完整节，没有多余的不需翻转的节点 eg.[1,2,3,4] k=2

2.有需要翻转的，返回第一节的尾：没有需要翻转的，返回head

3.空链表/单个元素链表

4.k=1
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        //13:35
        ListNode *slow = head, *fast = head;
        ListNode *tail = NULL, *start = head;
        ListNode *res = head;
        ListNode *next, *last;
        while (fast != NULL) {
            int i;
            for (i = 0; i < k; ++i) {
                fast = fast->next;
                if (fast == NULL) break;
            }
            if (i < k - 1) break;

            start = slow;
            last = NULL;
            while (slow != fast) {
                next = slow->next;
                slow->next = last;
                last = slow;
                slow = next;
            }
            if (tail != NULL) tail->next = last;
            else res = last;
            tail = start;
        }
        if (tail != NULL) tail->next = slow;
        return res;
    }
};
```
