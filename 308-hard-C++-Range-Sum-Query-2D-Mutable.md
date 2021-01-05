# solution

## logic

prefixSum[][]

使用prefixSum记录每一行，从第一列到当前列的数字之和

query：

```
for(row: rows)：
    sum += (prefixSum[row][col2] - prefixSum[row][col1-1])
```

```
class NumMatrix {
public:
    NumMatrix(vector<vector<int>>& matrix) {
        mat = matrix;
        int m = matrix.size();
        if (m>0) {
            vector<int> temp0(matrix[0].size()+1, 0);
            prefixSum.push_back(temp0);
        }
        for (int i=0; i<m; ++i) {
            vector<int> temp;
            int n = matrix[0].size();
            temp.push_back(0);
            for (int j=0; j<n; ++j) {
                temp.push_back(temp.back()+matrix[i][j]);
            }
            prefixSum.push_back(temp);
        } 
    }
    
    void update(int row, int col, int val) {
        int diff = val - mat[row][col];
        mat[row][col] = val;
        for (int i=col+1; i<=mat[0].size(); ++i) {
            prefixSum[row+1][i] += diff;
        }
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        int sum = 0;
        for (int i=row1+1; i<= row2+1; ++i) {
            sum += prefixSum[i][col2+1] - prefixSum[i][col1];
        }
        return sum;
    }
private:
    vector<vector<int>> mat;
    vector<vector<int>> prefixSum;
};

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix* obj = new NumMatrix(matrix);
 * obj->update(row,col,val);
 * int param_2 = obj->sumRegion(row1,col1,row2,col2);
 */
```
Runtime: 28 ms, faster than 82.46% of C++ online submissions for Range Sum Query 2D - Mutable.

Memory Usage: 13.9 MB, less than 22.51% of C++ online submissions for Range Sum Query 2D - Mutable.

## 线段树

一维线段树：中点

二维线段树：两个中点分割四块

