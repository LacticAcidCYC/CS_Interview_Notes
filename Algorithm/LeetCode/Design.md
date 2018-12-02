# Data Structure Design

## 1. LeetCode 251 [Flatten 2D Vector](https://leetcode.com/problems/flatten-2d-vector/)

### (1) Using C++ iterators

```c++
class Vector2D {
private:
    vector<vector<int>>::iterator i, iEnd;
    int j = 0;
    
public:
    Vector2D(vector<vector<int>>& vec2d) {
        i = vec2d.begin();
        iEnd = vec2d.end();
    }

    int next() {
        hasNext(); // It's used when user keep calling next() without hasNext()
        return (*i)[j++];
    }

    bool hasNext() {
        while (i != iEnd && j == (*i).size()) {
            i++;
            j = 0;
        }
        return i != iEnd;
    }
};

/**
 * Your Vector2D object will be instantiated and called as such:
 * Vector2D i(vec2d);
 * while (i.hasNext()) cout << i.next();
 */
```

[solution](https://leetcode.com/problems/flatten-2d-vector/discuss/67652/7-9-lines-added-Java-and-C%2B%2B-O(1)-space.)

[Spliterators](http://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/java/util/Spliterators.java#l679)



### (2) Using a vector

```c++
class Vector2D {
    vector<int> vec;
    int i;
    int j;
public:
    Vector2D(vector<vector<int>>& vec2d) {
        for(i = 0; i < vec2d.size(); i++){
            for(j = 0; j < vec2d[i].size();j++){
                vec.push_back(vec2d[i][j]);
            }
        }
        i = 0;
    }

    int next() {
        int res = vec[i++];
        return res;
    }

    bool hasNext() {
        return i < vec.size();
    }
};

/**
 * Your Vector2D object will be instantiated and called as such:
 * Vector2D i(vec2d);
 * while (i.hasNext()) cout << i.next();
 */
```



## 2. LeetCode 933 [Number of Recent Calls](https://leetcode.com/problems/number-of-recent-calls/)

### (1) Deque

```c++
class RecentCounter {
public:
    RecentCounter() {
        
    }
    
    int ping(int t) {
        while (!dq.empty()) {
            int min = dq.back();
            if (t - min > 3000) {
                dq.pop_back();
            } else {
                break;
            }
        }
        dq.push_front(t);
        return dq.size();
    }
private:
    deque<int> dq;
};

/**
 * Your RecentCounter object will be instantiated and called as such:
 * RecentCounter* obj = new RecentCounter();
 * int param_1 = obj->ping(t);
 */
```



### (2) vector + two pointers

```c++
class RecentCounter {
    // max capicity should >= 3000 + 1
    // however, due to the implementation of ping(),
    // it's possible that there are 3002 elements in the queue
    // so the min size of queue is 3002
    static const int POOL_SIZE = 3002;
    int pings[POOL_SIZE];
    int st;
    int ed;
    int size;
    
public:
    RecentCounter() {
        st = 0;
        ed = 0;
        size = 0;
    }
    
    int ping(int t) {
        pings[ed++] = t;
        ed %= POOL_SIZE;
        size ++;

        while (pings[st] < t - 3000) {
            st = (++st) % POOL_SIZE;
            size --;
        }
        return size;
    }
};
```



### (3) Queue

```c++
class RecentCounter {
public:
    RecentCounter() {
        
    }
    
    int ping(int t) {
        q.push(t);
        while (q.front() < t - 3000) {
            q.pop();
        }
        return q.size();
    }
    
private:
    queue<int> q;
};

/**
 * Your RecentCounter object will be instantiated and called as such:
 * RecentCounter* obj = new RecentCounter();
 * int param_1 = obj->ping(t);
 */
```



## 3. LeetCode 146 [LRU Cache](https://leetcode.com/problems/lru-cache/)

### List

[cplusplus](http://www.cplusplus.com/reference/list/list/?kw=list)

```c++
class LRUCache {
public:
    LRUCache(int capacity) : _capacity(capacity) {}
    
    int get(int key) {
        auto it = cache.find(key);
        if (it == cache.end()) return -1;
        use(it);
        return it->second.first;
    }
    
    void put(int key, int value) {
        auto it = cache.find(key);
        if (it != cache.end()) {
            use(it);
        } else {
            if (cache.size() == _capacity) {
                cache.erase(used.back());
                used.pop_back();
            }
            used.push_front(key);
        }
        cache[key] = {value, used.begin()};
    }
    
private:
    typedef list<int> LI; // double-linked list
    typedef pair<int, LI::iterator> PIIT;
    typedef unordered_map<int, PIIT> HIPIIT;
    
    void use(HIPIIT::iterator it) {
        int key = it->first;
        used.erase(it->second.second);
        used.push_front(key);
        it->second.second = used.begin();
    }
    
    LI used;
    HIPIIT cache;
    int _capacity;
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

[solution](https://leetcode.com/problems/lru-cache/discuss/45976/C%2B%2B11-code-74ms-Hash-table-%2B-List)



## 4. LeetCode 460

### (1) Hashmap + Balanced Search Tree(Set in C++) (O(logk) + O(logk))

```c++
struct CacheNode {
    int key;
    int val;
    int freq;
    long tick;
    
    bool operator <(const CacheNode &rhs) const {
        if (freq < rhs.freq) {
            return true;
        } else if (freq == rhs.freq) {
            return tick < rhs.tick;
        } else {
            return false;
        }
    }
};

class LFUCache {
public:
    LFUCache(int capacity) : _capacity(capacity), _tick(0) {}
    
    int get(int key) {
        auto it = _mp.find(key);
        if (it == _mp.end()) return -1;
        use(it->second);
        return it->second.val;
    }
    
    void put(int key, int value) {
        if (_capacity == 0) return;
        
        auto it = _mp.find(key);
        if (it != _mp.end()) {
            it->second.val = value;
            use(it->second);
            return;
        } else {
            if (_cache.size() == _capacity) {
                const CacheNode &lfu = *_cache.cbegin();
                _mp.erase(lfu.key);
                _cache.erase(lfu);
            }
            
            CacheNode node{key, value, 1, ++_tick};
            _mp[key] = node;
            _cache.insert(node);
        }
    }

private:
    void use(CacheNode &node) {
        _cache.erase(node);
        node.freq++;
        node.tick = ++_tick;
        _cache.insert(node);
    }
    
    unordered_map<int, CacheNode> _mp; // key <=> Node
    set<CacheNode> _cache; // used for sorting the nodes
    
    long _tick; // time counter
    int _capacity; // capacity
};
/**
 * Your LFUCache object will be instantiated and called as such:
 * LFUCache obj = new LFUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

[solution](https://leetcode.com/problems/lfu-cache/discuss/94516/Concise-C%2B%2B-O(1)-solution-using-3-hash-maps-with-explanation)



### (2) Hashmap + Doubly-linked List (O(1) + O(1))

```c++

```



















