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

