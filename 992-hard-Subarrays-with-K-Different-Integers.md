# K个不同整数的子树组
求一个树组的有K个不同整数的子树组个数

## 滑动窗口

题解的方法：双滑动窗口
```
class Solution {
public:
    int subarraysWithKDistinct(vector<int>& A, int K) {
        int n=A.size();
        unordered_map<int,int> windowl, windowr;
        int count=0;
        int leftl=0, leftr=0, right=0;
        while(right<n) {
            int c1=A[right];
            ++windowl[c1];
            ++windowr[c1];
            ++right;
            if(windowl.size()>=K) { //可以不要这个预判条件 直接进入语句块执行 不满足预判条件不影响
                while(windowl.size()>K) {
                    int c2=A[leftl];
                    --windowl[c2];
                    if(windowl[c2]==0) {
                        windowl.erase(c2);
                    }
                    ++leftl;
                }
                while(windowr.size()>=K) {
                    int c2=A[leftr];
                    --windowr[c2];
                    if(windowr[c2]==0) {
                        windowr.erase(c2);
                    }
                    ++leftr;
                }
                count += leftr-leftl;
            }
        }
        return count;
    } 
};
```
执行用时 :
324 ms
, 在所有 C++ 提交中击败了
9.03%
的用户

内存消耗 :
46.2 MB
, 在所有 C++ 提交中击败了
8.00%
的用户

## 我自己原本的滑动窗口法 - 双指针
```
class Solution {
public:
    int subarraysWithKDistinct(vector<int>& A, int K) {
        //滑动窗口+哈希map
        unordered_map<int,int> counts;
        int left=0, right=0;
        int n=A.size();
        int sum=0;
        while(right<n) {
            int temp=A[right];
            ++counts[temp];
            ++right;
            while(counts.size()>K) {
                temp=A[left];
                --counts[temp];
                if(counts[temp]==0) counts.erase(temp);
                ++left;
            }
            int left0=left;
            while(counts.size()==K) {
                ++sum;
                temp=A[left];
                if(counts[temp]==1) break;
                --counts[temp];
                ++left;
            }
            for(int i=left0;i<left;++i) ++counts[A[i]];
            left=left0;
        }
        return sum;
    }
};
```
执行用时 :
136 ms
, 在所有 C++ 提交中击败了
51.17%
的用户

内存消耗 :
39.1 MB
, 在所有 C++ 提交中击败了
8.00%
的用户
