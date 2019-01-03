# Binary Search Tree

## set & map (in C++)

[set-reference](http://www.cplusplus.com/reference/set/set/)

[map-reference](http://www.cplusplus.com/reference/map/map/)



## 1. LeetCode 729 [My Calendar I](https://leetcode.com/problems/my-calendar-i/)

### (1) Brute Force

### Intuition

Store all the booking event in a vector of pairs;

When a new event comes, check every existing events for overlap

```c++
// Time Complexity: O(n^2) (for n booking)
// Space Complexity: O(n)

class MyCalendar {
public:
    MyCalendar() {
        
    }
    
    // O(n)
    bool book(int start, int end) {
        for (auto &p : events) {
            if (max(p.first, start) < min(p.second, end)) return false;
        }
        events.push_back({start, end});
        return true;
    }
    
private:
    vector<pair<int, int>> events; 
};

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar obj = new MyCalendar();
 * bool param_1 = obj.book(start,end);
 */
```



### (2) Binary Search Tree (set / map)

Keep the intervals sorted.

Everytime a new event comes, check the interval started right after and right before the new event.

If there is overlap, then return false.

**TreeMap:**

```c++
// Time Complexity: O(nlogn) (for n booking)
// Space Complexity: O(n)

class MyCalendar {
public:
    MyCalendar() {
        
    }
    
    // O(logn)
    bool book(int start, int end) {
        // the first element in the container whose key is not considered 
        // to go before k (i.e., either it is equivalent or goes after).
        auto it = events.lower_bound(start); 
        if (it != events.end() && it->first < end) return false;
        if (it != events.begin() && (--it)->second > start) return false;
        events[start] = end;
        return true;
    }
    
private:
    map<int, int> events;
};

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar obj = new MyCalendar();
 * bool param_1 = obj.book(start,end);
 */
```

**TreeSet:**

```c++
class MyCalendar {
public:
    MyCalendar() {
        
    }
    
    bool book(int start, int end) {
        auto it = events.lower_bound({start, end});
        if (it != events.end() && it->first < end) return false;
        if (it != events.begin() && (--it)->second > start) return false;
        events.insert({start, end});
        return true;
    }
    
private:
    set<pair<int, int>> events;
};

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar obj = new MyCalendar();
 * bool param_1 = obj.book(start,end);
 */
```



## 2. LeetCode 731 [My Calendar II](https://leetcode.com/problems/my-calendar-ii/)

### (1) Brute Force

Reuse the class in MyCalendar (leetcode 729), use it as the overlap records;

Everytime a new event comes, find all the overlapped intervals, and check whether there are two among them overlap.

```c++
class MyCalendar {
public:
    MyCalendar() {}
    
    // O(logn)
    bool book(int start, int end) {
        auto it = events.lower_bound({start, end});
        if (it != events.end() && it->first < end) return false;
        if (it != events.begin() && (--it)->second > start) return false;
        events.insert({start, end});
        return true;
    }
    
private:
    set<pair<int, int>> events;
};

class MyCalendarTwo {
public:
    MyCalendarTwo() {}
    
    // O(nlogn)
    bool book(int start, int end) {
        MyCalendar overlaps;
        for (auto &p : events) {
            if (max(p.first, start) < min(p.second, end)) {
                // overlap exists
                pair<int, int> overlap = {max(p.first, start), min(p.second, end)};
                // check whether this overlap interval is overlapped with other intervals in overlaps
                if (!overlaps.book(overlap.first, overlap.second)) return false;
            }
        }
        events.push_back({start, end});
        return true;
    }

private:
    vector<pair<int, int>> events;
};

/**
 * Your MyCalendarTwo object will be instantiated and called as such:
 * MyCalendarTwo obj = new MyCalendarTwo();
 * bool param_1 = obj.book(start,end);
 */
```



### (2) Binary Search Tree (Boundary Count)

The idea is very simple. First step, add the interval into map. Second step, check if there exists triple book in the map.

For first step, it increments entrance by 1, and decrements exit by 1.
For second step, it traverses from leftmost to rightmost, by the sorting property of map. The `booked` keeps track of the number of overlapped intervals.

For example, when an index `i` does not belong to any interval, booked should be 0. When `i` belong to one interval, booked should be equal to this interval's `map[start]`value (i.e. 1), and when `i` continues moving rightward, booked will be become zero after `i` is out of the interval. When `i` belongs to `k` intervals, booked should be equal to `k`, as there were `k` `start`'s before `i`.

Entrance-Increment-Exit-Decrement is a typical way to solve interval problem. Another typical way is using binary search for a sorted interval array.

```c++
class MyCalendarTwo {
public:
    MyCalendarTwo() {
        
    }
    
    // O(n)
    bool book(int start, int end) {
        boundaries[start]++;
        boundaries[end]--;
        int booked = 0;
        
        for (auto &p : boundaries) {
            booked += p.second;
            if (booked > 2) {
                boundaries[start]--;
                boundaries[end]++;
                return false;
            }
        }
        
        return true;
    }
    
private:
    map<int, int> boundaries;
};

/**
 * Your MyCalendarTwo object will be instantiated and called as such:
 * MyCalendarTwo obj = new MyCalendarTwo();
 * bool param_1 = obj.book(start,end);
 */
```

[solution](https://leetcode.com/problems/my-calendar-ii/discuss/109522/Simplified-winner's-solution)



## 3. LeetCode 732 [My Calendar III](https://leetcode.com/problems/my-calendar-iii/)

Idea is the same as 731 (2)

```c++
class MyCalendarThree {
public:
    MyCalendarThree() {}
    
    int book(int start, int end) {
        boundaries[start]++;
        boundaries[end]--;
        
        int booked = 0;
        int max_overlap = 1;
        
        for (auto &p : boundaries) {
            booked += p.second;
            max_overlap = max(booked, max_overlap);
        }
        
        return max_overlap;
    }
    
private:
    map<int, int> boundaries;
};

/**
 * Your MyCalendarThree object will be instantiated and called as such:
 * MyCalendarThree obj = new MyCalendarThree();
 * int param_1 = obj.book(start,end);
 */
```



## 4. LeetCode 683 [K Empty Slots](https://leetcode.com/problems/k-empty-slots/)

```c++
class Solution {
public:
    int kEmptySlots(vector<int>& flowers, int k) {
        int n = flowers.size();
        if (k > n-2) return -1;
        set<int> ts;
        ts.insert(0);
        ts.insert(n+1);
        
        for (int i=0; i<flowers.size(); i++) {
            auto it = ts.lower_bound(flowers[i]);
            if (*it != n+1 && *it - flowers[i] - 1 == k) {
                return i + 1;
            }
            it--;
            if (*it != 0 && flowers[i] - *it - 1 == k) {
                return i + 1;
            }
            ts.insert(flowers[i]);
        }
        
        return -1;
    }
};
```



## 5. LeetCode 853 [Car Fleet](https://leetcode.com/problems/car-fleet/)

### (1) TreeMap

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(n)

class Solution {
public:
    int carFleet(int target, vector<int>& position, vector<int>& speed) {
        map<int, double, greater<int>> mp;
        int n = position.size();
        for (int i=0; i<n; i++) {
            mp[position[i]] =  (double)(target - position[i]) / speed[i];
        }
        
        int ans = 0;
        double cur_lowest = 0;
        for (auto &p : mp) {
            if (p.second > cur_lowest) {
                cur_lowest = p.second;
                ans++;
            }
        }
        
        return ans;
    }
};
```

[solution](https://leetcode.com/problems/car-fleet/discuss/139850/C%2B%2BJavaPython-Straight-Forward)



## 6. LeetCode 846 [Hand of Straights](https://leetcode.com/problems/hand-of-straights/)

```c++
class Solution {
public:
    bool isNStraightHand(vector<int>& hand, int W) {
        if (hand.size() % W != 0) return false;
        
        map<int, int> cards;
        
        for (auto card : hand) {
            cards[card]++;
        }
        
        for (auto it = cards.begin(); it != cards.end(); ) {
            if (it->second == 0) {
                it++;
                continue;
            }
            
            it->second--;
            
            for (int i=1; i<W; i++) {
                int next = it->first + i;
                
                if (cards.find(next) == cards.end() || cards[next] == 0) {
                    return false;
                }
                cards[next]--;
            }
        }
        
        return true;
    }
};
```

[solution](https://leetcode.com/problems/hand-of-straights/discuss/135598/C%2B%2BJavaPython-O(MlogM)-Complexity)





































