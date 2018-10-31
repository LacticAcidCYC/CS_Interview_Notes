# Divide & Conquer

## 1. LeetCode 315 [Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/)

[geeksforgeeks](https://www.geeksforgeeks.org/counting-inversions/)

[merge-sort](https://www.geeksforgeeks.org/merge-sort/)

Formally speaking, two elements a[i] and a[j] form an inversion if a[i] > a[j] and i < j.

### (1) Merge-sort based Solution

#### Explanation

The smaller numbers on the right of a number are exactly those that jump from its right to its left during a stable sort. So I do mergesort with added tracking of those right-to-left jumps.

[Explanation-Details](https://leetcode.com/problems/count-of-smaller-numbers-after-self/discuss/76607/C%2B%2B-O(nlogn)-Time-O(n)-Space-MergeSort-Solution-with-Detail-Explanation)

[C++-solution-in-comment](https://leetcode.com/problems/count-of-smaller-numbers-after-self/discuss/76584/Mergesort-solution)

#### inplace_merge

Merges two consecutive sorted ranges: `[first,middle)` and `[middle,last)`, putting the result into the combined sorted range `[first,last)`.

[cplusplus](http://www.cplusplus.com/reference/algorithm/inplace_merge/?kw=inplace_merge)



```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(n)

class Solution {
    typedef vector<pair<int, int>>::iterator pit;
public:
    vector<int> countSmaller(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) {
            return {};
        }
        vector<int> ans(n, 0);
        vector<pair<int, int>> nums_(n);
        for (int i=0; i<n; i++) {
            nums_[i] = {nums[i], i};
        }
        merge_sort(nums_.begin(), nums_.end(), ans);
        return ans;
    }
    
    void merge_sort(pit begin, pit end, vector<int> &ans) {
        if (end - begin == 1) {
            return;
        }
        
        auto mid = begin + (end - begin) / 2;
        merge_sort(begin, mid, ans);
        merge_sort(mid, end, ans);
        for (auto i = begin, j = mid; i != mid; i++) {
            while (j != end && i->first > j->first) {
                j++;
            }
            ans[i->second] += (j - mid);
        }
        
        inplace_merge(begin, mid, end);
    }
};
```



## 2. LeetCode 493 [Reverse Pairs](https://leetcode.com/problems/reverse-pairs/)

### Similar to LeetCode 315

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(1)

class Solution {
    typedef vector<int>::iterator vit;
public:
    int reversePairs(vector<int>& nums) {
        if (nums.size() <= 1) return 0;
        int cnt = 0;
        merge_sort_and_count(nums.begin(), nums.end(), cnt);
        return cnt;
    }
    
    void merge_sort_and_count(vit begin, vit end, int &cnt) {
        if (end - begin == 1) {
            return;
        }
        
        auto mid = begin + (end - begin) / 2;
        merge_sort_and_count(begin, mid, cnt);
        merge_sort_and_count(mid, end, cnt);
        
        for (auto i=begin, j=mid; i != mid; i++) {
            // pay attention to overflow here: 2 * *j!!!
            while (j != end && *i > 2L * *j) {
                j++;
            }
            cnt += (j - mid);
        }
        
        inplace_merge(begin, mid, end);
    }
};
```

[solution](https://leetcode.com/problems/reverse-pairs/discuss/97287/C++-with-iterators)



## 3. LeetCode 327 [Count of Range Sum](https://leetcode.com/problems/count-of-range-sum/)

### Similar to LeetCode 315

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(n)

class Solution {
    typedef vector<long>::iterator vit;
public:
    int countRangeSum(vector<int>& nums, int lower, int upper) {
        int n = nums.size();
        if (n == 0) return 0;
        int cnt = 0;
        // pay attention to the range of sum (can I use int here?)
        vector<long> sums(n+1, 0);
        for (int i=1; i<=n; i++) {
            sums[i] = nums[i-1] + sums[i-1];
        }
        merge_sort_and_cnt(sums.begin(), sums.end(), cnt, lower, upper);
        return cnt;
    }
    
    void merge_sort_and_cnt(vit begin, vit end, int &cnt, int &lower, int &upper) {
        if (end - begin == 1) {
            return;
        }
        
        auto mid = begin + (end - begin) / 2;
        merge_sort_and_cnt(begin, mid, cnt, lower, upper);
        merge_sort_and_cnt(mid, end, cnt, lower, upper);
        for (auto i = begin, j = mid, k = mid; i != mid; i++) {
            while (j != end && *j - *i < lower) j++;
            while (k != end && *k - *i <= upper) k++;
            
            cnt += (k - j);
        }
        
        inplace_merge(begin, mid, end);
    }
};
```

[stefanPochmann-solution](https://leetcode.com/problems/count-of-range-sum/discuss/77991/Short-and-simple-O(n-log-n))

[notes-website](https://www.cp.eng.chula.ac.th/~piak/teaching/algo/algo2008/count-inv.htm)



### Consider Binary Indexed Tree Solution of the problems above later...









































