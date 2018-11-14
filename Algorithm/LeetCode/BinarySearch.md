# Binary Search

## Key

### (1) If using

```c++
left = mid + 1;
right = mid;

=> while condition: left < right
mid = left + (right - left) / 2;
```



### (2) If using

```c++
left = mid;
right = mid - 1;

=> while condition: left < right
mid = left + (right - left) / 2 + 1;
```



### (3) If using

```c++
left = mid + 1;
right = mid - 1;

=> while condition: left <= right
mid = left + (right - left) / 2; OR mid = left + (right - left) / 2 + 1;
```





## 1. LeetCode 34 [Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)



```c++
// Time Complexity: O(logn)
// Space Complexity: O(1)

class Solution {
public:
    vector<int> searchRange(vector<int>& A, int target) {
        if (A.empty()) return {-1,-1};
        int n = A.size();
        int i = 0, j = n - 1;
        vector<int> ret(2, -1);
        // Search for the left one
        while (i < j)
        {
            int mid = i + (j - i) / 2;
            if (A[mid] < target) i = mid + 1;
            else j = mid;
        }
        if (A[i] != target) return ret;
        else ret[0] = i;

        // Search for the right one
        j = n-1;  // We don't have to set i to 0 the second time.
        while (i < j)
        {
            int mid = i + (j - i) / 2 + 1;	// Make mid biased to the right
            if (A[mid] > target) j = mid - 1;  
            else i = mid;				// So that this won't make the search range stuck.
        }
        ret[1] = i;
        return ret; 
    }
};
```





## 2. LeetCode 4 [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)



```c++
// Time Complexity: O(log(min(len1, len2)))
// Space Complexity: O(1)

class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        const int l1 = nums1.size();
        const int l2 = nums2.size();
        if (l1 > l2) {
            return findMedianSortedArrays(nums2, nums1);
        }
        
        // find A(m1), A(m1-1), B(m2), B(m2-1)
        // m1 + m2 = k = (l1 + l2 + 1) / 2
        // just do binary search on shorter array
        const int k = (l1 + l2 + 1) / 2;
        int left = 0, right = l1;
        while (left < right) {
            int mid1 = left + (right - left) / 2;
            int mid2 = k - mid1;
            if (nums1[mid1] < nums2[mid2-1]) {
                left = mid1 + 1;
            } else {
                right = mid1;
            }
        }
        
        const int m1 = left;
        const int m2 = k - left;
        
        // make sure check corner cases!
        // C(k-1) = max(A(m1-1), B(m2-1)) 
        const int c1 = max(m1 == 0 ? INT_MIN : nums1[m1-1], 
                           m2 == 0 ? INT_MIN : nums2[m2-1]);
        if ((l1 + l2) % 2 == 1) {
            return c1;
        }
        
        // C(k) = min(A(m1), B(m2)) 
        const int c2 = min(m1 == l1 ? INT_MAX : nums1[m1], 
                           m2 == l2 ? INT_MAX : nums2[m2]);
        return (c1 + c2) / 2.0;
    }
};
```

