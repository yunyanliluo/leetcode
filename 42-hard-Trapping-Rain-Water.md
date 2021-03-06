# 接雨水

https://leetcode-cn.com/problems/trapping-rain-water/

## 第三次做，觉得dp最合理
但是不会做，学了dp，双指针和栈的做法

双指针

badcases:

1. [2,0,2] 左右两次，将左右等高的容水量计算重复

更新在双指针C++解法那里

## 第二次做这道题，第一反应还是用栈，这一次成功做出来了
单调栈

一些bad case：

- 输入：
[6,4,2,0,3,2,0,3,1,4,5,3,2,7,5,3,0,1,2,1,3,4,6,8,1,3]

输出：
77

预期：
83

```
class Solution {
public:
    int trap(vector<int>& height) {
        stack<int> hts;
        unordered_map<int, int> counts;
        int res = 0;
        for(int ht: height) {
            if (hts.empty() || hts.top() > ht) {
                hts.push(ht);
            } else if (hts.top() == ht){
                if (counts.find(ht) != counts.end()) counts[ht]++;
                else counts[ht] = 2;
            } else {
                int num = 0;
                int temp = 0;
                int sum = 0;
                int last = 0;
                while (!hts.empty() && hts.top() < ht) {
                    int cnt = 1;
                    if (counts.find(hts.top()) != counts.end())
                        cnt = counts[hts.top()];
                    num += cnt;
                    temp += hts.top() * cnt;
                    last = hts.top();
                    if (counts.find(hts.top()) != counts.end())
                        counts.erase(hts.top());
                    hts.pop();
                }
                if (hts.empty()) {
                    sum = last * num - temp;
                    hts.push(ht);
                } else {
                    sum = ht * num - temp;
                    // 以下四行是fix的地方.因为永远是单调栈，所以corner case在于新的插入数和栈顶相等。
                    if (counts.find(ht) != counts.end())  counts[ht] += (num + 1);
                    else counts[ht] = num + 1;
                    if (hts.top() != ht) hts.push(ht);
                    else if (counts[ht] == num + 1)  counts[ht]++;
                }
                res += sum;
            }
        }
        return res;
    }
};
```
执行用时：
8 ms
, 在所有 C++ 提交中击败了
80.92%
的用户

内存消耗：
14.5 MB
, 在所有 C++ 提交中击败了
15.09%
的用户


## Dp

每一列装水的高度，取决于左右两侧最高高度

两个数组leftTallest，rightTallest

C++ code
```
class Solution {
public:
    int trap(vector<int>& height) {
        int n=height.size();
        if(n<=2) return 0;
        int sum=0;
        vector<int> leftTallest(n), rightTallest(n);
        leftTallest[1]=0;
        int i;
        for(i=2;i<n;++i) {
            leftTallest[i]=((height[leftTallest[i-1]]>height[i-1])?leftTallest[i-1]:(i-1));
        }
        rightTallest[n-2]=n-1;
        for(i=n-3;i>=0;--i) {
            rightTallest[i]=(height[rightTallest[i+1]]>height[i+1])?rightTallest[i+1]:(i+1);
        }
        for(i=1;i<n-1;++i) {
            if(height[leftTallest[i]]>height[i]&&height[rightTallest[i]]>height[i]) {
                int h=min(height[leftTallest[i]], height[rightTallest[i]]);
                sum+=(h-height[i]);
            }
        }
        return sum;
    }
};
```
执行用时 :
32 ms
, 在所有 C++ 提交中击败了
12.96%
的用户

内存消耗 :
9.3 MB
, 在所有 C++ 提交中击败了
17.00%
的用户

时间On，空间On

但是依然不具有时间空间竞争力

那是因为代码写的不好

做两点优化

1.Left，right数组修改为仅留一个，另一个数组用变量替代

2.left数组不存下标，存height值，减少访存

C++ code
```
class Solution {
public:
    int trap(vector<int>& height) {
        int n=height.size();
        if(n<=2) return 0;
        int sum=0;
        vector<int> leftTallest(n);
        leftTallest[1]=height[0];
        int i;
        for(i=2;i<n;++i) {
            leftTallest[i]=((leftTallest[i-1]>height[i-1])?leftTallest[i-1]:height[i-1]);
        }
        int rightTallest=0;
        for(i=n-2;i>0;--i) {
            rightTallest=max(rightTallest, height[i+1]);
            int h=min(leftTallest[i], rightTallest);
            if(h>height[i]) {
                sum+=(h-height[i]);
            }
        }
        return sum;
    }
};
```
执行用时 :
8 ms
, 在所有 C++ 提交中击败了
70.27%
的用户

