## 1. LeetCode 930 [Binary Subarrays With Sum](https://leetcode.com/problems/binary-subarrays-with-sum/) (same as 560)

In an array `A` of `0`s and `1`s, how many **non-empty** subarrays have sum `S`?

**Count the occurrence of all prefix sum.**

### Prefix Sum

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int numSubarraysWithSum(vector<int>& A, int S) {
        unordered_map<int, int> map;
        map[0] = 1;
        int prefix_sum = 0, cnt = 0;
        for (auto const &i : A) {
            prefix_sum += i;
            cnt += map[prefix_sum - S];
            map[prefix_sum]++;
        }
        return cnt;
    }
};
```

[solution](https://leetcode.com/problems/binary-subarrays-with-sum/discuss/186683/C%2B%2BJavaPython-Straight-Forward)



## 2. LeetCode 560 [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

### Prefix Sum

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int, int> map;
        map[0] = 1;
        int prefix_sum = 0, cnt = 0;
        for (auto const &i : nums) {
            prefix_sum += i;
            cnt += map[prefix_sum-k];
            map[prefix_sum]++;
        }
        return cnt;
    }
};
```



## 3. LeetCode 713 [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)

### Sliding Window

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)
// corner cases: k=0, k=1

class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        int n = nums.size();
        //if (k == 0) { return 0;}
        int left = 0, right = 0, cur_product = 1, cnt = 0;
        while (right < n) {
            cur_product *= nums[right++];
            while (left < right && cur_product >= k) {
                // make sure left < right
                cur_product /= nums[left++];
            }
            cnt += (right - left);
        }
        
        return cnt;
    }
};
```



## 4. LeetCode 152 [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)
// corners: nums.size() == 1

// curMax: maximum product subarray ends with current number
// curMin: minimum product subarray ends with current number

class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int maxP = nums[0];
        int curMax, curMin, lastMax = nums[0], lastMin = nums[0];
        
        for (int i=1; i<nums.size(); i++) {
            if (nums[i] > 0) {
                curMax = max(nums[i], nums[i] * lastMax);
                curMin = min(nums[i], nums[i] * lastMin);
            } else {
                curMax = max(nums[i], nums[i] * lastMin);
                curMin = min(nums[i], nums[i] * lastMax);
            }
            
            maxP = max(maxP, curMax);
            
            lastMin = curMin;
            lastMax = curMax;
        }
        
        return maxP;
    }
};

// improved version
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int maxP = nums[0];
        int curMax = nums[0], curMin = nums[0];
        
        for (int i=1; i<nums.size(); i++) {
            if (nums[i] <= 0) {
                swap(curMax, curMin);
            }
            curMax = max(nums[i], nums[i] * curMax);
            curMin = min(nums[i], nums[i] * curMin);
            
            maxP = max(maxP, curMax);
        }
        
        return maxP;
    }
};
```



## 5. LeetCode 53 [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

### Kadane Algorithm (k = 1 case)

[wiki](https://en.wikipedia.org/wiki/Maximum_subarray_problem)

[geeksforgeeks](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)

### (1) Iterative

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int maxSum = INT_MIN;
        int curSum = 0; // current maximum sum end with current number
        
        for (int i=0; i<n; i++) {
            curSum += nums[i];
            maxSum = max(maxSum, curSum);
            curSum = max(curSum, 0);
        }
        
        return maxSum;
    }
};
```



### (2) Divide and Conquer

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(1)

// T(n) = 2T(n/2) + O(n) => O(nlogn)

class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        return maxSubArraySum(nums, 0, nums.size()-1);
    }

private:
    int maxSubArraySum(vector<int>& nums, int start, int end) {
        if (start == end) {
            return nums[start];
        }
        
        int mid = start + (end - start) / 2;
        
        return max(maxCrossingSum(nums, start, mid, end),
                   max(maxSubArraySum(nums, start, mid),
                       maxSubArraySum(nums, mid+1, end)));
    }
    
    int maxCrossingSum(vector<int>& nums, int start, int mid, int end) {
        int tempSum = 0;
        int leftSum = INT_MIN, rightSum = INT_MIN;
        
        for (int i=mid; i>=start; i--) {
            tempSum += nums[i];
            if (tempSum > leftSum) {
                leftSum = tempSum;
            }
        }
        
        tempSum = 0;
        for (int i=mid+1; i<=end; i++) {
            tempSum += nums[i];
            if (tempSum > rightSum) {
                rightSum = tempSum;
            }
        }
        
        return leftSum + rightSum;
    }
};
```

