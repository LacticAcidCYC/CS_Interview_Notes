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



## 4. LeetCode 84 [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

[segment-tree-range-minimum-query](https://www.geeksforgeeks.org/segment-tree-set-1-range-minimum-query/)

A **simple solution** is to one by one consider all bars as starting points and calculate area of all rectangles starting with every bar. Finally return maximum of all possible areas. Time complexity of this solution would be O(n^2).

We can use **Divide and Conquer** to solve this in O(nLogn) time. The idea is to find the minimum value in the given array. Once we have index of the minimum value, the max area is maximum of following three values.
**a)** Maximum area in left side of minimum value (Not including the min value)
**b)** Maximum area in right side of minimum value (Not including the min value)
**c)** Number of bars multiplied by minimum value.

How to find the minimum efficiently? [Range Minimum Query using Segment Tree](https://www.geeksforgeeks.org/segment-tree-set-1-range-minimum-query/) can be used for this. We build segment tree of the given histogram heights. Once the segment tree is built, all [range minimum queries take O(Logn) time](https://www.geeksforgeeks.org/segment-tree-set-1-range-minimum-query/). So over all complexity of the algorithm becomes.

Overall Time = Time to build Segment Tree + Time to recursively find maximum area

[Time to build segment tree is O(n)](https://www.geeksforgeeks.org/segment-tree-set-1-sum-of-given-range/). Let the time to recursively find max area be T(n). It can be written as following.
T(n) = O(Logn) + T(n-1)
The solution of above recurrence is O(nLogn). So overall time is O(n) + O(nLogn) which is O(nLogn).

```c++
// A Divide and Conquer Program to find maximum rectangular area in a histogram 
#include <math.h> 
#include <limits.h> 
#include <vector> 
#include <iostream> 

using namespace std; 

// A utility function to get minimum of two numbers in hist[] 
int minVal(vector<int>& hist, int i, int j) 
{ 
    if (i == -1) return j; 
    if (j == -1) return i; 
    return (hist[i] < hist[j])? i : j; 
}

// A recursive function that constructs Segment Tree for hist[ss..se]. 
// si is index of current node in segment tree st 
int constructSTUtil(vector<int>& hist, int ss, int se, int *st, int si) 
{ 
    // If there is one element in array, store it in current node of 
    // segment tree and return 
    if (ss == se) 
       return (st[si] = ss); 
  
    // If there are more than one elements, then recur for left and 
    // right subtrees and store the minimum of two values in this node 
    int mid = ss + (se - ss) / 2;
    st[si] =  minVal(hist, constructSTUtil(hist, ss, mid, st, si*2+1), 
                     constructSTUtil(hist, mid+1, se, st, si*2+2)); 
    return st[si]; 
} 

/* Function to construct segment tree from given array. This function 
   allocates memory for segment tree and calls constructSTUtil() to 
   fill the allocated memory */
int *constructST(vector<int>& hist) 
{ 
    int n = hist.size();
    // Allocate memory for segment tree 
    int x = (int)(ceil(log2(n))); //Height of segment tree 
    int max_size = 2*(int)pow(2, x) - 1; //Maximum size of segment tree 
    int *st = new int[max_size]; 
  
    // Fill the allocated memory st 
    constructSTUtil(hist, 0, n-1, st, 0); 
  
    // Return the constructed segment tree 
    return st; 
} 

/*  A recursive function to get the index of minimum value in a given range of 
    indexes. The following are parameters for this function. 
  
    hist   --> Input array for which segment tree is built 
    st    --> Pointer to segment tree 
    index --> Index of current node in the segment tree. Initially 0 is 
             passed as root is always at index 0 
    ss & se  --> Starting and ending indexes of the segment represented by 
                 current node, i.e., st[index] 
    qs & qe  --> Starting and ending indexes of query range */
int RMQUtil(vector<int>& hist, int *st, int ss, int se, int qs, int qe, int index) 
{ 
    // If segment of this node is a part of given range, then return the 
    // min of the segment 
    if (qs <= ss && qe >= se) 
        return st[index]; 
  
    // If segment of this node is outside the given range 
    if (se < qs || ss > qe) 
        return -1; 
  
    // If a part of this segment overlaps with the given range 
    int mid = ss + (se - ss) / 2;
    return minVal(hist, RMQUtil(hist, st, ss, mid, qs, qe, 2*index+1), 
                  RMQUtil(hist, st, mid+1, se, qs, qe, 2*index+2)); 
} 
  
// Return index of minimum element in range from index qs (quey start) to 
// qe (query end).  It mainly uses RMQUtil() 
int RMQ(vector<int>& hist, int *st, int qs, int qe) 
{ 
    int n = hist.size();
    // Check for erroneous input values 
    if (qs < 0 || qe > n-1 || qs > qe) 
    { 
        cout << "Invalid Input"; 
        return -1; 
    } 
  
    return RMQUtil(hist, st, 0, n-1, qs, qe, 0); 
}

// A recursive function to find the maximum rectangular area. 
// It uses segment tree 'st' to find the minimum value in hist[l..r] 
int getMaxAreaRec(vector<int>& hist, int *st, int l, int r) 
{
    int n = hist.size();
    // Base cases 
    if (l > r)  return INT_MIN; 
    if (l == r)  return hist[l]; 
  
    // Find index of the minimum value in given range 
    // This takes O(Logn)time 
    int m = RMQ(hist, st, l, r); 
  
    /* Return maximum of following three possible cases 
       a) Maximum area in Left of min value (not including the min) 
       a) Maximum area in right of min value (not including the min) 
       c) Maximum area including min */
    return max(max(getMaxAreaRec(hist, st, l, m-1), 
               getMaxAreaRec(hist, st, m+1, r)), 
               (r-l+1)*(hist[m]) ); 
} 

// The main function to find max area 
int getMaxArea(vector<int>& hist) 
{ 
    int n = hist.size();
    // Build segment tree from given array. This takes 
    // O(n) time 
    int *st = constructST(hist); 
  
    // Use recursive utility function to find the 
    // maximum area 
    return getMaxAreaRec(hist, st, 0, n-1); 
} 

int main() 
{ 
    vector<int> hist = {6, 1, 5, 4, 5, 2, 6}; 
    cout << "Maximum area is " << getMaxArea(hist); 
    return 0; 
} 
```

[geeksforgeeks](https://www.geeksforgeeks.org/largest-rectangular-area-in-a-histogram-set-1/)

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        if (heights.empty()) return 0;
        int n = heights.size();
        int *st = constructST(heights); 

        return getMaxAreaRec(heights, st, 0, n-1); 
    }
    
private:
    int minVal(vector<int>& hist, int i, int j) { 
        if (i == -1) return j; 
        if (j == -1) return i; 
        return (hist[i] < hist[j])? i : j; 
    }

    int constructSTUtil(vector<int>& hist, int ss, int se, int *st, int si) { 
        if (ss == se) 
           return (st[si] = ss); 

        int mid = ss + (se - ss) / 2;
        st[si] =  minVal(hist, constructSTUtil(hist, ss, mid, st, si*2+1), 
                         constructSTUtil(hist, mid+1, se, st, si*2+2)); 
        return st[si]; 
    } 

    int *constructST(vector<int>& hist) { 
        int n = hist.size();
        int x = (int)(ceil(log2(n))); //Height of segment tree 
        int max_size = 2*(int)pow(2, x) - 1; //Maximum size of segment tree 
        int *st = new int[max_size]; 

        constructSTUtil(hist, 0, n-1, st, 0); 

        return st; 
    } 

    int RMQUtil(vector<int>& hist, int *st, int ss, int se, int qs, int qe, int index) 
    { 
        if (qs <= ss && qe >= se) 
            return st[index]; 

        if (se < qs || ss > qe) 
            return -1; 

        int mid = ss + (se - ss) / 2;
        return minVal(hist, RMQUtil(hist, st, ss, mid, qs, qe, 2*index+1), 
                      RMQUtil(hist, st, mid+1, se, qs, qe, 2*index+2)); 
    } 

    int getMaxAreaRec(vector<int>& hist, int *st, int l, int r) 
    {
        int n = hist.size();
        if (l > r)  return INT_MIN; 
        if (l == r)  return hist[l]; 

        int m = RMQUtil(hist, st, 0, n-1, l, r, 0); 

        return max(max(getMaxAreaRec(hist, st, l, m-1), 
                   getMaxAreaRec(hist, st, m+1, r)), 
                   (r-l+1)*(hist[m]) ); 
    } 
};
```









































