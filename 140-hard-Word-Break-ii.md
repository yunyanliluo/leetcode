# Word Break ii

## 使用139 Word Break判断 + DP回溯
C++ code
```
class Solution {
public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        /*139wordbreak部分 判断是否有解*/
        int n=s.size();
        vector<bool> dp(n+1,false);
        unordered_set<string> st(wordDict.begin(), wordDict.end());
        dp[0]=true;
        int maxLen=0;
        for(int i=0;i<wordDict.size();++i)
            maxLen=max(maxLen, (int)wordDict[i].size());
        for(int i=1;i<=n;++i) {
            for(int j=max(i-maxLen, 0);j<i;++j) {
                if(dp[j]&&st.find(s.substr(j, i-j))!=st.end()) {
                    dp[i]=true; 
                    break;
                }
            }
        }
        /*DP回溯部分*/
        vector<vector<string>> trace(n+1);//长度为0～n的字符串对应的解
        if(dp[n]) { //如果有解
            for(int i=1;i<=n;++i) {//使用动态规划自底向上处理长度为1～n的字符串对应的解
                for(int j=max(i-maxLen, 0);j<i;++j) {
                    if(dp[j]&&st.find(s.substr(j, i-j))!=st.end()) { //找到一个解
                        if(trace[j].empty()) { //如果当前问题对应的子问题没有解（也就是j==0）当前问题已经是最小子问题
                            trace[i].push_back(s.substr(j, i-j));
                        }
                        else {
                            for(vector<string>::iterator it=trace[j].begin();it!=trace[j].end();++it) {
                                trace[i].push_back((*it)+" "+s.substr(j, i-j));
                            }
                        }
                    }
                }
            }
        }
        return trace[n];
    }
};
```
执行用时 :12 ms, 在所有 C++ 提交中击败了86.11%的用户
内存消耗 :10 MB, 在所有 C++ 提交中击败了94.19%的用户

## 执行最快的范例(4ms)：139 word break判断 + 回溯法回溯
```
class Solution {
    unordered_set<string> sstr;
    vector<string> ans;
    vector<string> path;
public:// 复用139的代码判断一下
    bool wordBreak2(string s, vector<string>& wordDict) {
        if(wordDict.empty())    return false;
        unordered_set<string> sstr(wordDict.begin(), wordDict.end());
        int dp[s.size()+1]{0};
        dp[0] = 1;
        int maxl = wordDict[0].size();
        for(int i=1; i<wordDict.size(); i++)
            maxl = max(maxl, (int)wordDict[i].size()); 
        for(int i=1; i<=s.size(); i++){
            for(int j=max(i-maxl, 0); j<i; j++){
                if(dp[j]==1 && sstr.find(s.substr(j, i-j)) != sstr.end()){
                    dp[i] = 1;
                    break;
                }
            }
        }
        return dp[s.size()];
    }
    void helper(string s, int l, int r){ //回溯法
        //回溯停止条件
        if(l>r){ //子字符串的首末（左右）位置有左>右
            string str;
            for(int i=0; i<path.size()-1; i++) //将path vector中的内容一一倒出
                str = str + path[i] + " "; //将string之间使用“ ”连接
            str += path.back(); //单独处理最后一个string，因为后续不再需要“ ”
            ans.push_back(str);//作为一个解
            return ;
        }
        //回溯的处理（未触发停止条件）
        for(int i=1; i<=r-l+1; i++){ //i是截取字符串的长度
            string tmp = s.substr(l, i);
            if(sstr.find(tmp) != sstr.end()){ //在字典找到一个子字符串
                path.push_back(tmp); //压入path vector
                helper(s, l+i, r); //回溯（递归调用）
                path.pop_back(); //从path弹出
            }
        }  
    }
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        if(!wordBreak2(s, wordDict))    return ans;
        sstr = unordered_set<string>(wordDict.begin(), wordDict.end());
        helper(s, 0, s.size()-1);
        return ans;
    }
};
```

⚠️单纯使用回溯法，一定会timeout
## 单纯使用回溯法，一定会timeout

最后执行的输入：

"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaabaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"

["a","aa","aaa","aaaa","aaaaa","aaaaaa","aaaaaaa","aaaaaaaa","aaaaaaaaa","aaaaaaaaaa"]

解决方案就是先判断有解，再回溯，不要一上来就回溯

以下代码是timeout代码

```
class Solution {
public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        n=s.size();
        if(n==0) return res;
        string temp="";
        BackTrack(s, wordDict, 0, temp);
        return res;
    }
    void BackTrack(string& s, vector<string>& wordDict, int start, string& temp)
    {
        if(start>=n){
            res.push_back(temp);
            return;
        }
        int len;
        for(len=1;len<=n-start;++len)
        {
            string sstr=s.substr(start, len);
            vector<string>::iterator it;
            for (it=wordDict.begin(); it!=wordDict.end(); ++it)
                if(*it==sstr) break;
            if(it==wordDict.end()) continue;
            int prelen=temp.size();
            temp=(prelen==0)?sstr:(temp+" "+sstr);
            BackTrack(s, wordDict, start+len, temp);
            temp.erase(prelen);
        }
    }
private:
    vector<string> res;
    int n; //s.size()
};
```
