**特别鸣谢：**

[花花酱](https://www.youtube.com/user/xxfflower/)

------------------------------------------------------------------------------------------------------

**Onsite-面经：**

https://www.1point3acres.com/bbs/forum.php?mod=viewthread&tid=472493

------------------------------------------------------------------------------------------------------

**参考帖子：**

[google面经总结1](https://www.1point3acres.com/bbs/forum.php?mod=viewthread&tid=446944)

[google面经总结2](https://www.1point3acres.com/bbs/forum.php?mod=viewthread&tid=448608)

[**扫地机器人，Robot API、实现及总结**](https://www.1point3acres.com/bbs/thread-403845-1-1.html)

[Unique-Path总结](https://www.1point3acres.com/bbs/forum.php?mod=viewthread&tid=423857&extra=&page=1&_dsign=2a102aab)

[人车匹配](https://www.1point3acres.com/bbs/forum.php?mod=viewthread&tid=447853&ctid=201329)

------------------------------------------------------------------------------------------------------

**面经：**

https://drive.google.com/open?id=1G7fg6mghszqbeo0gVR1E8vOqo6K3ub0Q

https://drive.google.com/open?id=1PqHO9Hnkun5OoYbhqoAoc6P7xyBM3Uyp

https://drive.google.com/open?id=17WePTrYfMWNlhWyaPcf8PyjNQe9qmp46

https://drive.google.com/open?id=1rxLbgD8Scy-Zvg4MJ7ICQuN_3xjTtNQl

https://drive.google.com/open?id=1v1b_G2acMqKDv1zNUiUVpv0kP-UXevm4

------------------------------------------------------------------------------------------------------

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



## 6. LeetCode 307 [Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/)

### Intuition

We have an array arr[0 . . . n-1]. We should be able to
**1** Find the sum of first i elements.
**2** Change value of a specified element of the array arr[i] = x where 0 <= i <= n-1.

A **simple solution** is to run a loop from 0 to i-1 and calculate sum of elements. To update a value, simply do arr[i] = x. The first operation takes O(n) time and second operation takes O(1) time. Another simple solution is to create another array and store sum from start to i at the i’th index in this array. Sum of a given range can now be calculated in O(1) time, but update operation takes O(n) time now. This works well if the number of query operations are large and very few updates.

**Can we perform both the operations in O(log n) time once given the array?** 
One Efficient Solution is to use [Segment Tree](https://www.geeksforgeeks.org/segment-tree-set-1-sum-of-given-range/) that does both operations in O(Logn) time.

### (1) Segment Tree

```c++
// Time Complexity:
// construct tree: O(n)
// update: O(logn)
// sum of range: O(logn)
// Space Complexity: O(n)

class NumArray {
private:
    vector<int> st; // segment tree
    int N; // size of given array

    // A recursive function that constructs Segment Tree for nums[ss..se]. 
	// si is index of current node in segment tree st (start from 0(root))
    int constructSTUtil(vector<int>& nums, int ss, int se, vector<int> &st, int si) { 
        if (ss == se) 
           return (st[si] = nums[ss]); 

        int mid = ss + (se - ss) / 2;
        st[si] =  constructSTUtil(nums, ss, mid, st, si*2+1) + constructSTUtil(nums, mid+1, se, st, si*2+2); 
        return st[si]; 
    } 

    /* Function to construct segment tree from given array. This function 
    allocates memory for segment tree and calls constructSTUtil() to 
    fill the allocated memory */
    vector<int> constructST(vector<int>& nums) { 
        int n = nums.size();
        if (n == 0) {
            return {};
        }
        int x = (int)(ceil(log2(n))); //Height of segment tree 
        int max_size = 2*(int)pow(2, x) - 1; //Maximum size of segment tree 
        vector<int> st(max_size, 0); 

        constructSTUtil(nums, 0, n-1, st, 0); 

        return st; 
    }
    
    // A recursive function that updates the value in Segment Tree
    void updateUtil(vector<int> &st, int i, int val, int start, int end, int index) {
        if (st.empty()) return;
        if (start == end) {
            st[index] = val;
        } else {
            int mid = start + (end - start) / 2;
            if (i <= mid) {
                updateUtil(st, i, val, start, mid, index*2+1);
            } else {
                updateUtil(st, i, val, mid+1, end, index*2+2);
            }
            st[index] = st[index*2+1] + st[index*2+2];
        }
    }

    /*  A recursive function to get the sum of value in a given range of 
    indexes. The following are parameters for this function. 
  
    st    --> segment tree 
    index --> Index of current node in the segment tree. Initially 0 is 
              passed as root is always at index 0 
    ss & se  --> Starting and ending indexes of the segment represented by 
                 current node, i.e., st[index] 
    qs & qe  --> Starting and ending indexes of query range */
    int sumRangeUtil(vector<int>& st, int ss, int se, int qs, int qe, int index) 
    { 
        if (st.empty()) return 0;
        if (qs <= ss && qe >= se) 
            return st[index]; 
        
        if (se < qs || ss > qe) 
            return 0;

        int mid = ss + (se - ss) / 2;
        return sumRangeUtil(st, ss, mid, qs, qe, 2*index+1) + sumRangeUtil(st, mid+1, se, qs, qe, 2*index+2); 
    }
    
public:
    NumArray(vector<int> nums) {
        N = nums.size();
        st = constructST(nums); 
    }
    
    void update(int i, int val) {
        updateUtil(st, i, val, 0, N-1, 0);
    }
    
    int sumRange(int i, int j) {
        return sumRangeUtil(st, 0, N-1, i, j, 0);
    }
}; 

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(i,val);
 * int param_2 = obj.sumRange(i,j);
 */
```

[solution](https://leetcode.com/problems/range-sum-query-mutable/discuss/75724/17-ms-Java-solution-with-segment-tree)



### (2) Binary Index Tree (Fenwick Tree)

*Using Binary Indexed Tree, we can do both tasks in O(Logn) time. The advantages of Binary Indexed Tree over Segment are, requires less space and very easy to implement.*

**How does Binary Indexed Tree work?**
The idea is based on the fact that all positive integers can be represented as sum of powers of 2. For example 19 can be represented as 16 + 2 + 1. Every node of BI Tree stores sum of n elements where n is a power of 2. For example, sum of first 12 elements can be obtained by sum of last 4 elements (from 9 to 12) plus sum of 8 elements (from 1 to 8). The number of set bits in binary representation of a number n is O(Logn). Therefore, we traverse at-most O(Logn) nodes in both getSum() and update() operations. Time complexity of construction is O(nLogn) as it calls update() for all n elements.

**Representation**
Binary Indexed Tree is represented as an array. Let the array be BITree[]. Each node of Binary Indexed Tree stores sum of some elements of given array. Size of Binary Indexed Tree is equal to n where n is size of input array. In the below code, we have used size as n+1 for ease of implementation.

**Construction**
We construct the Binary Indexed Tree by first initializing all values in BITree[] as 0. Then we call update() operation for all indexes to store actual sums, update is discussed below.

**Operations**

Node at index 0 is a dummy node.

A node at index y is parent of a node at index x, iff y can be obtained by removing last set bit from binary representation of x.

e.g. 

7(0111) -> 6(0110) -> 4(0100)

```latex
getSum(index): Returns sum of arr[0..index]
// Returns sum of arr[0..index] using BITree[0..n].  It assumes that
// BITree[] is constructed for given array arr[0..n-1]
1) Initialize sum as 0 and index as index+1.
2) Do following while index is greater than 0.
...a) Add BITree[index] to sum
...b) Go to parent of BITree[index].  Parent can be obtained by removing
     the last set bit from index, i.e., index = index - (index & (-index))
3) Return sum.
```



The update process needs to make sure that all BITree nodes that have arr[i] as part of the section they cover must be updated. We get all such nodes of BITree by repeatedly adding the decimal number corresponding to the last set bit.

e.g. 

1(0001) -> 2(0010) -> 4(0100) -> 8(1000)...

5(0101) -> 6(0110) -> 8(1000)...

```latex
update(index, val): Updates BIT for operation arr[index] += val
// Note that arr[] is not changed here.  It changes
// only BI Tree for the already made change in arr[].
1) Initialize index as index+1.
2) Do following while index is smaller than or equal to n.
...a) Add value to BITree[index]
...b) Go to parent of BITree[index].  Parent can be obtained by removing
     the last set bit from index, i.e., index = index + (index & (-index))
```

Following are the implementations of Binary Indexed Tree.

```c++
// Time Complexity:
// construct tree: O(nlogn)
// update: O(logn)
// sum of range: O(logn)
// Space Complexity: O(n)

class NumArray {
private:
    vector<int> BITree;
    vector<int> _nums;
    
    void updateBIT(int index, int val) {
        index++;
        while (index < BITree.size()) {
            BITree[index] += val;
            index += (index & (-index));
        }
    }
    
    int getSum(int index) {
        int sum = 0;
        index++;
        while (index > 0) {
            sum += BITree[index];
            index -= (index & (-index));
        }
        return sum;
    }

public:
    NumArray(vector<int> nums) : _nums(nums) {
        if (nums.empty()) return;
        int n = nums.size();
        BITree.resize(n+1);
        for (int i=0; i<n; i++) {
            updateBIT(i, nums[i]);
        }
    }
    
    void update(int i, int val) {
        if (_nums.empty() || i < 0 || i >= _nums.size()) return;
        if (_nums[i] != val) {
            updateBIT(i, val - _nums[i]);
            _nums[i] = val;
        }
    }
    
    int sumRange(int i, int j) {
        if (_nums.empty() || i < 0 || i >= _nums.size() || j < 0 || j >= _nums.size()) return 0;
        return getSum(j) - getSum(i-1);
    }
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(i,val);
 * int param_2 = obj.sumRange(i,j);
 */
```

[solution](https://leetcode.com/problems/range-sum-query-mutable/discuss/75721/strongly-recommend-for-beginnersclean-C%2B%2B-implementation-with-detailed-explaination)

[geeksforgeeks](https://www.geeksforgeeks.org/binary-indexed-tree-or-fenwick-tree-2/)

[youtube](https://www.youtube.com/watch?v=WbafSgetDDk)

[other](https://stackoverflow.com/questions/41969429/why-the-bit-operation-i-i-equals-to-rightmost-bit)



## 7. LeetCode 308 [Range Sum Query 2D - Mutable](https://leetcode.com/problems/range-sum-query-2d-mutable/)

### Simple idea using prefix sum

Time Complexity:
construct tree: O(mn)
update: O(n)
sum of range: O(1)?
Space Complexity: O(mn)

### Binary Index Tree

Idea is the same as 307

```c++
// Time Complexity:
// construct tree: O(mnlogmlogn)
// update: O(logmlogn)
// sum of range: O(logmlogn)
// Space Complexity: O(mn)

class NumMatrix {
private:
    vector<vector<int>> bit;
    vector<vector<int>> _matrix;
    int m;
    int n;
    
    void updateBIT(int row, int col, int val) {
        row++;
        col++;
        for (int i=row; i<bit.size(); i += i & (-i)) {
            for (int j=col; j<bit[0].size(); j += j & (-j)) {
                bit[i][j] += val;
            }
        }
    }
    
    int getSum(int row, int col) {
        row++;
        col++;
        int sum = 0;
        for (int i=row; i>0; i -= i & (-i)) {
            for (int j=col; j>0; j -= j & (-j)) {
                sum += bit[i][j];
            }
        }
        
        return sum;
    }
    
public:
    NumMatrix(vector<vector<int>> matrix) : _matrix(matrix) {
        if (matrix.empty() || matrix[0].empty()) { return;}
        m = matrix.size();
        n = matrix[0].size();
        bit = vector<vector<int>>(m+1, vector<int>(n+1, 0));
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                updateBIT(i, j, matrix[i][j]);
            }
        }
    }
    
    void update(int row, int col, int val) {
        if (m == 0 || n == 0 || row < 0 || row >= m || col < 0 || col >= n) return;
        if (val != _matrix[row][col]) {
            updateBIT(row, col, val - _matrix[row][col]);
            _matrix[row][col] = val;
        }
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        if (m == 0 || n == 0 || row1 < 0 || row1 >= m || col1 < 0 || col1 >= n
           || row2 < 0 || row2 >= m || col2 < 0 || col2 >= n) return 0;
        return getSum(row2, col2) + getSum(row1-1, col1-1) - getSum(row1-1, col2) - getSum(row2, col1-1);
    }
};

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix obj = new NumMatrix(matrix);
 * obj.update(row,col,val);
 * int param_2 = obj.sumRegion(row1,col1,row2,col2);
 */
```

[solution](https://leetcode.com/problems/range-sum-query-2d-mutable/discuss/75870/Java-2D-Binary-Indexed-Tree-Solution-clean-and-short-17ms)



### Related Questions (LeetCode 315, 218, 493)

#### LeetCode 315 [Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/)

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(n)

class Solution {
private:
    vector<int> bit;
    
    void updateBIT(int number) {
        while (number < bit.size()) {
            bit[number]++;
            number += number & (-number);
        }
    }
    
    int getCnt(int number) {
        int cnt = 0;
        while (number > 0) {
            cnt += bit[number];
            number -= number & (-number);
        }
        
        return cnt;
    }
    
public:
    vector<int> countSmaller(vector<int>& nums) {
        if (nums.empty()) return {};
        int n = nums.size();
        vector<int> res(n);
        
        int min_val = INT_MAX; // use for avoiding minus value
        int max_val = INT_MIN; // use for constructing the binary index tree
        vector<int> _nums(n);
        
        for (auto &number : nums) {
            min_val = min(min_val, number);
            max_val = max(max_val, number);
        }
        
        for (int i=0; i<n; i++) {
            _nums[i] = nums[i] - min_val + 1;
        }
        
        bit.resize(max_val - min_val + 2);
        for (int i=n-1; i>=0; i--) {
            res[i] = getCnt(_nums[i]-1);
            updateBIT(_nums[i]);
        }
        
        return res;
    }
};
```

[solution](https://leetcode.com/problems/count-of-smaller-numbers-after-self/discuss/76611/Short-Java-Binary-Index-Tree-BEAT-97.33-With-Detailed-Explanation)



## 8. LeetCode 340 [Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)

```c++
class Solution {
public:
    int lengthOfLongestSubstringKDistinct(string s, int k) {
        int len = s.length();
        if (k >= len) return len;
        unordered_map<char, int> map;
        int left = 0, right = 0, maxLen = 0, cnt = 0;
        while (right < len) {
            if (map[s[right++]]++ == 0) {
                cnt++;
            }
            while (cnt > k) {
                if (map[s[left++]]-- == 1) {
                    cnt--;
                }
            }
            maxLen = max(maxLen, right-left);
        }
        return maxLen;
    }
};
```



## 9. LeetCode 359 [Logger Rate Limiter](https://leetcode.com/problems/logger-rate-limiter/)

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



## 10. LeetCode 399 [Evaluate Division](https://leetcode.com/problems/evaluate-division/)

```c++
// E: number of edges(equations)
// Q: number of queries
// Time Complexity: O(E + Q)
// Space Complexity: O(E)

class Solution {
public:
    vector<double> calcEquation(vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries) {
        unordered_map<string, pair<string, double>> parents;
        
        for (int i=0; i<equations.size(); i++) {
            const string& A = equations[i].first;
            const string& B = equations[i].second;
            const double k = values[i];
            
            if (parents.count(A) && parents.count(B)) {
                auto& rA = find(A, parents);
                auto& rB = find(B, parents);
                parents[rA.first] = {rB.first, k * rB.second / rA.second}; 
            } else if (parents.count(A)) {
                parents[B] = {A, 1.0 / k};
            } else if (parents.count(B)) {
                parents[A] = {B, k};
            } else {
                parents[A] = {B, k};
                parents[B] = {B, 1.0};
            }
        }
        
        vector<double> ans;
        for (const auto& query : queries) {
            const string& X = query.first;
            const string& Y = query.second;
            if (!parents.count(X) || !parents.count(Y)) {
                ans.push_back(-1.0);
                continue;
            }
            
            auto& rX = find(X, parents);
            auto& rY = find(Y, parents);
            if (rX.first != rY.first) {
                ans.push_back(-1.0);
            } else {
                ans.push_back(rX.second / rY.second);
            }
        }
        
        return ans;
    }
    
private:
    pair<string, double>& find(const string& X, unordered_map<string, pair<string, double>> &parents) {
        if (X != parents[X].first) {
            const auto& p = find(parents[X].first, parents);
            parents[X].first = p.first;
            parents[X].second *= p.second;
        }
        return parents[X];
    }
};
```



## 11. LeetCode 418 [Sentence Screen Fitting](https://leetcode.com/problems/sentence-screen-fitting/)

```c++
class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        string _sentence = "";
        for (string &word : sentence) {
            _sentence += word;
            _sentence += " ";
        }
        //_sentence.pop_back();
        
        int start = 0;
        int l = _sentence.size();
        int res = 0;
        
        while (rows > 0) {
            rows--;
            //cout << _sentence[start] << endl;
            if (start + cols >= l) {
                res += (start + cols) / l;
            }
            start = (start + cols) % l;
            if (_sentence[start] == ' ') {
                if (start == l - 1) res++; // if include the last word!!
                start = (start + 1) % l;
            } else {
                while (start >= 0 && _sentence[start] != ' ') {
                    start--;
                }
                start++;
            }
        }
        
        return res;
    }
};

// improved version
class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        string _sentence = "";
        for (string &word : sentence) {
            _sentence += word;
            _sentence += " ";
        }
        
        int start = 0;
        int l = _sentence.size();
        int res = 0;
        
        while (rows > 0) {
            rows--;
            int nxt = start + cols;

            start = nxt % l;
            if (_sentence[start] == ' ') {
                start = (start + 1) % l;
                nxt++;
            } else {
                while (start >= 0 && _sentence[start] != ' ') {
                    start--;
                    nxt--;
                }
                start++;
                nxt++;
            }
            res += nxt / l;
        }
        
        return res;
    }
};

// shorter
class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        string _sentence = "";
        for (string &word : sentence) {
            _sentence += word;
            _sentence += " ";
        }
        
        int start = 0, nxt;
        int l = _sentence.size();
        int res = 0;
        
        while (rows > 0) {
            rows--;
            nxt = start + cols;

            if (_sentence[nxt % l] == ' ') {
                nxt++;
            } else {
                while (nxt % l >= 0 && _sentence[nxt % l] != ' ') {
                    nxt--;
                }
                nxt++;
            }
            res += nxt / l;
            start = nxt % l;
        }
        
        return res;
    }
};
```



## 12. LeetCode 490 [The Maze](https://leetcode.com/problems/the-maze/)

### Similar to 200 (Number of Islands)

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

// BFS
class Solution {
public:
    bool hasPath(vector<vector<int>>& maze, vector<int>& start, vector<int>& destination) {
        int m = maze.size();
        int n = maze[0].size();
        
        if (start[0] == destination[0] && start[1] == destination[1]) return true;
        
        vector<pair<int,int>> dirs = {
            {-1, 0},
            {0, 1},
            {1, 0},
            {0, -1}
        };
        
        set<pair<int, int>> visited;
        queue<pair<int, int>> q;
        q.push({start[0], start[1]});
        visited.insert({start[0], start[1]});
        
        while (!q.empty()) {
            auto cur = q.front();
            q.pop();
            
            for (int i=0; i<4; i++) {
                pair<int, int> nxt = cur;
                while (nxt.first >= 0 && nxt.second >= 0 && nxt.first < m && nxt.second < n 
                       && maze[nxt.first][nxt.second] == 0) {
                    nxt.first += dirs[i].first;
                    nxt.second += dirs[i].second;
                }
                nxt.first -= dirs[i].first;
                nxt.second -= dirs[i].second;
                
                if (visited.count(nxt)) continue;
                visited.insert(nxt);
                
                if (nxt.first == destination[0] && nxt.second == destination[1]) {
                    return true;
                }
                q.push(nxt);
            }
        }
        
        return false;
    }
};

// replace set to vector (visited)
class Solution {
public:
    bool hasPath(vector<vector<int>>& maze, vector<int>& start, vector<int>& destination) {
        int m = maze.size();
        int n = maze[0].size();
        
        if (start[0] == destination[0] && start[1] == destination[1]) return true;
        
        vector<pair<int,int>> dirs = {
            {-1, 0},
            {0, 1},
            {1, 0},
            {0, -1}
        };
        
        vector<vector<int>> visited(m, vector<int>(n, 0));
        queue<pair<int, int>> q;
        q.push({start[0], start[1]});
        visited[start[0]][start[1]] = 1;
        
        while (!q.empty()) {
            auto cur = q.front();
            q.pop();
            
            for (int i=0; i<4; i++) {
                pair<int, int> nxt = cur;
                while (nxt.first >= 0 && nxt.second >= 0 && nxt.first < m && nxt.second < n 
                       && maze[nxt.first][nxt.second] == 0) {
                    nxt.first += dirs[i].first;
                    nxt.second += dirs[i].second;
                }
                nxt.first -= dirs[i].first;
                nxt.second -= dirs[i].second;
                
                if (visited[nxt.first][nxt.second]) continue;
                visited[nxt.first][nxt.second] = 1;
                
                if (nxt.first == destination[0] && nxt.second == destination[1]) {
                    return true;
                }
                q.push(nxt);
            }
        }
        
        return false;
    }
};
```



## 13. LeetCode 674 [Longest Continuous Increasing Subsequence](https://leetcode.com/problems/longest-continuous-increasing-subsequence/)

```c++
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {
        if (nums.empty()) return 0;
        int n = nums.size();
        int res = 1, len_lcis = 1;
        for (int i = 1; i < n; ++i) {
            if (nums[i] > nums[i-1]) {
                len_lcis++;
                res = max(res, len_lcis);
            } else {
                len_lcis = 1;
            }
        }
        
        return res;
    }
};
```



## 14. LeetCode 684 [Redundant Connection](https://leetcode.com/problems/redundant-connection/)

```c++
class UnionFindSet {
public:
    UnionFindSet(int n) {
        _parent = vector<int>(n+1, 0);
        _rank = vector<int>(n+1, 0);
        
        for (int i=1; i<=n; i++) {
            _parent[i] = i;
        }
    }
    
    bool Union(int x, int y) {
        int rx = Find(x);
        int ry = Find(y);
        
        if (rx == ry) return false;
        if (_rank[rx] > _rank[ry]) {
            _parent[ry] = rx;
        } else {
            _parent[rx] = ry;
            if (_rank[rx] == _rank[ry]) {
                _rank[ry]++;
            }
        }
        
        return true;
    }
    
    int Find(int x) {
        if (_parent[x] != x) {
            _parent[x] = Find(_parent[x]);
        }
        return _parent[x];
    }
    
private:
    vector<int> _parent;
    vector<int> _rank;
};

class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = edges.size();
        UnionFindSet ufs(n);
        vector<int> ans;
        
        for (int i=0; i<n; i++) {
            if (!ufs.Union(edges[i][0], edges[i][1])) {
                ans = {edges[i][0], edges[i][1]};
                break;
            }
        }
        
        return ans;
    }
};
```



## 15. LeetCode 685 [Redundant Connection II](https://leetcode.com/problems/redundant-connection-ii/)

```c++
class Solution {
    vector<int> parent;
public:
    vector<int> findRedundantDirectedConnection(vector<vector<int>>& edges) {
        parent = vector<int>(edges.size()+1, 0);
        vector<int> doubleParentA, doubleParentB;
        
        for(vector<int> &edge : edges) {
            if(!parent[edge[1]])
                parent[edge[1]] = edge[0];
            else {
                //two parent (candidates)
                doubleParentA = {parent[edge[1]], edge[1]};
                doubleParentB = edge;
                edge[1] = 0;
            }
        }
        
        for(int i=0; i<parent.size(); i++) parent[i] = i;
        
        for(vector<int> &edge : edges) {
            int a = edge[0];
            int b = edge[1];
            int v = find(a);
            if(b == 0) continue;
            if(v == b) {
                if(doubleParentA.empty()) {
                    return edge;
                }
                else return doubleParentA;
            }
            parent[b] = v;
        }
        return doubleParentB;
    }
    
    int find(int x) {
        int root = parent[x];
        if(x != root) {
            root = find(root);
        }
        return root;
    }
};
```



## 16. LeetCode 853 [Car Fleet](https://leetcode.com/problems/car-fleet/)

### Recent Problem!!!(12.5)

### (1) TreeMap

```c++
// n : number of cars
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



### (2) Bucket sort / Counting sort

```c++
// Time Complexity: O(target)
// Space Complexity: O(n)

class Solution {
public:
    int carFleet(int target, vector<int>& position, vector<int>& speed) {
        vector<double> buckets(target+1, -1);
        int ans = 0;
        int n = position.size();
        
        for (int i=0; i<n; i++) {
            buckets[position[i]] = (double) (target - position[i]) / speed[i];
        }
        
        double max_time = 0;
        for (int i=target; i>=0; i--) {
            if (buckets[i] > max_time) {
                max_time = buckets[i];
                ans++;
            }
        }
        
        return ans;
    }
};
```

[solution](https://leetcode.com/problems/car-fleet/discuss/140782/Java-O(N)-without-sorting)



## 17. LeetCode 723 [Candy Crush](https://leetcode.com/problems/candy-crush/)

```c++
class Solution {
public:
    vector<vector<int>> candyCrush(vector<vector<int>>& board) {
        int m = board.size(), n = board[0].size();
        bool toBeContinued = false;
        
        for (int i = 0; i < m; ++i) { // horizontal crushing 
            for (int j = 0; j + 2 < n; ++j) {
                int& v1 = board[i][j];
                int& v2 = board[i][j+1];
                int& v3 = board[i][j+2];
                
                int v0 = std::abs(v1);
                
                if (v0 && v0 == std::abs(v2) && v0 == std::abs(v3)) {
                    v1 = v2 = v3 = - v0;
                    toBeContinued =  true;
                }
            }
        }
        
        for (int i = 0; i + 2 < m; ++i) {  // vertical crushing
            for (int j = 0; j < n; ++j) {
                int& v1 = board[i][j];
                int& v2 = board[i+1][j];
                int& v3 = board[i+2][j];
                int v0 = std::abs(v1);
                if (v0 && v0 == std::abs(v2) && v0 == std::abs(v3)) {
                    v1 = v2 = v3 = -v0;
                    toBeContinued = true;
                }
            }
        }
        
        for (int j = 0; j < n; ++j) { // gravity step
            int dropTo = m - 1;
            for (int i = m - 1; i >= 0; --i) {
                if (board[i][j] >= 0) {
                    board[dropTo--][j] = board[i][j];
                }
            }
            
            for (int i = dropTo; i >= 0; i--) {
                board[i][j] = 0;
            }
        }
        
        return toBeContinued ? candyCrush(board) : board;
    }
};
```



## 18. LeetCode 739 [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

### Related LeetCode 496

Monotone Stack

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        int N = T.size();
        vector<int> ans(N);
        stack<int> st;
        for (int i = N - 1; i >= 0; --i) {
            while (!st.empty() && T[i] >= T[st.top()]) st.pop();
            ans[i] = st.empty() ? 0 : st.top() - i;
            st.push(i);
        }
        return ans;
    }
};
```



## 19. LeetCode 135 [Candy](https://leetcode.com/problems/candy/)

First, satisfy left-neighbor relationship, then right, finally combine.

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

// two-array for extra space
class Solution {
public:
    int candy(vector<int>& ratings) {
        int n = ratings.size();
        if (n == 1) return 1;
        
        vector<int> left2right(n, 1);
        vector<int> right2left(n, 1);
        
        for (int i=1; i<n; i++) {
            if (ratings[i] > ratings[i-1]) {
                left2right[i] = left2right[i-1] + 1;
            }
        }
        
        for (int i=n-2; i>=0; i--) {
            if (ratings[i] > ratings[i+1]) {
                right2left[i] = right2left[i+1] + 1;
            }
        }
        
        int res = 0;
        
        for (int i=0; i<n; i++) {
            res += max(left2right[i], right2left[i]);
        }
        
        return res;
    }
};

// improved
// one-array for extra space
class Solution {
public:
    int candy(vector<int>& ratings) {
        int n = ratings.size();
        if (n == 1) return 1;
        
        vector<int> candies(n, 1);
        
        for (int i=1; i<n; i++) {
            if (ratings[i] > ratings[i-1]) {
                candies[i] = candies[i-1] + 1;
            }
        }
        
        int res = candies[n-1];
        for (int i=n-2; i>=0; i--) {
            if (ratings[i] > ratings[i+1]) {
                candies[i] = max(candies[i] ,candies[i+1] + 1);
            }
            res += candies[i];
        }
        
        return res;
    }
};
```

[solution](https://leetcode.com/articles/candy/)



## 20. LeetCode 849 [Maximize Distance to Closest Person](https://leetcode.com/problems/maximize-distance-to-closest-person/)

```c++
class Solution {
public:
    int maxDistToClosest(vector<int>& seats) {
        int leftP = -1;
        int n = seats.size();
        int max_dist = 0;
        
        for (int i=0; i<n; i++) {
            if (seats[i] == 1) {
                if (leftP == -1) {
                    max_dist = i - leftP - 1;
                } else {
                    max_dist = max(max_dist, (i - leftP) / 2);
                }
                leftP = i;
            }
        }
        
        max_dist = max(max_dist, n - leftP - 1);
        return max_dist;
    }
};
```

[solution](https://leetcode.com/problems/maximize-distance-to-closest-person/discuss/137912/C%2B%2BJava-1-Pass-Solution)



## 21. LeetCode 562 [Longest Line of Consecutive One in Matrix](https://leetcode.com/problems/longest-line-of-consecutive-one-in-matrix/)

```c++
// Time Complexity: O(4mn)
// Space Complexity: O(4mn)

class Solution {
public:
    int longestLine(vector<vector<int>>& M) {
        if (M.empty() || M[0].empty()) return 0;
        int m = M.size();
        int n = M[0].size();
        
        vector<vector<vector<int>>> dp(m, vector<vector<int>>(n, vector<int>(4)));
        int longest = 0;
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (M[i][j] == 1) {
                    for (int k=0; k<4; k++) {
                        dp[i][j][k] = 1;
                    }
                    // horizontal (0, -1)
                    if (j - 1 >= 0 && dp[i][j-1][0] > 0) {
                        dp[i][j][0] += dp[i][j-1][0];
                    }
                    
                    // vertical (-1, 0)
                    if (i - 1 >= 0 && dp[i-1][j][1] > 0) {
                        dp[i][j][1] += dp[i-1][j][1];
                    }
                    
                    // diagonal (-1,-1)
                    if (j - 1 >= 0 && i - 1 >= 0 && dp[i-1][j-1][2] > 0) {
                        dp[i][j][2] += dp[i-1][j-1][2];
                    }
                    
                    // anti-diagonal (-1,+1)
                    if (j + 1 < n && i - 1 >= 0 && dp[i-1][j+1][3] > 0) {
                        dp[i][j][3] += dp[i-1][j+1][3];
                    }
                    
                    longest = max(longest, dp[i][j][0]);
                    longest = max(longest, dp[i][j][1]);
                    longest = max(longest, dp[i][j][2]);
                    longest = max(longest, dp[i][j][3]);
                }
            }
        }
        
        return longest;
    }
};
```

[solution](https://leetcode.com/problems/longest-line-of-consecutive-one-in-matrix/discuss/102266/Java-O(nm)-Time-DP-Solution)



## 22. LeetCode 947 [Most Stones Removed with Same Row or Column](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/)

**Thanks for [lee215](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/discuss/197668/Count-the-Number-of-Islands-O(N))'s Explanation as below**

**Problem:**
we can remove a stone if and only if,
there is another stone in the same column OR row.
We try to remove as many as stones as possible.

Find more details in chinese on the [jianshu](https://www.jianshu.com/p/30d2058db7f7)

**One sentence to solve:**
Connected stones can be reduced to 1 stone,
the maximum stones can be removed = stones number - islands number.
so just count the number of "islands".

### **Count the number of islands**

We call a connected graph as an island.
One island must have at least one stone left.
The maximum stones can be removed = stones number - islands number

The whole problem is transferred to:
What is the number of islands?

You can show all your skills on a DFS implementation,
and solve this problem as a normal one.

### **Unify index**

Struggle between rows and cols?
You may duplicate your codes when you try to the same thing on rows and cols.
In fact, no logical difference between col index and rows index.

An easy trick is that, add 10000 to col index.
So we use 0 ~ 9999 for row index and 10000 ~ 19999 for col.

### **Search on the index, not the points**

When we search on points,
we alternately change our view on a row and on a col.

We think:
a row index, connect two stones on this row
a col index, connect two stones on this col.

In another view：
A stone, connect a row index and col.

Have this idea in mind, the solution can be much simpler.
The number of islands of *points*,
is the same as the number of islands of *indexes*.

### **Union-Find**

I use union find to solve this problem.
As I mentioned, the elements are not the points, but the indexes.

1. for each point, union two indexes.
2. return points number - union number

Copy a template of union-find,
write 2 lines above,
you can solve this problem in several minutes.

### (1) Union-Find

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(nlogn)

// without union by rank
class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        int n = stones.size();
        for (int i=0; i<n; i++) {
            Union(stones[i][0], stones[i][1] + 10000);
        }
        return n - islands;
    }
    
private:
    void Union(int x, int y) {
        int rx = Find(x);
        int ry = Find(y);
        if (rx != ry) {
            _parents[rx] = ry;
            islands--;
        }
    }
    
    int Find(int x) {
        if (!_parents.count(x)) {
            _parents[x] = x;
            islands++;
        } else if (_parents[x] != x) {
            _parents[x] = Find(_parents[x]);
        }
        return _parents[x];
    }
    
    unordered_map<int, int> _parents;
    int islands = 0;
};
```



### (2) Union-Find with Full Optimization

```c++
// Time Complexity: O(n)
// Space Complexity: O(n + 20000)

class UnionFindSet {
public:
    UnionFindSet(int n) {
        _parents.resize(n);
        _ranks.resize(n);
        
        for (int i=0; i<n; i++) {
            _parents[i] = i;
        }
    }
    
    bool Union(int x, int y) {
        int rx = Find(x);
        int ry = Find(y);
        if (rx == ry) return false;
        if (_ranks[rx] > _ranks[ry]) {
            _parents[ry] = rx;
        } else {
            _parents[rx] = ry;
            if (_ranks[rx] == _ranks[ry]) {
                _ranks[ry]++;
            }
        }
        return true;
    }
    
    int Find(int x) {
        if (_parents[x] != x) {
            // path compression
            _parents[x] = Find(_parents[x]);
        }
        return _parents[x];
    }
    
private:
    vector<int> _parents;
    vector<int> _ranks;
};

class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        int n = stones.size();
        UnionFindSet ufs(20000);
        
        for (int i=0; i<n; i++) {
            ufs.Union(stones[i][0], stones[i][1]+10000);
        }
        
        unordered_set<int> components;
        for (int i=0; i<n; i++) {
            components.insert(ufs.Find(stones[i][0]));
        }
        
        return n - (int)components.size();
    }
};
```



### (3) Union-Find (Union the index of the stone in array, not the point index)

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

class UnionFindSet {
public:
    UnionFindSet(int n) {
        _parents.resize(n);
        _ranks.resize(n);
        count = n;
        
        for (int i=0; i<n; i++) {
            _parents[i] = i;
        }
    }
    
    bool Union(int x, int y) {
        int rx = Find(x);
        int ry = Find(y);
        if (rx == ry) return false;
        if (_ranks[rx] > _ranks[ry]) {
            _parents[ry] = rx;
        } else {
            _parents[rx] = ry;
            if (_ranks[rx] == _ranks[ry]) {
                _ranks[ry]++;
            }
        }
        count--;
        return true;
    }
    
    int Find(int x) {
        if (_parents[x] != x) {
            // path compression
            _parents[x] = Find(_parents[x]);
        }
        return _parents[x];
    }
    
    int getCount() {
        return count;
    }
    
private:
    vector<int> _parents;
    vector<int> _ranks;
    int count;
};

class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        int n = stones.size();
        UnionFindSet ufs(n);
        
        for (int i=0; i<n; i++) {
            for (int j=i+1; j<n; j++) {
                if (stones[i][0] == stones[j][0] || stones[i][1] == stones[j][1]) {
                    ufs.Union(i, j);
                }
            }
        }
        
        return n - ufs.getCount();
    }
};
```



### (4) DFS

Using hashmaps of vectors to quickly access all occupied columns for each row, and all occupied rows for each column. Then use DFS to group all rows connected by columns, counting the number of stones in that group.

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        int ans = 0;
        
        for (auto stone : stones) {
            rows[stone[0]].push_back(stone[1]);
            cols[stone[1]].push_back(stone[0]);
        }
        
        for (auto row : rows) {
            ans += max(0, dfs(row.first) - 1);
        }
        
        return ans;
    }
    
private:
    int dfs(int row) {
        int ans = 0;
        if (_rowsSet.insert(row).second) {
            ans += rows[row].size();
            for (auto c : rows[row]) {
                for (auto r : cols[c]) {
                    ans += dfs(r);
                }
            }
        }
        
        return ans;
    }
    
    unordered_map<int, vector<int>> rows, cols; // map between row and col
    unordered_set<int> _rowsSet; // use for counting groups in dfs
};
```

[solution](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/discuss/197851/C%2B%2B-20-ms-DFS)



## 23. LeetCode 911 [Online Election](https://leetcode.com/problems/online-election/)

```c++
class TopVotedCandidate {
public:
    // O(nlogn) => O(n)(can be optimized)
    TopVotedCandidate(vector<int> persons, vector<int> times) {
        int max_vote = -1;
        int max_vote_time = -1;
        int leading_id = -1;
        int n = persons.size();
        
        for (int i=0; i<n; i++) {
            mp[persons[i]].first++;
            mp[persons[i]].second = times[i];
            if (mp[persons[i]].first > max_vote || 
                (mp[persons[i]].first == max_vote && mp[persons[i]].second > max_vote_time)) {
                max_vote = mp[persons[i]].first;
                max_vote_time = mp[persons[i]].second;
                leading_id = persons[i];
            }
            leadings[times[i]] = leading_id;
        }
    }
    
    // O(logn)
    int q(int t) {
        auto it = leadings.lower_bound(t);
        if (it->first == t) return it->second;
        if (it == leadings.begin()) return -1;
        it--;
        return it->second;
    }
    
private:
    unordered_map<int, pair<int, int>> mp; // person id => {freq, recent vote time}
    map<int, int> leadings; // time => leading person id
};

/**
 * Your TopVotedCandidate object will be instantiated and called as such:
 * TopVotedCandidate obj = new TopVotedCandidate(persons, times);
 * int param_1 = obj.q(t);
 */

// don't need to record the recent vote time
class TopVotedCandidate {
public:
    TopVotedCandidate(vector<int> persons, vector<int> times) {
        int max_vote = -1;
        int leading_id = -1;
        int n = persons.size();
        
        for (int i=0; i<n; i++) {
            mp[persons[i]]++;
            if (mp[persons[i]] >= max_vote) {
                max_vote = mp[persons[i]];
                leading_id = persons[i];
            }
            leadings[times[i]] = leading_id;   
        }
    }
    
    int q(int t) {
        auto it = leadings.lower_bound(t);
        if (it->first == t) return it->second;
        if (it == leadings.begin()) return -1;
        it--;
        return it->second;
    }
    
private:
    unordered_map<int, int> mp; // person id => freq
    map<int, int> leadings; // time => leading person id
};

/**
 * Your TopVotedCandidate object will be instantiated and called as such:
 * TopVotedCandidate obj = new TopVotedCandidate(persons, times);
 * int param_1 = obj.q(t);
 */

// improved version (use hashmap for leadings and implement binary search in q())

```



## 24. LeetCode 277 [Find the Celebrity](https://leetcode.com/problems/find-the-celebrity/)

```c++
// Time Complexity: O(2n)
// Space Complexity: O(1)

// Forward declaration of the knows API.
bool knows(int a, int b);

class Solution {
public:
    int findCelebrity(int n) {
        if (n == 0) return -1;
        if (n == 1) return 0;
        int candidate = 0;
        for (int i=1; i<n; i++) {
            if (knows(candidate, i)) {
                candidate = i;
            }
        }
        
        for (int i=0; i<n; i++) {
            if (i == candidate) continue;
            if (knows(candidate, i) || !knows(i, candidate)) return -1;
        }
        return candidate;
    }
};
```



## 25. LeetCode 524 [Longest Word in Dictionary through Deleting](https://leetcode.com/problems/longest-word-in-dictionary-through-deleting/)

```c++
class Solution {
public:
    string findLongestWord(string s, vector<string>& d) {
        string res = "";
        for (string &word : d) {
            if (isSubsequence(s, word)) {
                if (word.length() > res.length() || (word.length() == res.length() && word < res)) {
                    res = word;
                }
            }
        }
        return res;
    }
    
private:
    bool isSubsequence(string &s, string &t) {
        int ls = s.length();
        int lt = t.length();
        int i = 0;
        int j = 0;
        
        while (i < ls && j < lt) {
            if (s[i] == t[j]) {
                i++;
                j++;
            } else {
                i++;
            }
        }
        
        return j == lt;
    }
};
```



## 26. LeetCode 140 [Word Break II](https://leetcode.com/problems/word-break-ii/)

```c++
class Solution {
public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> hashset(wordDict.begin(), wordDict.end());
        unordered_map<int, vector<string>> mp;
        return dfs(s, hashset, 0, mp);
    }
    
    vector<string> dfs(string &s, unordered_set<string> &wordSet, int start, unordered_map<int, vector<string>> &mp) {
        if (mp.count(start)) {
            return mp[start];
        }
        
        vector<string> res;
        int n = s.length();
        if (start == n) {
            res.push_back("");
        }
        
        for (int i=start+1; i<=n; i++) {
            if (wordSet.count(s.substr(start, i-start))) {
                vector<string> subres = dfs(s, wordSet, i, mp);
                for (auto ss : subres) {
                    res.push_back(s.substr(start, i-start) + (ss.empty() ? "" : (" " + ss)));
                }
            }
        }
        
        mp[start] = res;
        
        return res;
    }
};
```



## 27. LeetCode 246 [Strobogrammatic Number](https://leetcode.com/problems/strobogrammatic-number/) | 247 [Strobogrammatic Number II](https://leetcode.com/problems/strobogrammatic-number-ii/) | 248 [Strobogrammatic Number III](https://leetcode.com/problems/strobogrammatic-number-iii/)

### 246

```c++
class Solution {
public:
    bool isStrobogrammatic(string num) {
        unordered_map<char, char> lut{{'0', '0'}, {'1', '1'}, {'6', '9'}, {'8', '8'}, {'9', '6'}};
        int n = num.length();
        for (int l = 0, r = n - 1; l <= r; l++, r--)
            if (lut.find(num[l]) == lut.end() || lut[num[l]] != num[r])
                return false; 
        return true; 
    }
};
```



### 247

```c++
class Solution {
public:
    vector<string> findStrobogrammatic(int n) {
        if (n == 0) return {};
        unordered_map<char, char> mp = {
            {'0', '0'},
            {'1', '1'},
            {'6', '9'},
            {'8', '8'},
            {'9', '6'}
        };
        vector<string> res;
        string str_left, str_right;
        generateNumbers(mp, res, n, 0, str_left, str_right);
        
        return res;
    }
    
private:
    void generateNumbers(unordered_map<char, char> &mp, vector<string> &res, int &n, int index, string left, string right) {
        if (index == n) {
            res.push_back(left + right);
        } else if (index == n-1) {
            res.push_back(left + '0' + right);
            res.push_back(left + '1' + right);
            res.push_back(left + '8' + right);
        } else {
            if (index == 0) {
                generateNumbers(mp, res, n, index+2, left+'1', mp['1']+right);
                generateNumbers(mp, res, n, index+2, left+'6', mp['6']+right);
                generateNumbers(mp, res, n, index+2, left+'8', mp['8']+right);
                generateNumbers(mp, res, n, index+2, left+'9', mp['9']+right);
            } else {
                generateNumbers(mp, res, n, index+2, left+'0', mp['0']+right);
                generateNumbers(mp, res, n, index+2, left+'1', mp['1']+right);
                generateNumbers(mp, res, n, index+2, left+'6', mp['6']+right);
                generateNumbers(mp, res, n, index+2, left+'8', mp['8']+right);
                generateNumbers(mp, res, n, index+2, left+'9', mp['9']+right);
            }
        }
    }
};
```



### 248

```c++
class Solution {
public:
    int strobogrammaticInRange(string low, string high) {
        if (!compare(low, high)) return 0;

        vector<pair<char, char>> nums = { { '0', '0' }, { '1', '1' }, { '6', '9' }, { '8', '8' }, { '9', '6' } };

        int cnt = strobogrammaticInRange(nums, low, high, "", 0);
        cnt = strobogrammaticInRange(nums, low, high, "0", cnt);
        cnt = strobogrammaticInRange(nums, low, high, "1", cnt);
        cnt = strobogrammaticInRange(nums, low, high, "8", cnt);

        return cnt;
    }
    
private:
    bool compare(string s1, string s2) {
        if (s1.length() != s2.length())
            return s1.length() <= s2.length();

        for (int i = 0; i < s1.length(); i++) {
            if (s1[i] < s2[i]) return true;
            else if (s1[i] > s2[i]) return false;
        }

        return true;
    }

    int strobogrammaticInRange(const vector<pair<char, char>>& nums, const string& low, const string& high, string t, int cnt) {
        if (high.length() < t.length())
            return cnt;
        if (compare(low, t) && compare(t, high)) 
            if (t.length() == 1 || t.length() > 1 && t.front() != '0')
                cnt++;

        for (auto iter = nums.begin(); iter != nums.end(); ++iter) 
            cnt = strobogrammaticInRange(nums, low, high, string(1, iter->first) + t + string(1, iter->second), cnt);

        return cnt;
    }
};
```



## 28. LeetCode 777 [Swap Adjacent in LR String](https://leetcode.com/problems/swap-adjacent-in-lr-string/)

```c++
class Solution {
public:
    bool canTransform(string start, string end) {
        vector<pair<char, int>> vs;
        vector<pair<char, int>> ve;
        
        for (int i=0; i<start.size(); i++) {
            if (start[i] == 'R' || start[i] == 'L') {
                vs.push_back({start[i], i});
            }
        }
        
        for (int i=0; i<end.size(); i++) {
            if (end[i] == 'R' || end[i] == 'L') {
                ve.push_back({end[i], i});
            }
        }
        
        if (vs.size() != ve.size()) return false;
        for (int i=0; i<vs.size(); i++) {
            if (vs[i].first != ve[i].first) return false;
            if (vs[i].first == ve[i].first) {
                if (vs[i].first == 'R' && vs[i].second > ve[i].second) {
                    return false;
                }
                if (vs[i].first == 'L' && vs[i].second < ve[i].second) {
                    return false;
                }
            }
        }
        
        return true;
    }
};
```



## 29. LeetCode 489 [Robot Room Cleaner](https://leetcode.com/problems/robot-room-cleaner/)

```c++
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
    void cleanRoom(Robot& robot) {
        set<pair<int, int>> cleaned;
        int cur_dir = 0; // 0: up, 1: right, 2: down, 3: left
        vector<vector<int>> dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
        dfs(robot, cleaned, 0, 0, cur_dir, dirs);
    }
    
    void dfs(Robot& robot, set<pair<int, int>> &cleaned, int r, int c, int &cur_dir, vector<vector<int>> &dirs) {
        if (cleaned.count({r, c})) {
            return;
        }
        
        robot.clean();
        cleaned.insert({r, c}); // already cleaned
        
        for (int i=0; i<4; i++) {
            // 4 directions
            if (robot.move()) {
                dfs(robot, cleaned, r + dirs[cur_dir][0], c + dirs[cur_dir][1], cur_dir, dirs);
                robot.turnRight();
                robot.turnRight();
                robot.move();
                robot.turnLeft();
                robot.turnLeft();
            }
            robot.turnRight();
            cur_dir = (cur_dir + 1) % 4;
        }
    }
};
```

[solution](https://leetcode.com/problems/robot-room-cleaner/discuss/139057/Very-easy-to-understand-Java-solution)



## 30. LeetCode 163 [Missing Ranges](https://leetcode.com/problems/missing-ranges/)

```c++
class Solution {
public:
    vector<string> findMissingRanges(vector<int>& nums, int lower, int upper) {
        int size = nums.size();
        vector<string> res;
        long missingL = lower;
        for (int i=0; i<size; i++) {
            if (nums[i] == missingL) {
                missingL++;
            } else if (missingL < nums[i]){
                long missingU = long(nums[i]) - 1;
                res.push_back(helper(missingL, missingU));
                missingL = long(nums[i]) + 1;
            }  
        }
        if (missingL <= upper) {
            res.push_back(helper(missingL, upper));
        }
        return res;
    }

    string helper(long a, long b) {
        if (b > a) {
            return to_string(a) + "->" + to_string(b);
        } else {
            return to_string(a);
        }
    }
};
```



## 31. LeetCode 159 [Longest Substring with At Most Two Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/)

### (1) Sliding Window

```c++
class Solution {
public:
    int lengthOfLongestSubstringTwoDistinct(string s) {
        int n = s.length();
        if (n <= 2) return n;
        unordered_map<char, int> wordCnts;
        int l = 0, r = 0, max_len = 0;
        while (r < n) {
            wordCnts[s[r++]]++;
            while (wordCnts.size() > 2) {
                if (wordCnts[s[l]]-- == 1) {
                    wordCnts.erase(s[l]);
                }
                l++;
            }
            max_len = max(max_len, r - l);
        }
        return max_len;
    }
};
```



### (2) Other Solution

```c++
class Solution {
public:
    int lengthOfLongestSubstringTwoDistinct(string s) {
        int n = s.length();
        if (n <= 2) return n;
        
        char first_char = '\0', second_char = '\0';
        int cur_cnt = 0, total = 0, last_second_cnt = 0;
        for (auto const &c : s) {
            if (c == first_char || c == second_char) {
                cur_cnt++;
                last_second_cnt = c == second_char ? last_second_cnt+1 : 1;
            } else {
                cur_cnt = last_second_cnt + 1;
                last_second_cnt = 1;
            }
            
            if (c != second_char) {
                first_char = second_char;
                second_char = c;
            }
            
            total = max(total, cur_cnt);
        }
        
        return total;
    }
};
```



### Follow up

### LeetCode 340 [Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)

```c++
class Solution {
public:
    int lengthOfLongestSubstringKDistinct(string s, int k) {
        int n = s.length();
        if (n <= k) return n;
        if (k == 0) return 0;
        unordered_map<char, int> mp;
        int l = 0, r = 0, max_len = 0;
        while (r < n) {
            mp[s[r++]]++;
            while (mp.size() > k) {
                if (mp[s[l]]-- == 1) {
                    mp.erase(s[l]);
                }
                l++;
            }
            max_len = max(max_len, r - l);
        }
        return max_len;
    }
};

// other version
class Solution {
public:
    int lengthOfLongestSubstringKDistinct(string s, int k) {
        if (s.empty()) return 0;
        int n = s.length();
        unordered_map<char, int> mp;
        int l = 0, r = 0, res = 0, cnt = 0;
        while (r < n) {
            if (mp[s[r++]]++ == 0) {
                cnt++;
            }
            
            while (cnt > k) {
                if (mp[s[l++]]-- == 1) {
                    cnt--;
                }
            }
            res = max(res, r-l);
        }
        
        return res;
    }
};
```



## 32. LeetCode 295 [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)

```c++
class MedianFinder {
public:
    /** initialize your data structure here. */
    MedianFinder() {}
    
    void addNum(int num) {
        left_half.push(num);
        right_half.push(left_half.top());
        left_half.pop();
        
        if (right_half.size() > left_half.size()) {
            left_half.push(right_half.top());
            right_half.pop();
        }
    }
    
    double findMedian() {
        return left_half.size() > right_half.size() ? 
            left_half.top() : 1.0 * (left_half.top() + right_half.top()) / 2;
        
    }
    
private:
    priority_queue<int, vector<int>, less<int>> left_half;
    priority_queue<int, vector<int>, greater<int>> right_half;
};

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```

[solution](https://leetcode.com/problems/find-median-from-data-stream/solution/)



## 33. LeetCode 844 [Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/)

### (1) Left-To-Right Visit

```c++
class Solution {
public:
    bool backspaceCompare(string S, string T) {
        int endS = 0;
        for (int i=0; i<S.length(); i++) {
            if (S[i] == '#') {
                endS = endS == 0 ? 0 : endS - 1;
            } else {
                swap(S[i], S[endS++]);
            }
        }
        
        int endT = 0;
        for (int i=0; i<T.length(); i++) {
            if (T[i] == '#') {
                endT = endT == 0 ? 0 : endT - 1;
            } else {
                swap(T[i], T[endT++]);
            }
        }
        
        if (endS != endT) return false;
        for (int i=endS-1; i>=0; i--) {
            if (S[i] != T[i]) return false;
        }
        
        return true;
    }
};
```



### (2) Right-To-Left Visit

#### Intuition

Get the first valid chracter(not deleted by backspace) in both string, check whether they are the same.

Key Test Case:

"nzp#o#g"
"b#nzp#o#g"

```c++
class Solution {
public:
    bool backspaceCompare(string S, string T) {
        int ptrS = S.length()-1;
        int ptrT = T.length()-1;
        while (ptrS >= 0 && ptrT >= 0) {
            for (int cnt = 0; ptrS >= 0 && (cnt || S[ptrS] == '#'); ptrS--) {
                cnt += S[ptrS] == '#' ? 1 : -1;
            }
            
            for (int cnt = 0; ptrT >= 0 && (cnt || T[ptrT] == '#'); ptrT--) {
                cnt += T[ptrT] == '#' ? 1 : -1;
            }
            
            if (ptrS >= 0 && ptrT >= 0) {
                if (S[ptrS] == T[ptrT]) {
                    ptrS--;
                    ptrT--;
                } else {
                    return false;
                }
            }
        }
        
        if (ptrS >= 0) {
            for (int cnt = 0; ptrS >= 0 && (cnt || S[ptrS] == '#'); ptrS--) {
                cnt += S[ptrS] == '#' ? 1 : -1;
            }
        }
        
        if (ptrT >= 0) {
            for (int cnt = 0; ptrT >= 0 && (cnt || T[ptrT] == '#'); ptrT--) {
                cnt += T[ptrT] == '#' ? 1 : -1;
            }
        }
        
        return ptrS == -1 && ptrT == -1;
    }
};
```

[solution1](https://leetcode.com/problems/backspace-string-compare/discuss/135603/C%2B%2BJavaPython-O(N)-time-and-O(1)-space)

[solution2](https://leetcode.com/problems/backspace-string-compare/discuss/162827/Java-Two-Pointer-With-Explanation-beat-98)



## 34. LeetCode 133 [Clone Graph](https://leetcode.com/problems/clone-graph/)

```c++
/**
 * Definition for undirected graph.
 * struct UndirectedGraphNode {
 *     int label;
 *     vector<UndirectedGraphNode *> neighbors;
 *     UndirectedGraphNode(int x) : label(x) {};
 * };
 */
class Solution {
public:
    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
        if (!node) return nullptr;
        NODEMP mp;
        return dfs_clone(node, mp);
    }
    
private:
    typedef unordered_map<UndirectedGraphNode*, UndirectedGraphNode*> NODEMP;
    
    UndirectedGraphNode* dfs_clone(UndirectedGraphNode* node, NODEMP &mp) {
        if (!node) return nullptr;
        if (!mp.count(node)) {
            mp[node] = new UndirectedGraphNode(node->label);
            for (const auto &neigh : node->neighbors) {
                new_node->neighbors.push_back(dfs_clone(neigh, mp));
            }
        }
        
        return mp[node];
    }
};
```



## 35. LeetCode 205 [Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/)

### (1) Using two char-to-int mapping

```c++
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        vector<int> mp1(256, -1); // avoid ambiguity; 0 will be used below for mapping
        vector<int> mp2(256, -1);
        int n = s.length();
        
        for (int i=0; i<n; i++) {
            if (mp1[s[i]] != mp2[t[i]]) return false;
            else {
                mp1[s[i]] = i;
                mp2[t[i]] = i;
            }
        }
        
        return true;
    }
};
```



### (2) Using two char-to-char mapping

```c++
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
```



### FollowUp LeetCode 290 

```c++
class Solution {
public:
    bool wordPattern(string pattern, string str) {
        istringstream in(str);
        
        unordered_map<char, int> p2i;
        unordered_map<string, int> s2i;
        
        int i = 0, n = pattern.size();
        
        for (string word; in >> word; i++) {
            if (i == n) return false;
            if (p2i[pattern[i]] != s2i[word]) {
                return false;
            } else {
                p2i[pattern[i]] = i+1;
                s2i[word] = i+1;
            }
            
        }
        
        return i == n;
    }
};
```



## 36. LeetCode 857 [Minimum Cost to Hire K Workers](https://leetcode.com/problems/minimum-cost-to-hire-k-workers/)

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(n)

class Solution {
public:
    double mincostToHireWorkers(vector<int>& quality, vector<int>& wage, int K) {
        int n = quality.size();
        vector<pair<double, int>> ratios;
        
        // O(n)
        for (int i=0; i<n; i++) {
            ratios.push_back({1.0 * wage[i] / quality[i], quality[i]});
        }
        
        // O(nlogn)
        sort(ratios.begin(), ratios.end(), [](const pair<double, int> &A, const pair<double, int> &B) {
            return A.first < B.first;
        });
        
        double cost = 0;
        priority_queue<int, vector<int>> worker_qualities;
        
        // O(KlogK)
        for (int i=0; i<K; i++) {
            cost += ratios[K-1].first * ratios[i].second;
            worker_qualities.push(ratios[i].second);
        }
        
        double mincost = cost;
        
        // O(nlogK)
        for (int i=K; i<n; i++) {
            cost *= ratios[i].first / ratios[i-1].first;
            cost += ratios[i].first * ratios[i].second;
            worker_qualities.push(ratios[i].second);
            int max_q = worker_qualities.top();
            worker_qualities.pop();
            cost -= ratios[i].first * max_q;
            mincost = min(mincost, cost);
        }
        
        return mincost;
    }
};

// improved version
class Solution {
public:
    double mincostToHireWorkers(vector<int>& quality, vector<int>& wage, int K) {
        int n = quality.size();
        vector<pair<double, int>> ratios;
        for (int i=0; i<n; i++) {
            ratios.push_back({1.0 * wage[i] / quality[i], quality[i]});
        }
        
        sort(ratios.begin(), ratios.end(), [](const pair<double, int> &A, const pair<double, int> &B) {
            return A.first < B.first;
        });
        
        int total_quality = 0;
        priority_queue<int, vector<int>> worker_qualities;
        for (int i=0; i<K; i++) {
            total_quality += ratios[i].second;
            worker_qualities.push(ratios[i].second);
        }
        
        double mincost = 1.0 * total_quality * ratios[K-1].first;
        
        for (int i=K; i<n; i++) {
            worker_qualities.push(ratios[i].second);
            int max_q = worker_qualities.top();
            worker_qualities.pop();
            
            total_quality = total_quality + ratios[i].second - max_q;
            mincost = min(mincost, 1.0 * total_quality * ratios[i].first);
        }
        
        return mincost;
    }
};
```

[solution](https://leetcode.com/problems/minimum-cost-to-hire-k-workers/discuss/141768/Detailed-explanation-O(NlogN))



## 37. LeetCode 271 [Encode and Decode Strings](https://leetcode.com/problems/encode-and-decode-strings/)

```c++
class Codec {
public:

    // Encodes a list of strings to a single string.
    string encode(vector<string>& strs) {
        string encoded_string = "";
        for(string &s : strs) {
            int length = (int)s.length();
            encoded_string += to_string(length) + "@" + s;
        }
        return encoded_string;
    }

    // Decodes a single string to a list of strings.
    vector<string> decode(string s) {
        vector<string> strs;
        for(int head=0; head < s.size();) {
            int pos = s.find_first_of("@", head);
            int length = stoi(s.substr(head, pos-head));
            head = pos + 1;
            strs.push_back(s.substr(head, length));
            head += length;
        }
        return strs;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.decode(codec.encode(strs));
```

[solution1](https://leetcode.com/problems/encode-and-decode-strings/discuss/70412/AC-Java-Solution)

[solution2](https://leetcode.com/problems/encode-and-decode-strings/discuss/70443/Accepted-simple-C%2B%2B-solution)

[solution3](https://leetcode.com/problems/encode-and-decode-strings/discuss/70460/A-solution-without-delimiter)



## 38. LeetCode 288 [Unique Word Abbreviation](https://leetcode.com/problems/unique-word-abbreviation/)

```c++
class ValidWordAbbr {
public:
    ValidWordAbbr(vector<string> dictionary) {
        for (const auto &word : dictionary) {
            int l = word.length();
            string abbr = l > 2 ? word[0] + to_string(l-2) + word[l-1] : word;
            abbr_dict[abbr].insert(word);
        }
    }
    
    bool isUnique(string word) {
        int l = word.length();
        string abbr = l > 2 ? word[0] + to_string(l-2) + word[l-1] : word;
        if (abbr_dict[abbr].empty()) return true;
        if (abbr_dict[abbr].size() == 1 && abbr_dict[abbr].count(word)) return true;
        return false;
        // return abbr_dict[abbr].size() == abbr_dict[abbr].count(word);
    }
    
private:
    unordered_map<string, unordered_set<string>> abbr_dict;
};

/**
 * Your ValidWordAbbr object will be instantiated and called as such:
 * ValidWordAbbr obj = new ValidWordAbbr(dictionary);
 * bool param_1 = obj.isUnique(word);
 */
```



## 39. LeetCode 460 [LFU Cache](https://leetcode.com/problems/lfu-cache/)

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



## 40. LeetCode 926 [Flip String to Monotone Increasing](https://leetcode.com/problems/flip-string-to-monotone-increasing/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int minFlipsMonoIncr(string S) {
        int n = S.length();
        vector<int> flip_to_zero(n+1, 0);
        vector<int> flip_to_one(n+1, 0);
        
        for (int i=1; i<=n; i++) {
            flip_to_zero[i] = flip_to_zero[i-1] + S[i-1] - '0';
        }
        
        for (int i=n-1; i>=0; i--) {
            flip_to_one[i] = flip_to_one[i+1] + '1' - S[i];
        }
        
        int min_flip = INT_MAX;
        for (int i=0; i<=n; i++) {
            min_flip = min(min_flip, flip_to_zero[i] + flip_to_one[i]);
        }
        
        return min_flip;
    }
};

// dp
class Solution {
public:
    int minFlipsMonoIncr(string S) {
        int n = S.length();
        vector<vector<int>> dp(n+1, vector<int>(2, 0)); // n * 2
        
        for (int i=1; i<=n; i++) {
            if (S[i-1] == '0') {
                dp[i][0] = dp[i-1][0];
                dp[i][1] = min(dp[i-1][0], dp[i-1][1]) + 1;
            } else {
                dp[i][0] = dp[i-1][0] + 1;
                dp[i][1] = min(dp[i-1][0], dp[i-1][1]);
            }
        }
        
        return min(dp[n][0], dp[n][1]);
    }
};

// improved dp
class Solution {
public:
    int minFlipsMonoIncr(string S) {
        int n = S.length();
        //vector<vector<int>> dp(n+1, vector<int>(2, 0)); // n * 2
        int last_is_zero = 0;
        int last_is_one = 0;
        
        for (int i=0; i<n; i++) {
            if (S[i] == '0') {
                last_is_one = min(last_is_zero, last_is_one) + 1;
            } else {
                last_is_one = min(last_is_zero, last_is_one);
                last_is_zero += 1;
            }
        }
        
        return min(last_is_zero, last_is_one);
    }
};
```



## 41. LeetCode 818 [Race Car](https://leetcode.com/problems/race-car/)

```c++
class Solution {
public:
    int racecar(int target) {
        queue<pair<int, int>> q;
        q.push({0, 1});
        
        int steps = 0;
        set<pair<int, int>> visited;
        visited.insert({0, 1});
        
        while (!q.empty()) {
            int k = q.size();
            
            for (int i=0; i<k ; i++) {
                pair<int, int> last = q.front();
                q.pop();
                if (last.first == target) {
                    return steps;
                }
                // A
                pair<int, int> next = {last.first + last.second, last.second * 2};
                if (!visited.count(next) && next.first > 0 && next.first < (target << 1)) {
                    q.push(next);
                    visited.insert(next);
                }

                // R
                next = {last.first, last.second > 0 ? -1 : 1};
                if (!visited.count(next) && next.first > 0 && next.first < (target << 1)) {
                    q.push(next);
                    visited.insert(next);
                }
            } 
            steps++; 
        }
        
        return -1;
    }
};
```

[solution](https://leetcode.com/problems/race-car/discuss/124326/Summary-of-the-BFS-and-DP-solutions-with-intuitive-explanation)

[dp-solution]([Race Car](https://leetcode.com/problems/race-car/))



## 42. LeetCode 853 [Car Fleet](https://leetcode.com/problems/car-fleet/)

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(n)

class Solution {
public:
    int carFleet(int target, vector<int>& position, vector<int>& speed) {
        if (position.empty()) return 0;
        vector<pair<int, double>> times;
        
        int n = position.size();
        for (int i=0; i<n; i++) {
            times.push_back({position[i], 1.0 * (target - position[i]) / speed[i]});
        }
        
        sort(times.begin(), times.end(), [](const pair<int, double> &A, const pair<int, double> &B) {
            return A.first > B.first || (A.first == B.first && A.second < B.second);
        });
        
        int fleets = 1;
        double max_time = times[0].second;
        for (int i=1; i<n; i++) {
            if (times[i].second > max_time) {
                fleets++;
                max_time = times[i].second;
            }
        }
        
        return fleets;
    }
};
```



## 43. LeetCode 803 [Bricks Falling When Hit](https://leetcode.com/problems/bricks-falling-when-hit/)

### Union-Find / DFS

```c++
class Solution {
public:
    vector<int> hitBricks(vector<vector<int>>& grid, vector<vector<int>>& hits) {    
        dirs_ = {0, -1, 0, 1, 0};    
        m_ = grid.size();
        n_ = grid[0].size();
        g_.swap(grid);
        seq_ = 1;

        vector<int> ans;
        for (int i = 0; i < hits.size(); ++i)
            ans.push_back(hit(hits[i][1], hits[i][0]));
        return ans;
    }

private:
    vector<vector<int>> g_;
    vector<int> dirs_;
    int seq_;
    int m_;
    int n_;

    // hit x, y and return the number of bricks fallen.
    int hit(int x, int y) {
        if (x < 0 || x >= n_ || y < 0 || y >= m_) return 0;    
        g_[y][x] = 0;
        int ans = 0; 
        for (int i = 0; i < 4; ++i) {
            ++seq_;
            int count = 0;
            if (!fall(x + dirs_[i], y + dirs_[i + 1], false, count)) continue;      
                ++seq_;
            ans += count;
            fall(x + dirs_[i], y + dirs_[i + 1], true, count);
        }
        return ans;
    }

    // Check whether the CC contains (x, y) will fall or not.
    // Set all nodes in this CC as seq_ or 0 if clear.
    // count: the # of nodes in this CC.
    bool fall(int x, int y, bool clear, int& count) {
        if (x < 0 || x >= n_ || y < 0 || y >= m_) return true;
        if (g_[y][x] == seq_ || g_[y][x] == 0) return true;
        if (y == 0) return false;
        g_[y][x] = clear ? 0 : seq_;
        ++count;
        for (int i = 0; i < 4; ++i)
            if (!fall(x + dirs_[i], y + dirs_[i + 1], clear, count)) return false;
        return true;
    }
};
```



## 44. LeetCode 900 [RLE Iterator](https://leetcode.com/problems/rle-iterator/)

### (1) Two Arrays + One pointer

```c++
// Time Complexity: O(1)
// Space Complexity: O(2n)

class RLEIterator {
public:
    RLEIterator(vector<int> A) : ptr(0) {
        int n = A.size();
        for (int i=0; i<n; i+=2) {
            if (A[i] == 0) continue;
            counts.push_back(A[i]);
            numbers.push_back(A[i+1]);
        } 
    }
    
    int next(int n) {
        if (ptr == counts.size()) return -1;
        if (n <= counts[ptr]) {
            counts[ptr] -= n;
        } else {
            while (ptr < counts.size() && n > counts[ptr]) {
                n -= counts[ptr];
                ptr++;
            }
            if (ptr == counts.size()) return -1;
            counts[ptr] -= n;
        }
        return numbers[ptr];
    }
    
private:
    vector<int> counts;
    vector<int> numbers;
    int ptr;
};

/**
 * Your RLEIterator object will be instantiated and called as such:
 * RLEIterator obj = new RLEIterator(A);
 * int param_1 = obj.next(n);
 */
```



## 45. LeetCode 843 [Guess the Word](https://leetcode.com/problems/guess-the-word/)

### Minimax (Read Solution Later)

```c++
/**
 * // This is the Master's API interface.
 * // You should not implement it, or speculate about its implementation
 * class Master {
 *   public:
 *     int guess(string word);
 * };
 */
class Solution {
public:
    void findSecretWord(vector<string>& wordlist, Master& master) {
        for (int i = 0, x = 0; i < 10 && x < 6; ++i) {
            unordered_map<string, int> count;
            for (string w1 : wordlist)
                for (string w2 : wordlist)
                    if (match(w1, w2) == 0)
                        count[w1]++;
            pair<string, int> minimax = make_pair(wordlist[0], 1000);
            for (string w : wordlist)
                if (count[w] <= minimax.second)
                    minimax = make_pair(w, count[w]);
            x = master.guess(minimax.first);
            vector<string> wordlist2;
            for (string w : wordlist)
                if (match(minimax.first, w) == x)
                    wordlist2.push_back(w);
            wordlist = wordlist2;
        }
    }
    
private:
    int match(string a, string b) {
        int matches = 0;
        for (int i = 0; i < a.length(); ++i) if (a[i] == b[i]) matches ++;
        return matches;
    }
};
```

[solution](https://leetcode.com/problems/guess-the-word/discuss/133862/Random-Guess-and-Minimax-Guess-with-Comparison)



## 46. LeetCode 332 [Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/)

```c++
class Solution {
public:
    vector<string> findItinerary(vector<pair<string, string>> tickets) {
        unordered_map<string, deque<string>> graph;
        for (auto const &ticket : tickets) {
            graph[ticket.first].push_back(ticket.second);
        }
        
        for (auto &route : graph) {
            sort(route.second.begin(), route.second.end());
        }
        
        vector<string> res;
        string from = "JFK";
        dfs(graph, res, from);
        return vector<string>(res.rbegin(), res.rend());
    }
    
private:
    void dfs(unordered_map<string, deque<string>> &graph, vector<string> &res, const string &from) {
        while (!graph[from].empty()) {
            string nxt = graph[from].front();
            graph[from].pop_front();
            dfs(graph, res, nxt);
        }
        res.push_back(from);
    }
};
```



## 47. LeetCode 269 [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/)

```c++
class Solution {
public:
    string alienOrder(vector<string>& words) {
        string res = "";
        if (words.empty()) return res;
        
        unordered_map<char, unordered_set<char>> graph;
        unordered_map<char, int> indegrees;
        unordered_set<char> alphabet; // can be avoided by initializing the indegrees map
        
        for (const string &word : words) {
            for (const char &c : word) {
                alphabet.insert(c); // or set indegrees[c] = 0
            }
        }
        
        // make graph
        int n = words.size();
        for (int i=0; i<n-1; i++) {
            string s1 = words[i];
            string s2 = words[i+1];
            int ptr = 0;
            while (ptr < s1.size() && ptr < s2.size()) {
                if (s1[ptr] == s2[ptr]) {
                    ptr++;
                } else {
                    // avoid duplicate edges!!!
                    if (graph[s1[ptr]].insert(s2[ptr]).second) {
                        indegrees[s2[ptr]]++;
                    }
                    break;
                }
            }
        }
        
        // topological sort
        // can use a queue (store all node with indegree 0) to optimize
        int l = alphabet.size();
        for (int i=0; i<l; i++) {
            auto it = alphabet.begin();
            for (; it != alphabet.end(); it++) {
                //cout << *it << ": " << indegrees[*it] << endl;
                if (indegrees[*it] == 0) {
                    //cout << *it << endl;
                    break;
                }
            }
            if (it == alphabet.end()) return "";
            res.push_back(*it);
            indegrees[*it] = -1;
            for (const char &c : graph[*it]) {
                indegrees[c]--;
            }
            alphabet.erase(it);
        }
        return res;
    }
};
```



## 49. LeetCode 220 [Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/)

```c++
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        set<long> _nums;
        int n = nums.size();
        
        
        for (int i=0; i<n; i++) {
            if (_nums.size() == k + 1) {
                _nums.erase(nums[i-k-1]);
            }
            
            auto it = _nums.lower_bound((long)nums[i] - t);
            if (it != _nums.end() && *it - nums[i] <= t) {
                return true;
            }
            
            _nums.insert(nums[i]);
        }
        
        return false;
    }
};
```



## 50. LeetCode 773 [Sliding Puzzle](https://leetcode.com/problems/sliding-puzzle/)

### (1) DFS

```c++
class Solution {
public:
    int slidingPuzzle(vector<vector<int>>& b) {
        int min_moves = INT_MAX;
        string s = to_string(b[0][0]) + to_string(b[0][1]) + to_string(b[0][2]) 
                    + to_string(b[1][0]) + to_string(b[1][1]) + to_string(b[1][2]);

        dfs(s, unordered_map<string, int>() = {}, s.find('0'), s.find('0'), 0, min_moves);
        return min_moves == INT_MAX ? -1 : min_moves;
    }
    
private:
    unordered_map<int, vector<int>> moves{{0,{1,3}},{1,{0,2,4}},{2,{1,5}},{3,{0,4}},{4,{3,5,1}},{5,{4,2}}};
    
    void dfs(string s, unordered_map<string, int>& m, int cur_zero, int swap_zero, int cur_move, int& min_moves) {
        swap(s[cur_zero], s[swap_zero]);
        if (s == "123450") min_moves = min(min_moves, cur_move);
        if (cur_move < min_moves && (m[s] == 0 || m[s] > cur_move)) {
            m[s] = cur_move;
            for (auto new_zero : moves[swap_zero]) dfs(s, m, swap_zero, new_zero, cur_move + 1, min_moves);
        }
    }
};
```

[solution](https://leetcode.com/problems/sliding-puzzle/discuss/113613/C%2B%2B-9-lines-DFS-and-BFS)



### (2) BFS

```c++
class Solution {
public:
    int slidingPuzzle(vector<vector<int>>& b) {
        string s = to_string(b[0][0]) + to_string(b[0][1]) + to_string(b[0][2])
                    + to_string(b[1][0]) + to_string(b[1][1]) + to_string(b[1][2]);

        unordered_map<string, int> m({{s, 0}});
        queue<pair<string, int>> q({{s, s.find('0')}});

        for (; !q.empty() && q.front().first != "123450"; q.pop()) {
            for (auto new_zero : moves[q.front().second]) {
                auto str = q.front().first;
                swap(str[q.front().second], str[new_zero]);
                if (m.insert({str, m[q.front().first] + 1}).second) q.push({ str, new_zero });
            }   
        }
        return q.empty() ? -1 : m[q.front().first];
    }
    
private:
    unordered_map<int, vector<int>> moves{{0,{1,3}},{1,{0,2,4}},{2,{1,5}},{3,{0,4}},{4,{3,5,1}},{5,{4,2}}};
};
```



## 51. LeetCode 658 [Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/)

```c++
// O(logn + k)
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        if (arr.empty()) return {};
        int n = arr.size();
        int l = 0, r = n - 1;
        int x_pos = -1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (x == arr[mid]) {
                x_pos = mid;
                break;
            } else if (x > arr[mid]) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        
        if (x_pos == -1) {
            x_pos = l;
        }
        
        int start = x_pos, end = x_pos;
        while (k--) {
            if (start == 0) end++;
            else if (end == n) start--;
            else if (x - arr[start-1] <= arr[end] - x) {
                start--;
            } else {
                end++;
            }
        }
        
        return vector<int>(arr.begin()+start, arr.begin()+end);
    }
};

// O(log(n-k))
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int n = arr.size();
        int l = 0, r = n - k;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (x - arr[mid] > arr[mid+k] - x) {
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        
        return vector<int>(arr.begin()+l, arr.begin()+l+k);
    }
};
```



## 52. LeetCode 894 [All Possible Full Binary Trees](https://leetcode.com/problems/all-possible-full-binary-trees/)

```c++
// Time Complexity: O(2^n)
// Space Complexity: O()

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> allPossibleFBT(int N) {
        if (N % 2 == 0) return {};
        return recur(N);
    }
    
private:
    vector<TreeNode*> recur(int n) {
        vector<TreeNode*> res;
        if (n == 1) {
            TreeNode* root = new TreeNode(0);
            res.push_back(root);
            return res;
        }
        
        for (int i=1; i<n; i+=2) {
            for (TreeNode* tn1 : recur(i)) {
                for (TreeNode* tn2 : recur(n - i - 1)) {
                    TreeNode* root = new TreeNode(0);
                    root->left = tn1;
                    root->right = tn2;
                    res.push_back(root);
                }
            }
        }
        return res;
    }
};

// with memo
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> allPossibleFBT(int N) {
        if (N % 2 == 0) return {};
        return recur(N);
    }
    
private:
    unordered_map<int, vector<TreeNode*>> memo;
    
    vector<TreeNode*> recur(int n) {
        vector<TreeNode*> res;
        if (memo.count(n)) return memo[n];
        if (n == 1) {
            TreeNode* root = new TreeNode(0);
            res.push_back(root);
            memo[n] = res;
            return res;
        }
        
        for (int i=1; i<n; i+=2) {
            for (TreeNode* tn1 : recur(i)) {
                for (TreeNode* tn2 : recur(n - i - 1)) {
                    TreeNode* root = new TreeNode(0);
                    root->left = tn1;
                    root->right = tn2;
                    res.push_back(root);
                }
            }
        }
        memo[n] = res;
        return res;
    }
};
```



## 53. LeetCode 568 [Maximum Vacation Days](https://leetcode.com/problems/maximum-vacation-days/)

```c++
class Solution {
public:
    int maxVacationDays(vector<vector<int>>& flights, vector<vector<int>>& days) {
        int maxplay = 0, n = days.size(), k = days[0].size(); // n city , k weeks
        vector<vector<int>> dp(n, vector<int>(k, 0)); // dp[i][j] - max days play if you spent week j in city i;
        for (int j = k - 1; j >= 0; j--)
            for (int i = 0; i < n; i++) {
                dp[i][j] = days[i][j];
                for (int i1 = 0; i1 < n && j < k - 1; i1++)
                    if (flights[i][i1] || i == i1) 
                        dp[i][j] = max(dp[i][j], days[i][j] + dp[i1][j + 1]);
                
                if (j == 0 && (i == 0 || flights[0][i])) 
                    maxplay = max(maxplay, dp[i][0]);
            }
        return maxplay;
    }
};
```

















