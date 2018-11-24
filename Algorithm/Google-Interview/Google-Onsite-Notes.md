## 1. LeetCode 134 [Gas Station](https://leetcode.com/problems/gas-station/)

### Greedy

```c++
// Time Complexity: O(N)
// Space Complexity: O(1)

class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int n = gas.size();
        int end = 0, start = n - 1;
        int sum = gas[start] - cost[start];
        while (start > end) {
            if (sum >= 0) {
                sum += gas[end] - cost[end];
                end++;
            } else {
                start--;
                sum += gas[start] - cost[start];
            }
        }
        return sum < 0 ? -1 : start;
    }
};
```



## 2. LeetCode 162 [Find Peak Element](https://leetcode.com/problems/find-peak-element/)

### Binary Search

```c++
// Time Complexity: O(logN)
// Space Complexity: O(1)

class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int n = (int) nums.size();
        if (n == 1) return 0;
        if (nums[1] < nums[0]) {
            return 0;
        } else if (nums[n-2] < nums[n-1]) {
            return n-1;
        }
        int l = 1, r = n - 2;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (nums[mid] > nums[mid-1] && nums[mid] > nums[mid+1]) {
                return mid;
            } else if (nums[mid] <= nums[mid-1]) {
                r = mid;
            } else {
                l = mid + 1;
            }
        }
        return l;
    }
};
```



## 3. LeetCode 205 [Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/)

```c++
// Time Complexity: O(N)
// Space Complexity: O(N)

// 2 hashmap
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        unordered_map<char, char> mp;
        unordered_map<char, char> reverse_mp;
        int n = s.length();
        
        for (int i=0; i<n; i++) {
            if (!mp.count(s[i]) && !reverse_mp.count(t[i])) {
                mp[s[i]] = t[i];
                reverse_mp[t[i]] = s[i];
            } else {
                if (mp[s[i]] != t[i] || reverse_mp[t[i]] != s[i]) {
                    return false;
                }
            }
        }
        
        return true;
    }
};

// better
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        vector<int> mp1(256, -1);
        vector<int> mp2(256, -1);
        int n = s.length();
        
        for (int i=0; i<n; i++) {
            if (mp1[s[i]] != mp2[t[i]]) return false;
            mp1[s[i]] = i;
            mp2[t[i]] = i;
        }
        
        return true;
    }
};
```



## 4. LeetCode 249 [Group Shifted Strings](https://leetcode.com/problems/group-shifted-strings/)

```c++
// Time Complexity: O(NK)
// Space Complexity: O(NK)
// where N is the length of strs, and K is the maximum length of a string in strs.

class Solution {
public:
    vector<vector<string>> groupStrings(vector<string>& strings) {
        if (strings.empty()) return {};
        
        vector<vector<string>> res;
        unordered_map<string, vector<string>> mp;
        
        for (auto &s : strings) {
            auto key = s;
            int shift = key[0] - 'a';
            for (auto &c : key) {
                c = (c - shift) < 'a' ? (c - shift + 26) : c - shift;
            }
            mp[key].push_back(s);
        }
        
        for (auto &p : mp) {
            res.push_back(p.second);
        }
        
        return res;
    }
};
```



### Related LeetCode 49

#### (1) Categorize by Sorted String

```c++
// Time Complexity: O(NKlogK)
// Space Complexity: O(NK)
// where N is the length of strs, and K is the maximum length of a string in strs. 

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        if (strs.empty()) return {};
        vector<vector<string>> res;
        
        unordered_map<string, vector<string>> mp;
        for (auto &s : strs) {
            auto key = s;
            sort(key.begin(), key.end());
            mp[key].push_back(s);
        }
        
        for (auto &p : mp) {
            res.push_back(p.second);
        }
        
        return res;
    }
};
```



#### (2) Categorize by Count

```c++
// Time Complexity: O(NK)
// Space Complexity: O(NK)
// Time Complexity: O(NK), where N is the length of strs, and K is the maximum length of a
// string in strs. Counting each string is linear in the size of the string, and we count
// every string.

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        if (strs.empty()) return {};
        vector<vector<string>> res;
        
        unordered_map<string, vector<string>> mp;
        for (auto &s : strs) {
            string key = "";
            vector<int> cnts(26, 0);
            for (auto &c : s) {
                cnts[c - 'a']++;
            }
            
            for (int i=0; i<26; i++) {
                key += ("#" + to_string(cnts[i]));
            }
            
            mp[key].push_back(s);
        }
        
        for (auto &p : mp) {
            res.push_back(p.second);
        }
        
        return res;
    }
};
```



## 5. LeetCode 253 [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)

### High Frequency

### (1) Min Heap

```c++
// Time Complexity: O(NlogN)
// Space Complexity: O(N)

/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    int minMeetingRooms(vector<Interval>& intervals) {
        sort(intervals.begin(), intervals.end(), [](Interval &a, Interval &b) {
            return a.start < b.start;
        });
        priority_queue<int, vector<int>, greater<int>> pq; // store the end time
        for (auto const &i : intervals) {
            if (!pq.empty()) {
                if (pq.top() <= i.start) {
                    pq.pop();
                }
            }
            pq.push(i.end);
        }
        return pq.size();
    }
};
```



### (2) Chronological Ordering

```c++
// Time Complexity: O(NlogN)
// Space Complexity: O(N)

/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    int minMeetingRooms(vector<Interval>& intervals) {
        vector<int> starts;
        vector<int> ends;
        
        for (auto const &i : intervals) {
            starts.push_back(i.start);
            ends.push_back(i.end);
        }
        sort(starts.begin(), starts.end());
        sort(ends.begin(), ends.end());
        
        int ps = 0, pe = 0; // ptr_start or ptr_end
        //int minRooms = 0;
        while (ps < intervals.size()) {
            if (ends[pe] <= starts[ps]) {
                pe++;
                //minRooms--;
            }
            ps++;
            //minRooms++;
        }
        return ps - pe;
    }
};
```

