# 前k个高频元素

## 哈希表+vector排序
这是我一开始的想法，

哈希map计数每个元素的次数，

然后把map复制到一个vector，根据次数排序vector，

输出前k。

时间O（nlogn），空间On。

## 哈希表+最小堆

哈希表计数次数不变，

使用priority_queue最小堆处理排序任务，具体而言，

将map一个一个加入容量k的最小堆，和堆顶元素比较，如果大于堆顶，则替换堆顶，最后最小堆剩下的即为最大k个

时间O（nlogk），最小堆可能将n个元素插入堆，每插入一个Ologk。

空间On。

C++ code
```
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        int n=nums.size();
        if(n==0||k==0) return {};
        static unordered_map<int, int> cnt; //struct里不能access非静态变量
        cnt.clear(); //static区域在退出程序时清空，因此有连续多个testcases时，每一个testcase的静态变量会互相影响，需要每次先清空。
        for(auto& num:nums) {
            ++cnt[num];
        }
        struct node { //用自定义类型做优先队列元素
            int key;
            node(int a) {key=a;}
            bool operator< (const node& a) const {
                return cnt[key]>cnt[a.key]; //小顶堆 小的在先 
            }
        };
        priority_queue<node> minheap;
        for(auto& item:cnt) {
            minheap.push(item.first); //pq的函数：push，pop
            if(minheap.size()>k) minheap.pop();
        }
        vector<int> res;
        while(!minheap.empty()) {
            int maxkey=minheap.top().key;
            res.push_back(maxkey);
            minheap.pop();
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```
执行用时 :
24 ms
, 在所有 C++ 提交中击败了
59.00%
的用户

内存消耗 :
15.8 MB
, 在所有 C++ 提交中击败了
5.02%
的用户

注意：
- struct里不能access非静态变量

- cnt.clear(); //static区域在退出程序时清空，因此有连续多个testcases时，每一个testcase的静态变量会互相影响，需要每次先清空。

上面用到的数据结构是优先队列，下面做一个用法介绍

### 优先队列
定义：priority_queue<Type, Container, Functional>
```
//升序队列
priority_queue <int,vector<int>,greater<int> > q;
//降序队列
priority_queue <int,vector<int>,less<int> >q;

//greater和less是std实现的两个仿函数（就是使一个类的使用看上去像一个函数。其实现就是类中实现一个operator()，这个类就有了类似函数的行为，就是一个仿函数类了）
```
运行结果：

4 3 2 1 0

0 1 2 3 4

cbd abcd abc


用自定义类型做优先队列元素的例子
```
#include <iostream>
#include <queue>
using namespace std;

//方法1
struct tmp1 //运算符重载<
{
    int x;
    tmp1(int a) {x = a;}
    bool operator<(const tmp1& a) const
    {
        return x < a.x; //大顶堆
    }
};

//方法2
struct tmp2 //重写仿函数
{
    bool operator() (tmp1 a, tmp1 b) 
    {
        return a.x < b.x; //大顶堆
    }
};

int main() 
{
    tmp1 a(1);
    tmp1 b(2);
    tmp1 c(3);
    priority_queue<tmp1> d;
    d.push(b);
    d.push(c);
    d.push(a);
    while (!d.empty()) 
    {
        cout << d.top().x << '\n';
        d.pop();
    }
    cout << endl;

    priority_queue<tmp1, vector<tmp1>, tmp2> f;
    f.push(b);
    f.push(c);
    f.push(a);
    while (!f.empty()) 
    {
        cout << f.top().x << '\n';
        f.pop();
    }
}
```
运行结果：

3

2

1
 
3

2

1


## 哈希表+桶排序

哈希表计数次数不变，

桶排序：使用vector，将map按照value也即次数当作vector下标的方式插入vector，也就剩下了排序的过程。

最后vector倒序输出，即按照频率较大优先输出。

注意，由于频率可能相等，于是vector装的是list，list是频率相同的元素。

vector不一定多大，设置n，

最后从末尾逆序，判断是否空list。

时间On，空间On。

C++ code
```
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        int n=nums.size();
        if(n==0||k==0) return {};
        unordered_map<int, int> cnt;
        for(auto& num:nums) {
            ++cnt[num];
        }
        vector<list<int>> l(n+1);
        for(auto& item:cnt) {
            int val=item.second;
            if(l[val].empty()) {
                l[val]=*(new list<int>);
            }
            l[val].push_back(item.first);
        }
        vector<int> res;
        for(int i=n;i>=0;--i) {
            if(l[i].empty()) continue;
            k-=l[i].size();
            res.insert(res.end(), l[i].begin(), l[i].end());
            if(k<=0) return res;
        }
        return res;
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
85.19%
的用户

内存消耗 :
17.8 MB
, 在所有 C++ 提交中击败了
5.08%
的用户
