# 字符串相乘
两个字符串（长度110以下）相乘，输出字符串。不能使用转int。

腾讯题

WA1: 乘0，等于0，输出结果却是一串0，而不是一个0

WA2:我的方法是乘法术式的方法，c在最后剩下一个的时候应该加在字符串前方

WA3:c还要清零再进入下一个循环

哎，看了0ms答案，自己写了一遍

C++
```
class Solution {
public:
    string multiply(string num1, string num2) {
        int m=num1.size(), n=num2.size();
        string res(m+n, '0');
        for(int i=m-1; i>=0; --i) {
            for(int j=n-1; j>=0; --j) {
                int temp=res[i+j+1]-'0'+(num1[i]-'0')*(num2[j]-'0');
                res[i+j]+=temp/10;
                res[i+j+1]='0'+temp%10;
            }
        }
        for(int i=0;i<m+n;++i) {
            if(res[i]!='0') return res.substr(i);
        }
        return "0";
    }
};
```
执行用时 :
4 ms
, 在所有 C++ 提交中击败了
97.36%
的用户

内存消耗 :
8.8 MB
, 在所有 C++ 提交中击败了
63.92%
的用户
