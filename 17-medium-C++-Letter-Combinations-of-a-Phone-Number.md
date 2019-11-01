# *Letter Combinations of a Phone Number*

Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

Example:

Input: "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].

Note:
Although the above answer is in lexicographical order, your answer could be in any order you want.

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## solutions

### 回溯法
my C++ code
```C++ code
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        phonemap["2"]="abc"; //把map赋值放在这里是因为类变量赋值，一旦和初始化不放在一起，就不能在类变量的位置写
        phonemap["3"]="def";
        phonemap["4"]="ghi";
        phonemap["5"]="jkl";
        phonemap["6"]="mno";
        phonemap["7"]="pqrs";
        phonemap["8"]="tuv";
        phonemap["9"]="wxyz";
        if(digits.size()==0) return res;
        string tempres="";
        BackTrack(tempres, digits);
        return res;
    }
    void BackTrack(string tempres, string digits)
    {
        if(digits=="")
        {
            res.push_back(tempres);
            return;
        }
        string charcts=phonemap[digits.substr(0,1)];
        for(int i=0;i<charcts.size();++i)
        {
            BackTrack(tempres+charcts.substr(i,1), digits.substr(1,digits.size()-1));
        }
    }
private:
    map<string, string> phonemap;    
    vector<string> res;
};
```
执行用时 :0 ms, 在所有 cpp 提交中击败了100.00%的用户

内存消耗 :8.6 MB, 在所有 cpp 提交中击败了51.58%的用户

时间复杂度： O(3^N × 4^M)，其中 N 是输入数字中对应 3 个字母的数目（比方说 2，3，4，5，6，8）， M 是输入数字中对应 4 个字母的数目（比方说 7，9），N+M 是输入数字的总数。

空间复杂度：O(3^N × 4^M)，这是因为需要保存 3^N × 4^M个结果。


### 一个非常精巧的队列解法
Java code from：https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/solution/javafei-chang-jing-qiao-de-dui-lie-jie-fa-by-xxxzz/
```Java code
public List<String> letterCombinations(String digits) {
		LinkedList<String> ans = new LinkedList<String>();
		if(digits.isEmpty()) return ans;
		String[] mapping = new String[] {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
		ans.add("");
		for(int i =0; i<digits.length();i++){
			int x = Character.getNumericValue(digits.charAt(i));
			while(ans.peek().length()==i){
				String t = ans.remove();
				for(char s : mapping[x].toCharArray())
					ans.add(t+s);
			}
		}
		return ans;
	}
```