[geeksforgeeks](https://www.geeksforgeeks.org/maximum-subarray-sum-using-divide-and-conquer-algorithm/)



### (3) Follow Up: What if subarray's length must >= k

### Intuition

Given array: A

(1) Construct dp array: dp (dp[i]: maximum subarray end at i position)

(2) Construct a window of size **k-1** after current visited number;

(3) in the for loop,

int tmp = window_sum + dp[i] (make sure at least k number in subarray)

ans = max(ans, tmp)



e.g. [-7, -3, 2, 5, -1]

-> dp: [-7, -3, 2, 7, 6]



## 6. LeetCode 932 [Beautiful Array](https://leetcode.com/problems/beautiful-array/)

### **Intuition**:

Try to divide and conquer,
so we have left part, right part.

One way is to divide into [1, N / 2] and [N / 2 + 1, N].
But it will cause problems when we merge them.

Another way is to divide into odds part and evens part.
So there is no `k` with `A[k] * 2 = odd + even`

I brute force all permutations when N = 5:
20 beautiful array found,
only 4 don't fit odd + even pattern:
[2, 1, 4, 5, 3]

[3, 1, 2, 5, 4]

[3, 5, 4, 1, 2]

[4, 5, 2, 1, 3]



### **Beautiful Array Properties**

Saying that an array is beautiful,
there is no `i < k < j`,
such that `A[k] * 2 = A[i] + A[j]`

Apply these 3 following changes a beautiful array,
we can get a new beautiful array

**1. Deletion**
Easy to prove.

**2. Addition**
If we have `A[k] * 2 != A[i] + A[j]`,
`(A[k] + x) * 2 = A[k] * 2 + 2x != A[i] + A[j] + 2x = (A[i] + x) + (A[j] + x)`

E.g: `[1,3,2] + 1 = [2,4,3]`.

**3. Multiplication**
If we have `A[k] * 2 != A[i] + A[j]`,
`(A[k] * x) * 2 = A[k] * 2 * x != (A[i] + A[j]) * x = (A[i] * x) + (A[j] * x)`

E.g: `[1,3,2] * 2 = [2,6,4]`



### **Explanation**

With the observations above, we can easily construct any beautiful array.
Assume we have a beautiful array `A` with length `N`

`A1 = A * 2 - 1` is beautiful with only odds from `1` to `N * 2 -1`
`A2 = A * 2` is beautiful with only even from `2` to `N * 2`
`B = A1 + A2` beautiful array with length `N * 2`

E.g:

```
A = [2, 1, 4, 5, 3]
A1 = [3, 1, 7, 9, 5]
A2 = [4, 2, 8, 10, 6]
B = A1 + A2 = [3, 1, 7, 9, 5, 4, 2, 8, 10, 6]
```

**Time Complexity**:

I have iteration version here `O(N)`
Naive recursion is `O(NlogN)`
Recursion with one call or with cache is `O(N)`



### Code:

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    vector<int> beautifulArray(int N) {
        vector<int> ans{1};
        
        while (ans.size() < N) {
            vector<int> temp;
            for (auto const &i : ans) {
                if (i*2-1 <= N) {
                    temp.push_back(i*2-1);
                }
            }
            for (auto const &i : ans) {
                if (i*2 <= N) {
                    temp.push_back(i*2);
                }
            }
            ans = temp;
        }
        
        return ans;
    }
};
```

[solution](https://leetcode.com/problems/beautiful-array/discuss/186679/)



## 7. LeetCode 325 [Maximum Size Subarray Sum Equals k](https://leetcode.com/problems/maximum-size-subarray-sum-equals-k/)

### Key: array elment can be negative, zero or positive

### Prefix Sum

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int maxSubArrayLen(vector<int>& nums, int k) {
        unordered_map<int, int> map;
        int maxLen = 0;
        int n = nums.size();
        int sum = 0;
        map[0] = -1;
        
        for (int i=0; i<n; i++) {
            sum += nums[i];
            if (map.count(sum - k)) {
                maxLen = max(maxLen, i-map[sum-k]);
            }
            if (!map.count(sum)) {
                map[sum] = i;
            }
        }
        
        return maxLen;
    }
};
```

