# LRU缓存机制
设计和实现一个  LRU (最近最少使用) 缓存机制。它应该支持以下操作： 获取数据 get 和 写入数据 put 。

获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。

写入数据 put(key, value) - 如果密钥不存在，则写入其数据值。当缓存容量达到上限时，它应该在写入新数据之前删除最近最少使用的数据值，从而为新的数据值留出空间。

进阶:

你是否可以在 O(1) 时间复杂度内完成这两种操作？

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/lru-cache

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 哈希表+双向链表
使用C++数据结构 unordered_map和list

put和get都是O（1）。空间O（capacity）。

C++ code
```
class LRUCache {
public:
    class Value {
    public:
        Value(int val, list<int>::iterator node): value(val), key_node(node) {}
        list<int>::iterator getNode() {return key_node;} //必须使用类方法处理私有成员的读取和赋值
        int getValue() {return value;}
        void setValue(int val) {value=val;}
    private:
        int value;
        list<int>::iterator key_node;
    };
    LRUCache(int capacity) {
        size=0;
        cap=capacity;
    }
    
    int get(int key) {
        unordered_map<int, Value>::iterator iter=key_map.find(key);
        if(iter==key_map.end()) return -1;
        key_list.splice(key_list.begin(), key_list, iter->second.getNode()); //list的replace方法，参数分别是插入位置，list和待移动节点迭代器
        return iter->second.getValue();
    }
    
    void put(int key, int value) {
        unordered_map<int, Value>::iterator iter=key_map.find(key);
        if(iter==key_map.end()) {
            if (size==cap) { //如果cache放满了
                key_map.erase(key_list.back()); //erase方法参数是key
                key_list.pop_back(); //list的尾部处理方法
                --size;
            }
            key_list.push_front(key); //list的头部处理方法
            Value val(value, key_list.begin());
            key_map.insert(pair<int, Value>(key, val)); //这里不能使用key_map[key]=val的方法插入
            ++size;
        }
        else {
            key_list.splice(key_list.begin(), key_list, iter->second.getNode());
            iter->second.setValue(value);
        }
    }
private:
    unordered_map<int, Value> key_map;
    list<int> key_list;
    int size;
    int cap;
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
 ```
 执行用时 :
92 ms
, 在所有 C++ 提交中击败了
100.00%
的用户

内存消耗 :
38.2 MB
, 在所有 C++ 提交中击败了
69.17%
的用户
