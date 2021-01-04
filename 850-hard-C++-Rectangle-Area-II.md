# solution

## segment tree

line swiping：一条竖直的直线从左到右扫描，这条直线上的current有效长度 * 移动距离 = current area

需要实现的功能：add，remove，query，正好符合线段树这一数据结构

使用线段树，线段树节点拥有min（y轴最低点），max（y轴最高点），count（这段线段的次数）

query(): if count > 0: return max - min; else return (left ? left->query() : 0) + (right ? right->query() : 0);

add(int m, int n)/remove(int m, int n): if m == min && n == max: count++/--; else { int mid; if mid < m: right->add/remove(m, n); else if mid >= n: left->add/remove(m, n); else {left->add/remove(m, mid); right->add/remove(mid, n);}}

注意：

1. 由于有效边长的计算=max-min，因此线段树的长度有实际意义，不存在长度为0的线段树节点，同时允许线段树分享mid点：[1,3]的儿子是[1,2]和[2,3].

2. add()操作时对于空节点（left/right == NULL）的处理。由于矩形的对称性，可以认为remove()不会遇到空节点。

3. mid取左中点，可能存在mid == min，需要讨论mid == min时，不往下递归

4. count用于边的计数，在remove掉时判断是否这条边还是有效长度

 - 剪枝：边y1=1,y2=3进入矩形，在[1,3]置count++；根据矩形对称性，一定有边y1=1,y2=3离开矩形，在[1,3]置count--。不需要下移到叶子节点置count。

 - 在获取query()时，如果本节点count>0,那么有效长度=max-min；不然，有效长度=左子树有效长度+右子树有效长度。

主函数：

预处理：

将边插入数组并按照x值由小到大排序

格式：{x, 1/-1, y1, y2}

OPEN边（1）和CLOSE边（-1）分别代表进入矩形的边（左）和离开矩形的边（右）

注意：

1.for循环里area每次+=之后都需要%=一下，免得越加越多然后溢出

```
class Solution {
public:
    class Node {
        public:
            int min, max, validLen, count; // validLen实际上没有用。如果使用validLen，会有最后一个测试样例WA，不明原因。
            Node *left, *right;
            Node(int mi, int ma, int va, int co): min(mi), max(ma), validLen(va), count(co), left(NULL), right(NULL) {}
            void add(int s, int e){
                if (s == e) return;
                if (s == min && e == max) {
                    count ++;
                    validLen = e - s;
                    return;
                }
                int mid = min + ( max - min ) / 2;
                if ( mid < s ) {
                    if (!right) right = new Node(mid, max, 0, 0);
                    right -> add(s, e);
                }
                else if (mid >= e && min < mid ) {
                    if (!left) left = new Node(min, mid, 0, 0);
                    left -> add(s, e);
                }
                else {
                    if (!left && min < mid) left = new Node(min, mid, 0, 0);
                    if (!right) right = new Node(mid, max, 0, 0);
                    if(left) left -> add(s, mid);
                    right -> add(mid, e);
                }
                // if (count == 0) {  // 在query（）中计算有效长度
                //     validLen = 0;
                //     if(left) validLen += left->query(); 
                //     if(right) validLen += right->query();
                // } else {
                //     validLen = max - min;
                // }
                // cout << "line 37: min=" << min << ", max=" << max << ", add(" << s << "," << e << ") validLen= " << validLen << endl;
                return;
            }
            void remove(int s, int e) {
                if (s == e) return;
                if (s == min && e == max) {
                    count --;
                    // if (count == 0) {
                    //     validLen = 0;
                    //     if (left) validLen += left->query();
                    //     if (right) validLen += right->query();
                    // } else {
                    //     validLen = max - min;
                    // }
                    // cout << "line 49: min=" << min << ", max=" << max << ", remove(" << s << "," << e << ") returns " << newLen << endl;
                    return;
                }
                int mid = min + ( max - min ) / 2;
                int newLen = 0;
                if ( mid < s ) {
                    if (!right) right = new Node(mid, max, 0, 0);
                    right -> remove(s, e);
                }
                else if (mid >= e && min < mid) {
                    if (!left) left = new Node(min, mid, 0, 0);
                    left -> remove(s, e);
                }
                else {
                    if (!left && min < mid) left = new Node(min, mid, 0, 0);
                    if (!right) right = new Node(mid, max, 0, 0);
                    if(left) left -> remove(s, mid);
                    right -> remove(mid, e);
                }
                // if (count == 0) {
                //     validLen = 0;
                //     if(left) validLen += left->query(); 
                //     if(right) validLen += right->query();
                // } else {
                //     validLen = max - min;
                // }
                // cout << "line 69: min=" << min << ", max=" << max << ", remove(" << s << "," << e << ") returns " << validLen - newLen << endl;
                return;
            }
            int query() {
                // return validLen;
                if (count > 0) {
                    return max - min;
                } else {
                    return (left ? left->query() : 0) + (right ? right->query() : 0);
                }
            }
    };
    int rectangleArea(vector<vector<int>>& rectangles) {
        vector<vector<int>> rects;
        for (vector<int> temp: rectangles) {
            vector<int> rect1 = {temp[0], 1, temp[1], temp[3]};
            vector<int> rect2 = {temp[2], -1, temp[1], temp[3]};
            rects.push_back(rect1);
            rects.push_back(rect2);
        }
        sort(rects.begin(), rects.end(), [](vector<int> a, vector<int> b) {return a[0] <= b[0];});
        Node* root = new Node(0, 1000000000, 0, 0);
        long long int area = 0;
        long long int currValidLen = 0;
        int currX = -1;
        for (vector<int> temp: rects) {
            // cout << temp[0] << " " << temp[1] << " " << temp[2] << " " << temp[3] << endl;
            if (temp[0] != currX) {
                currValidLen = root->query();
                // cout << "currValidLen: " << currValidLen << endl;
                area += ( currValidLen * ( temp[0] - currX ) ) % 1000000007;
                area %= 1000000007; // 这里需要注意要有！！
                currX = temp[0];
            }
            if (temp[1] == 1) {
                root->add(temp[2], temp[3]);
            }
            else if (temp[1] == -1) {
                root->remove(temp[2], temp[3]);
            }
        }
        return area % 1000000007;
    }
};
```

