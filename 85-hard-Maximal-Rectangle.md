# 最大矩形

找出给定二维数组的最大矩形面积

## DP

C++ code
```
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        int m=matrix.size();
        int n=m>0?matrix[0].size():0;
        if(m*n==0) return 0;
        int maxArea=0;
        int dp[n]={0}, traceX[n]={0}, traceY[n]={0};

        if(matrix[0][0]=='1') {
            dp[0]=1;
            traceX[0]=0;traceY[0]=0;
            maxArea=1;
        }
        else dp[0]=0;cout<<dp[0]<<" ";
        for(int j=1;j<n;++j) {
            if(matrix[0][j]=='1') {
                dp[j]=dp[j-1]+1;
                if(dp[j-1]) {
                    traceX[j]=traceX[j-1];traceY[j]=traceY[j-1];
                }
                else {traceX[j]=0;traceY[j]=j;}
                maxArea=max(maxArea, dp[j]);
            }
            else dp[j]=0;
            cout<<dp[j]<<" ";
        }cout<<endl;
        for(int i=1;i<m;++i) {
            if(matrix[i][0]=='1') {
                if(!dp[0]) {traceX[0]=i;traceY[0]=0;}
                dp[0]+=1;
                maxArea=max(maxArea, dp[0]);
            }
            else dp[0]=0;
            cout<<dp[0]<<" ";
            for(int j=1;j<n;++j) {
                if(matrix[i][j]=='1') {
                    if(dp[j]==0) {
                        traceX[j]=i;
                        if(dp[j-1]) traceY[j]=traceY[j-1];
                        else traceY[j]=j;
                        dp[j]=j-traceY[j]+1;
                    }
                    else if(dp[j-1]==0) {
                        traceY[j]=j;
                        if(!dp[j]) traceX[0]=i;
                        dp[j]=i-traceX[j]+1;
                    }
                    else {
                        int traceXTEMP1=traceX[j];
                        int traceYTEMP1=max(traceY[j], traceY[j-1]);
                        int dpTEMP1=(i-traceXTEMP1+1)*(j-traceYTEMP1+1);
                        int traceYTEMP2=traceY[j-1];
                        int traceXTEMP2=max(traceX[j], traceX[j-1]);
                        int dpTEMP2=(i-traceXTEMP2+1)*(j-traceYTEMP2+1);
                        dp[j]=max(dpTEMP1, dpTEMP2);
                        traceX[j]=(dp[j]==dpTEMP1)?traceXTEMP1:traceXTEMP2;
                        traceY[j]=(dp[j]==dpTEMP1)?traceYTEMP1:traceYTEMP2;
                    }
                    maxArea=max(maxArea, dp[j]);
                }
                else dp[j]=0;
                cout<<dp[j]<<" ";    
            }
            cout<<endl;
        }
        return maxArea;
    }
};
```
WA

执行结果：
解答错误

输入:

