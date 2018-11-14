## 1. LeetCode 338 [Counting Bits](https://leetcode.com/problems/counting-bits/)

```latex
Explanation:
000|0
-----
000|1
-----
00|10
00|11
-----
0|100
0|101
0|110
0|111
-----
|1000
|1001
|1010
|1011
|1100
|1101
|1110
|1111
-----
```



```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    vector<int> countBits(int num) {
        vector<int> dp(num+1, 0);
        int offset = 1;
        for (int i=1; i<=num; i++) {
            if (i == offset * 2) {
                offset *= 2;
            }
            dp[i] = dp[i-offset] + 1;
        }
        return dp;
    }
};
```

[solution](https://leetcode.com/problems/counting-bits/discuss/79557/How-we-handle-this-question-on-interview-Thinking-process-%2B-DP-solution)



## 2. LeetCode 279 [Perfect Squares](https://leetcode.com/problems/perfect-squares/)

## (1) DP

```c++
// Time Complexity: O(n*n^0.5) ?
// Space Complexity: O(n)

class Solution {
public:
    int numSquares(int n) {
        static vector<int> dp{0};
        int m = dp.size();
        dp.resize(max(m,n+1), INT_MAX);
        for(int i=1,j; (j=i*i)<=n; i++) {
            for(int k = max(m,j); k<=n; k++) {
                dp[k] = min(dp[k], dp[k-j] + 1);
            }
        }
        return dp[n];
    }
};

class Solution {
public:
    int numSquares(int n) {
        int s = (int) sqrt(n);
        vector<int> dp(n+1, n);
        dp[0] = 0;
        for (int i=1; i<=s; i++) {
            int square = i*i;
            for (int j=square; j<=n; j++) {
                dp[j] = min(dp[j], dp[j-square]+1);
            }
        }
        return dp[n];
    }
};
```



### (2) DFS

```c++
// Time Complexity: O(n*n^0.5) ?
// Space Complexity: O(1) ?

class Solution {
public:
    int numSquares(int n) {
        int s = (int) sqrt(n);
        int min_cnt = n;
        backtrack(min_cnt, n, s, 0);
        return min_cnt;
    }
    
    void backtrack(int &min_cnt, int number, int square, int cur_cnt) {
        if (number == 0) {
            min_cnt = min(min_cnt, cur_cnt);
            return;
        }
        if (square == 0) return;
        for (int i=number/(square*square); i>=0 && cur_cnt+i<min_cnt; i--) {
            backtrack(min_cnt, number-i*square*square, square-1, cur_cnt+i);
        }
    }
};
```



### (3) Math

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int numSquares(int n) {
        while(n % 4 == 0) n /= 4;
        if(n % 8 == 7) return 4;
        for(int i=0; i*i<=n; ++i) {
            int j = sqrt(n - i*i);
            if(i*i + j*j == n)
                return !!i + !!j;
        }
        return 3;
    }
};

class Solution {
public:
    int numSquares(int n) {
        for(int i=0; i*i<=n; ++i) {
            for(int j=0; j*j<=n; ++j) {
                int k = sqrt(n - i*i - j*j);
                if(i*i + j*j + k*k == n)
                    return !!i + !!j + !!k;
            }
        }
        return 4;
    }
};
```



## 3. LeetCode 120 [Triangle](https://leetcode.com/problems/triangle/)

```c++
// Time Complexity: O(nm)
// Space Complexity: O(nm)

class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = (int)triangle.size();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        dp[0][0] = triangle[0][0];
        int total = dp[0][0];
        for(int i=1; i<n; i++) {
            int m = (int)triangle[i].size();
            int tempMin = INT_MAX;
            for(int j=0; j<m; j++) {
                if(j == 0) {
                    dp[i][j] = dp[i-1][j] + triangle[i][j];
                } else if(j == m-1) {
                    dp[i][j] = dp[i-1][j-1] + triangle[i][j];
                } else {
                    dp[i][j] = min(dp[i-1][j-1], dp[i-1][j]) + triangle[i][j];
                }
                tempMin = min(tempMin, dp[i][j]);
            }
            total = tempMin;
        }
        return total;
    }
};
```



## 4. LeetCode 62 [Unique Paths](https://leetcode.com/problems/unique-paths/)

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>> dp(m, vector<int>(n, 0));
        for (int i=0; i<m; i++) {
            dp[i][0] = 1;
        }
        for (int i=0; i<n; i++) {
            dp[0][i] = 1;
        }
        for (int i=1; i<m; i++) {
            for (int j=1; j<n; j++) {
                dp[i][j] = dp[i][j-1] + dp[i-1][j];
            }
        }
        return dp[m-1][n-1];
    }
};
```



## 5. 平分背包