内存消耗 :
9.2 MB
, 在所有 C++ 提交中击败了
30.23%
的用户

## 双指针法（trick）

从边界向中间，左右各一个指针前进，

每一次挪哪个指针，取决于height[left - 1]，height[right + 1]哪个小，

就更新哪边的一列面积，

然后指针移动1.（保证了，两边把矮的淘汰，留下高的和一个未知的比较高度）。

已知max_left和max_right是由height[left - 1]，height[right + 1]更新得到的，

因此两边哪个高，就意味着哪个max大。

参考：https://leetcode-cn.com/problems/trapping-rain-water/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8/

```C++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        if (n < 3) return 0;
        int sum = 0;
        int maxleft = height[0], maxright = height[n - 1];
        int i = 0, j = n - 1;
        while (i < j) {
            if (maxleft <= maxright) {
                int start = i;
                ++i;
                int adds = 0;
                while (i < j && height[i] < maxleft) {
                    adds += height[i];
                    ++i;
                }
                sum += maxleft * (i - start - 1) - adds;
                maxleft = height[i];
            } else {
                int start = j;
                --j;
                int adds = 0;
                while (i < j && height[j] < maxright) {
                    adds += height[j];
                    --j;
                }
                sum += maxright * (start - j - 1) - adds;
                maxright = height[j];
            }
        }
        return sum;
    }
};
```
执行用时：
4 ms
, 在所有 C++ 提交中击败了
93.96%
的用户

内存消耗：
13.7 MB
, 在所有 C++ 提交中击败了
86.45%
的用户


Java code
```Java
public int trap(int[] height) {
    int sum = 0;
    int max_left = 0;
    int max_right = 0;
    int left = 1;
    int right = height.length - 2; // 加右指针进去
    for (int i = 1; i < height.length - 1; i++) {
        //从左到右更
        if (height[left - 1] < height[right + 1]) {
            max_left = Math.max(max_left, height[left - 1]);
            int min = max_left;
            if (min > height[left]) {
                sum = sum + (min - height[left]);
            }
            left++;
        //从右到左更
        } else {
            max_right = Math.max(max_right, height[right + 1]);
            int min = max_right;
            if (min > height[right]) {
                sum = sum + (min - height[right]);
            }
            right--;
        }
    }
    return sum;
}

//作者：windliang
//链接：https://leetcode-cn.com/problems/trapping-rain-water/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8/
//来源：力扣（LeetCode）
//著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

On，O1

## 栈

这道题我最初的想法就是使用栈，但是没想清楚这个逻辑

参考链接：https://leetcode-cn.com/problems/trapping-rain-water/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8/

从左往右遍历，如果当前比栈顶矮，入栈，

如果高，那么栈顶出栈，

如果栈空，那么当前压栈，然后continue遍历，

否则计算弹出的元素一列的面积，使用新栈顶元素和当前元素做左右墙，并计算墙间距，以min做墙高，以弹出元素做基础高度与之相减，得到面积，

假如剩下新栈顶面积还小于当前元素，那么继续上面的弹栈和计算面积，

不小于当前元素了，就当前压栈，继续遍历

C++ code
```
class Solution {
public:
    int trap(vector<int>& height) {
        int n=height.size();
        if(n<=2) return 0;
        int sum=0;
        stack<int> stk;
        int curr=0;
        while(curr<n) {
            while(!stk.empty()&&height[curr]>height[stk.top()]) {
                int h=height[stk.top()];
                stk.pop();
                if(!stk.empty()) {
                    int minH=min(height[curr], height[stk.top()]);
                    int width=curr-stk.top()-1;
                    sum+=width*(minH-h);cout<<sum<<" ";
                }
            }
            stk.push(curr++);
        }
        return sum;
    }
};
```
执行用时 :
24 ms
, 在所有 C++ 提交中击败了
13.40%
的用户

内存消耗 :
9.7 MB
, 在所有 C++ 提交中击败了
5.05%
的用户

On, On