题解 java
```
class NumMatrix {
    private static SegmentTreeNode root;
    private static int[][] matrix;

    private class SegmentTreeNode {
        int up;
        int down;
        int left;
        int right;
        int sum;
        SegmentTreeNode upperLeft;
        SegmentTreeNode upperRight;
        SegmentTreeNode lowerLeft;
        SegmentTreeNode lowerRight;

        public SegmentTreeNode(int up, int down, int left, int right, int sum) {
            this.up = up;
            this.down = down;
            this.left = left;
            this.right = right;
            this.sum = sum;
            this.upperLeft = null;
            this.upperRight = null;
            this.lowerLeft = null;
            this.lowerRight = null;
        }
    }

    public NumMatrix(int[][] matrix) {
        this.matrix = matrix;
        if(matrix.length > 0 && matrix[0].length > 0) {
            int M = matrix.length, N = matrix[0].length;
            int[][] prefixSum = new int[M + 1][N + 1];
            for(int i = 1; i <= M; i++) {
                for(int j = 1; j <= N; j++) {
                    prefixSum[i][j] = prefixSum[i - 1][j] + prefixSum[i][j - 1] - prefixSum[i - 1][j - 1] + matrix[i - 1][j - 1];
                }
            }
            this.root = createSegmentTree(prefixSum, 0, (M - 1), 0, (N - 1));
        }
    }

    private SegmentTreeNode createSegmentTree(int[][] prefixSum, int up, int down, int left, int right) {
        if(up > down || left > right) {
            return null;
        }
        int sum = prefixSum[down + 1][right + 1] - prefixSum[up][right + 1] - prefixSum[down + 1][left] + prefixSum[up][left];
        SegmentTreeNode root = new SegmentTreeNode(up, down, left, right, sum);
        if(up == down && left == right) {
            return root;
        }
        int rowMid = (up + ((down - up) / 2));
        int colMid = (left + ((right - left) / 2));
        root.upperLeft = createSegmentTree(prefixSum, up, rowMid, left, colMid);
        root.upperRight = createSegmentTree(prefixSum, up, rowMid, colMid + 1, right);
        root.lowerLeft = createSegmentTree(prefixSum, rowMid + 1, down, left, colMid);
        root.lowerRight = createSegmentTree(prefixSum, rowMid + 1, down, colMid + 1, right);
        return root;
    }

    public void update(int row, int col, int val) {
        int diff = val - matrix[row][col];
        matrix[row][col] = val;
        updateSegmentTree(root, row, col, diff);
    }

    private void updateSegmentTree(SegmentTreeNode root, int row, int col, int diff) {
        if(root == null) {
            return;
        }
        if(root.up > row || root.down < row || root.left > col || root.right < col) {
            return;
        }
        root.sum += diff;
        if(root.up == root.down && root.left == root.right) {
            return;
        }
        int rowMid = (root.up + ((root.down - root.up) / 2));
        int colMid = (root.left + ((root.right - root.left) / 2));
        if(row <= rowMid) {
            if(col <= colMid) {
                updateSegmentTree(root.upperLeft, row, col, diff);
            } else {
                updateSegmentTree(root.upperRight, row, col, diff);
            }
        }
        if(col <= colMid) {
            updateSegmentTree(root.lowerLeft, row, col, diff);
        } else {
            updateSegmentTree(root.lowerRight, row, col, diff);
        }
    }

    public int sumRegion(int row1, int col1, int row2, int col2) {
        if(matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }
        return sumRangeSegmentTree(root, row1, row2, col1, col2);
    }

    private int sumRangeSegmentTree(SegmentTreeNode root, int up, int down, int left, int right) {
        if(root == null) {
            return 0;
        }
        if(root.up > down || root.down < up || root.left > right || root.right < left) {
            return 0;
        }
        if(root.up == up && root.down == down && root.left == left && root.right == right) {
            return root.sum;
        }
        int rowMid = (root.up + ((root.down - root.up) / 2));
        int colMid = (root.left + ((root.right - root.left) / 2));
        if(down <= rowMid) {
            if(right <= colMid) {
                return sumRangeSegmentTree(root.upperLeft, up, down, left, right);
            } else if(left >= (colMid + 1)) {
                return sumRangeSegmentTree(root.upperRight, up, down, left, right);
            } else {
                return sumRangeSegmentTree(root.upperLeft, up, down, left, colMid) + sumRangeSegmentTree(root.upperRight, up, down, colMid + 1, right);
            }
        } else if(up >= (rowMid + 1)) {
            if(right <= colMid) {
                return sumRangeSegmentTree(root.lowerLeft, up, down, left, right);
            } else if(left >= (colMid + 1)) {
                return sumRangeSegmentTree(root.lowerRight, up, down, left, right);
            } else {
                return sumRangeSegmentTree(root.lowerLeft, up, down, left, colMid) + sumRangeSegmentTree(root.lowerRight, up, down, colMid + 1, right);
            }
        } else {
            if(right <= colMid) {
                return sumRangeSegmentTree(root.upperLeft, up, rowMid, left, right) + sumRangeSegmentTree(root.lowerLeft, rowMid + 1, down, left, right);
            } else if(left >= (colMid + 1)) {
                return sumRangeSegmentTree(root.upperRight, up, rowMid, left, right) + sumRangeSegmentTree(root.lowerRight, rowMid + 1, down, left, right);
            }
        }
        return sumRangeSegmentTree(root.upperLeft, up, rowMid, left, colMid) + sumRangeSegmentTree(root.upperRight, up, rowMid, colMid + 1, right) + sumRangeSegmentTree(root.lowerLeft, rowMid + 1, down, left, colMid) + sumRangeSegmentTree(root.lowerRight, rowMid + 1, down, colMid + 1, right);
    }
}
```