```c++
// Time Complexity: O(nM) (M=sum(nums)/2)
// Space Complexity: O(nM)

// 平分数组（0-1背包）
int backpack(vector<int> nums) {
    int sum = 0;
    for (auto i : nums) {
        sum += i; // 数组元素总和
    }
    
    int m = sum / 2; // 背包容量
    int n = (int) nums.size();
    vector<vector<int>> dp(n+1, vector<int>(m+1, 0));
    for (int i=1; i<=n; i++) {
        for (int j=1; j<=m; j++) {
            if (nums[i] > j) {
                dp[i][j] = dp[i-1][j];
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i-1][j-nums[i]]+nums[i]);
            }
        }
    }
    
    return sum - 2 * dp[n][m];
}
```



## 6. LeetCode 63 [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        if (m == 0 || n == 0) return 0;
        if (obstacleGrid[0][0] == 1 || obstacleGrid[m-1][n-1] == 1) return 0;
        
        // initialize the first col and the first row
        vector<vector<int>> paths(m, vector<int>(n, 0));
        paths[0][0] = 1;
        for (int j=1; j<n; j++) {
            paths[0][j] = obstacleGrid[0][j] == 1 ? 0 : paths[0][j-1];
        }
        for (int i=1; i<m; i++) {
            paths[i][0] = obstacleGrid[i][0] == 1 ? 0 : paths[i-1][0];
        }
        
        for (int i=1; i<m; i++) {
            for (int j=1; j<n; j++) {
                if (obstacleGrid[i][j] != 1) {
                    paths[i][j] = paths[i-1][j] + paths[i][j-1];
                } else {
                    paths[i][j] = 0;
                }
            }
        }
        return paths[m-1][n-1];
    }
};
```



## 7. LeetCode 303 [Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/)

```c++
class NumArray {
private:
    unordered_map<int, int> map;
public:
    NumArray(vector<int> nums) {
        int sum = 0;
        map[0] = 0;
        for (int i=1; i<=nums.size(); i++) {
            sum += nums[i-1];
            map[i] = sum;
        }
    }
    
