## 1. LeetCode 930 Binary Subarrays With Sum (same as 560)

In an array `A` of `0`s and `1`s, how many **non-empty** subarrays have sum `S`?

Count the occurrence of all prefix sum.

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

sliding window!

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

### (1) Iterative

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
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



## 6. LeetCode 325

```c++

```





















































