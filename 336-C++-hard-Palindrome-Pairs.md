# 回文对

```
Given a list of unique words, return all the pairs of the distinct indices (i, j) in the given list, so that the concatenation of the two words words[i] + words[j] is a palindrome.

 

Example 1:

Input: words = ["abcd","dcba","lls","s","sssll"]
Output: [[0,1],[1,0],[3,2],[2,4]]
Explanation: The palindromes are ["dcbaabcd","abcddcba","slls","llssssll"]
Example 2:

Input: words = ["bat","tab","cat"]
Output: [[0,1],[1,0]]
Explanation: The palindromes are ["battab","tabbat"]
Example 3:

Input: words = ["a",""]
Output: [[0,1],[1,0]]
 

Constraints:

1 <= words.length <= 5000
0 <= words[i].length <= 300
words[i] consists of lower-case English letters.
```
## my attempt - tle 超时
isPalindrome(string w) 使用双指针判断回文字符串
```C++
class Solution {
public:
    bool isPalindrome(string w) {
        if (w.size() <= 1) return true;
        int i = 0, j = w.size() - 1;
        while (i < j) {
            if (w[i] != w[j]) {
                return false;
            }
            i++;
            j--;
        }
        return true;
    }
    vector<vector<int>> palindromePairs(vector<string>& words) {
        int n = words.size();
        vector<vector<int>> res;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                string temp1 = words[i] + words[j];
                string temp2 = words[j] + words[i];
                if (isPalindrome(temp1)) {
                    res.push_back({i, j});
                }
                if (isPalindrome(temp2)) {
                    res.push_back({j, i});
                }
            }
        }
        return res;
    }
};
```
## brute force - TLE 超时
使用 str == reversed_str 判断回文
```
class Solution {
public:
    vector<vector<int>> palindromePairs(vector<string>& words) {
        int n = words.size();
        vector<vector<int>> res;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                string temp1 = words[i] + words[j];
                string temp2 = words[j] + words[i];
                string rtemp1 = temp1;
                reverse(rtemp1.begin(), rtemp1.end());
                string rtemp2 = temp2;
                reverse(rtemp2.begin(), rtemp2.end());
                if (temp1 == rtemp1) {
                    res.push_back({i, j});
                }
                if (temp2 == rtemp2) {
                    res.push_back({j, i});
                }
            }
        }
        return res;
    }
};
```

## hashmap O（1）查找单词
对每一个单词，可以计算出pair中的另一个单词，不失为下面三种情况：

为了便于答案不重复，有两种区分方式：（1）默认当前单词长度>=另一单词 （2）默认当前单词拼接在前面

在此方法使用第一种区分方式，第二种区分方式在下面trie方法中使用

- 长度 ==，reversed_word可以查询到
- word前段的reverse可以查询到，后段是个回文子串
- word后段的reverse可以查询到，前段是个回文子串

算法：

对于每个单词，

- 寻找reverse在不在
- findPalindromePrefixes()返回所有后段是回文的前段的reverse，查询
- findPalindromeSuffixes()返回所有后段是回文的后段的reverse，查询

```C++
class Solution {
public:
    vector<string> findPalindromePrefixes(string word) {
        vector<string> prefixes;
        for (int i = word.size() -1; i >= 0; --i) {
            string sstr = word.substr(i);
            string rev = sstr;
            reverse(rev.begin(), rev.end());
            if (rev == sstr) {
                string rev_p = word.substr(0, i);
                reverse(rev_p.begin(), rev_p.end());
                prefixes.push_back(rev_p);
            }
        }
        return prefixes;
    }
    vector<string> findPalindromeSuffixes(string word) {
        vector<string> suffixes;
        for (int i = 1; i <= word.size(); ++i) {
            string sstr = word.substr(0, i);
            string rev = sstr;
            reverse(rev.begin(), rev.end());
            if (rev == sstr) {
                string rev_s = word.substr(i);
                reverse(rev_s.begin(), rev_s.end());
                suffixes.push_back(rev_s);
            }
        }
        return suffixes;
    }
    vector<vector<int>> palindromePairs(vector<string>& words) {
        int n = words.size();
        vector<vector<int>> res;
        unordered_map<string, int> dict;
        for (int i = 0; i < n; ++i) {
            dict[words[i]] = i;
        }
        for (int i = 0; i < n; ++i) {
            string reversed_word = words[i];
            reverse(reversed_word.begin(), reversed_word.end());
            if (reversed_word != words[i] && dict.find(reversed_word) != dict.end()) {
                res.push_back({i, dict.find(reversed_word)->second});
            }
            vector<string> prefixes = findPalindromePrefixes(words[i]);
            vector<string> suffixes = findPalindromeSuffixes(words[i]);
            for (auto & prefix : prefixes) {
                if (dict.find(prefix) != dict.end()) {
                    res.push_back({i, dict.find(prefix)->second});
                }
            }
            for (auto & suffix : suffixes) {
                if (dict.find(suffix) != dict.end()) {
                    res.push_back({dict.find(suffix)->second, i});
                }
            }
        }
        return res;
    }
};
// Runtime: 332 ms, faster than 25.49% of C++ online submissions for Palindrome Pairs.
// Memory Usage: 90.3 MB, less than 33.19% of C++ online submissions for Palindrome Pairs.
```

## TRIE 字典树

算法：
- 将所有word的reverse插入字典树
- 按照三种方式进行查询

三种方式：

为了便于答案不重复，有两种区分方式：（1）默认当前单词长度>=另一单词 （2）默认当前单词拼接在前面

在此方法使用第二种区分方式，第一种区分方式在上面hash方法中使用

- 长度 ==，reversed_word可以查询到
- word前段的reverse可以查询到，后段是个回文子串
- word的reverse可以查询到（非终止位word_ending），并且查询到的剩余段是个回文子串

```Python
class TrieNode:
    def __init__(self):
        self.next = collections.defaultdict(TrieNode)
        self.ending_word = -1
        self.palindrome_suffixes = []

class Solution:
    def palindromePairs(self, words):

        # Create the Trie and add the reverses of all the words.
        trie = TrieNode()
        for i, word in enumerate(words):
            word = word[::-1] # We want to insert the reverse.
            current_level = trie
            for j, c in enumerate(word):
                # Check if remainder of word is a palindrome.
                if word[j:] == word[j:][::-1]:# Is the word the same as its reverse?
                    current_level.palindrome_suffixes.append(i)
                # Move down the trie.
                current_level = current_level.next[c]
            current_level.ending_word = i

        # Look up each word in the Trie and find palindrome pairs.
        solutions = []
        for i, word in enumerate(words):
            current_level = trie
            for j, c in enumerate(word):
                # Check for case 3.
                if current_level.ending_word != -1:
                    if word[j:] == word[j:][::-1]: # Is the word the same as its reverse?
                        solutions.append([i, current_level.ending_word])
                if c not in current_level.next:
                    break
                current_level = current_level.next[c]
            else: # Case 1 and 2 only come up if whole word was iterated.
                # Check for case 1.
                if current_level.ending_word != -1 and current_level.ending_word != i:
                    solutions.append([i, current_level.ending_word])
                # Check for case 2.
                for j in current_level.palindrome_suffixes:
                    solutions.append([i, j])
        return solutions
```