    int sumRange(int i, int j) {
        return map[j+1] - map[i];
    }
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * int param_1 = obj.sumRange(i,j);
 */
```



## 8. LeetCode 32 [Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/)

### (1) Stack

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int longestValidParentheses(string s) {
        int n = s.length();
        stack<int> st; // store the index
        
        for (int i=0; i<n; i++) {
            if (s[i] == '(') {
                st.push(i);
            } else {
                if (st.empty() || s[st.top()] == ')') {
                    st.push(i);
                } else {
                    st.pop();
                }
            }
        }
        // now the stack only contains the unmatched chars' indexes
        if (st.empty()) {
            // all characters are matched
            return n;
        }
        
        int r = n, l = 0, max_len = -1;
        while (!st.empty()) {
            l = st.top();
            st.pop();
            max_len = max(max_len, r-l-1);
            r = l;
        }
        // make sure not missing the front
        max_len = max(max_len, r);
        return max_len;
    }
};

// calculate when building the stack
class Solution {
public:
    int longestValidParentheses(string s) {
        stack<int> stk; // idx
        
        int res =0, max_len = 0;
        int left_most = -1;
        for (int i=0; i < s.size(); i++) {
            if (s[i] == '(') {
                stk.push(i);
            } else {
                if (stk.empty()) {
                    left_most = i;
                } else {
                    stk.pop();
                    if (stk.empty()) {
                        max_len = max(max_len, i - left_most);
                    } else {
                        max_len = max(max_len, i - stk.top());
                    }
                }
            }
        }
        return max_len;
    }
};
```

[stack-solution](https://leetcode.com/problems/longest-valid-parentheses/discuss/14126/My-O(n)-solution-using-a-stack)



### (2) DP

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
   int longestValidParentheses(string s) {
        if (s.length() <= 1) return 0;
        int curMax = 0;
        vector<int> longest(s.size(),0);
        for(int i=1; i < s.length(); i++){
            if(s[i] == ')') {
                if(s[i-1] == '('){
                    longest[i] = (i-2) >= 0 ? (longest[i-2] + 2) : 2;
                    curMax = max(longest[i],curMax);
                }
                else{ // if s[i-1] == ')', combine the previous length.
                    if(i-longest[i-1]-1 >= 0 && s[i - longest[i-1] - 1] == '('){
                        longest[i] = longest[i-1] + 2 + ((i - longest[i-1] - 2 >= 0) ? longest[i - longest[i-1] - 2] : 0);
                        curMax = max(longest[i],curMax);
                    }
                }
            }
            //else if s[i] == '(', skip it, because longest[i] must be 0
        }
        return curMax;
    }
};
```

[dp-solution](https://leetcode.com/problems/longest-valid-parentheses/discuss/14133/My-DP-O(n)-solution-without-using-stack)



## 9. LeetCode 727 [Minimum Window Subsequence](https://leetcode.com/problems/minimum-window-subsequence/)

### Explanation

dp(i, j) stores the "largest" starting index (make the length smallest) of the valid substring W of S[0, i] such that T[0, j] is a subsequence of W. Otherwise, dp(i, j) = -1; So dp(i, j) would be: 

if S[i] == T[j], this means we could borrow the start index from dp(i-1, j-1) to make the current substring valid;
else, we only need to borrow the start index from dp(i-1, j) which could either exist or not.

Finally, go through the last row to find the substring with min length and appears first.

```c++
// Time Complexity: O(mn) (len_S * len_T)
// Space Complexity: O(mn) (len_S * len_T)

class Solution {
public:
    string minWindow(string S, string T) {
        int len_S = S.length();
        int len_T = T.length();
        
        // initialize all value to -1
        vector<vector<int>> dp(len_S, vector<int>(len_T, -1));
        
        // initialize the first col
        for (int i=0; i<len_S; i++) {
            if (S[i] == T[0]) {
                dp[i][0] = i;
            } else {
                if (i != 0) {
                    dp[i][0] = dp[i-1][0];
                }
            }
        }
        
        // calculate dp[][]
        for (int i=1; i<len_S; i++) {
            for (int j=1; j<len_T; j++) {
                if (S[i] == T[j]) {
                    dp[i][j] = dp[i-1][j-1];
                } else {
                    dp[i][j] = dp[i-1][j];
                }
            }
        }
        
        // Finally, go through the last col to find the substring with min length and appears first.
        int begin = -1, min_len = INT_MAX;
        for (int i=0; i<len_S; i++) {
            int index = dp[i][len_T-1];
            if (index != -1) {
                if (i + 1 - index < min_len) {
                    begin = index;
                    min_len = i + 1 - index;
                }
            }
        }
        return begin == -1 ? "" : S.substr(begin, min_len);
    }
};
```

[dp-solution](https://leetcode.com/problems/minimum-window-subsequence/discuss/109362/Java-Super-Easy-DP-Solution-(O(mn))

[dp-solution-2](https://leetcode.com/problems/minimum-window-subsequence/discuss/109358/C%2B%2B-DP-with-explanation-O(ST)-53ms)

[mypost](https://leetcode.com/problems/minimum-window-subsequence/discuss/185659/Easy-to-understand-C%2B%2B-DP-Solution-O(mn))



## 10. LeetCode 91 [Decode Ways](https://leetcode.com/problems/decode-ways/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

// state transition equation
// dp[i]:  decode ways of i-length messages
// current char: s[i-1]
// dp[i] = dp[i-1] (when s[i-1] is not '0')
// dp[i] = dp[i-1] + dp[i-2] (when s[i-2]s[i-1] can become '10-26')

class Solution {
public:
    int numDecodings(string s) {
        if (s[0] == '0') return 0;
        int n = s.length();
        vector<int> dp(n+1, 0); // dp[i]: decode ways of i-length messages
        dp[0] = 1; // init according to the state transition
        dp[1] = 1; // obviously one
        for (int i=2; i<=n; i++) {
            if (s[i-1] != '0') {
                dp[i] += dp[i-1]; // can split before s[i-1](current element)
            }
            int last_two_digits = stoi(s.substr(i-2, 2));
            if (last_two_digits >= 10 && last_two_digits <= 26) {
                dp[i] += dp[i-2]; // can split before s[i-2]
            }
        }
        
        return dp[n];
    }
};
```



## 11. LeetCode 931 Minimum Falling Path Sum

### Similar: LeetCode 120 Triangle

Given a **square** array of integers `A`, we want the **minimum** sum of a *falling path* through `A`.

A falling path starts at any element in the first row, and chooses one element from each row.  The next row's choice must be in a column that is different from the previous row's column by at most one.

**Example 1:**

```
Input: [[1,2,3],[4,5,6],[7,8,9]]
Output: 12
Explanation: 
The possible falling paths are:
```

- `[1,4,7], [1,4,8], [1,5,7], [1,5,8], [1,5,9]`
- `[2,4,7], [2,4,8], [2,5,7], [2,5,8], [2,5,9], [2,6,8], [2,6,9]`
- `[3,5,7], [3,5,8], [3,5,9], [3,6,8], [3,6,9]`

The falling path with the smallest sum is `[1,4,7]`, so the answer is `12`.

**Note:**

1. `1 <= A.length == A[0].length <= 100`
2. `-100 <= A[i][j] <= 100`



### (1) DFS

```c++
// DFS TLE O(3^n)
class Solution {
public:
    int minFallingPathSum(vector<vector<int>>& A) {
        int minSum = INT_MAX;
        int n = A.size();
        int sum = 0, step = 0;
        dfs(A, n, sum, step, minSum, 0);
        
        return minSum;
    }
    
    void dfs(vector<vector<int>>& A, int n, int sum, int step, int& minSum, int col) {
        if (step == n) {
            if (sum < minSum) {
                minSum = sum;
            }
            return;
        }
        
        if (step == 0) {
            for (int i=0; i<n; i++) {
                dfs(A, n, sum+A[step][i], step+1, minSum, i);
            }
        } else {
            for (int i=col-1; i<=col+1; i++) {
                if (i >= 0 && i < n) {
                    dfs(A, n, sum+A[step][i], step+1, minSum, i);
                }
            }
        }
    }
};
```



### (2) DP

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n^2)

