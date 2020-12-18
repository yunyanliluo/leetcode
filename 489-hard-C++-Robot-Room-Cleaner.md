# solution

dfs 递归

dfs是从某个位置出发，向其余三个方向行进（使用hashmap来记录来时经过的点，排除来时方向），clean完毕回到这个位置，并且维持方向不变

使用hashmap记录每个经过的点

自定义class Point

unordered_set<Point>报错：因为哈希表没有自定义Point的hash函数，需要自己定义
    
建议使用两维哈希表:unordered_map<int, unordered_set<int>>避免上述问题
    
注意 机器人需要回溯时必须move回去 不能单纯point赋值

注意 未知地图大小不能使用二维数组/vector，使用哈希表解决

注意 位置需要绝对位置记录 方向也需要绝对方向记录
    
```
/**
 * // This is the robot's control interface.
 * // You should not implement it, or speculate about its implementation
 * class Robot {
 *   public:
 *     // Returns true if the cell in front is open and robot moves into the cell.
 *     // Returns false if the cell in front is blocked and robot stays in the current cell.
 *     bool move();
 *
 *     // Robot will stay in the same cell after calling turnLeft/turnRight.
 *     // Each turn will be 90 degrees.
 *     void turnLeft();
 *     void turnRight();
 *
 *     // Clean the current cell.
 *     void clean();
 * };
 */

class Solution {
public:
    class Dir {
        public:
            int x;
            int y;
            Dir(): x(0), y(0) {}
            Dir(int X, int Y): x(X), y(Y) {}
    };
    
    void dfs(Robot& robot, Dir p, Dir dir, unordered_map<int, unordered_set<int>>& uset) {
        Dir position = p;
        unordered_map<int, unordered_set<int>>::iterator iter = uset.find(position.x);
        if (iter != uset.end()) {
            unordered_set<int>& st = iter->second;
            if (st.find(position.y) != st.end()) {
                return;
            } else {
                st.insert(position.y);
            }
        } else {
            unordered_set<int> st;
            st.insert(position.y);
            uset.insert(make_pair(position.x,st));
        }
        
        robot.clean();
        // cout<<position.x<<" "<<position.y<<endl;
        Dir d = dir;
        for(int i=0; i<4; ++i) {
            robot.turnRight();
            if (d.x==1&&d.y==0) {
                d.x = 0;
                d.y = 1;
            }
            else if (d.x==0&&d.y==1) {
                d.x = -1;
                d.y = 0;
            }
            else if (d.x==-1&&d.y==0) {
                d.x = 0;
                d.y = -1;
            }
            else {
                d.x = 1;
                d.y = 0;
            }
            // 向d方向move dfs
            if (robot.move()) {
                position.x = p.x + d.x;
                position.y = p.y + d.y;
                
                dfs(robot, position, d, uset);
                robot.turnRight();
                robot.turnRight();
                robot.move();
                robot.turnRight();
                robot.turnRight();
                
            }
                
        }
    }
    void cleanRoom(Robot& robot) {
        unordered_map<int, unordered_set<int>> uset;
        Dir p(0,0);
        Dir d(-1,-1);
        dfs(robot, p, d, uset);
        return;
    }
    
};
```
