# 每日温度

## 栈
单调栈，逆序遍历

On，On
C++ code
```
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        int n=T.size();
        if(n==0) return {};
        vector<int> res(n, 0);
        stack<int> stk;
        stk.push(n-1);
        for(int i=n-2;i>=0;--i) {
            while(!stk.empty()&&T[stk.top()]<=T[i]) {
                stk.pop();
            }
            if(!stk.empty()) {
                res[i]=stk.top()-i;
            }
            stk.push(i);
        }
        return res;
    }
};
```
执行用时 :
72 ms
, 在所有 C++ 提交中击败了
69.28%
的用户

内存消耗 :
36.8 MB
, 在所有 C++ 提交中击败了
5.00%
的用户

## DP
将res数组当作dp，逆序遍历

Java code
```
public int[] dailyTemperatures(int[] T) {
    int length = T.length;
    int[] result = new int[length];

    //从右向左遍历
    for (int i = length - 2; i >= 0; i--) {
        // j+= result[j]是利用已经有的结果进行跳跃
        for (int j = i + 1; j < length; j+= result[j]) {
            if (T[j] > T[i]) {
                result[i] = j - i;
                break;
            } else if (result[j] == 0) { //遇到0表示后面不会有更大的值，那当然当前值就应该也为0
                result[i] = 0;
                break;
            }
        }
    }

    return result;
}

作者：pulsaryu
链接：https://leetcode-cn.com/problems/daily-temperatures/solution/jie-ti-si-lu-by-pulsaryu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