class Solution {
public:
    int minFallingPathSum(vector<vector<int>>& A) {
        int n = A.size();
        vector<vector<int>> dp(n, vector<int>(n, INT_MAX));
        
        // initialize the first row
        for (int i=0; i<n; i++) {
            dp[0][i] = A[0][i];
        }
        
        for (int i=1; i<n; i++) {
            for (int j=0; j<n; j++) {
                if (j == 0) {
                    dp[i][j] = A[i][j] + min(dp[i-1][j], dp[i-1][j+1]);
                } else if (j == n-1) {
                    dp[i][j] = A[i][j] + min(dp[i-1][j], dp[i-1][j-1]);
                } else {
                    dp[i][j] = A[i][j] + min(dp[i-1][j-1], min(dp[i-1][j], dp[i-1][j+1]));
                }
            }
        }
        
        int minSum = INT_MAX;
        // check the last row for minSum
        for (int i=0; i<n; i++) {
            if (dp[n-1][i] < minSum) {
                minSum = dp[n-1][i];
            }
        }
        
        return minSum;
    }
};
```

[solution](https://leetcode.com/problems/minimum-falling-path-sum/discuss/186828/c%2B%2B-DP-12-ms)



## 12. LeetCode 322 [Coin Change](https://leetcode.com/problems/coin-change/)

```c++
// Time Complexity: O(nm) (n: number of coin value; m: amount)
// Space Complexity: O(nm)

class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        int n = (int) coins.size();
        vector<int> dp(amount+1, INT_MAX);
        dp[0] = 0; // base case
        for (int i=0; i<n; i++) {
            for (int j=coins[i]; j<=amount; j++) {
                if (dp[j-coins[i]] != INT_MAX) {
                    dp[j] = min(dp[j], dp[j-coins[i]]+1);
                }
            }
        }
        return dp[amount] == INT_MAX ? -1 : dp[amount];
    }
};
```



### Other Solution

**DFS +  Pruning**

```c++
// Time Complexity: O(nm) (n: number of coin value; m: amount)
// Space Complexity: O(nm)

class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        if (amount == 0) return 0;
        if (coins.empty()) return -1;
        int min_count = INT_MAX;
        sort(coins.begin(), coins.end(), greater<int>());
        backtrack(coins, amount, 0, min_count, 0);

        return min_count == INT_MAX ? -1 : min_count;
    }

    void backtrack(const vector<int>& coins, int amount, int index, int &min_cnt, int cur_cnt) {
        if (amount == 0) {
            min_cnt = min(min_cnt, cur_cnt);
            return;
        }
        if (index == coins.size()) {
            return;
        }
        for (int i=amount/coins[index]; i>=0 && cur_cnt + i < min_cnt; i--) {
            backtrack(coins, amount-i*coins[index], index+1, min_cnt, cur_cnt+i);
        }
    }
};
```



## 13. LeetCode 518 [Coin Change 2](https://leetcode.com/problems/coin-change-2/)

```c++
// Time Complexity: O(nm) (n: number of coin value; m: amount)
// Space Complexity: O(m)

