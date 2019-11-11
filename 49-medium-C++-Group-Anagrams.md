# *Group Anagrams*

Given an array of strings, group anagrams together.

Example:

Input: ["eat", "tea", "tan", "ate", "nat", "bat"],

Output:

[

["ate","eat","tea"],

["nat","tan"],

["bat"]

]

Note:

All inputs will be in lowercase.

The order of your output does not matter.

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/group-anagrams

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## solutions

### map<string, vector<string>>

依次遍历字符串，排序当前字符串，然后排好序的字符串作key、字符串数组作value加入到map中去。

```C++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<string, vector<string>> wordmap;
        for(vector<string>::iterator iter=strs.begin();iter!=strs.end();++iter)
        {
            string sortedstr = *iter;
            sort(sortedstr.begin(), sortedstr.end());
            wordmap[sortedstr].push_back(*iter);
        }
        for(unordered_map<string, vector<string>>::iterator iter=wordmap.begin();iter!=wordmap.end();++iter)
        {
            res.push_back(iter->second);
        }
        return res;
    }
};
```
执行用时 :44 ms, 在所有 cpp 提交中击败了96.64%的用户

内存消耗 :18.7 MB, 在所有 cpp 提交中击败了79.02%的用户

时间复杂度：O（n * KlogK），K 是字符串的最长长度， 其中排序算作O（KlogK）。

空间复杂度：O（NK），用来存储结果。

### 类似的方法 - 字母出现次数的字符串表示

类似上一种方法，计数当前字符串中26个字母出现的次数，然后将这些次数写成字符串"1#2#0#0#0"的形式作为key加入map。

计数的时间复杂度OK，比排序的时间复杂度OKlogK看起来要快，测试却更慢（时间是原来的三倍）。

原文链接：https://leetcode-cn.com/problems/group-anagrams/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--16/

首先初始化 key = "0#0#0#0#0#"，数字分别代表 abcde 出现的次数，# 用来分割。

这样的话，"abb" 就映射到了 "1#2#0#0#0"。

"cdc" 就映射到了 "0#0#2#1#0"。

"dcc" 就映射到了 "0#0#2#1#0"。

Java：
```Java
public List<List<String>> groupAnagrams(String[] strs) {
    HashMap<String, List<String>> hash = new HashMap<>();
    for (int i = 0; i < strs.length; i++) {
        int[] num = new int[26];
        //记录每个字符的次数
        for (int j = 0; j < strs[i].length(); j++) {
            num[strs[i].charAt(j) - 'a']++;
        }
        //转成 0#2#2# 类似的形式
        String key = "";
        for (int j = 0; j < num.length; j++) {
            key = key + num[j] + '#';
        }
        if (hash.containsKey(key)) {
            hash.get(key).add(strs[i]);
        } else {
            List<String> temp = new ArrayList<String>();
            temp.add(strs[i]);
            hash.put(key, temp);
        }

    }
    return new ArrayList<List<String>>(hash.values());
}
```

my C++ code:
```C++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<string, vector<string>> wordmap;
        for(vector<string>::iterator iter=strs.begin();iter!=strs.end();++iter)
        {
            string tempstr = *iter;
            int count[26]={0};
            for(int i=0;i<tempstr.size();++i)
            {
                ++count[tempstr[i]-'a'];
            }
            string ct="";
            for(int i=0;i<26;++i)
            {
                ct+=(to_string(count[i])+"#");
            }
            wordmap[ct].push_back(*iter);
        }
        for(unordered_map<string, vector<string>>::iterator iter=wordmap.begin();iter!=wordmap.end();++iter)
        {
            res.push_back(iter->second);
        }
        return res;
    }
};
```
时间复杂度： O（nK），K 是字符串的最长长度。

空间复杂度：O（NK），用来存储结果。


### 算数基本定理

原文链接：https://leetcode-cn.com/problems/group-anagrams/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--16/

算术基本定理，又称为正整数的唯一分解定理，即：每个大于1的自然数，要么本身就是质数，要么可以写为2个以上的质数的积，而且这些质因子按大小排列之后，写法仅有一种方式方式。

利用这个，我们把每个字符串都映射到一个正数上。

用一个数组存储质数 prime = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103}。

然后每个字符串的字符减去 ' a ' ，然后取到 prime 中对应的质数。把它们累乘。

例如 abc ，就对应 'a' - 'a'， 'b' - 'a'， 'c' - 'a'，即 0, 1, 2，也就是对应素数 2 3 5，然后相乘 2 * 3 * 5 = 30，就把 "abc" 映射到了 30。

Java：
```Java
public List<List<String>> groupAnagrams(String[] strs) {
    HashMap<Integer, List<String>> hash = new HashMap<>();
    //每个字母对应一个质数
    int[] prime = { 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103 };
    for (int i = 0; i < strs.length; i++) {
        int key = 1;
        //累乘得到 key
        for (int j = 0; j < strs[i].length(); j++) {
            key *= prime[strs[i].charAt(j) - 'a'];
        } 
        if (hash.containsKey(key)) {
            hash.get(key).add(strs[i]);
        } else {
            List<String> temp = new ArrayList<String>();
            temp.add(strs[i]);
            hash.put(key, temp);
        }

    }
    return new ArrayList<List<String>>(hash.values());
}
```

my C++ code：

```C++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<long long int, vector<string>> wordmap;
        int prime[26]={2,3,5,7,11,13,17,19,23,29,31,37,41,43,47,53,59,61,67,71,73,79,83,89,97,101};
        for(vector<string>::iterator iter=strs.begin();iter!=strs.end();++iter)
        {
            string tempstr = *iter;
            long long int key=1;
            for(int i=0;i<tempstr.size();++i)
            {
                key*=prime[tempstr[i]-'a'];
            }
            wordmap[key].push_back(*iter);
        }
        for(unordered_map<long long int, vector<string>>::iterator iter=wordmap.begin();iter!=wordmap.end();++iter)
        {
            res.push_back(iter->second);
        }
        return res;
    }
};
```
学习一下思路，我跑的时候爆栈了

“Line 13: Char 20: runtime error: signed integer overflow: 67 * 396721246575877553 cannot be represented in type 'long long int' (solution.cpp)”

时间复杂度：O（n * K），K 是字符串的最长长度。

空间复杂度：O（NK），用来存储结果。
