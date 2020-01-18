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
这份代码没有通过，问题是：

Line 18: Char 39: runtime error: member access within misaligned address 0xbebebebebebebebe for type 'struct trieNode', which requires 8 byte alignment (solution.cpp)
0xbebebebebebebebe: note: pointer points here
<memory cannot be printed>

但是经检查，不存在没有