炸炸代码 Java

```
class Solution {

    public int rectangleArea(int[][] rectangles) {
        int INT_IN = 0;
        int INT_OUT = 1;
        if (rectangles.length < 1)
            return 0;

        PriorityQueue<int[]> events = new PriorityQueue<>((a, b) -> {
            if (a[0] != b[0])
                return Integer.compare(a[0], b[0]);
            return Integer.compare(a[2], b[2]);
        });
        long totalArea = 0;
        int ymin = Integer.MAX_VALUE;
        int ymax = Integer.MIN_VALUE;
        for (int i = 0; i < rectangles.length; ++i) {
            int[] rect = rectangles[i];
            events.offer(new int[] { rect[0], i, INT_IN });
            events.offer(new int[] { rect[2], i, INT_OUT });
            ymax = Math.max(ymax, rect[3]);
            ymin = Math.min(ymin, rect[1]);
        }

        SegTreeNode line = new SegTreeNode(ymin, ymax);
        int height = 0;
        int x = events.peek()[0];
        while (!events.isEmpty()) {
            int[] event = events.poll();
            totalArea += (long) (event[0] - x) * (long) height;

            int[] rect = rectangles[event[1]];
            if (event[2] == INT_IN)
                line.insert(rect[1], rect[3]);
            else
                line.delete(rect[1], rect[3]);

            x = event[0];
            height = line.query();
        }

        return (int) (totalArea % 1000000007);
    }

    class SegTreeNode {
        int start;
        int mid;
        int end;
        int count;
        SegTreeNode left;
        SegTreeNode right;

        SegTreeNode(int low, int high) {
            start = low;
            end = high;
            mid = low + (high - low) / 2;
        }

        void insert(int low, int high) {
            if (low >= high || low >= end || high <= start)
                return;

            if (low <= start && high >= end) {
                ++count;
                return;
            }

            if (left == null)
                left = new SegTreeNode(start, mid);
            if (right == null)
                right = new SegTreeNode(mid, end);

            left.insert(low, Math.min(mid, high));
            right.insert(Math.max(mid, low), high);
        }

        void delete(int low, int high) {
            if (low >= high || low >= end || high <= start)
                return;

            if (low == start && high == end) {
                count = Math.max(0, count - 1);
                return;
            }

            if (left != null)
                left.delete(low, Math.min(mid, high));
            if (right != null)
                right.delete(Math.max(mid, low), high);
        }

        int query(int low, int high) {
            if (low <= start && high >= end && count > 0)
                return end - start;

            return (left != null ? left.query(low, Math.min(mid, high)) : 0)
                    + (right != null ? right.query(Math.max(mid, low), high) : 0);
        }

        int query() {
            return query(start, end);
        }
    }
}
```
