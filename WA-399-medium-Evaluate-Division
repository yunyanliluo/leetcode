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

C++ code

尚未AC

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
                parent[x]=parent[parent[x]];
                val[x]=val[x]*val[parent[x]];
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
                //cout<<"val "<<val[x]<<" parent "<<val[parent[x]]<<endl;
                res*=(val[x]*val[parent[x]]);
                parent[x]=parent[parent[x]];
                val[x]=val[x]*val[parent[x]];
                x=parent[x];
            }
            return res;
        }
        double cal(int p, int q) {
            if(p==-1||q==-1) return -1.0;
            if(p==q) return 1.0;
            double res=-1.0;
            if(isConnected(p, q)) {
                res=(double)1.0*cal_x(q)/cal_x(p); 
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
        return res;
    }
};
```
10 / 11 个通过测试用例

状态：解答错误

提交时间：2 分钟之前

输入：

[["a","b"],["e","f"],["b","e"]]

[3.4,1.4,2.3]

[["b","a"],["a","f"],["f","f"],["e","e"],["c","c"],["a","c"],["f","e"]]

输出：

[0.29412,1.40000,1.00000,1.00000,-1.00000,-1.00000,5.58571]

预期：

[0.29412,10.948,1.0,1.0,-1.0,-1.0,0.71429]
