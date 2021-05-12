# 外星字典 Alien Dictionary

## 推理

1. 字典顺序 -> 相邻单词的依赖关系，相邻单词依赖关系 -> 字典顺序，充分必要条件，因此只需要处理“相邻单词”的依赖关系

2. 相邻单词依赖关系转化->拓扑排序：用邻接矩阵和入度数组记录，邻接矩阵可以使用hashmap/vector<char>[26]，每一次将入度为0的点放入答案，再更新剩余点的入度

3. 终止条件：全部点均已放入答案 或 有点没有放入答案（说明有环，返回“”）

## 数据结构设计

1.queue，每当有入度为0的点，push，当queue非空，pop，更新以该点为起点的边的终点（入度-1），检查若入度==0，则push

2.vector，入度数组

3.hashmap/vector，邻接矩阵

## badcase

1.put ALL characters into in_cnt!!! in_cnt is global character dictionary

2.if input invalid return ""

```C++
class Solution {
public:
    string alienOrder(vector<string>& words) {
        if (words.size() == 0) return "";
        if (words.size() == 1) return words[0];
        unordered_map<char, unordered_set<char>> dict;
        unordered_map<char, int> in_cnt;
        for (int i = 1; i < words.size(); ++i) {
            string parent = words[i - 1];
            string child = words[i];
            int len = min(parent.size(), child.size());
            int j;
            for (j = 0; j < len; ++j) {
                char p = parent[j], q = child[j];
                if (p != q) {
                    if (dict.find(p) == dict.end()) {
                        unordered_set<char> vec;
                        dict.insert(make_pair(p, vec));
                    }
                    if (in_cnt.find(p) == in_cnt.end()) {
                        in_cnt.insert(make_pair(p, 0));
                    }
                    if (in_cnt.find(q) == in_cnt.end()) {
                        in_cnt.insert(make_pair(q, 0));
                    }
                    if (dict[p].find(q) == dict[p].end()) {
                        dict[p].insert(q);
                        in_cnt[q]++;
                    }
                    break;
                } else { // as below EVERY character put into in_cnt. put ALL characters into in_cnt!!! in_cnt is global character dictionary
                    if (in_cnt.find(p) == in_cnt.end()) {
                        in_cnt.insert(make_pair(p, 0));
                    }
                    if (in_cnt.find(q) == in_cnt.end()) {
                        in_cnt.insert(make_pair(q, 0));
                    }
                }
            }
            // if invalid return ""
            if (j == len && parent.size() > child.size()) {
                return "";
            }
            // put ALL characters into in_cnt!!! in_cnt is global character dictionary
            for (int k = j; k < parent.size(); k++) {
                char p = parent[k];
                if (in_cnt.find(p) == in_cnt.end()) {
                    in_cnt.insert(make_pair(p, 0));
                }
            }
            for (int k = j; k < child.size(); k++) {
                char q = child[k];
                if (in_cnt.find(q) == in_cnt.end()) {
                    in_cnt.insert(make_pair(q, 0));
                }
            }
        }
        queue<char> que;
        for (auto & item : in_cnt) {
            if (item.second == 0) {
                que.push(item.first);
            }
        }
        string res;
        while (!que.empty()) {
            char c = que.front();
            que.pop();
            res+=c;
            for (auto & ch : dict[c]) {
                in_cnt[ch]--;
                if (in_cnt[ch] == 0) {
                    que.push(ch);
                }
            }
        }
        if (res.size() != in_cnt.size()) {
            return "";
        }
        return res;
    }
};
// Runtime: 4 ms, faster than 80.00% of C++ online submissions for Alien Dictionary.
// Memory Usage: 9.8 MB, less than 40.60% of C++ online submissions for Alien Dictionary.
```
