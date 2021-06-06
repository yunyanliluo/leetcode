# 加油站

## brute force
假设圈长度n

一共有n个可能的起点，转一圈，经历n个加油站

所以遍历时最多经过n+n个加油站，即可判断

```C++
class Solution {
public:
    /**
     * @param gas: An array of integers
     * @param cost: An array of integers
     * @return: An integer
     */
    int canCompleteCircuit(vector<int> &gas, vector<int> &cost) {
        // write your code here
        // brute force
        int n = gas.size();
        int startindex = 0;
        int remain = 0;
        for (int i = 0; i < n + n; ++i) {
            remain += (gas[i % n] - cost[i % n]);
            if (remain < 0) {
                startindex = (i + 1) % n;
                remain = 0;
            }
            else if (i - startindex + 1 == n) {
                return startindex;
            }
        }
        return -1;
    }
};
```

## greedy
1.是否有解：如果总的gas，小于总的cost，直接返回-1

2.哪里有解：从index = 0开始找，当gas_remain的油箱低于0了就代表从index出发不可以,index += 1。
循环一遍即可,因为已经肯定了有答案，所有只要有一个i可以一直走到结束都可以保持油箱 > 0，那么它肯定是答案
```C++
class Solution {
public:
    /**
     * @param gas: An array of integers
     * @param cost: An array of integers
     * @return: An integer
     */
    int canCompleteCircuit(vector<int> &gas, vector<int> &cost) {
        // write your code here
        int n = gas.size();
        int sumgas = 0, sumcost = 0;
        int start = 0, remain = 0;
        for (int i = 0; i < n; ++i) {
            sumgas += gas[i];
            sumcost += cost[i];
            remain += (gas[i] - cost[i]);
            if (remain < 0) {
                start = i + 1;
                remain = 0;
            }
        }
        if (sumgas < sumcost) return -1;
        return start;
    }
};
```