[reference](https://zxi.mytechroad.com/blog/algorithms/binary-search/leetcode-4-median-of-two-sorted-arrays/)



## 3. LeetCode 278 [First Bad Version](https://leetcode.com/problems/first-bad-version/)



```c++
// Time Complexity: O(logn)
// Space Complexity: O(1)

// Forward declaration of isBadVersion API.
bool isBadVersion(int version);

class Solution {
public:
    int firstBadVersion(int n) {
        int l = 1, r = n;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (isBadVersion(mid)) {
                r = mid;
            } else {
                l = mid + 1;
            }
        }
        return l;
    }
};
```



## 4. LeetCode 162 [Find Peak Element](https://leetcode.com/problems/find-peak-element/)



```c++
// Time Complexity: O(logn)
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
                r = mid - 1;
                // r = mid;
            } else {
                l = mid + 1;
            }
        }
        return l;
    }
};
```



## 5. LeetCode 33 [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)



```c++
// Time Complexity: O(logn)
// Space Complexity: O(1)

int search(vector<int>& nums, int target) {
    if(!nums.size()) return -1;
    int n = nums.size();
    if(target < nums[0] && target > nums[n-1]) return -1;
    int left = 0, right = n-1;
    // when doing target check inside while loop
    // loop condition must be left <= right
    // or do extra check outside the loop
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if(nums[mid] == target) return mid;
        else if(nums[mid] >= nums[left]) {
            // check whether target is in [nums[left], nums[mid]) and in the left part
            if(target < nums[mid] && target >= nums[left]) right = mid - 1; // right = mid;
            else left = mid + 1;
        } else {
            // nums[mid] is in the right part
            // check whether target is in (nums[mid], nums[right]] and in the right part
            if(target > nums[mid] && target <= nums[right]) left = mid + 1;
            else right = mid - 1; // right = mid;
        }
    }
    return -1;
}
```



## 6. LeetCode 153 [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

```c++
// Time Complexity: O(logn)
// Space Complexity: O(1)

class Solution {
public:
    int findMin(vector<int>& nums) {
        int n = (int) nums.size();
        int l = 0, r = n - 1;
        while (l < r) {
            // If already in the right part or original array is in ascending order
            if (nums[r] > nums[l]) return nums[l];
            int mid = l + (r - l) / 2;
            if (nums[mid] < nums[mid-1] && nums[mid] < nums[mid+1]) return nums[mid];
            else if (nums[mid] >= nums[l]) {
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        return nums[l];
    }
};

// consider corner case:
// n = 1
// n = 0
// array is in ascending order
```



```c++
// Time Complexity: O(logn)
// Space Complexity: O(1)

class Solution {
public:
    int findMin(vector<int>& nums) {
        int n = nums.size();
        int left = 0, right = n-1;
        int minNum = INT_MAX;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] >= nums[left]) {
                minNum = min(minNum, nums[left]);
                left = mid + 1;
            } else {
                minNum = min(minNum, nums[mid]);
                right = mid - 1;
            }
        }
        return minNum;
    }
};
```





## 7. LeetCode 35 [Search Insert Position](https://leetcode.com/problems/search-insert-position/)



```c++
// Time Complexity: O(logn)
// Space Complexity: O(1)

// find upper_bound or target index
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int n = (int) nums.size();
        int l = 0, r = n - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (nums[mid] == target) return mid;
            else if (nums[mid] < target) {
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        // check the last number && corner case!!
        return nums[l] >= target ? l : l+1;
    }
};
```



## 8. LeetCode 270 [Closest Binary Search Tree Value](https://leetcode.com/problems/closest-binary-search-tree-value/)

### (1) binary search

```c++
// Time Complexity: O(logn) (n = height)
// Space Complexity: O(1)

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
    int closestValue(TreeNode* root, double target) {
        // dont initialize to INT_MAX or INT_MIN
        int upper_bound = root->val;
        int lower_bound = root->val;
        while (root) {
            if (root->val == target) return root->val;
            else if (root->val > target) {
                upper_bound = root->val;
                root = root->left;
            } else {
                lower_bound = root->val;
                root = root->right;
            }
        }
        return abs(target-upper_bound) > abs(target-lower_bound) ? lower_bound : upper_bound;
    }
};

class Solution {
public:
    int closestValue(TreeNode* root, double target) {
        int closest = root->val;
        while (root) {
            if (abs(closest-target) >= abs(root->val-target)) {
                closest = root->val;
            }
            root = target < root->val ? root->left : root->right;
        }
        return closest;
    }
};
```



### (2) recursive

```c++
// Time Complexity: O(logn) (n=height)
// Space Complexity: O(1)

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
    int closestValue(TreeNode* root, double target) {
        auto a = root->val;
        auto child = target < a ? root->left : root->right;
        if (!child) return a;
        auto b = closestValue(child, target);
        return abs(target-a) >= abs(target-b) ? b : a;
    }
};
```





## 9. LeetCode 378 [Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)

### Related data structure & function:

#### A. priority_queue

```c++
template <class T, class Container = vector<T>,
  class Compare = less<typename Container::value_type> > class priority_queue;
```

#### B. make_pair() && pair<T1,T2> && {}

```c++
// c++11
template <class T1, class T2>
  pair<V1,V2> make_pair (T1&& x, T2&& y);  // see below for definition of V1 and V2
  
pair<V1,V2>(std::forward<T1>(x),std::forward<T2>(y))
// Where the types V1 and V2 are the decay equivalents of T1 and T2, respectively (except for // reference_wrapper types, for which the corresponding reference type is used instead).
    
// If T1 and/or T2 are rvalue references, the objects are moved and x and/or y are left in an undefined but valid state.
```

*{} initialization:*

[reference1](http://www.enseignement.polytechnique.fr/informatique/INF478/docs/Cpp/en/cpp/language/value_initialization.html)

[reference2](http://www.modernescpp.com/index.php/initialization)

#### C. upper_bound()

```c++
template <class ForwardIterator, class T>
  ForwardIterator upper_bound (ForwardIterator first, ForwardIterator last,
                               const T& val);
```



### (1) max-heap (priority_queue)

```c++
// max-heap
// Time Complexity: O(n^2logk)
// Space Complexity: O(1)

int kthSmallest(vector<vector<int>>& matrix, int k) {
    int n = (int) matrix.size();
    priority_queue<int, vector<int>> pq;
    int l = n * n;
    for (int i=0; i<k; i++) {
        pq.push(matrix[i/n][i%n]);
    }
    for (int i=k; i<l; i++) {
        int cur = matrix[i/n][i%n];
        if (pq.top() > cur) {
            pq.pop();
            pq.push(cur);
        }
    }
    return pq.top();
}
```



### (2) min-heap (priority_queue)

```c++
// min-heap
// Time Complexity: O(klogn) (n: row of the matrix)
// Space Complexity: O(n)

struct compare {
    bool operator()(const pair<int, pair<int, int>> &A, const pair<int, pair<int, int>> &B) {
        return A.first > B.first;
    }
};

int kthSmallest2(vector<vector<int>>& matrix, int k) {
    int n = (int) matrix.size();
    priority_queue<pair<int, pair<int, int>>, vector<pair<int, pair<int, int>>>, greater<pair<int, pair<int, int>>>> pq;
    
    for (int i=0; i<n; i++) {
//        pq.push(make_pair(matrix[i][0], make_pair(i, 0)));
        pq.push({ matrix[i][0], { i, 0 }});
    }
    
    while (k-- > 1) {
        int i = pq.top().second.first;
        int j = pq.top().second.second;
        pq.pop();
        if (j < n-1) {
//            pq.push(make_pair(matrix[i][j+1], make_pair(i, j+1)));
            pq.push({ matrix[i][j+1], { i, j+1 }});
        }
    }
    return pq.top().first;
}
```



### (3) binary search + search space reduction

```c++
// Time Complexity: O(nlognlog(max-min)) => O(nlog(max-min))
// Space Complexity: O(1)

int kthSmallest3(vector<vector<int>>& matrix, int k) {
    int n = (int) matrix.size();
    int low = matrix[0][0], high = matrix[n-1][n-1];
    
    while (low < high) {
        int mid = low + (high - low) / 2;
        int cnt = getLessEqual(matrix, mid);
        // O(nlogn)
//        for (int i=0; i<n; i++) {
//            cnt += (upper_bound(matrix[i].begin(), matrix[i].end(), mid) - matrix[i].begin());
//        }
        if (cnt < k) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }
    
    return low;
}

int getLessEqual(vector<vector<int>>& matrix, int val) {
    int n = (int) matrix.size();
    int i = n - 1, j = 0;
    int cnt = 0;
    while (i >= 0 && j < n) {
        if (matrix[i][j] > val) {
            i--;
        } else {
            cnt += (i+1);
            j++;
        }
    }
    return cnt;
}
```

*Similar Question: LeetCode 240*



## 10. LeetCode 74 [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)

### (1) column binary search + row binary search

```c++
// Time Complexity: O(log(m) + log(n))
// Space Complexity: O(1)

bool searchMatrix(vector<vector<int>>& matrix, int target) {
    if (matrix.empty() || matrix[0].empty()) return false;
    int m = matrix.size();
    int n = matrix[0].size();
    // check whether target number is in [matrix[0][0], matrix[m-1][n-1]]
    if (target < matrix[0][0] || target > matrix[m-1][n-1]) {
        return false;
    }
    int l = 0, r = m-1;
    // column bs: find the row that may contain the target number
    while (l < r) {
        int mid = l + (r - l) / 2 + 1;
        if (matrix[mid][0] == target) { return true;}
        if (matrix[mid][0] > target) {
            r = mid - 1;
        } else {
            l = mid;
        }
    }
    // check whether target number is in [matrix[l][0], matrix[l][n-1]]
    if (target < matrix[l][0] || target > matrix[l][n-1]) return false;
    int row = l;
    l = 0;
    r = n-1;
    // search in the row to find if it contains the target number 
    while (l <= r) {
        int mid = l + (r - l) / 2;
        if (matrix[row][mid] == target) { return true;}
        if (matrix[row][mid] > target) {
            r = mid - 1;
        } else {
            l = mid + 1;
        }
    }
    return false;
}
```



### (2) directed search in matrix (left = 0, right = m * n-1)

```c++
// Time Complexity: O(log(m*n))
// Space Complexity: O(1)

bool searchMatrix(vector<vector<int>>& matrix, int target) {
    if (matrix.empty() || matrix[0].empty()) return false;
    int m = matrix.size();
    int n = matrix[0].size();
    if (target < matrix[0][0] || target > matrix[m-1][n-1]) {
        return false;
    }
    int l = 0, r = m * n - 1;
    while (l <= r) {
        int mid = l + (r - l) / 2;
        int mid_val = matrix[mid/n][mid%n];
        if (mid_val == target) { return true;}
        if (mid_val > target) {
            r = mid - 1;
        } else {
            l = mid + 1;
        }
    }
    return false;
}
```



## 11. LeetCode 240 [Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)

### (1) Brute force

```c++
// Time Complexity: O(mn)
// Space Complexity: O(1)

```



### (2) Binary Search

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(1)

```



### (3) Divide and Conquer

```c++
//Time Complexity: O(nlogn) => O(logn)
//Space Complexity: O(logn)

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.size() == 0 || matrix[0].size() == 0) return false;
        int m = matrix.size();
        int n = matrix[0].size();
        return searchSubMatrix(0, 0, n-1, m-1, matrix, target);
    }
    
private:
    bool searchSubMatrix(int left, int top, int right, int bottom, vector<vector<int>>& matrix, int target) {
        if (left > right || top > bottom) {
            return false;
        } else if (target < matrix[top][left] || target > matrix[bottom][right]) {
            return false;
        }
        
        int mid = left + (right - left) / 2;
        // Locate `row` such that matrix[row-1][mid] < target < matrix[row][mid]
        int row_top = top, row_bottom = bottom;
        while (row_top < row_bottom) {
            int mid_row = row_top + (row_bottom - row_top) / 2;
            if (matrix[mid_row][mid] == target) {
                return true;
            } else if (matrix[mid_row][mid] < target) {
                row_top = mid_row + 1;
            } else {
                row_bottom = mid_row;
            }
        }
        // pay attention to the missed 'matrix[row_top][mid]'
        if (matrix[row_top][mid] == target) {
            return true;
        }
        // pay attention to the possible missed line (right of 'matrix[row_top][mid]')
        return searchSubMatrix(left, row_top, mid-1, bottom, matrix, target) || searchSubMatrix(mid+1, top, right, row_top, matrix, target);
    }
};
```

### (4) Search Space Reduction

```c++
// Time Complexity: O(m+n)
// Space Complexity: O(1)

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty() || matrix[0].empty()) return false;
        int m = matrix.size();
        int n = matrix[0].size();
        if (target < matrix[0][0] || target > matrix[m-1][n-1]) return false;
        
        int row = m - 1, col = 0;
        while (row >= 0 && col <= n-1) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                row--;
            } else {
                col++;
            }
        }
        return false;
    }
};
```



## 12. LeetCode 704 [Binary Search](https://leetcode.com/problems/binary-search/)

```c++
// Time Complexity: O(logn)
// Space Complexity: O(1)

