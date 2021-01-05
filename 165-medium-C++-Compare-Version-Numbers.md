# solutions

c++ 字符串转数字
stoi(string str, size_t = 0, int base = 10)

```
class Solution {
public:
    int compare(string str1, string str2) {
        int n1 = stoi(str1), n2 = stoi(str2);
        return n1 > n2 ? 1 : (n1 == n2 ? 0 : -1);
    }
    
    int compareVersion(string v1, string v2) {
        string version1 = v1 + '.';
        string version2 = v2 + '.';
        int len1 = version1.size(), len2 = version2.size();
        int s1 = 0, s2 = 0;
        int e1 = version1.find('.', s1 + 1), e2 = version2.find('.', s2 + 1);
        while (e1 != string::npos && e2 != string::npos) {
            int res = compare(version1.substr(s1, e1), version2.substr(s2, e2));
            if (res != 0) {
                return res;
            }
            s1 = e1 + 1;
            s2 = e2 + 1;
            e1 = version1.find('.', s1 + 1);
            e2 = version2.find('.', s2 + 1);
        }
        if (e1 == string::npos && e2 != string::npos) {
            for (int i = s2; i < len2; ++i) {
                if (version2[i] != '.' && version2[i] != '0') {
                    return -1;
                }
            }
            return 0;
        }
        else if (e1 != string::npos && e2 == string::npos) {
            for (int i = s1; i < len1; ++i) {
                if (version1[i] != '.' && version1[i] != '0') {
                    return 1;
                }
            }
            return 0;
        }
        else {
            return 0;
        }
    }
};
```

Runtime: 0 ms, faster than 100.00% of C++ online submissions for Compare Version Numbers.

Memory Usage: 6.6 MB, less than 43.31% of C++ online submissions for Compare Version Numbers.
