# 字符串乘法

限制 两字符串长度1~200

## code

corner cases:

1. 若答案0则返回0 badcase: 0*0=0(返回了空)
 
2. 0占位问题 badcase: 408*5=2040(24)

2. 去开头零 badcase: 408*0=0(000)
```
class Solution {
public:
    string addhelper(string& addee, int adder, int k) {
        int c = adder;
        if (c == 0 && addee.size() < k + 1) { //0占位 badcase: 408*5=2040(24)
            addee += "0";
        }
        while (c != 0) {
            char ch = addee.size() >= k + 1 ? addee[k] : '0';
            int num = ch - '0';
            int tempres = num + c;
            char newch = (tempres % 10) + '0';
            if (addee.size() >= k + 1) addee[k] = newch;
            else addee += newch;
            c = tempres / 10;
            ++k;
        }
        return addee;
    } // adder only 0-9; k means kth position from right to left
    string multiply(string num1, string num2) {
        // 13:57
        string res = "";
        int k = 0, c = 0; 
        int n = num1.size(), m = num2.size();
        for (int i = n - 1; i >= 0; --i) {
            int a = num1[i] - '0';
            c = 0;
            k = n - i - 1;
            for (int j = m - 1; j >= 0; --j) {
                int b = num2[j] - '0';
                int tempres = a * b + c;
                addhelper(res, tempres % 10, k);
                c = tempres / 10;
                ++k;
            }
            if (c != 0) addhelper(res, c, k);
        }
        int len = res.size();
        while(len - 1 > 0 && res[len - 1] == '0') --len;  // 去开头零 badcase: 408*0=0(000)
        res.erase(res.begin() + len, res.end());
        reverse(res.begin(), res.end());
        return res;
    }
};

```
