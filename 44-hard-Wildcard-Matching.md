# 通配符匹配
？匹配单个字符
.匹配0个或任意个字符

## DP
Omn,Omn

C++ code
```
class Solution {
public:
    bool isMatch(string s, string p) {
        int m=s.size(), n=p.size();
        //去掉开头相同的a-z
        int start=0;
        while(start<m&&start<n) {
            if(s[start]==p[start]) {
                ++start;
            }
            else if(p[start]=='?'||p[start]=='*') break;
            else return false;
        }
        //去掉末尾相同的a-z
        int mend=m-1, nend=n-1;
        while(mend>=start&&nend>=start) {
            if(s[mend]==p[nend]) {
                --mend;--nend;
            }
            else if(p[nend]=='?'||p[nend]=='*') break;
            else return false;
        }
        //处理去头去尾的s，p的鲁棒性
        if(mend<start&&nend<start) return true;
        if(mend<start) {
            for(int i=start;i<=nend;++i) if(p[i]!='*') return false;
            return true;
        }
        if(nend<start) return false;
        //DP处理去头去尾的s，p
        vector<bool> isMatched(nend-start+2, false); //for p[j], s[0..i] is matched once
        vector<vector<bool>> dp(mend-start+2, isMatched);
        dp[0][0]=true;
        //isMatched[0]=true;
        for(int i=start;i<=nend;++i) 
            if(p[i]=='*') {
                dp[0][i-start+1]=true; //isMatched[i-start+1]=true;
            }
            else break;
        for(int i=1;i<=mend-start+1;++i) {
            int ip=start+i-1;
            for(int j=1;j<=nend-start+1;++j) {
                int jp=start+j-1;
                if(s[ip]==p[jp]||p[jp]=='?') dp[i][j]=dp[i-1][j-1];
                else if(p[jp]=='*') {
                    //dp[m=0..i][j-1] has a true
                    //dp[i][j]=isMatched[j-1];
                    dp[i][j]=dp[i][j-1]||dp[i-1][j];
                }
                //else dp[i][j]=false;
                //cout<<dp[i][j]<<" ";
                //isMatched[j]= dp[i][j]?true:isMatched[j];
            }cout<<endl;
        }
        return dp[mend-start+1][nend-start+1];
    }
};
```
执行用时 :
88 ms
, 在所有 C++ 提交中击败了
49.85%
的用户

内存消耗 :
11.5 MB
, 在所有 C++ 提交中击败了
64.98%
的用户

改进前

1.状态转换方程：本来使用isMatched判断，后来优化为不使用这个数组

2.TLE使得数组掐头去尾

3.后来发现cout语句debug占的时间太多了

4.发现dp数组初始化之后，能省略false赋值就省略，能省去isMatched数组和cout语句就省去

## 双指针

欣赏这种方法

s和p匹配过程中可能会遇到的情况：

- 如果i和j标记的字符正好相等或者j字符是'?'匹配成功，则"移除"i和j元素，即自增i、j。

- 否则如果j字符是'*'依然可以匹配成功，则用istart和jstart分别标记i元素和j元素之后自增j。

- 再否则如果istart>-1说明之前匹配过'*'，因为'*'可以匹配多个字符，所以这里要再次利用这个最近匹配过的'*'匹配更多的字符，移动i标记istart的下一个字符，再让istart重新标记i元素同时移动j标记jstart的下一个字符。

- 上述三种情况都不满足，则匹配失败，返回false。

最后当s中的字符都判断完毕，则认为s为空，此时需要p为空或者p中只剩下星号的时候，才能成功匹配。

作者：jerrymouse1998

链接：https://leetcode-cn.com/problems/wildcard-matching/solution/javashuang-zhi-zhen-tan-xin-he-dong-tai-gui-hua-xi/


java code
```
public boolean isMatch(String s, String p) {
    if (p==null||p.isEmpty())return s==null||s.isEmpty();
    int i=0,j=0,istart=-1,jstart=-1,slen=s.length(),plen=p.length();
    //判断s的所有字符是否匹配
    while (i<slen){
        //三种匹配成功情况以及匹配失败返回false
        if (j<plen&&(s.charAt(i)==p.charAt(j)||p.charAt(j)=='?')){
            i++;
            j++;
        }else if (j<plen&&p.charAt(j)=='*'){
            istart=i;
            jstart=j++;
        }else if (istart>-1){
            i=++istart;
            j=jstart+1;
        }else {
            return false;
        }
    }
    //s中的字符都判断完毕，则认为s为空，此时需要p为空或者p中只剩下星号的时候，才能成功匹配。
    //如果p中剩余的都是*，则可以移除剩余的*
    while (j<plen&&p.charAt(j)=='*')j++;
    return j==plen;
}

作者：jerrymouse1998
链接：https://leetcode-cn.com/problems/wildcard-matching/solution/javashuang-zhi-zhen-tan-xin-he-dong-tai-gui-hua-xi/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
