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



## 9. LeetCode 359 

```c++

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



## 11. LeetCode 418

```c++

```



## 12. LeetCode 490 

```c++

```



## 13. LeetCode 674 

```c++

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



## 17. LeetCode 723 

```c++

```



## 18. LeetCode 739 

### Related LeetCode 496

Monotone Stack

```c++

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

















