### Similar Question: LeetCode 560, LeetCode 209



## 8. LeetCode 628 [Maximum Product of Three Numbers](https://leetcode.com/problems/maximum-product-of-three-numbers/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int maximumProduct(vector<int>& nums) {
        int min1 = INT_MAX, min2 = INT_MAX;
        int max1 = INT_MIN, max2 = INT_MIN, max3 = INT_MIN;
        
        for (auto const &i : nums) {
            if (i <= min1) {
                min2 = min1;
                min1 = i;
            } else if (i < min2) {
                min2 = i;
            } 
            
            if (i >= max1) {
                max3 = max2;
                max2 = max1;
                max1 = i;
            } else if (i >= max2) {
                max3 = max2;
                max2 = i;
            } else if (i > max3) {
                max3 = i;
            }
        }
        
        return max(max1 * max2 * max3, max1 * min1 * min2);
    }
};
```



## 9. LeetCode 724 [Find Pivot Index](https://leetcode.com/problems/find-pivot-index/)

### Prefix Sum

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return -1;
        if (n == 1) return 0;
        vector<int> sums(n+1, 0); // sums[i]: nums[0...i-1]
        
        for (int i=1; i<=n; i++) {
            sums[i] = nums[i-1] + sums[i-1];
        }
        
        for (int i=0; i<n; i++) {
            if (sums[i] == (sums[n] - sums[i+1])) {
                return i;
            }
        }
        
        return -1;
    }
};
```



## 10. LeetCode 523 [Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/)

### Explanation

If k != 0:

Check the remainder of each prefix sum, if the remainder already exists and the index of it is smaller than i-1(i: current index), which make sure the size of subarray is at least 2, we find the subarray which meets the requirement.

k == 0:

Check if there are two continuous 0.

**These two situations can be merged into one process!!**

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

// just use an variable to store sum
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        if (n < 2) return false;
        k = abs(k);
        
        int sum = 0;
        unordered_map<int, int> map;
        // pay attention to the initialization of map[0]
        map[0] = -1;
        for (int i=0; i<n; i++) {
            sum += nums[i];
            if (k > 0) {
                sum = sum % k;
            }
            
            if (map.count(sum)) {
                if(map[sum] < i-1) {
                    return true;
                }
            } else {
                map[sum] = i;
            }
        }
        
        return false;
    }
};

// prefixsum array
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        if (n < 2) return false;
        k = abs(k);
        
        vector<int> sum_remainders(n+1, 0);
        unordered_map<int, int> map;
        map[0] = -1;
        for (int i=1; i<=n; i++) {
            sum_remainders[i] = sum_remainders[i-1] + nums[i-1];
            if (k > 0) {
                sum_remainders[i] = sum_remainders[i] % k;
            }
            
            if (map.count(sum_remainders[i])) {
                if(map[sum_remainders[i]] < i-2) {
                    return true;
                }
            } else {
                map[sum_remainders[i]] = i-1;
            }
        }
        
        return false;
    }
};

// original
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        if (n < 2) return false;
        if (k == 0) {
            int index_zero = -1;
            for (int i=0; i<n; i++) {
                if (nums[i] == 0) {
                    if(index_zero != -1 && i-index_zero == 1) {
                        return true;
                    }
                    index_zero = i;
                }
            }
            return false;
        } 
        
        vector<int> sums(n+1, 0);
        unordered_map<int, int> map;
        map[0] = -1;
        for (int i=1; i<=n; i++) {
            sums[i] = (sums[i-1] + nums[i-1]) % k;
            if (map.count(sums[i])) {
                if(i-1-map[sums[i]] > 1) {
                    return true;
                }
            } else {
                map[sums[i]] = i-1;
            }
        }
        
        return false;
    }
};
```



## 11. LeetCode 56 [Merge Intervals](https://leetcode.com/problems/merge-intervals/)

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(1)

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
    vector<Interval> merge(vector<Interval>& intervals) {
        int n = intervals.size();
        if (n == 0) return {};
        sort(intervals.begin(), intervals.end(), [](Interval &A, Interval &B) {
            return A.start < B.start;
        });
        vector<Interval> ans;
        ans.push_back(intervals[0]);
        
        for (int i=1; i<n; i++) {
            if (intervals[i].start > ans.back().end) {
                ans.push_back(intervals[i]);
            } else {
                // pay attention to the situation that: ans.back().end > intervals[i].end
                ans.back().end = max(intervals[i].end, ans.back().end);
            }
        }
        
        return ans;
    }
};
```



