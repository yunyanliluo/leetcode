# Implement Trie
实现Trie

Implement a trie with insert, search, and startsWith methods.

Example:

Trie trie = new Trie();

trie.insert("apple");

trie.search("apple");   // returns true

trie.search("app");     // returns false

trie.startsWith("app"); // returns true

trie.insert("app");   

trie.search("app");     // returns true

Note:

You may assume that all inputs are consist of lowercase letters a-z.

All inputs are guaranteed to be non-empty strings.

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/implement-trie-prefix-tree

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 使用139 word break的trie写法

C++ code
```
class Trie {
public:
    /** Initialize your data structure here. */
    Trie() {
        isKey=false;
        for(int i=0;i<26;++i) {
            children[i]=NULL;
        }
    }
    
    /** Inserts a word into the trie. */
    void insert(string word) {
        if(this->children[word[0]-'a']==NULL) 
            this->children[word[0]-'a']=new Trie();
        if(word.size()>1)
            (*(this->children[word[0]-'a'])).insert(word.substr(1,word.size()-1));
        else
            this->children[word[0]-'a']->isKey=true;
    }
    
    /** Returns if the word is in the trie. */
    bool search(string word) {
        int n=word.size();
        if(n==1) {
            if(this->children[word[0]-'a']&&this->children[word[0]-'a']->isKey) return true;
            else return false;
        }
        else if(this->children[word[0]-'a']) {
            return (*(this->children[word[0]-'a'])).search(word.substr(1,n-1));
        }
        else
            return false;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(string prefix) {
        int n=prefix.size();
        if(n==1) {
            if(this->children[prefix[0]-'a']) return true;
            else return false;
        }
        else if(this->children[prefix[0]-'a']) {
            return (*(this->children[prefix[0]-'a'])).startsWith(prefix.substr(1,n-1));
        }
        else
            return false;
    }
private:
    bool isKey;
    struct Trie* children[26];
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
 ```
执行用时 :156 ms, 在所有 C++ 提交中击败了11.09%的用户

内存消耗 :137.4 MB, 在所有 C++ 提交中击败了5.02%的用户