class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size();
        vector<int> dp(amount+1, 0);
        dp[0] = 1; // base case
        for (int i=0; i<n; i++) {
            for (int j=coins[i]; j<=amount; j++) {
                dp[j] = dp[j] + dp[j-coins[i]];
            }
        }
        return dp[amount];
    }
};
```



## 14. LeetCode 72 [Edit Distance](https://leetcode.com/problems/edit-distance/)

This is a classic problem of Dynamic Programming. We define the state `dp[i][j]` to be the minimum number of operations to convert `word1[0..i - 1]` to `word2[0..j - 1]`. The state equations have two cases: the boundary case and the general case. Note that in the above notations, both `i` and `j` take values starting from `1`.

For the boundary case, that is, to convert a string to an empty string, it is easy to see that the mininum number of operations to convert `word1[0..i - 1]` to `""` requires at least `i` operations (deletions). In fact, the boundary case is simply:

1. `dp[i][0] = i`;
2. `dp[0][j] = j`.

Now let's move on to the general case, that is, convert a non-empty `word1[0..i - 1]` to another non-empty `word2[0..j - 1]`. Well, let's try to break this problem down into smaller problems (sub-problems). Suppose we have already known how to convert `word1[0..i - 2]` to `word2[0..j - 2]`, which is `dp[i - 1][j - 1]`. Now let's consider `word[i - 1]` and `word2[j - 1]`. If they are euqal, then no more operation is needed and `dp[i][j] = dp[i - 1][j - 1]`. Well, what if they are not equal?

If they are not equal, we need to consider three cases:

1. Replace `word1[i - 1]` by `word2[j - 1]` (`dp[i][j] = dp[i - 1][j - 1] + 1 (for replacement)`);
2. Delete `word1[i - 1]` and `word1[0..i - 2] = word2[0..j - 1]` (`dp[i][j] = dp[i - 1][j] + 1 (for deletion)`);
3. Insert `word2[j - 1]` to `word1[0..i - 1]` and `word1[0..i - 1] + word2[j - 1] = word2[0..j - 1]` (`dp[i][j] = dp[i][j - 1] + 1 (for insertion)`).

Make sure you understand the subtle differences between the equations for deletion and insertion. For deletion, we are actually converting `word1[0..i - 2]` to `word2[0..j - 1]`, which costs `dp[i - 1][j]`, and then deleting the `word1[i - 1]`, which costs `1`. The case is similar for insertion.

Putting these together, we now have:

1. `dp[i][0] = i`;
2. `dp[0][j] = j`;
3. `dp[i][j] = dp[i - 1][j - 1]`, if `word1[i - 1] = word2[j - 1]`;
4. `dp[i][j] = min(dp[i - 1][j - 1] + 1, dp[i - 1][j] + 1, dp[i][j - 1] + 1)`, otherwise.

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int l1 = word1.length();
        int l2 = word2.length();
        vector<vector<int>> dp(l1+1, vector<int>(l2+1, 0));
        
        for (int i=0; i<=l1; i++) {
            dp[i][0] = i;
        }
        
        for (int j=1; j<=l2; j++) {
            dp[0][j] = j;
        }
        
        for (int i=1; i<=l1; i++) {
            for (int j=1; j<=l2; j++) {
                if (word1[i-1] == word2[j-1]) {
                    dp[i][j] = dp[i-1][j-1];
                } else {
                    dp[i][j] = min(dp[i-1][j-1] + 1, min(dp[i-1][j] + 1, dp[i][j-1] + 1));
                }
            }
        }
        
        return dp[l1][l2];
    }
};
```

[solution](https://leetcode.com/problems/edit-distance/discuss/25846/20ms-Detailed-Explained-C%2B%2B-Solutions-O(n)-Space)



## 15. LeetCode 198 [House Robber](https://leetcode.com/problems/house-robber/)

[Analysis](https://leetcode.com/problems/house-robber/discuss/156523/From-good-to-great.-How-to-approach-most-of-DP-problems.)

### (1) DP

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        if (n == 1) return nums[0];
        vector<int> dp(n+1, 0);
        dp[0] = 0;
        dp[1] = nums[0];
        for (int i=2; i<=n; i++) {
            dp[i] = max(dp[i-1], nums[i-1] + dp[i-2]);
        }
        
        return dp[n];
    }
};

// improved
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        if (n == 1) return nums[0];
        int prev1 = 0;
        int prev2 = 0;
        for (const auto &i : nums) {
            int tmp = prev1;
            prev1 = max(prev1, i+prev2);
            prev2 = tmp;
        }
        
        return prev1;
    }
};
```



## 16. LeetCode 213 [House Robber II](https://leetcode.com/problems/house-robber-ii/)

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        if (n == 1) return nums[0];
        
        int max_val = 0;
        int prev1 = 0;
        int prev2 = 0;
        
        // 0 -> n-2
        for (int i=0; i<n-1; i++) {
            int tmp = prev1;
            prev1 = max(prev1, nums[i] + prev2);
            prev2 = tmp;
        }
        max_val = prev1;
        
        prev1 = 0;
        prev2 = 0;
        // n-1 -> 1
        for (int i=n-1; i>0; i--) {
            int tmp = prev1;
            prev1 = max(prev1, nums[i] + prev2);
            prev2 = tmp;
        }
        return max(max_val, prev1);
    }
};
```



## 17. LeetCode 337 [House Robber III](https://leetcode.com/problems/house-robber-iii/)