## 12. LeetCode 57 [Insert Interval](https://leetcode.com/problems/insert-interval/)

### (1) Binary Search + processing

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

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
    vector<Interval> insert(vector<Interval>& intervals, Interval newInterval) {
        int n = intervals.size();
        if (n == 0) {
            return vector<Interval> {newInterval};
        }
        int left = 0, right = n - 1;
        // find the upper_bound of newInterval.start
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (intervals[mid].start >= newInterval.start) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        // if newInterval.start is the largest start time (not <= here!!!)
        if (intervals[left].start < newInterval.start) {
            if (intervals[left].end >= newInterval.start) {
                intervals[left].end = max(intervals[left].end, newInterval.end);
            } else {
                intervals.push_back(newInterval);
            }
            return intervals;
        }
        
        // range constructor: [begin, end)
        vector<Interval> ans(intervals.begin(), intervals.begin()+left);
        // pay attention to the situation that left = 0 here
        if (ans.empty() || ans.back().end < newInterval.start) {
            ans.push_back(newInterval);
        } else {
            ans.back().end = max(ans.back().end, newInterval.end);
        }
        
        for (int i=left; i<n; i++) {
            if (intervals[i].start > ans.back().end) {
                ans.push_back(intervals[i]);
            } else {
                ans.back().end = max(intervals[i].end, ans.back().end);
            }
        }
        
        return ans;
    }
};
```



### (2) One pass

```c++
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
    vector<Interval> insert(vector<Interval>& intervals, Interval newInterval) {
        int n = intervals.size();
        vector<Interval> ans;
        
        for (int i=0; i<n; i++) {
            if (intervals[i].end < newInterval.start) {
                ans.push_back(intervals[i]);
            } else if (intervals[i].start > newInterval.end) {
                ans.push_back(newInterval);
                for (int j=i; j<n; j++) {
                    ans.push_back(intervals[j]);
                }
                return ans;
            } else {
                newInterval.start = min(intervals[i].start, newInterval.start);
                newInterval.end = max(intervals[i].end, newInterval.end);
            }
        }
        ans.push_back(newInterval);
        
        return ans;
    }
};
```



## 13. LeetCode 42 [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

### Two Pointer

My idea is similiar to my idol(lol) [StefanPochmann](https://leetcode.com/problems/trapping-rain-water/discuss/17364/7-lines-C-C++), while this algorithm keeps track of the total water assuming that the intermediate space is empty. In each step, recalculate the total water.

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        if (n <= 2) return 0;
        // two pointers and height of the container (lower height in two sides)
        int left = 0, right = n - 1, level = min(height[left], height[right]);
        int res = level * (right - left - 1); // initialize the beginning possible volume of watre
        while (right - left > 1) { // when left == right - 1, exit the while loop
            // move the lower side, because we use that side to count the height(level)
            int lower = height[(height[left] <= height[right]) ? ++left : --right];
            if (lower > level) { // if next height is higher than level, we need to update the level now and add more possible water inside
                res -= level;
                res += min(height[left]-level, height[right]-level) * (right - left - 1);
                level = min(height[left], height[right]);
            } else {// if next height is smaller than level, then height of container is still level
                res -= lower;
            }
        }
        
        return res;
    }
};
```

[solution](https://leetcode.com/problems/trapping-rain-water/discuss/17364/7-lines-C-C++)

[mypost](https://leetcode.com/problems/trapping-rain-water/discuss/151477/Share-my-c%2B%2B-code(8ms))



## 14. LeetCode 26 [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if (nums.empty()) return 0;
        int j = 1;
        for (int i=1; i<nums.size(); i++) {
            if (nums[i] > nums[i-1]) {
                nums[j++] = nums[i];
            }
        }
        nums.resize(j);
        return j;
    }
};

// improved
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if (nums.empty()) return 0;
        int j = 0;
        for (auto i : nums) {
            if (j == 0 || i > nums[j-1]) {
                nums[j++] = i;
            }
        }
        return j;
    }
};
```



## 15. LeetCode 80 [Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)

### (1) two pointers

12345

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if (nums.empty()) return 0;
        int left = 0, right = 0, cnt = 0;
        int cur = nums[left];
        
        while (right < nums.size()) {
            if (nums[right] == cur) {
                if (cnt++ < 2) {
                    nums[left++] = cur;
                }
            } else {
                cnt = 1;
                cur = nums[right];
                nums[left++] = cur;
            }
            right++;
        }
        return left;
    }
};
```



