# 直线上最多的点数
判断一个数组里最多多少点共线

## hashmap
迅速过了28/36个测试样例，但由于
测试样例有连续几个相同点，并且数组仅有一个相同点，没有其他点，没AC，逻辑繁琐

## 优化方法
思路：https://leetcode-cn.com/problems/max-points-on-a-line/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--35/

考虑当前节点和右面所有节点之间1-1的斜率，若相等，则一定共线（斜率+公共点）

C++ code
```
class Solution {
public:
    int maxPoints(vector<vector<int>>& points) {
        int n=points.size();
        if(n<=2) return n;
        int maxCount = 2; //全局最优
        for(int i=0;i<n;++i) {
            int repeat = 0;
            int maxCurr = 0; //当前最优
            unordered_map<string, int> umap;
            for(int j=i+1;j<n;++j) {
                int x = points[i][0] - points[j][0];
                int y = points[i][1] - points[j][1];
                if(x==0 && y==0) {
                    ++repeat;
                    continue;
                }
                int gcd_v = gcd(x,y); //求最大公约数
                x /= gcd_v;
                y /= gcd_v;
                string key = to_string(x)+"@"+to_string(y); //表示斜率，以@分隔，简化搜索
                ++umap[key];
                maxCurr = max(maxCurr, umap[key]);
            }
            maxCount = max(maxCount, maxCurr+repeat+1);
        }
        return maxCount;
    }
    int gcd(int a, int b) {
        while(b!=0) {
            int temp=a%b;
            a=b;
            b=temp;
        }
        return a;
    }
};
```

执行用时 :
52 ms
, 在所有 C++ 提交中击败了
26.25%
的用户

内存消耗 :
11.1 MB
, 在所有 C++ 提交中击败了
54.90%
的用户