[solution](https://leetcode.com/problems/house-robber-iii/discuss/79330/Step-by-step-tackling-of-the-problem)

### (1) naive

```c++
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
    int rob(TreeNode* root) {
        if (!root) return 0;
        int res = 0;
        
        // rob root
        if (root->left) {
            res += (rob(root->left->left) + rob(root->left->right));
        }
        if (root->right) {
            res += (rob(root->right->left) + rob(root->right->right));
        }
        
        // compare rob root and not rob root
        return max(res + root->val, rob(root->left) + rob(root->right));
    }
};
```



### (2) recursive + memo

```c++
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
    int rob(TreeNode* root) {
        if (!root) return 0;
        unordered_map<TreeNode*, int> mp;
        return helper(mp, root);
    }
    
    int helper(unordered_map<TreeNode*, int> &mp, TreeNode* root) {
        if (!root) return 0;
        if (mp.count(root)) {
            return mp[root];
        }
        
        int res = 0;
        // rob root
        if (root->left) {
            res += (helper(mp, root->left->left) + helper(mp, root->left->right));
        }
        if (root->right) {
            res += (helper(mp, root->right->left) + helper(mp, root->right->right));
        }
        
        mp[root] = max(res + root->val, helper(mp, root->left) + helper(mp, root->right));
        return mp[root];
    }
};
```



### (3) DFS + DP

#### Split the result into 2 values

```c++
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
    int rob(TreeNode* root) {
        if (!root) return 0;
        vector<int> res = dfs_dp(root);
        return max(res[0], res[1]);
    }
    
    vector<int> dfs_dp(TreeNode* root) {
        if (!root) return {0, 0};
        
        vector<int> val(2, 0);
        vector<int> left = dfs_dp(root->left);
        vector<int> right = dfs_dp(root->right);
        
        val[0] = max(left[0], left[1]) + max(right[0], right[1]);
        val[1] = root->val + left[0] + right[0];
        
        return val;
    }
};
```



## 18. LeetCode 935 [Knight Dialer](https://leetcode.com/problems/knight-dialer/)

Let dp(i)(j) stand for the number of the distinct number end with **i** after pressing **j** times.

Initialization: dp(i)(0) = 1 for i in 0-9

Generally, we use dp(i)(j-1) to update the dp(next)(j) for all the next number which number **i** can jump to.

Finally, we can just sum up dp(i)(N-1) for i in 0-9

```c++
class Solution {
public:
    int knightDialer(int N) {
        const int mod = 1000000007;
        if (N == 1) return 10;
        vector<vector<int>> steps = {
            {4,6},
            {6,8},
            {7,9},
            {4,8},
            {0,3,9},
            {},
            {0,1,7},
            {2,6},
            {1,3},
            {2,4}
        };
        
        vector<vector<int>> dp(10, vector<int>(N, 0));
        for (int i=0; i<10; i++) {
            dp[i][0] = 1;
        }
        
        for (int j=1; j<N; j++) {
            for (int i=0; i<10; i++) {
                int pre = dp[i][j-1];
                if (pre == 0) continue;
                for (int k=0; k<steps[i].size(); k++) {
                    int next = steps[i][k];
                    dp[next][j] = (dp[next][j] + pre) % mod;
                }
            }
        }
        
        int res = 0;
        for (int i=0; i<10; i++) {
            //cout << dp[i][N-1] << endl;
            res = (res + dp[i][N-1]) % mod;
        }
        
        return res;
    }
};
```



## 19. LeetCode 64 [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) return 0;
        int m = grid.size();
        int n = grid[0].size();
        
        vector<vector<int>> dp(m, vector<int>(n, 0));
        
        // initialization
        dp[0][0] = grid[0][0];
        for (int i=1; i<m; i++) {
            dp[i][0] = dp[i-1][0] + grid[i][0]; 
        }
        
        for (int j=1; j<n; j++) {
            dp[0][j] = dp[0][j-1] + grid[0][j]; 
        }
        
        for (int i=1; i<m; i++) {
            for (int j=1; j<n; j++) {
                dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j];
            }
        }
        
        return dp[m-1][n-1];
    }
};
```



## 20. LeetCode 139 [Word Break](https://leetcode.com/problems/word-break/)

[solution](https://leetcode.com/articles/word-break/)

### (1) Brute-Force (recursion + backtracking)

```c++
// Time complexity : O(2^n) 
// Consider the worst case where s = "aaaaaaaaaaaaaab" and every prefix of s 
// is present in the dictionary of words, then the recursion tree can grow upto 2^n.
// Space complexity : O(n) The depth of recursion tree can go up to n. 