[
[“0","1","1","0","1","1","1","1","1","1","1","1","1","1","1","1","1","1","0"],
[“1","0","1","1","1","1","1","1","1","1","1","1","1","1","1","1","1","1","1"],
[“1","1","0","1","1","1","1","1","1","1","1","1","0","1","1","1","1","1","1"],
[“1","1","1","1","1","1","1","1","1","1","1","1","1","0","1","1","1","1","1"],
[“1”,”1","1","1","1","1","1","1","1","1","1",-40,"1","1","0","1","1","1","1"],
[“1","1","1","0","1","1","1","0","1","1","1","1","1","1","1","1","1","0","1"],
[“1","0","1","1","1","1","1","1","1","1","1","1","1","1","0","1","1","1","1"],
[“1","1","1","1","1","1","1","1","1","1","1","1","1","1","1","0","1","1","0"],
[“0","0","1","1","1","1","1","1","1","1","1","1","1","1","1","0","1","1","1"],
[“1","1","0","1","1","1","1","1","1","1","0","1","1","1","1","1","1","1","1"],
[“1”,"1","1","1","1","1","1","1",-30,"0","1","1","1","1","1","1","1","1","1"],
[“0","1","1","0","1","1","1","0","1","1","1","1","1","1","1","1","1","1","1"],
[“1","1","1","1","0","1","1","1","1","1","1","1","1","1","0","1","1","1","1"],
[“1","1","1","1","1","1","1","1","1","1","1","1","1","1","1","1","1","1","1"],
[“1”,”1”,"1","1","1","1","1","1","1","1","1","1","1","1","1","1","1","1",-1-],
[“1”,”1”,"1","1","1","1","1","1","1","1","1","1","1",-36,"1","1","1","0","1"],
[“1","1","1","1","1","1","1","1","0","1","1","0","1","1","0","1","1","1","1"],
[“1","1","1","1","1","1","0","1","1","1","1","1","1","1","1","0","1","1","1"]]

输出

40

stdout

0 1 2 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 0 
1 0 2 2 3 4 6 8 10 12 14 16 18 20 22 24 26 28 15 
2 2 0 2 4 6 9 12 15 18 21 24 0 3 6 9 12 15 12 
3 4 3 4 6 9 12 16 20 24 28 32 9 0 4 8 12 16 15 
4 6 6 8 10 12 16 20 25 30 35 40 18 10 0 5 10 15 16 
5 8 9 0 3 10 15 0 6 12 18 24 15 12 7 8 12 0 5 
6 0 4 2 4 12 18 4 7 14 21 28 20 18 0 2 14 3 6 
7 2 5 4 6 14 21 8 10 16 24 32 25 24 7 0 8 4 0 
0 0 6 6 9 16 24 12 15 18 27 36 30 30 14 0 9 6 3 
1 2 0 4 8 18 27 16 20 24 0 10 14 18 12 5 10 8 6 
2 4 3 5 10 20 30 20 25 0 1 11 16 21 16 10 12 14 16 
0 3 4 0 6 22 33 0 6 2 3 12 18 24 20 15 18 21 24 
1 4 6 3 0 12 24 3 7 4 6 13 20 27 0 4 8 12 16 
2 5 8 6 4 13 26 6 8 6 9 14 22 30 4 5 10 15 20 
3 6 10 9 8 14 28 9 12 15 18 21 24 33 8 10 12 18 24 
4 8 12 12 12 15 30 12 16 20 24 28 32 36 12 15 18 0 7 
5 10 15 20 20 24 32 15 0 5 10 0 5 26 0 4 8 3 8 
6 12 18 24 25 30 0 6 2 6 12 3 6 28 3 0 5 4 9

预期结果

51

## 参考题解 先考虑84题 直方图最大矩形面积

最大矩形面积等于对每一行（及其上方所有行）组成的直方图执行84题解法（求直方图最大矩形面积）

84有一个栈方法和一个左右第一矮方法（用时一模一样），下面使用后者作为函数直接调用举例：

C++ code
```
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        int m=matrix.size();
        if(m==0) return 0;
        int n=matrix[0].size();
        if(n==0) return 0;
        int maxArea=0;
        vector<int> heights(n,0);
        for(int r=0;r<m;++r) {
            for(int c=0;c<n;++c) {
                if(matrix[r][c]=='0') heights[c]=0;
                else ++heights[c];
            }
            maxArea=max(maxArea, largestRectangleArea(heights));
        }
        return maxArea;
    }
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
36 ms
, 在所有 C++ 提交中击败了
38.80%
的用户

内存消耗 :
10.5 MB
, 在所有 C++ 提交中击败了
76.62%
的用户

时间Omn（调用的方法On），空间On

另

可以将84的栈方法写入同一个函数，没有什么技术含量，

代码参考https://leetcode-cn.com/problems/maximal-rectangle/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-1-8/，

Java code
```
public int maximalRectangle(char[][] matrix) {
    if (matrix.length == 0) {
        return 0;
    }
    int[] heights = new int[matrix[0].length + 1]; //小技巧后边讲
    int maxArea = 0;
    for (int row = 0; row < matrix.length; row++) {
        Stack<Integer> stack = new Stack<Integer>();
        heights[matrix[0].length] = 0;
        //每求一个高度就进行栈的操作
        for (int col = 0; col <= matrix[0].length; col++) {
            if (col < matrix[0].length) { //多申请了 1 个元素，所以要判断
                if (matrix[row][col] == '1') {
                    heights[col] += 1;
                } else {
                    heights[col] = 0;
                }
            }
            if (stack.isEmpty() || heights[col] >= heights[stack.peek()]) {
                stack.push(col);
            } else {
                //每次要判断新的栈顶是否高于当前元素
                while (!stack.isEmpty() && heights[col] < heights[stack.peek()]) {
                    int height = heights[stack.pop()];
                    int leftLessMin = stack.isEmpty() ? -1 : stack.peek();
                    int RightLessMin = col;
                    int area = (RightLessMin - leftLessMin - 1) * height;
                    maxArea = Math.max(area, maxArea);
                }
                stack.push(col);
            }
        }

    }
    return maxArea;
}

//作者：windliang
//链接：https://leetcode-cn.com/problems/maximal-rectangle/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-1-8/
//来源：力扣（LeetCode）
//著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
其中，里边有一个小技巧，84 题的略复杂那个栈解法中，我们用了两个 while 循环，第二个 while 循环用来解决遍历完元素栈不空的情况。其实，我们注意到两个 while 循环的逻辑完全一样的。所以我们可以通过一些操作，使得遍历结束后，依旧进第一个 while 循环，从而剩下了第 2 个 while 循环，代码看起来会更简洁。

那就是 heights 多申请一个元素，赋值为 0。这样最后一次遍历的时候，栈顶肯定会大于当前元素，所以就进入了第一个 while 循环。

作者：windliang
链接：https://leetcode-cn.com/problems/maximal-rectangle/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-1-8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


## dp

改编自84题leftLessMin、rightLessMin方法

它们从上一行到下一行拥有递推规律：

- 若heights[i]==‘0’, 则左、右更新为0、heights.size()（表示两边不再有比他矮的，绝对的矮不包含等高，防止对下次循环的影响）

- 若heights[i]==’1’，则左、右分别更新，可能被离i更近的0的列取代，或者没0则保持不变

时间Omn，空间On

C++ code
```
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        int m=matrix.size();
        if(m==0) return 0;
        int n=matrix[0].size();
        if(n==0) return 0;
        int maxArea=0;
        vector<int> heights(n,0);
        vector<int> leftLessMin(n, -1);
        vector<int> rightLessMin(n, n);
        for(int r=0;r<m;++r) {
            int boundary=-1; //记录上次出现 0 的位置
            for(int c=0;c<n;++c) { //正向遍历更新heights和leftLessMin
                if(matrix[r][c]=='0') {
                    heights[c]=0;
                    leftLessMin[c]=-1;
                    boundary=c; //更新 0 的位置
                }
                else {
                    ++heights[c];
                    leftLessMin[c]=max(leftLessMin[c], boundary);
                }
            }
            boundary=n;
            for(int c=n-1;c>=0;--c) { //反向遍历更新rightLessMin并计算面积
                if(matrix[r][c]=='0') {
                    rightLessMin[c]=n;
                    boundary=c;
                }
                else rightLessMin[c]=min(rightLessMin[c], boundary);
                int area=(rightLessMin[c]-leftLessMin[c]-1)*heights[c];
                maxArea=max(maxArea, area);
            }
        }
        return maxArea;
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
89.37%
的用户

内存消耗 :
10.8 MB
, 在所有 C++ 提交中击败了
67.31%
的用户
