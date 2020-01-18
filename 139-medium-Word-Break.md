# 139.Word Break

Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, determine if s can be segmented into a space-separated sequence of one or more dictionary words.

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/word-break
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 回溯（DFS）+使用哈希集合查询单词
遇到一个输入"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab"
["a","aa","aaa","aaaa","aaaaa","aaaaaa","aaaaaaa","aaaaaaaa","aaaaaaaaa","aaaaaaaaaa"]时，timeout

C++ code
```
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        for(vector<string>::iterator iter=wordDict.begin();iter!=wordDict.end();iter++)//首先将字典中的单词一个一个插入哈希集合
        {
            st.insert(*iter);
        }
        return BackTrack(s, 0, s.size()-1);//然后回溯
    }
    bool BackTrack(string s, int start, int end) {
        if(start>end) return true;//回溯停止标志
        for(int tempEnd=0;tempEnd<=end-start;tempEnd++) {
            if(findWord(s, start, tempEnd)) {//如果查询到了这个单词，则继续查询后面的字符串
                int res=BackTrack(s, start+tempEnd+1, end);
                if(res) return true;
            }
        }
        return false;
    }
    bool findWord(string s, int start, int end) { //使用find函数在哈希集合查询单词
        string subs=s.substr(start, end+1);
        if(st.find(subs)!=st.end()) return true;
        return false;
    }
private:
    unordered_set<string> st;
};
```

## trie
其实使用哈希集合插入和查询都是O（1），使用trie插入和查询都是O（m），慢了一点儿。

这份代码没有通过，问题是：

Line 18: Char 39: runtime error: member access within misaligned address 0xbebebebebebebebe for type 'struct trieNode', which requires 8 byte alignment (solution.cpp)
0xbebebebebebebebe: note: pointer points here
<memory cannot be printed>

意思是，结构体定义中有children指针，在生成实例时没有初始化为NULL。

但是经检查，不存在没有初始化为NULL的节点。目前还没有AC。

C++ code
```
class Solution {
public:
    struct trieNode{
        bool isKey;
        struct trieNode* children[26];
    };
    struct trieNode* trieCreate() {
        struct trieNode* root=(struct trieNode*)malloc(sizeof(struct trieNode));
        root->isKey=false;
        for(int i=0;i<26;++i) {
            root->children[i]=NULL;
        }
        return root;
    }
    void trieInsert(struct trieNode* root, string s) {
        struct trieNode* node=root;
        for(int i=0;i<s.size();++i) {
            if(node->children[s[i]-'a']==NULL) 
                node->children[s[i]-'a']=trieCreate();
            node=node->children[s[i]-'a'];
        }
        node->isKey=true;
    }
    bool trieSearch(struct trieNode* root, string s) {
        struct trieNode* node=root;
        for(int i=0;i<s.size()&&node!=NULL;++i) {
            node=node->children[s[i]-'a'];
        }
        if(node&&node->isKey) return true;
        return false;
    }
    bool wordBreak(string s, vector<string>& wordDict) {
        root=(struct trieNode*)malloc(sizeof(struct trieNode));
        for(vector<string>::iterator iter=wordDict.begin();iter!=wordDict.end();iter++)
            trieInsert(root, *iter);
        return BackTrack(s, 0, s.size()-1);
    }
    bool BackTrack(string s, int start, int end) {
        if(start>end) return true;
        for(int tempEnd=0;tempEnd<=end-start;tempEnd++) {
            if(findWord(s, start, tempEnd)) {
                int res=BackTrack(s, start+tempEnd+1, end);
                if(res) return true;
            }
        }
        return false;
    }
    bool findWord(string s, int start, int end) {
        string subs=s.substr(start, end+1);
        if(trieSearch(root, subs)) return true;
        return false;
    }
private:
    struct trieNode* root;
};
```

一种加速
单词长度每次+1，因此可以记住上一次查找到的节点位置，继续查找下一个字母。
C++ code
```
class Solution {
public:
    struct trieNode{
        bool isKey;
        struct trieNode* children[26];
    };
    struct trieNode* trieCreate() {
        struct trieNode* root=(struct trieNode*)malloc(sizeof(struct trieNode));
        root->isKey=false;
        for(int i=0;i<26;++i) {
            root->children[i]=NULL;
        }
        return root;
    }
    void trieInsert(struct trieNode* root, string s) {
        struct trieNode* node=root;
        for(int i=0;i<s.size();++i) {
            if(node->children[s[i]-'a']==NULL) 
                node->children[s[i]-'a']=trieCreate();
            node=node->children[s[i]-'a'];
        }
        node->isKey=true;
    }
    bool trieSearch(struct trieNode* root, string s) {
        struct trieNode* node=root;
        for(int i=0;i<s.size()&&node!=NULL;++i) {
            node=node->children[s[i]-'a'];
        }
        curr=node;
        if(node&&node->isKey) return true;
        return false;
    }
    bool wordBreak(string s, vector<string>& wordDict) {
        root=(struct trieNode*)malloc(sizeof(struct trieNode));
        for(vector<string>::iterator iter=wordDict.begin();iter!=wordDict.end();iter++)
            trieInsert(root, *iter);
        return BackTrack(s, 0, s.size()-1);
    }
    bool BackTrack(string s, int start, int end) {
        if(start>end) return true;
        curr=root;
        for(int tempEnd=0;tempEnd<=end-start&&curr!=NULL;tempEnd++) {
            if(findWord(s, start+tempEnd, 0)) {
                int res=BackTrack(s, start+tempEnd+1, end);
                if(res) return true;
            }
        }
        return false;
    }
    bool findWord(string s, int start, int end) {
        string subs=s.substr(start, end+1);
        if(trieSearch(curr, subs)) return true;
        return false;
    }
private:
    struct trieNode* root;
    struct trieNode* curr;
};
```

## DP
最后，还是使用DP解决问题，其实这道是DP题。

C++ code
```
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        int n=s.size();
        bool dp[n+1];
        dp[0]=true;//不妨认为空字符串总在字典中
        for(int i=1;i<=n;++i) {//考虑从0起始长度为i的子字符串
            dp[i]=false;
            for(int j=1;j<=i;++j) {//如果从0至j和从j至i都在字典中，则true
                if(dp[j-1]&&find(wordDict.begin(), wordDict.end(), s.substr(j-1, i-j+1))!=wordDict.end()) {
                    dp[i]=true; 
                    break;
                }
            }
        }
        return dp[n];
    }
};
```
执行用时 :20 ms, 在所有 C++ 提交中击败了52.11%的用户
内存消耗 :14.3 MB, 在所有 C++ 提交中击败了35.73%的用户
