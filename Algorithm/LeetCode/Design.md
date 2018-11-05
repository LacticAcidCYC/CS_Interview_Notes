# Data Structure Design

## 1. LeetCode 251

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



## 2. LeetCode 933

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

