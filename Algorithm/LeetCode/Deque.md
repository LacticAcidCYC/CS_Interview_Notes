## 1. LeetCode 239 [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)

## *Data Structure*

### - deque

[cplusplus-reference](http://www.cplusplus.com/reference/deque/deque/?kw=deque)

```c++
// constructor
deque<T> dq;
{1, 3, 5, 6, 8, 11}
[front, ..., back]

// Iterators:
dq.begin();
dq.end();
dq.rbegin();
dq.rend();

// Capacity:
dq.size();
dq.empty();

// Element access:
operator[];
dq.at(i);
dq.back();
dq.front();

// Modifiers:
dq.push_back(); // add element at th end
dq.push_front(); // add element at the beginning
dq.pop_back(); // delete last element
dq.pop_front(); // delete first element

```



### (1) Deque Solution

```c++
// Time Complexity: O(n) (n = length of nums)
// every number is inserted and deleted once at most, so the time complexity is O(2n)=>O(n)
// Space Complexity: O(k)

class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        deque<int> dq;
        vector<int> res;
        
        for (int i=0; i<n; i++) {
            // pop front element if it is already out of the window
            if (!dq.empty() && dq.front() == i - k) dq.pop_front();
            // pop all back elements which are smaller than the current element
            while (!dq.empty() && nums[dq.back()] < nums[i]) dq.pop_back();
            // push current element
            dq.push_back(i);
            // push current max
            if (i >= k-1) res.push_back(nums[dq.front()]); 
        }
        return res;
    }
};
```



### (2) Two Pass + Window (Greedy?)

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        if (nums.empty()) return {};
        int n = nums.size();
        vector<int> max_left(n);
        vector<int> max_right(n);
        
        // initialization
        max_left[0] = nums[0];
        max_right[n-1] = nums[n-1];
        
        for (int i=1; i<n; i++) {
            max_left[i] = i % k == 0 ? nums[i] : max(max_left[i-1], nums[i]);
            
            int j = n - i - 1;
            max_right[j] = (j % k == 0) ? nums[j] : max(max_right[j+1], nums[j]);
        }
        
        vector<int> sliding_max(n-k+1);
        for (int i=0, j=0; i + k <= n; i++) {
            sliding_max[j++] = max(max_right[i], max_left[i+k-1]);
        }
        
        return sliding_max;
    }
};
```

[solution](https://leetcode.com/problems/sliding-window-maximum/discuss/65881/O(n)-solution-in-Java-with-two-simple-pass-in-the-array)



## 2. LeetCode 862 [Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int shortestSubarray(vector<int>& A, int K) {
        int n = A.size();
        int res = n + 1;
        vector<int> ps(n+1);
        
        for (int i=0; i<n; i++) {
            ps[i+1] = ps[i] + A[i];
        }
        
        deque<int> dq;
        for (int i=0; i<n+1; i++) {
            while (!dq.empty() && (ps[i] - ps[dq.front()] >= K)) {
                res = min(res, i - dq.front());
                dq.pop_front();
            }
            
            while (!dq.empty() && ps[i] <= ps[dq.back()]) {
                dq.pop_back();
            }
            
            dq.push_back(i);
        }
        
        return res <= n ? res : -1;
    }
};
```























