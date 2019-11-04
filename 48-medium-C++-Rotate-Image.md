# *Rotate Image*

You are given an n x n 2D matrix representing an image.

Rotate the image by 90 degrees (clockwise).

Note:

You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.

Example 1:

Given input matrix = 

[

  [1,2,3],
  
  [4,5,6],
  
  [7,8,9]
  
],

rotate the input matrix in-place such that it becomes:

[

  [7,4,1],
  
  [8,5,2],
  
  [9,6,3]
  
]

Example 2:

Given input matrix =

[

  [ 5, 1, 9,11],
  
  [ 2, 4, 8,10],
  
  [13, 3, 6, 7],
  
  [15,14,12,16]
  
], 

rotate the input matrix in-place such that it becomes:

[

  [15,13, 2, 5],
  
  [14, 3, 4, 1],
  
  [12, 6, 8, 9],
  
  [16, 7,10,11]
  
]

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/rotate-image

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

# my answer

### 转置+水平翻转

a[m][k]->a[k][m]

a[k][m]->a[k][l-m]

可以使用二重循环，循环内调用swap。

倘若将swap()看作3次赋值操作，则算法一共使用3n^2的时间复杂度。

### 四个位置的数字依次交换

观察到

a[m][k]->a[k][l-m]

a[k][l-m]->a[l-m][l-k]

a[l-m][l-k]->a[l-k][m]

a[l-k][m]->a[m][k]

因此可以四个一组处理矩阵上的元素。

```C++ code
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n=matrix.size();
        if(n==0) return;
        //一圈一圈地处理。每处理一个元素，一共牵动四个位置的元素改变位置。所以将元素按照四个一组，每处理一圈，实际上只需要处理圈边长-1次。
        //处理每一圈都从左上角的元素向右遍历处理，处理一行（边长-1）。每一圈需要处理的元素个数依次减小。每一圈需要处理的起始元素的行数和列数也在递增。
        int l=n-1, k=0; //l是每一圈遍历的元素个数，k是行序号，也是当前行的起始处理元素的列序号
        do
        {cout<<l<<endl;
            for(int i=0;i<l;++i)
            {
                rotateFour(matrix, k, k+i, n); //每处理一个元素，一共牵动四个位置的元素改变位置。
            }
            ++k;
        } while((l-=2)>0);
        return;
    }
    void rotateFour(vector<vector<int>>& matrix, int r, int c, int& n)
    {
        int temp=matrix[r][c];
        matrix[r][c]=matrix[n-1-c][r];
        matrix[n-1-c][r]=matrix[n-1-r][n-1-c];
        matrix[n-1-r][n-1-c]=matrix[c][n-1-r];
        matrix[c][n-1-r]=temp;
        return;
    }
};
```
执行用时 :8 ms, 在所有 cpp 提交中击败了68.86%的用户

内存消耗 :8.9 MB, 在所有 cpp 提交中击败了90.90%的用户

rotateFour()拥有5次赋值操作，则算法一共使用5n^2/4的时间复杂度。

## solutions

### 其实，不需要以圈为单位处理，可以将矩阵划分为4个小矩阵，遍历左上角的矩阵即可。

Java code from: 链接：https://leetcode-cn.com/problems/rotate-image/solution/xuan-zhuan-tu-xiang-by-leetcode/

```Java code
class Solution {
  public void rotate(int[][] matrix) {
    int n = matrix.length;
    for (int i = 0; i < (n + 1) / 2; i ++) {
      for (int j = 0; j < n / 2; j++) {
        int temp = matrix[n - 1 - j][i];
        matrix[n - 1 - j][i] = matrix[n - 1 - i][n - j - 1];
        matrix[n - 1 - i][n - j - 1] = matrix[j][n - 1 -i];
        matrix[j][n - 1 - i] = matrix[i][j];
        matrix[i][j] = temp;
      }
    }
  }
}
```
