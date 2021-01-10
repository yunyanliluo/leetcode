# solutions
贪心
```
class Solution {
public:
    bool validateStackSequences(vector<int>& pushed, vector<int>& popped) {
        int n = popped.size();
        if (n == 0) return true;
        stack<int> s;
        int pushptr = 0;
        for (int popptr = 0; popptr < n; ++ popptr) {
            while (s.empty() || s.top() != popped[popptr]) {
                if (pushptr < n)  s.push(pushed[pushptr++]);
                else return false;
            }
            s.pop();
        }
        return true;
    }
};
```
Runtime: 20 ms, faster than 24.46% of C++ online submissions for Validate Stack Sequences.

Memory Usage: 15.6 MB, less than 84.74% of C++ online submissions for Validate Stack Sequences.
