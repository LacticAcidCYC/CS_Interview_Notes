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



## 4. LeetCode 460 [LFU Cache](https://leetcode.com/problems/lfu-cache/)

[huahua-video](https://zxi.mytechroad.com/blog/hashtable/leetcode-460-lfu-cache/)

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
        if (it == _mp.cend()) return -1;
        use(it->second);
        return it->second.val;
    }
    
    void put(int key, int value) {
        if (_capacity == 0) return;
        
        auto it = _mp.find(key);
        if (it != _mp.cend()) {
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
struct CacheNode {
    int key;
    int val;
    int freq;
    
    // pointer to the node in the list
    list<int>::const_iterator it;
};

class LFUCache {
public:
    LFUCache(int capacity) : _capacity(capacity), _min_freq(0) {}
    
    int get(int key) {
        auto it = _mp.find(key);
        if (it == _mp.cend()) return -1;
        use(it->second);
        return it->second.val;
    }
    
    void put(int key, int value) {
        if (_capacity == 0) return;
        
        auto it = _mp.find(key);
        if (it != _mp.cend()) {
            // key already exists, update the value and call use()
            it->second.val = value;
            use(it->second);
            return;
        }
        
        if (_mp.size() == _capacity) {
            // reach capacity, need to remove a node which
            // (1) has the lowest freq
            // (2) least recently used if there are multiple ones
            
            const int key_to_remove = _freqMP[_min_freq].back();
            _freqMP[_min_freq].pop_back();
            _mp.erase(key_to_remove);
        }
        
        // new item has freq of 1, thus _min_freq needs to be set to 1
        const int freq = 1;
        _min_freq = freq;
        
        // Add the key to freq list
        _freqMP[freq].push_front(key);
        
        // create a new node
        _mp[key] = {key, value, freq, _freqMP[freq].cbegin()};
    }
    
private:
    void use(CacheNode& node) {
        // update the frequency
        const int prev_freq = node.freq;
        const int cur_freq = ++(node.freq);
        
        // remove the entry from old freq list
        _freqMP[prev_freq].erase(node.it);
        
        if (_freqMP[prev_freq].empty() && prev_freq == _min_freq) {
            _min_freq++;
        }
        
        // insert the key into the front of the new freq list
        _freqMP[cur_freq].push_front(node.key);
        
        // update the pointer of node
        node.it = _freqMP[cur_freq].cbegin();
    }
    
    int _capacity;
    int _min_freq;
    
    // key -> CacheNode
    unordered_map<int, CacheNode> _mp;
    
    // freq -> keys with freq
    unordered_map<int, list<int>> _freqMP;
    
};

/**
 * Your LFUCache object will be instantiated and called as such:
 * LFUCache obj = new LFUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```



## 5. LeetCode 359 [Logger Rate Limiter](https://leetcode.com/problems/logger-rate-limiter/)

### Hash Table

```c++
class Logger {
public:
    /** Initialize your data structure here. */
    Logger() {}
    
    /** Returns true if the message should be printed in the given timestamp, otherwise returns false.
        If this method returns false, the message will not be printed.
        The timestamp is in seconds granularity. */
    bool shouldPrintMessage(int timestamp, string message) {
        if (messages.count(message)) {
            if (timestamp - messages[message] < 10) {
                return false;
            }   
        }
        messages[message] = timestamp;
        return true;
    }
    
private:
    unordered_map<string, int> messages;
};

/**
 * Your Logger object will be instantiated and called as such:
 * Logger obj = new Logger();
 * bool param_1 = obj.shouldPrintMessage(timestamp,message);
 */
```



## 6. LeetCode 362 [Design Hit Counter](https://leetcode.com/problems/design-hit-counter/)

```c++
class HitCounter {
public:
    /** Initialize your data structure here. */
    HitCounter() : times(300), hits(300) {}
    
    /** Record a hit.
        @param timestamp - The current timestamp (in seconds granularity). */
    void hit(int timestamp) {
        int index = timestamp % 300;
        if (times[index] != timestamp) {
            times[index] = timestamp;
            hits[index] = 1;
        } else {
            hits[index]++;
        }
    }
    
    /** Return the number of hits in the past 5 minutes.
        @param timestamp - The current timestamp (in seconds granularity). */
    int getHits(int timestamp) {
        int total = 0;
        for (int i = 0; i < 300; i++) {
            if (timestamp - times[i] < 300) {
                total += hits[i];
            }
        }
        return total;
    }
    
private:
    vector<int> times;
    vector<int> hits;
};

/**
 * Your HitCounter object will be instantiated and called as such:
 * HitCounter obj = new HitCounter();
 * obj.hit(timestamp);
 * int param_2 = obj.getHits(timestamp);
 */
```



## 7. LeetCode 716 [Max Stack](https://leetcode.com/problems/max-stack/)

```c++
class MaxStack {
public:
    /** initialize your data structure here. */
    MaxStack() {}
    
    void push(int x) {
        int mx = maxStack.empty() ? x : maxStack.top();
        maxStack.push(mx > x ? mx : x);
        helperStack.push(x);
    }
    
    int pop() {
        maxStack.pop();
        int top = helperStack.top();
        helperStack.pop();
        return top;
    }
    
    int top() {
        return helperStack.top();
    }
    
    int peekMax() {
        return maxStack.top();
    }
    
    int popMax() {
        int mx = peekMax();
        stack<int> buff;
        while (top() != mx) {
            buff.push(pop());
        }
        pop();
        while (!buff.empty()) {
            push(buff.top());
            buff.pop();
        }
        
        return mx;
    }
    
private:
    stack<int> helperStack;
    stack<int> maxStack;
};

/**
 * Your MaxStack object will be instantiated and called as such:
 * MaxStack obj = new MaxStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.peekMax();
 * int param_5 = obj.popMax();
 */
```

















