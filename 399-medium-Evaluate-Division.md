# 除法求值

Equations are given in the format A / B = k, where A and B are variables represented as strings, and k is a real number (floating point number). Given some queries, return the answers. If the answer does not exist, return -1.0.

Example:

Given a / b = 2.0, b / c = 3.0.

queries are: a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? .

return [6.0, 0.5, -1.0, 1.0, -1.0 ].

i.e.

equations = [ ["a", "b"], ["b", "c"] ],

values = [2.0, 3.0],

queries = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ].

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/evaluate-division

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 并查集
每一个等式是由两个string和一个value组成。

将每一个string赋予一个序号，维护一个并查集，并查集中的数组下标对应于string的序号。

每处理一个等式，将两个string在并查集中合并。

并查集中增加一个属性：val，val表示父亲是它的多少倍。（例如：a/b=2.0，则a的val是1，b的parent是1，b的val是2）


定义class UnionFind：并查集类，类里有三个数组：

- parent：父亲下标
- rank：高度，用于合并时候的优化
- val：父亲是它的多少倍

即，并查集的成员变量：
```
        int* parent;
        int* rank;
        double* val;
```


在并查集中connected表示可以求得它们之间的倍数关系。

那么求p/q时，假设p，q connected，则p/q=cal（q）/cal（p），其中cal（）代表了祖先是当前元素的多少倍，也就是将当前元素的val乘以父亲的val、再乘以父亲的父亲的val……。


以一个example来解释：

Example:

Given a / b = 2.0, b / c = 3.0.

queries are: a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? .

return [6.0, 0.5, -1.0, 1.0, -1.0 ].

i.e.

equations = [ ["a", "b"], ["b", "c"] ],

values = [2.0, 3.0],

queries = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ].


则算法的处理过程是：

1. 建立一个大小为equations.size()*2=4的并查集
2. 顺序处理equations和values的每一个元素：将“a”赋予一个序号0，将“b”赋予一个序号1，将序号0和1在并查集中合并（将序号1的parent置成0），并将序号1的val置成2.0；“b”已经拥有一个序号1了，将“c”赋予一个序号2，将序号1和2在并查集中合并（将序号2的parent置成1），将序号2的val置成3.0
3. 顺序处理queries的每一个query：“a”序号0，“c”序号2，a/c=cal（c）/cal（a），cal（c）=3.0*2.0=6.0，cal（a）=1.0，a/c=6.0/1.0=6;以此类推。


另外，将string映射成序号的过程，使用的是vector<string>，如果vector中已经存在当前string则返回vector下标（从0开始），如果vector中不存在则将string插入vector并获得一个vector下标作为当前string的序号。


优化的部分是并查集的高度减小：

在find(int x)中，每一次查找祖先的过程中，将当前元素的父亲改成父亲的父亲（对应的val也要改），就实现了一次高度的减少。

类似地，在cal_x(int x)中（用于单个元素相对于祖先的倍数计算），每一次查找祖先的过程中，也将当前元素的父亲改成父亲的父亲（对应的val也要改）。


C++ code

```
class Solution {
public:
    class UnionFind {
    public:
        UnionFind(int n) {
            parent=new int [n];
            rank=new int [n];
            val=new double [n];
            for(int i=0;i<n;++i) {
                parent[i]=i;
                rank[i]=1;
                val[i]=1.0;
            }
        }
        int find(int x) {
            while(parent[x]!=x) {
                val[x]=val[x]*val[parent[x]];
                parent[x]=parent[parent[x]];
                x=parent[x];
            }
            return x;
        }
        bool isConnected(int p, int q) {
            return find(p)==find(q);
        }
        void join(int p, int q, double temp_val) {
            if(p==q) return;
            int p_root=find(p);
            int q_root=find(q);
            if(p_root!=q_root) {
                if(rank[q_root]>rank[p_root]) {
                    parent[p_root]=q_root;
                    val[p_root]=1.0*val[q]/val[p]/temp_val;
                    ++rank[q_root];
                }
                else { 
                    parent[q_root]=p_root;
                    val[q_root]=1.0*val[p]/val[q]*temp_val;
                    ++rank[p_root];

                }
            }
        }
        double cal_x(int x) {
            double res=1.0;
            while(parent[x]!=x) {
                val[x]=val[x]*val[parent[x]];
                parent[x]=parent[parent[x]];
                res*=val[x];
                x=parent[x];
            }
            return res;
        }
        double cal(int p, int q) {
            if(p==-1||q==-1) return -1.0;
            if(p==q) return 1.0;
            double res=-1.0;
            if(isConnected(p, q)) {
                double cal_p=cal_x(p), cal_q=cal_x(q);
                res=(double)1.0*cal_q/cal_p; 
            }
            return res;
        }
    private:
        int* parent;
        int* rank;
        double* val;
    };
    int ind(vector<string>& strVec, string& str) {
        int index=0;
        for(const auto& s:strVec) {
            if(s!=str) ++index;
            else break;
        }
        if(index==strVec.size()) return -1;
        return index;
    }
    int trans(vector<string>& strVec, string& str) {
        if(find(strVec.begin(), strVec.end(), str)==strVec.end())
            strVec.push_back(str);
        return ind(strVec, str);
    }
    vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries) {
        UnionFind* uf = new UnionFind(2*equations.size());
        vector<string> strVec;
        int n=equations.size();
        for(int i=0;i<n;i++) {
            vector<string> equa=equations[i]; double value =values[i];
            int p=trans(strVec, equa.front()), q=trans(strVec, equa.back());
            uf->join(p, q, value);
        }
        vector<double> res;
        n=queries.size();
        for(int i=0;i<n;i++) {
            vector<string> query=queries[i];
            int p=ind(strVec, query.front()), q=ind(strVec, query.back());
            double tempres=uf->cal(p, q);
            res.push_back(tempres);
        }
        delete uf;
        return res;
    }
};
```
执行用时 :
0 ms
, 在所有 C++ 提交中击败了
100.00%
的用户

内存消耗 :
10 MB
, 在所有 C++ 提交中击败了
5.23%
的用户
