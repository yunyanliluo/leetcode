# Sort List
Sort a linked list in O(n log n) time using constant space complexity.

Example 1:

Input: 4->2->1->3

Output: 1->2->3->4

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/sort-list

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 使用归并排序的方法 + 递归
本方法依据精选题解：https://leetcode-cn.com/problems/sort-list/solution/sort-list-gui-bing-pai-xu-lian-biao-by-jyd/

归并排序处理链表，只更改指针，不生成数组，可以达到O（1）空间复杂度，但注意不能使用递归，因为递归则使用O（logn）栈空间。
这里使用递归，不满足题意，但是可以解决问题，达到时间复杂度要求。

思路说明：这里复制题解的内容，题解讲得太清楚了

题目要求时间空间复杂度分别为
O
(
n
l
o
g
n
)
O(nlogn)和
O
(
1
)
O(1)，根据时间复杂度我们自然想到二分法，从而联想到归并排序；

对数组做归并排序的空间复杂度为 
O
(
n
)
O(n)，分别由新开辟数组
O
(
n
)
O(n)和递归函数调用
O
(
l
o
g
n
)
O(logn)组成，而根据链表特性：

数组额外空间：链表可以通过修改引用来更改节点顺序，无需像数组一样开辟额外空间；
递归额外空间：递归调用函数将带来
O
(
l
o
g
n
)
O(logn)的空间复杂度，因此若希望达到
O
(
1
)
O(1)空间复杂度，则不能使用递归。
通过递归实现链表归并排序，有以下两个环节：

- 分割 cut 环节： 找到当前链表中点，并从中点将链表断开（以便在下次递归 cut 时，链表片段拥有正确边界）；
-- 我们使用 fast,slow 快慢双指针法，奇数个节点找到中点，偶数个节点找到中心左边的节点。
-- 找到中点 slow 后，执行 slow.next = None 将链表切断。
-- 递归分割时，输入当前链表左端点 head 和中心节点 slow 的下一个节点 tmp(因为链表是从 slow 切断的)。
-- cut 递归终止条件： 当head.next == None时，说明只有一个节点了，直接返回此节点。
- 合并 merge 环节： 将两个排序链表合并，转化为一个排序链表。
-- 双指针法合并，建立辅助ListNode h 作为头部。
-- 设置两指针 left, right 分别指向两链表头部，比较两指针处节点值大小，由小到大加入合并链表头部，指针交替前进，直至添加完两个链表。
-- 返回辅助ListNode h 作为头部的下个节点 h.next。
-- 时间复杂度 O(l + r)，l, r 分别代表两个链表长度。
- 当题目输入的 head == None 时，直接返回None。

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
    ListNode* sortList(ListNode* head) {
        if(head==NULL||head->next==NULL) return head;
        ListNode *fast=head->next, *slow=head; //最开始不AC的原因是fast设置成了head，偶数长度时mid取的过于靠后了,例如在cut到链表长度为2时，如果fast, slow都指向头部，那么fast会走两步，slow走一步，cut点是不对的。 如果fast先走一步，就会避免长度为2时出现的特殊错误情况
        while(fast&&fast->next) {
            fast=fast->next->next;
            slow=slow->next;
        }
        ListNode* mid=slow->next;
        slow->next=NULL;
        ListNode* h1=sortList(head), *h2=sortList(mid);
        ListNode* res=(ListNode*)malloc(sizeof(ListNode));
        ListNode* h=res;
        while(h1&&h2) {
            if(h1->val<h2->val) {
                h->next=h1;
                h1=h1->next;
                h=h->next;
                h->next=NULL;
            }
            else {
                h->next=h2;
                h2=h2->next;
                h=h->next;
                h->next=NULL;
            }
        }
        h->next=h1?h1:h2;
        return res->next;
    }
};
```
执行用时 :32 ms, 在所有 C++ 提交中击败了98.96%的用户

内存消耗 :24.9 MB, 在所有 C++ 提交中击败了10.39%的用户

## 使用归并排序 + 自底向上直接合并

仅根据intv计算每个单元边界，并完成链表的每轮排序合并，例如:
- 当intv = 1时，将链表第1和第2节点排序合并，第3和第4节点排序合并，……。
- 当intv = 2时，将链表第1-2和第3-4节点排序合并，第5-6和第7-8节点排序合并，……。
- 当intv = 4时，将链表第1-4和第5-8节点排序合并，第9-12和第13-16节点排序合并，……。

此方法时间复杂度
O
(
n
l
o
g
n
)
O(nlogn)，空间复杂度
O
(
1
)
O(1)。

思路（题解提供）：

模拟上述的多轮排序合并：
- 统计链表长度length，用于通过判断intv < length判定是否完成排序；
- 额外声明一个节点res，作为头部后面接整个链表，用于：
-- intv *= 2即切换到下一轮合并时，可通过res.next找到链表头部h；
-- 执行排序合并时，需要一个辅助节点作为头部，而res则作为链表头部排序合并时的这个辅助头部pre；后面的合并排序可以将上次合并排序的尾部用做辅助节点pre的更新。
- 在每轮intv下的合并流程：
1.根据intv找到合并单元1和单元2的头部h1, h2。由于链表长度可能不是2^n，需要考虑边界条件：
-- 在找h2过程中，如果链表剩余元素个数少于intv，则无需合并环节，直接break，执行下一轮合并；
-- 若h2存在，但以h2为头部的剩余元素个数少于intv，也执行合并环节，h2单元的长度为c2 = intv - i。
2.合并长度为c1, c2的h1, h2链表，其中：
-- 合并完后，需要修改新的合并单元的尾部pre指针指向下一个合并单元头部h。（在寻找h1, h2环节中，h指针已经被移动到下一个单元头部）
-- 合并单元尾部同时也作为下次合并的辅助头部pre。
3.当h == None，代表此轮intv合并完成，跳出。
- 每轮合并完成后将单元长度×2，切换到下轮合并：intv *= 2。


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
    ListNode* sortList(ListNode* head) {
        ListNode* h=head;
        int length=0;
        while(h) {
            ++length;
            h=h->next;
        }
        ListNode* res=(ListNode*)malloc(sizeof(ListNode));
        res->next=head;
        int intv=1;
        while(intv<length) {
            h=res->next; //这里注意h不能写为head，因为head位置随着首节点改变，写res->next才一定是链表第一个节点
            ListNode* pre=res;
            while(h) {
                ListNode* h1=h, *h2; //同理，这里h1=h不能写成h1=head
                int i=intv;
                while(h&&i) {
                    h=h->next;
                    --i;
                }
                if(i) break;
                h2=h;
                i=intv;
                while(h&&i) {
                    h=h->next;
                    --i;
                }
                int c1=intv, c2=intv-i;
                /*merge h1, h2*/
                while(c1&&c2) {
                    if(h1->val<h2->val) {
                        pre->next=h1;
                        h1=h1->next;
                        pre=pre->next;
                        --c1;
                    }
                    else {
                        pre->next=h2;
                        h2=h2->next;
                        pre=pre->next;
                        --c2;
                    }
                }
                pre->next=c1?h1:h2; //这里注意c1！=0（而不是h1！=NULL）则赋值h1，否则h2
                while(c1>0||c2>0) {
                    pre=pre->next;
                    --c1;--c2;
                }
                pre->next=h; //是因为下一次可能在h长度小于intv的情况下break，先把h连在pre后面
            }
            intv*=2;
        }
        return res->next;
    }
};
```

执行用时 :28 ms, 在所有 C++ 提交中击败了99.77%的用户

内存消耗 :11.6 MB, 在所有 C++ 提交中击败了80.98%的用户
