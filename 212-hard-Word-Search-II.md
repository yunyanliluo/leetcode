# 单词搜索II

给定一个二维网格 board 和一个字典中的单词列表 words，找出所有同时在二维网格和字典中出现的单词。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。

同一个单元格内的字母在一个单词中不允许被重复使用。

示例:

输入: 

words = ["oath","pea","eat","rain"] and board =

[

['o','a','a','n'],

['e','t','a','e'],

['i','h','k','r'],

['i','f','l','v']

]

输出: ["eat","oath"]

说明:

你可以假设所有输入都由小写字母 a-z 组成。

提示:

你需要优化回溯算法以通过更大数据量的测试。你能否早点停止回溯？

如果当前单词不存在于所有单词的前缀中，则可以立即停止回溯。什么样的数据结构可以有效地执行这样的操作？散列表是否可行？为什么？ 

前缀树如何？如果你想学习如何实现一个基本的前缀树，请先查看这个问题： 实现Trie（前缀树）。

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/word-search-ii

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## hashmap+回溯（dfs）
TLE

C++ code
```
class Solution {
public:
    class Position {
        public:
            int x;
            int y;
            Position(){}
            Position(int a, int b):x(a),y(b){}
    };
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        int m=board.size();
        int n=(m==0)?0:board[0].size();
        if(n==0||words.size()==0) return {};
        unordered_map<char,list<class Position>> boardMap;
        for(int i=0;i<m;++i) {
            for(int j=0;j<n;++j) {
                Position position(i,j);
                boardMap[board[i][j]].push_back(position);
            }
        }
        vector<string> res;
        vector<vector<bool>> visited(m,vector<bool>(n,false)); //用于单元格内字母不被重复使用
        for(auto &word:words) {
            if(findchars(boardMap, word)) {
                int len=word.size();
                for(Position& position:boardMap[word[0]]) {
                    int x=position.x, y=position.y;
                    if(BackTrack(word,1,len,x,y,board,visited,m,n,res)) break;
                }
            }
        }
        return res;
    }
    bool BackTrack(string& word, int start, int& len, int x, int y, vector<vector<char>>& board, vector<vector<bool>>& visited, int& m, int& n, vector<string>& res) {
        visited[x][y]=true;
        if(start==len) {
            res.push_back(word);
            visited[x][y]=false;
            return true;
        }
        vector<vector<int>> directions = {{0,1},{0,-1},{1,0},{-1,0}};
            int i=start;
            for(auto& direction:directions) {
                int xCurr=x+direction[0], yCurr=y+direction[1];
                if(xCurr>=0&&xCurr<m
                &&yCurr>=0&&yCurr<n
                &&board[xCurr][yCurr]==word[i]
                &&visited[xCurr][yCurr]==false) {
                    if(BackTrack(word,i+1,len,xCurr,yCurr,board,visited,m,n,res)) {
                        visited[x][y]=false;
                        return true;
                    }
                }
            }
        visited[x][y]=false;
        return false;
    }
    bool findchars(unordered_map<char,list<class Position>>& boardMap, string& word) {
        int len=word.size();
        if(len==0) return false;
        for(int i=0;i<len;++i) {
            if(boardMap.find(word[i])==boardMap.end()) return false;
        }
        return true;
    }
};
```

## 字典树（前缀树）trie+dfs
参考：https://leetcode-cn.com/problems/word-search-ii/solution/cqian-zhui-shu-ju-ta-ma-rong-yi-dong-by-chashao/

首先使用对每一个board[i][j]使用dfs这没意见吧？关键题目的测试用例有一种是words里面都是前缀很像的词，比如[aaaaab, aaaaac, aaaaad]，那么每次都需要从aaaaa开始就没必要了，所以这里使用前缀树，对于前缀相同的word，就不需要每次都遍历一边，而是在递归遍历前缀树时，只在当前字符不同的时候再分道扬镳。所以对于这种测试用例会变得巨他吗快。

因此：

words->trie

board->dfs

C++ code
```
struct Node {
    bool word;
    string str;
    unordered_map<char, Node*> words;
};
class Trie {
public:
    Trie() {
        root = new Node();
    }
    void insert(string word) {
        Node* p = root;
        for (char c: word) {
            if (p->words.find(c) == p->words.end()) {
                Node* t = new Node();
                p->words[c] = t;
            } 
            p = p->words[c];
        }
        p->str = word; // node对应的word，为了之后根据node来找到结果
        p->word = true;
    }
    void search(vector<string>& res, vector<vector<char>>& board) {
        for (int i = 0; i < board.size(); i++) {
            for (int j = 0; j < board[i].size(); j++) {
                help(res, board, root, i, j);
            }
        }
    }
    void help(vector<string>&res, vector<vector<char>>& board, Node* p, int x, int y) {
        if (p->word) {
            p->word = false; // 其他方向就不会再把答案放进去了
            res.push_back(p->str);
        } 
        if (x < 0 || x == board.size() || y < 0 || y == board[x].size()) return;
        if (p->words.find(board[x][y]) == p->words.end()) return;
        p = p->words[board[x][y]]; // 此时的p是其他字符了
        char cur = board[x][y];
        board[x][y] = '#';
        help(res, board, p, x+1, y);
        help(res, board, p, x-1, y);
        help(res, board, p, x, y+1);
        help(res, board, p, x, y-1);
        board[x][y] = cur;
    }
    
private:
    Node* root;
};
class Solution {
public:
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        Trie trie;
        vector<string> res;
        for (string& w: words) {
            trie.insert(w);
        }
        trie.search(res, board);
        return res;
        
    }
};
```
执行用时：
672 ms
, 在所有 C++ 提交中击败了
18.22%
的用户

内存消耗：
8.9 MB
, 在所有 C++ 提交中击败了
49.93%
的用户

###这是一份十分精致的字典树/前缀树/trie模板
```
struct Node { //trieNode使用struct
    bool word;
    string str;
    unordered_map<char, Node*> words;
};
class Trie { //trie使用class
public:
    Trie() {
        root = new Node();
    }
    void insert(string word) {
        Node* p = root;
        for (char c: word) {
            if (p->words.find(c) == p->words.end()) {
                Node* t = new Node();
                p->words[c] = t;
            } 
            p = p->words[c];
        }
        p->str = word; // node对应的word，为了之后根据node来找到结果
        p->word = true;
    }
    void search(vector<string>& res, vector<vector<char>>& board) {
   
    }
private:
    Node* root;
};
class Solution {
public:
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        Trie trie; 
        vector<string> res;
        for (string& w: words) {
            trie.insert(w);
        }
        trie.search(res, board);
        return res;
        
    }
};
```
