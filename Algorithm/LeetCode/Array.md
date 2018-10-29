## 1. LeetCode 930 Binary Subarrays With Sum (same as 560)

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



















