### (2) Similar to the idea in LeetCode 26

11223344

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if (nums.empty()) return 0;
        int j = 0;
        
        for (auto i : nums) {
            if (j < 2 || i > nums[j-2]) {
                nums[j++] = i;
            }
        }
        
        return j;
    }
};
```

[solution](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/discuss/27976/3-6-easy-lines-C%2B%2B-Java-Python-Ruby)



## 16. LeetCode 128 [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> num_set;
        
        for (int num : nums) {
            num_set.insert(num);
        }
        
        int length = 0;
        for (int num : num_set) {
            if (!num_set.count(num-1)) {
                int cur_num = num;
                int cur_len = 1;
                
                while (num_set.count(cur_num+1)) {
                    cur_num++;
                    cur_len++;
                }
                
                length = max(length, cur_len);
            }
        }
        return length;
    }
};
```

[solution](https://leetcode.com/problems/longest-consecutive-sequence/discuss/41057/Simple-O(n)-with-Explanation-Just-walk-each-streak)



## 17. LeetCode 941 [Valid Mountain Array](https://leetcode.com/problems/valid-mountain-array/)

### Explanation

One-pass:

Use a staus variable to record the process of going up(0) and going down(1). And the initial status is -1.

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

// original
class Solution {
public:
    bool validMountainArray(vector<int>& A) {
        int n = A.size();
        if (n < 3) return false;
        
        int up = -1;
        
        for (int i=1; i<n; i++) {
            if (A[i] < A[i-1]) {
                if (up == -1) return false;
                up = 1;
            } else if (A[i] == A[i-1]) {
                return false;
            } else {
                if (up == 1) return false;
                up = 0;
            }
        }
        
        return up == 1;
    }
};
```



### Better

**Intuition**

If we walk along the mountain from left to right, we have to move strictly up, then strictly down.

**Algorithm**

Scan the array to find the peak, check if the peak is the first or the last number, return false;

Then keep scanning the array to the end, if the end can be reached, then return true, otherwise return false.

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    bool validMountainArray(vector<int>& A) {
        int n = A.size();
        if (n < 3) return false;
        
        int i = 0;
        
        while (i < n-1 && A[i] < A[i+1]) {
            i++;
        }
        
        if (i == 0 || i == n-1) return false;
        
        while (i < n-1 && A[i] > A[i+1]) {
            i++;
        }
        
        return i == n-1;
    }
};
```



## 18. LeetCode 674 [Longest Continuous Increasing Subsequence](https://leetcode.com/problems/longest-continuous-increasing-subsequence/)

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



## 19. LeetCode 277 [Find the Celebrity](https://leetcode.com/problems/find-the-celebrity/)

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



## 20. LeetCode 54 [Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)

```c++
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        if(matrix.empty()) return vector<int>();
        int m = (int)matrix.size();
        int n = (int)matrix[0].size();
        vector<int> res;
        //4 bounds
        int up = 0, down = m-1, left = 0, right = n-1;
        while(true) {
            for(int c=left; c<=right; c++) res.push_back(matrix[up][c]);
            if(++up > down) break;

            for(int r=up; r<=down; r++) res.push_back(matrix[r][right]);
            if(--right < left) break;

            for(int c=right; c>=left; c--) res.push_back(matrix[down][c]);
            if(--down < up) break;

            for(int r=down; r>=up; r--) res.push_back(matrix[r][left]);
            if(++left > right) break;
        }
        return res;
    }
};
```



## 21. LeetCode 918 [Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/)

### Intuition

LeetCode 53 Maximum Subarray

### (1) normal + circular (Solution 1)

```c++
// Time Complexity: O(2n)
// can be optimized to O(n) => just one pass

class Solution {
public:
    int maxSubarraySumCircular(vector<int>& A) {
        int maxSum = maxSubArray(A);
        int minSum = minSubArray(A);
        int sum = accumulate(A.begin(), A.end(), 0);
        
        if (minSum == sum) {
            return maxSum;
        }
        
        return max(maxSum, sum - minSum);
    }
    
private:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int maxSum = INT_MIN;
        int curSum = 0;
        
        for (int i=0; i<n; i++) {
            curSum += nums[i];
            maxSum = max(maxSum, curSum);
            curSum = max(curSum, 0);
        }
        
        return maxSum;
    }
    
    int minSubArray(vector<int>& nums) {
        int n = nums.size();
        int minSum = INT_MAX;
        int curSum = 0;
        
        for (int i=0; i<n; i++) {
            curSum += nums[i];
            minSum = min(minSum, curSum);
            curSum = min(curSum, 0);
        }
        
        return minSum;
    }
};
```



### (2) normal + circular (neglect first and last element)

```c++
// Time Complexity: O(2n)
// can be optimized to O(n) => just one pass

class Solution {
public:
    int maxSubarraySumCircular(vector<int>& A) {
        if (A.size() == 1) return A[0];
        vector<int> A_(A.begin()+1, A.end()-1);
        
        int maxSum = maxSubArray(A);
        int minSum = minSubArray(A_);
        int sum = accumulate(A.begin(), A.end(), 0);
        
        return max(maxSum, sum - minSum);
    }
    
private:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int maxSum = INT_MIN;
        int curSum = 0;
        
        for (int i=0; i<n; i++) {
            curSum += nums[i];
            maxSum = max(maxSum, curSum);
            curSum = max(curSum, 0);
        }
        
        return maxSum;
    }
    
    int minSubArray(vector<int>& nums) {
        int n = nums.size();
        int minSum = INT_MAX;
        int curSum = 0;
        
        for (int i=0; i<n; i++) {
            curSum += nums[i];
            minSum = min(minSum, curSum);
            curSum = min(curSum, 0);
        }
        
        return minSum;
    }
};
```



## 22. LeetCode 845 [Longest Mountain in Array](https://leetcode.com/problems/longest-mountain-in-array/)

```c++
class Solution {
public:
    int longestMountain(vector<int>& A) {
        int n = A.size();
        if (n < 3) return 0;
        
        int l = 0, r = 0;
        bool up = false, down = false;
        int mountain_len = INT_MIN;
        
        while (r < n) {
            while (r < n - 1 && A[r] < A[r + 1]) {
                r++;
                up = true;
            }
            
            while (r < n - 1 && A[r] > A[r + 1]) {
                r++;
                down = true;
            }
            
            // cout << l << "," << r << endl;
            // cout << up << "," << down << endl;
            // cout << endl;
            
            if (up && down) {
                mountain_len = max(mountain_len, r - l + 1);
            }
            
            up = false;
            down = false;
            
            while (r < n - 1 && A[r] == A[r+1]) r++;
            
            l = r;
            if (r == n-1) break;
        }
        
        if (up && down) mountain_len = max(mountain_len, r - l + 1);
        
        return mountain_len == INT_MIN ? 0 : mountain_len;
    }
};

class Solution {
public:
    int longestMountain(vector<int>& A) {
        int n = A.size();
        if (n < 3) return 0;
        
        int l = 0, r = 0;
        bool up = false, down = false;
        int mountain_len = INT_MIN;
        
        while (r < n - 1) {
            while (r < n - 1 && A[r] < A[r + 1]) {
                r++;
                up = true;
            }
            
            while (r < n - 1 && A[r] > A[r + 1]) {
                r++;
                down = true;
            }
            
            if (up && down) {
                mountain_len = max(mountain_len, r - l + 1);
            }
            
            up = false;
            down = false;
            
            while (r < n - 1 && A[r] == A[r+1]) r++;
            
            l = r;
            //if (r == n-1) break;
        }
        
        if (up && down) mountain_len = max(mountain_len, r - l + 1);
        
        return mountain_len == INT_MIN ? 0 : mountain_len;
    }
};
```



## 23. LeetCode 723 

```c++

```

[solution](https://leetcode.com/problems/candy-crush/discuss/109221/AC-JAVA-Solution-easy-to-understand)



## 24. LeetCode 561 [Array Partition I](https://leetcode.com/problems/array-partition-i/)

```c++
class Solution {
public:
    int arrayPairSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        int res = 0;
        for (int i=0; i<n; i+=2) {
            res += nums[i];
        }
        
        return res;
    }
};
```



## 25. LeetCode 849 [Maximize Distance to Closest Person](https://leetcode.com/problems/maximize-distance-to-closest-person/)

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

