炸炸 java
```
class NumMatrix {
    boolean isEmpty;
    SegNode root;
    int[][] mat;

    public NumMatrix(int[][] matrix) {
        if (matrix.length == 0) {
            isEmpty = true;
            return;
        }

        mat = matrix;
        root = new SegNode(0, 0, matrix.length-1, matrix[0].length-1);
        for (int i = 0; i < matrix.length; ++i) {
            for (int j = 0; j < matrix[0].length; ++j) {
                root.add(i, j, matrix[i][j]);
            }
        }
    }

    public void update(int row, int col, int val) {
        if (isEmpty)
            return;
        root.add(row, col, val - mat[row][col]);
        mat[row][col] = val;
    }

    public int sumRegion(int row1, int col1, int row2, int col2) {
        if (isEmpty)
            return 0;
        return root.query(row1, col1, row2, col2);
    }

    class SegNode {
        int top;
        int bottom;
        int left;
        int right;
        int hmid;
        int vmid;
        int value;
        boolean isLeaf;
        SegNode[] children; // top left, top right, bottom left, bottom right

        SegNode(int t, int l, int b, int r) {
            top = t;
            left = l;
            bottom = b;
            right = r;
            hmid = (t + b) / 2;
            vmid = (l + r) / 2;
            value = 0;
            children = new SegNode[4];
            isLeaf = t == b && l == r;
            isEmpty = t > b || l > r;
        }

        void add(int row, int col, int val) {
            if (row < top || row > bottom || col < left || col > right)
                return;

            value += val;

            if (isLeaf)
                return;

            if (row <= hmid && col <= vmid) {
                // top left
                if (children[0] == null)
                    children[0] = new SegNode(top, left, hmid, vmid);
                children[0].add(row, col, val);
            } else if (row <= hmid) {
                // top right
                if (children[1] == null)
                    children[1] = new SegNode(top, vmid + 1, hmid, right);
                children[1].add(row, col, val);
            } else if (col <= vmid) {
                // bottom left
                if (children[2] == null)
                    children[2] = new SegNode(hmid + 1, left, bottom, vmid);
                children[2].add(row, col, val);
            } else {
                if (children[3] == null)
                    children[3] = new SegNode(hmid + 1, vmid + 1, bottom, right);
                children[3].add(row, col, val);
            }
        }

        int query(int t, int l, int b, int r) {
            if (t > bottom || l > right || b < top || r < left)
                return 0;

            if (isLeaf || t == top && l == left && b == bottom && r == right)
                return value;

            // top left
            if (b <= hmid && r <= vmid) {
                return children[0] == null ? 0 : children[0].query(t, l, b, r);
            }
            // top left + top right
            if (l <= vmid && b <= hmid) {
                return (children[0] == null ? 0 : children[0].query(t, l, b, vmid)) +
                    (children[1] == null ? 0 : children[1].query(t, vmid + 1, b, r));
            }
            // top left + bottom left
            if (t <= hmid && r <= vmid) {
                return (children[0] == null ? 0 : children[0].query(t, l, hmid, r)) +
                    (children[2] == null ? 0 : children[2].query(hmid + 1, l, b, r));
            }
            // top left + top right + bottom left + bottom right
            if (t <= hmid && l <= vmid) {
                return (children[0] == null ? 0 : children[0].query(t, l, hmid, vmid)) +
                    (children[1] == null ? 0 : children[1].query(t, vmid + 1, hmid, r)) +
                    (children[2] == null ? 0 : children[2].query(hmid + 1, l, b, vmid)) +
                    (children[3] == null ? 0 : children[3].query(hmid + 1, vmid + 1, b, r));
            }
            // top right
            if (l > vmid && b <= hmid) {
                return children[1] == null ? 0 : children[1].query(t, l, b, r);
            }
            // top right + bottom right
            if (t <= hmid) {
                return (children[1] == null ? 0 : children[1].query(t, l, hmid, r)) +
                    (children[3] == null ? 0 : children[3].query(hmid + 1, l, b, r));
            }
            // bottom left
            if (r <= vmid) {
                return (children[2] == null ? 0 : children[2].query(t, l, b, r));
            }
            // bottom left + bottom right
            if (l <= vmid) {
                return (children[2] == null ? 0 : children[2].query(t, l, b, vmid)) +
                    (children[3] == null ? 0 : children[3].query(t, vmid + 1, b, r));
            }
            // bottom right
            return children[3] == null ? 0 : children[3].query(t, l, b, r);
        }
    }
}

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix obj = new NumMatrix(matrix);
 * obj.update(row,col,val);
 * int param_2 = obj.sumRegion(row1,col1,row2,col2);
 */
```
