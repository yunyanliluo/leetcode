# 删除无效的括号
Remove the minimum number of invalid parentheses in order to make the input string valid. Return all possible results.

Note: The input string may contain letters other than the parentheses ( and ).

Example 1:

Input: "()())()"

Output: ["()()()", "(())()"]

Example 2:

Input: "(a)())()"

Output: ["(a)()()", "(a())()"]

Example 3:

Input: ")("

Output: [""]

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/remove-invalid-parentheses

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## DFS
参考：https://leetcode-cn.com/problems/remove-invalid-parentheses/solution/shen-du-you-xian-sou-suo-jie-ti-by-shaft/

我们需要先找出不合法的左括号个数和右括号个数

利用dfs不断删除”（"或者"）"，直到不合法个数为0

检验删除后的括号串是否合法。

时间复杂度果然比较高，但是写起来比较简单

C++ code
```
class Solution {
public:
    vector<string> removeInvalidParentheses(string s) {
        if(s=="") return {""};
        int left=0, right=0;
        for(auto& c:s) {
            if(c=='(') ++left;
            else if(c==')') {
                if(left>0) --left;
                else ++right;
            }
        }
        vector<string> res;
        dfs(res, s, 0, left, right);
        return res;
    }
    void dfs(vector<string>& res, string s, int start, int left, int right) {
        if(left==0&&right==0) {
            if(check(s))
                res.push_back(s);
        }
        for(int i=start;i<s.size();++i) {
            if(i>start&&s[i]==s[i-1]) continue;
            if(left>0&&s[i]=='(') dfs(res, s.substr(0, i)+s.substr(i+1, s.size()-i-1), i, left-1, right);
            else if(right>0&&s[i]==')') dfs(res, s.substr(0, i)+s.substr(i+1, s.size()-i-1), i, left, right-1);
        }
    }
    bool check(string s) {
        int sum=0;
        for(auto& c:s) {
            if(c=='(') ++sum;
            else if(c==')') {
                --sum;
                if(sum<0) return false;
            }
        }
        return sum==0;
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
44.75%
的用户

内存消耗 :
10.2 MB
, 在所有 C++ 提交中击败了
52.29%
的用户



我原本尝试详细地考虑所有可能的情况，详细地描绘，但是发现太复杂了，我最终通过了40+个测试样例，代码如下：

C++ code
```
class Solution {
public:
    vector<string> removeInvalidParentheses(string s) {
        //if(s=="") return {""};
        int i=0,j=s.size()-1;
        while(i<=j&&s[i]==')') ++i;
        while(i<=j&&s[j]=='(') --j;
        if(i>j) return {""};
        //else if(i==j) return {s.substr(i,1)};
        s=s.substr(i, j-i+1);cout<<"s:"<<s<<endl;
        vector<int> erasePos;
        vector<int> positions;
        int sum=0, boundary=-1;
        for(int i=0;i<s.size();++i) {
            if(s[i]=='(') ++sum;
            else if(s[i]==')') {
                --sum;
                positions.push_back(i);
            }
            if(sum<0) {
                ++sum;
                erasePos.push_back(i);
            }
            if(sum==0) {
                boundary=i;
            }
        }for(auto& pos: erasePos) cout<<pos<<" ";cout<<endl;
        if(boundary==-1) return {""};
        if(erasePos.empty()&&sum==0) return {s};
        vector<string> res;for(auto& position:positions) cout<<position<<" ";cout<<endl;
        unordered_set<string> strs; //解集去重
        string tempres="";
        BackTrack(strs, tempres, erasePos, positions, s, 0);
        res.insert(res.end(), strs.begin(), strs.end());
        return res;
    }
    void BackTrack(unordered_set<string>& strs, string& tempres, vector<int>& erasePos, vector<int>& positions, string&s, int len) {
        if(erasePos.empty()) {
            if(len<s.size()) tempres+=s.substr(len);
            strs.insert(tempres);cout<<"strs:["<<tempres<<endl;
            return;
        }
        int pos=erasePos[0];
        erasePos.erase(erasePos.begin());
        for(auto& position: positions) {
            if(position<len) continue;
            if(position>pos) break;
            int templen=tempres.size();
            if(position!=0) tempres+=s.substr(len, position-len);cout<<tempres<<endl;
            BackTrack(strs, tempres, erasePos, positions, s, position+1);
            tempres=tempres.substr(0, templen);
        }
        return;
    }
};
```

鲁棒性考虑：

1.是否能生成有效字符串？

2.没有（）只有其它字符的有效字符串应该通过

3.（多   /    ）多

然后，我找到了0ms的测试样例，实现了我的思路：

1.去掉多的）部分，可以去掉某位置及其前面所有位置的），使用回溯法(?)+递归

2.（多的问题，将（）兑换，字符串reverse，然后调用自身解决

C++ code
```
class Solution {
public:
    vector<string> removeInvalidParentheses(string s) {
        vector<string> res;
        remove(s, {'(', ')'}, 0, 0, res);
        return res;
    }
    void remove(string s, const vector<char>& par, int m, int n, vector<string>& res){
        int stack = 0;
        int l = s.size();
        for(int i = m;i < l;i++){
            if(s[i] == par[0]){
                stack++;
            }
            else if(s[i] == par[1]) stack--;
            //去掉多的右括号
            if(stack < 0){
                for(int j = n;j <= i;j++){
                    if(s[j] == par[1] && (j == n || s[j - 1] != par[1])){
                        string ss = s.substr(0, j) + s.substr(j + 1);
                        remove(ss, par, i, j, res);
                    }
                }
                return; //无效字符串不进行下面的
            }
        }
       /*if(stack == 0) {
            res.push_back(s);
            return;
        }*/
        //有多的左括号
        reverse(s.begin(), s.end());
        if(par[0] == '('){
            remove(s, {par[1], par[0]}, 0, 0, res);
        }
        //已经处理完了
        else{
            res.push_back(s);
        }
        return;
    }
};
```
执行用时 :
4 ms
, 在所有 C++ 提交中击败了
95.67%
的用户

内存消耗 :
10 MB
, 在所有 C++ 提交中击败了
53.21%
的用户
