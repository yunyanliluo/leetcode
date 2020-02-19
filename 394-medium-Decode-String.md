# 字符串编码

## 字符串处理

C++ code
```
class Solution {
public:
    string decodeString(string s) {
        int len=s.size();
        if(len==0) return "";
        string res="";
        int i=0;
        while(i<len) {
            //如果s[i]是原始数据，即非0（不考虑[]，因为[]前一定是数字，而考虑数字时连着把数字后面[数据]一并处理掉了）
            int p=i;
            if(s[p]<'0'||s[p]>'9') {
                while(p<len&&(s[p]<'0'||s[p]>'9')) ++p;
                res+=s.substr(i, p-i);
                i=p;
            } 
            //下面只有两种情况：i>=len或s[i]是数字
            if(i>=len) break;
            int temp=0;
            while(i<len&&s[i]<='9'&&s[i]>='0') {
                temp=temp*10+s[i++]-'0';
            }//s[i]=='['
            int start=i+1; //start of string inside []
            string ss="";
            int parNum=1;//'['的数量，这里使用了PV操作的思想，表示只有一个'['
            while((++i)<len&&(s[i]!=']'||parNum!=1)) { //有一种情况是"3[a2[c]]"，字符串嵌套括号，会产生]匹配的问题，到哪个]跳出循环
                if(s[i]=='[') --parNum;
                else if(s[i]==']') ++parNum;
            }
            ss=decodeString(s.substr(start, i-start)); //递归调用，处理嵌套字符串
            for(int j=0;j<temp;++j) res+=ss;
            ++i;//跳过']'
        }
        return res;
    }
};
```
执行用时 :
4 ms
, 在所有 C++ 提交中击败了
65.96%
的用户

内存消耗 :
9 MB
, 在所有 C++ 提交中击败了
13.53%
的用户

另一种思路可以借鉴

Python code
```
class Solution:
    def decodeString(self, s: str) -> str:
        def dfs(s, i):
            res, multi = "", 0
            while i < len(s):
                if '0' <= s[i] <= '9':
                    multi = multi * 10 + int(s[i])
                elif s[i] == '[':
                    i, tmp = dfs(s, i + 1)
                    res += multi * tmp
                    multi = 0
                elif s[i] == ']':
                    return i, res
                else:
                    res += s[i]
                i += 1
            return res
        return dfs(s,0)

作者：jyd
链接：https://leetcode-cn.com/problems/decode-string/solution/decode-string-fu-zhu-zhan-fa-di-gui-fa-by-jyd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```


