给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

https://leetcode-cn.com/problems/largest-rectangle-in-histogram/

很有趣，下面三种解法的用时一模一样。

## 栈解法

最简单版本

C++ code
```
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int maxArea=0;
        stack<int> stk;
        stk.push(-1);
        int i;
        for(i=0;i<heights.size();++i) {
            while(stk.top()!=-1&&heights[stk.top()]>=heights[i]) {
                int temp=stk.top();
                stk.pop();
                maxArea=max(maxArea, heights[temp]*(i-stk.top()-1));    
            }
            stk.push(i);
        }
        while(stk.top()!=-1) {
            int temp=stk.top();
            stk.pop();
            maxArea=max(maxArea, heights[temp]*(i-stk.top()-1));
        }
        return maxArea;
    }
};
```
执行用时 :
16 ms
, 在所有 C++ 提交中击败了
63.62%
的用户

内存消耗 :
10.5 MB
, 在所有 C++ 提交中击败了
44.51%
的用户

一个不写第二个while循环的方法，使代码略简洁：

在heights增加一个元素0，高度0一定低于前面非零所有，便自动处理了面积计算，

但是空间+O1，并且改变了heights数组


略复杂版本

C++ code
```
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int n=heights.size();
        if(n==0) return 0;
        int maxArea=0;
        stack<int> stk;
        int i=0;
        while(i<n) {
            if(stk.empty()||heights[stk.top()]<=heights[i]) {
                stk.push(i++);
            }
            else {
                int higherHeight=heights[stk.top()];
                stk.pop();
                int width=stk.empty()?i:(i-stk.top()-1);
                maxArea=max(maxArea, higherHeight*width);
            }
        }
        while(!stk.empty()) {
            int higherHeight=heights[stk.top()];
            stk.pop();
            int width=stk.empty()?i:(i-stk.top()-1);
            maxArea=max(maxArea, higherHeight*width);
        }
        return maxArea;
    }
};
```
执行用时 :
16 ms
, 在所有 C++ 提交中击败了
63.62%
的用户

内存消耗 :
10.7 MB
, 在所有 C++ 提交中击败了
25.52%
的用户


## 数组解法 
遍历三次

第一、二次记录向左、向右的比它矮的第一个位置，第三次计算面积

C++ code
```
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int n=heights.size();
        if(n==0) return 0;
        int maxArea=0;
        int leftLessMin[n];
        leftLessMin[0]=-1;
        for(int i=1;i<n;++i) {
            int temp=i-1;
            while(temp>=0&&heights[temp]>=heights[i]) temp=leftLessMin[temp];
            leftLessMin[i]=temp;
        }
        int rightLessMin[n];
        rightLessMin[n-1]=n;
        for(int i=n-2;i>=0;--i) {
            int temp=i+1;
            while(temp<n&&heights[temp]>=heights[i]) temp=rightLessMin[temp];
            rightLessMin[i]=temp;
        }
        for(int i=0;i<n;++i) {
            int area=(rightLessMin[i]-leftLessMin[i]-1)*heights[i];
            maxArea=max(maxArea, area);
        }
        return maxArea;
    }
};
```
执行用时 :
16 ms
, 在所有 C++ 提交中击败了
63.62%
的用户

内存消耗 :
10.4 MB
, 在所有 C++ 提交中击败了
62.98%
的用户