class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> hashset(wordDict.begin(), wordDict.end());
        return dfs(s, hashset, 0);
    }
    
    bool dfs(string &s, unordered_set<string> &wordDict, int begin) {
        if (begin == s.length()) {
            return true;
        }
        
        for (int i=begin+1; i<=s.length(); i++) {
            if (wordDict.count(s.substr(begin, i-begin)) && dfs(s, wordDict, i)) {
                return true;
            }
        }
        
        return false;
    }
};
```



### (2) Recursion with memorization

```c++
// Time complexity : O(n^2) ? Size of recursion tree can go up to n^2.
// but if considering string::substr's complexity, it would be O(n^3), same as below solution
// Space complexity : O(n) The depth of recursion tree can go up to n. 

class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> hashset(wordDict.begin(), wordDict.end());
        vector<int> memo(s.length(), -1);
        return dfs(s, hashset, 0, memo);
    }
    
    bool dfs(string &s, unordered_set<string> &wordDict, int begin, vector<int> &memo) {
        if (begin == s.length()) {
            return true;
        }
        
        if (memo[begin] != -1) {
            return !(memo[begin] == 0);
        }
        
        for (int i=begin+1; i<=s.length(); i++) {
            if (wordDict.count(s.substr(begin, i-begin)) && dfs(s, wordDict, i, memo)) {
                return true;
            }
        }
        
        return memo[begin] = 0;
    }
};
```



### (3) BFS + memorization

```c++
// Time complexity : O(n^3) 
// For every starting index, the search can continue till the end of the given string.
// Space complexity : O(n) The depth of recursion tree can go up to n. 

class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> hashset(wordDict.begin(), wordDict.end());
        int n = s.length();
        vector<int> visited(n, 0);
        queue<int> q;
        q.push(0);
        
        while (!q.empty()) {
            int begin = q.front();
            q.pop();
            if (visited[begin] == 0) {
                for (int i=begin+1; i<=n; i++) {
                    if (hashset.count(s.substr(begin, i-begin))) {
                        if (i == n) {
                            return true;
                        }
                        q.push(i);
                    } 
                }
                visited[begin] = 1;
            }
        }
        
        return false;
    }
};
```



### (4) DP

```c++
// Time complexity : O(n^3) Two loops are there to fill dp array.
// Space complexity : O(n) Length of pp array is n+1.

class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> hashset(wordDict.begin(), wordDict.end());
        int n = s.length();
        vector<bool> dp(n+1, false);
        dp[0] = true;
        
        for (int i=1; i<=n; i++) {
            for (int j=0; j<i; j++) {
                if (dp[j] && hashset.count(s.substr(j, i-j))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        
        return dp[n];
    }
};
```



## 21. LeetCode 413 [Arithmetic Slices](https://leetcode.com/problems/arithmetic-slices/)

### (1) Brute Force (O(n^3) => O(n^2))

The most naive solution is to consider every pair of elements(with atleast 1 element between them), so that the range of elements lying between these two elements acts as a slice. Then, we can iterate over every such slice(range) to check if all the consecutive elements within this range have the same difference.

```c++
// Time Complexity: O(n^3)
// Space Complexity: O(1)

class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& A) {
        int res = 0;
        int n = A.size();
        
        for (int s=0; s<n-2; s++) {
            int diff = A[s+1] - A[s];
            for (int e=s+2; e<n; e++) {
                int i = 0;
                for (i=s+1; i<=e; i++) {
                    if (s[i] - s[i-1] != diff) {
                        break;
                    }
                }
                if (i > e) {
                    res++;
                }
            }
        }
        return res;
    }
};
```

We can see, that if we are currently considering the range bound by the elements, let's say, A[s] (start) and A[e] (end), we have checked the consecutive elements in this range to have the same difference. Now, when we move on to the next range between the indices ssand e+1, we again perform a check on all the elements in the range s:e, along with one additional pair A[e+1] and A[e]. We can remove this redundant check in the range s:e and just check the last pair to have the same difference as the one used for the previous range(same s, incremented e).

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(1)

class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& A) {
        int res = 0;
        int n = A.size();
        
        for (int s=0; s<n-2; s++) {
            int diff = A[s+1] - A[s];
            for (int e=s+2; e<n; e++) {
                if (A[e] - A[e-1] == diff) {
                    res++;
                } else {
                    break;
                }
            }
        }
        return res;
    }
};
```



### (2) Recursive (DFS)

Suppose we know the number of arithmetic slices in the range **(0,i-1)** constituted by the elements **[a0,a1,a2,...a(i−1)]**, to be say **x**. If this range itself is an arithmetic slice, all the consecutive elements have the same difference (equal to say, **a(i−1)−a(i−2)**). Now, adding a new element **ai** to it to extend the range to **(0,i)** will constitute an arithmetic slice only if this new element satisfies **a[i] - a[i-1] = a[i-1] - a[i-2].** The new range then will have **x+1** arithmetic slices.