class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = (int) nums.size();
        int l = 0, r = n-1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (nums[mid] == target) return mid;
            else if (nums[mid] < target) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return -1;
    }
};
```



## 13. LeetCode 222 [Count Complete Tree Nodes](https://leetcode.com/problems/count-complete-tree-nodes/)

```c++
// Time Complexity: O((logn)^2)
// Space Complexity: O(1)

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
    int countNodes(TreeNode* root) {
        if (!root) {
            return 0;
        }
        TreeNode *left = root, *right = root;
        int level = 1;
        while (right->right) {
            level++;
            left = left->left;
            right = right->right;
        }
        if (!left) {
            return (1 << level) - 1;
        }
        return 1 + countNodes(root->left) + countNodes(root->right);
    }
};

// Explanation

The height of a tree can be found by just going left. Let a single node tree have height 0. Find the height h of the whole tree. If the whole tree is empty, i.e., has height -1, there are 0 nodes.

Otherwise check whether the height of the right subtree is just one less than that of the whole tree, meaning left and right subtree have the same height.

If yes, then the last node on the last tree row is in the right subtree and the left subtree is a full tree of height h-1. So we take the 2^h-1 nodes of the left subtree plus the 1 root node plus recursively the number of nodes in the right subtree.
If no, then the last node on the last tree row is in the left subtree and the right subtree is a full tree of height h-2. So we take the 2^(h-1)-1 nodes of the right subtree plus the 1 root node plus recursively the number of nodes in the left subtree.
Since I halve the tree in every recursive step, I have O(log(n)) steps. Finding a height costs O(log(n)). So overall O(log(n)^2).
```

[reference1]()

[reference2](https://leetcode.com/problems/count-complete-tree-nodes/discuss/61977/Accepted-clean-Java-solution)



## 14. LeetCode 300 [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(n)

class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        
        vector<int> lis;
        
        for (auto const &i : nums) {
            auto pos = lower_bound(lis.begin(), lis.end(), i);
            
            if (pos == lis.end()) {
                lis.push_back(i);
            } else {
                *pos = i;
            }
        }
        
        return (int) lis.size();
    }
};
```



## 15. LeetCode 354 [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/)

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(n)

class Solution {
public:
    int maxEnvelopes(vector<pair<int, int>>& envelopes) {
        sort(envelopes.begin(), envelopes.end(), [](const pair<int, int> &A, const pair<int, int> &B) {
            return A.first < B.first || (A.first == B.first && A.second > B.second);
        });
        
        vector<int> lis;
        
        for (auto const &p : envelopes) {
            auto pos = lower_bound(lis.begin(), lis.end(), p.second);
            
            if (pos == lis.end()) {
                lis.push_back(p.second);
            } else {
                *pos = p.second;
            }
        }
        
        return (int) lis.size();
    }
};
```



## 16. LeetCode 374 [Guess Number Higher or Lower](https://leetcode.com/problems/guess-number-higher-or-lower/)

```c++
// Forward declaration of guess API.
// @param num, your guess
// @return -1 if my number is lower, 1 if my number is higher, otherwise return 0
int guess(int num);

class Solution {
public:
    int guessNumber(int n) {
        int l = 1, r = n;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (guess(mid) == 0) return mid;
            else if (guess(mid) == -1) {
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }
        
        return -1;
    }
};
```























