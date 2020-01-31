# 最小覆盖子串
Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

Example:

Input: S = "ADOBECODEBANC", T = "ABC"

Output: "BANC"

Note:

If there is no such window in S that covers all characters in T, return the empty string "".

If there is such window, you are guaranteed that there will always be only one unique minimum window in S.

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/minimum-window-substring

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 滑动窗口法
C++ code
```
class Solution {
public:
    string minWindow(string s, string t) {
        string minstr="";
        int minl=s.size()+1;
        int left=0, right=0;
        int match=0;
        unordered_map<char, int>needs, window;
        for(char c: t) ++needs[c];
        while(right<s.size()) {
            char c1=s[right];
            if(needs.find(c1)!=needs.end()) {
                ++window[c1];
                if(needs[c1]==window[c1]) ++match;
            }
            ++right;
            while(match==needs.size()) {
                if(right-left<minl) {
                    minl=right-left;
                    minstr=s.substr(left, minl);
                }
                char c2=s[left];
                if(needs.find(c2)!=needs.end()) {
                    --window[c2];
                    if(window[c2]<needs[c2]) --match;
                }
                ++left;
            }
        }
        return minstr;
    }
};
```
执行用时 :
44 ms
, 在所有 C++ 提交中击败了
35.15%
的用户

内存消耗 :
19 MB
, 在所有 C++ 提交中击败了
12.87%
的用户

滑动窗口法其它题目 字符串的字母异位词