```c++
// Time Complexity: O(n) the recursion function is called at most n-2 times.
// Space Complexity: O(n) the depth of the recursion tree goes up to n-2.

class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& A) {
        int res = 0;
        dfs(A, A.size()-1, res);
        return res;
    }
    
    int dfs(vector<int> &A, int end, int &res) {
        if (end < 2) {
            return 0;
        }
        
        int count = 0;
        if (A[end] - A[end-1] == A[end-1] - A[end-2]) {
            count = 1 + dfs(A, end-1, res);
            res += count;
        } else {
            dfs(A, end-1, res);
        }
        
        return count;
    }
};
```



### (3) DP

dp[i]: store the number of arithmetic slices possible in the range (k, i), k refers to the minimum index possible such that (k, j) constitutes a valid arithmetic slice.

Initialization:

dp[0] = 0;

dp[1] = 0;

Recurrence Equation:

dp[i] = 1 + dp[i-1] if A[i] - A[i-1] == A[i-1] - A[i-2]

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& A) {
        int n = A.size();
        if (n < 3) return 0;
        
        vector<int> dp(n, 0);
        dp[0] = 0;
        dp[1] = 0;
        int res = 0;
        for (int i=2; i<n; i++) {
            if (A[i] - A[i-1] == A[i-1] - A[i-2]) {
                dp[i] = dp[i-1] + 1;
            }
            res += dp[i];
        }
        
        return res;
    }
};
```



### Constant Space

In the last approach, we can observe that we only require the element dp[i-1] to determine the value to be entered at dp[i]. Thus, instead of making use of a 1-D array to store the required data, we can simply keep a track of just the last element.

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& A) {
        int n = A.size();
        if (n < 3) return 0;
        
        int lastCnt = 0;
        int res = 0;
        for (int i=2; i<n; i++) {
            if (A[i] - A[i-1] == A[i-1] - A[i-2]) {
                lastCnt++;
            } else {
                lastCnt = 0;
            }
            res += lastCnt;
        }
        
        return res;
    }
};
```



## 22. LeetCode 300 [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

### (1) DP

dp(i): represents the length of the longest increasing subsequence possible considering the array elements up to ith index, by necessarily including the ith element.

In order to find out dp[i], we need to try to append the current element(nums[i]) in every possible increasing subsequences upto the (i-1)th index (including the (i-1)th index), such that the new sequence formed by adding the current element is also an increasing subsequence. Thus, we can easily determine dp[i] using:

dp[i] = max (dp[j]) + 1, 0 <= j < i;

At the end,

Res = max (dp[i]), 0 <= i < n 

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        
        vector<int> dp(n, 0);
        dp[0] = 1;
        int res = 1;
        
        for (int i=1; i<n; i++) {
            int curMax = 0;
            for (int j=0; j<i; j++) {
                if (nums[i] > nums[j]) {
                    curMax = max(curMax, dp[j]);
                }
            }
            dp[i] = curMax + 1;
            res = max(res, dp[i]);
        }
        
        return res;
    }
};
```



### (2) Binary Search + DP?

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



## 23. LeetCode 354 [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/)

### Related LeetCode 300

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



### Classic DP Solution

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

class Solution {
public:
    int maxEnvelopes(vector<pair<int, int>>& envelopes) {
        if (envelopes.empty()) return 0;
        sort(envelopes.begin(), envelopes.end());
        vector<int> dp(envelopes.size(), 1);
        
        for (int i = 0; i < envelopes.size(); ++i) {
            for (int j = 0; j < i; ++j) {
                if (envelopes[j].first < envelopes[i].first && envelopes[j].second < envelopes[i].second) {
                    dp[i]  = max(dp[i] , dp[j] + 1);
                }
            }
        }
        return *max_element(dp.begin(), dp.end());
    }
};
```

[solution](https://leetcode.com/problems/russian-doll-envelopes/discuss/82808/C%2B%2B-9-line-Short-and-Clean-O(nlogn)-solution-(plus-classic-O(n2)-dp-solution)



## 24. LeetCode 264 [Ugly Number II](https://leetcode.com/problems/ugly-number-ii/)

```c++
class Solution {
public:
    int nthUglyNumber(int n) {
        if(n <= 0) return 0;
        if(n == 1) return 1;
        vector<int> ugly(n);
        ugly[0] = 1;
        int a=0, b=0, c=0;
        for(int i=1; i<n; i++) {
            ugly[i] = min(ugly[a] * 2, min(ugly[b] * 3, ugly[c] * 5));
            if(ugly[i] == ugly[a] * 2) a++;
            if(ugly[i] == ugly[b] * 3) b++;
            if(ugly[i] == ugly[c] * 5) c++;
        }
        return ugly[n-1];
    }
};
```



## 25. LeetCode 343

```c++

```













































