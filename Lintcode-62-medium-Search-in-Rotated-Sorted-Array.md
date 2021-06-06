# 搜索旋转排序数组

## 二分法
通过比较mid和target是否同在单调一段来分类讨论

```Java
public class Solution {
    /**
     * @param A: an integer rotated sorted array
     * @param target: an integer to be searched
     * @return: an integer
     */
    public int search(int[] A, int target) {
        if (A == null || A.length == 0)
            return -1;
            
        // 通过判断mid和target是否处于翻转的同一边，来决定搜索的方向
        // 如果 > 最后一个数:在左边, otherwise 在右边
        int start = 0, end = A.length-1, div = A[A.length-1];
        
        while (start+1 < end) {
            int mid = start + (end-start)/2;
            if (A[mid] == target)
                return mid;
            //如果在同一边，按照正常的二分搜索方向
            if ((A[mid] > div && target > div) || (A[mid] <= div && target <= div)){
                if (A[mid] > target)
                    end = mid;
                else 
                    start = mid;
            } else {
                //如果在翻转的不同边，反转二分搜索的方向
                if (A[mid] > target)
                    start = mid;
                else 
                    end = mid; 
            }
        }
        
        if (A[start] == target)
            return start;
        if (A[end] == target) 
            return end;
            
        return -1;
    }
}
```

```C++
class Solution {
public:
    /**
     * @param A: an integer rotated sorted array
     * @param target: an integer to be searched
     * @return: an integer
     */
    int search(vector<int> &A, int target) {
        if (A.size() == 0) return -1;
        // 二分法
        // 通过比较mid和target是否同在单调一段来分类讨论
        int left = 0, right = A.size() - 1;
        int pivot = A[0];
        if (pivot == target) return 0;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (A[mid] > pivot) {
                if (target > pivot) {
                    if (target == A[mid]) return mid;
                    else if (target > A[mid]) {
                        left = mid + 1;
                    } else {
                        right = mid - 1;
                    }
                } else {
                    left = mid + 1;
                }
            }
            else {
                if (target > pivot) {
                    right = mid - 1;
                } else {
                    if (target == A[mid]) return mid;
                    else if (target > A[mid]) {
                        left = mid + 1;
                    } else {
                        right = mid - 1;
                    }
                }
            }
        }
        if (A[left] == target) return left;
        if (A[right] == target) return right;
        return -1;
    }
};
```
41 ms时间消耗
·
5.46 MB空间消耗
·
您的提交打败了89.60 %的提交
