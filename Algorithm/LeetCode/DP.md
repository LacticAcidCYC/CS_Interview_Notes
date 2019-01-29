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
            if (nums[i-1] > j) {
                dp[i][j] = dp[i-1][j];
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i-1][j-nums[i-1]]+nums[i-1]);
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

Dp: represents the length of longest valid substring ending at ith index.

**If s[i] == ')' and s[i-1] == '('** 

=> dp[i] = dp[i-2] + 2 (if i-2 >= 0)

**If s[i] == ')' and s[i-dp[i-1]-1] == '('** 

=> dp[i] = dp[i-1] + 2 + dp[i-dp[i-1]-2] if (i - dp[i-1] - 2 >= 0)

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

1. Essentially, we have to multiply the existed ugly numbers by 2, 3 and 5 to get a bigger ugly number, however, if we blindly multiply all the existed numbers by 2, 3 and 5, then the number could grow much faster than needed
2. Hence, every time we only try to find the next smallest ugly number
3. Also, since any existed number will be multiplied by 2, 3 and 5 once and only once, otherwise duplicate, we can use a pointer to keep track of where the 2, 3 and 5 are going to multiply in the next step.
4. Once, we find the next minimum, we can move on the corresponding pointer, otherwise it always stays at the already existed ugly number which would makes pointer useless

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

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



## 25. LeetCode 343 [Integer Break](https://leetcode.com/problems/integer-break/)

dp[i] stores the max product from i. Suppose we break the number i to j (j < i) and i - j, we want to compare dp[i] and j * (i - j) in each iteration. However, j or i - j maybe break into multiple smaller numbers to get greater product itself. For example, if j is 8, then we would want to consider f[j] which is 2 * 3 * 3 = 18 instead of j. So we would want to get the greater from j and f[j]. Then the transition function is dp[i] = max(dp[i], max(j, dp[j]) * max(i - j, f[i - j])).

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int integerBreak(int n) {
        vector<int> dp(n+1);
        dp[1] = 1;
        for (int i=2; i<=n; i++) {
            for (int j=1; j<=i/2; j++) {
                dp[i] = max(dp[i], max(j, dp[j]) * max(i-j, dp[i-j]));
            }
        }
        
        return dp[n];
    }
};
```

[dp-solution](https://leetcode.com/problems/integer-break/discuss/80694/Java-DP-solution)

[math](https://leetcode.com/problems/integer-break/discuss/80720/Easy-to-understand-C%2B%2B-with-explanation)

[math-2](https://leetcode.com/problems/integer-break/discuss/80689/A-simple-explanation-of-the-math-part-and-a-O(n)-solution)



## 26. LeetCode 357 [Count Numbers with Unique Digits](https://leetcode.com/problems/count-numbers-with-unique-digits/)

10^0 = 1 => 1 dp[0]

10^1 = 10 => 0-9: 10 dp[1]

10^2 = 100 => dp[2] = 9 * 9 + dp[1] _ _

10^3 = 1000 => dp[3] = 9 * 9 * 8 + dp[2] _ _ _

....

if n > 10, dp[i] = dp[10]

```c++
class Solution {
public:
    int countNumbersWithUniqueDigits(int n) {
        vector<int> dp(n+1, 0);
        dp[0] = 1;
        dp[1] = 10;
        int t = 1, s = 9;
        for(int i=2; i<=n; i++) {
            if(i > 10) dp[i] = dp[10];
            else {
                t *= s--;
                dp[i] = 9 * t + dp[i-1];
            }
        }
        return dp[n];
    }
};

// another
class Solution {
public:
    int countNumbersWithUniqueDigits(int n) {
        if(n == 0) return 1;
        if(n == 1) return 10;
        if(n == 2) return 91;
        int r[11];
        r[1] = 10;
        r[2] = 81;
        int cnt = r[1] + r[2];
        for(int i=3; i<=n; i++) {
            r[i] = r[i-1] * (11 - i);
            cnt += r[i];
        }
        return cnt;
    }
};
```



## 27. LeetCode 375 [Guess Number Higher or Lower II](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

[solution](https://leetcode.com/problems/guess-number-higher-or-lower-ii/discuss/84764/Simple-DP-solution-with-explanation~~)

### (1) DP

For each number x in range[i~j]
we do: result_when_pick_x = x + **max**{DP([i~x-1]), DP([x+1, j])}
--> *// the max means whenever you choose a number, the feedback is always bad and therefore leads you to a worse branch.*
then we get DP([i~j]) = **min**{xi, ... ,xj}
--> *// this min makes sure that you are minimizing your cost.*

Further Explanation:

dp(i)(j) is the minimal cost to guess from range(i...j). When you choose an x where i <= x <= j, you may find the target number from left i...x-1, or you may find the target number from the x+1...j, because you don't know which way should go, **so to guarantee you have enough money to find the target, you need to prepare the more, which is max(dp(i)(x-1), dp(x+1)(j)).**

```c++
// Time Complexity: O(n^3)
// Space Complexity: O(n^2)

class Solution {
public:
    int getMoneyAmount(int n) {
        vector<vector<int>> dp(n+1, vector<int>(n+1, INT_MAX));
        for(int i=0; i<=n; i++) dp[i][i] = 0;
        for(int i=0; i<n; i++) dp[i][i+1] = i;
        
        for(int j=3; j<=n; j++) {
            for(int i=j-2; i>0; i--) {
                for(int k=(i+j) / 2; k<j; k++) {
                    int left = dp[i][k-1];
                    int right = dp[k+1][j];
                    int localMax = k + max(left, right);
                    dp[i][j] = min(dp[i][j], localMax);
                    if(left >= right) break;
                }
            }
        }
        return dp[1][n];
    }
};
```



### (2) Recursive + memorization

```c++
// Time Complexity: O(n^3)
// Space Complexity: O(n^2)

class Solution {
public:
    int getMoneyAmount(int n) {
        vector<vector<int>> dp(n+1, vector<int>(n+1, 0));
        return dpRecursive(dp, 1, n);
    }
    
    int dpRecursive(vector<vector<int>>& dp, int i, int j) {
        if(i >= j) return 0;
        if(dp[i][j] != 0) return dp[i][j];
        int res = INT_MAX;
        for(int m=(i+j)/2; m<=j; m++) {
            int tmp = m + max(dpRecursive(dp, i, m-1), dpRecursive(dp, m+1, j));
            res = min(res, tmp);
        }
        dp[i][j] = res;
        return res;
    }
};
```



### (3) DP O(n^2)

[solution](https://leetcode.com/problems/guess-number-higher-or-lower-ii/discuss/84826/An-O(n2)-DP-Solution-Quite-Hard.)



## 28. LeetCode 673 [Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)

### Explanation

### Similar to LeetCode 300

Suppose for sequences ending at nums[i], we know the length length[i] of the longest subsequence, and the number count[i] of such sequences with that length.

For every `j < i` with `A[j] < A[i]`, we might append `A[i]` to a longest subsequence ending at `A[j]`. It means that we have demonstrated `count[j]` subsequences of length `length[j] + 1`.

Now, if those sequences are longer than `length[i]`, then we know we have `count[j]`sequences of this length. If these sequences are equal in length to `length[i]`, then we know that there are now `count[j]` additional sequences to be counted of that length (ie. `count[i] += count[j]`).

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

class Solution {
public:
    int findNumberOfLIS(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return n;
        
        vector<int> lengths(n, 0);
        vector<int> cnts(n, 1);
        int longest = 0, res = 0;
        
        for (int i=0; i<n; i++) {
            for (int j=0; j<i; j++) {
                if (nums[j] < nums[i]) {
                    if (lengths[j] + 1 > lengths[i]) {
                        lengths[i] = lengths[j] + 1;
                        cnts[i] = cnts[j];
                    } else if (lengths[j] + 1 == lengths[i]) {
                        cnts[i] += cnts[j];
                    }
                }
            }
            longest = max(longest, lengths[i]);
        }
        
        for (int i=0; i<n; i++) {
            if (lengths[i] == longest) {
                res += cnts[i];
            }
        }
        
        return res;
    }
};
```



## 29. LeetCode 132 [Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/)

Problem: Given a string *s*, partition *s* such that every substring of the partition is a palindrome.

Return the minimum cuts needed for a palindrome partitioning of *s*.



### Explanation

1. [youtube](<https://www.youtube.com/watch?v=lDYIvtBVmgo>) (O(n^3) + O(n^2))

2. A typical dp problem:
   For each position `i`, incrementally find palindrome of length `1,3,5,...`, then, of length `2,4,6`. Suppose the **start index** of the found palindrome is `idx_s` and the **end index**of the palindrome is `idx_e`, update the dp table as the follow formula:
   `dp[idx_e] = min(dp[idx_e], dp[idx_s-1] + 1)`

   Wait a min, how about `idx_s` equal to 0? Seems like we should and some kind of special case test. Actually, this can be avoided by a **sentinel** trick.

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

class Solution {
public:
    int minCut(string s) {
        int n = (int)s.size();
        vector<int> mincnts(n+1); // number of cuts for the first k characters
        for (int i=0; i<=n; i++) {
            mincnts[i] = i-1;
        }
        for (int i=0; i<n; i++) {
            // odd length palindrome
            for (int j=0; i-j>=0 && i+j<n && s[i-j] == s[i+j]; j++) {
                mincnts[i+j+1] = min(mincnts[i+j+1], 1+mincnts[i-j]); 
            }
            // even length palindrome
            for (int j=1; i-j+1>=0 && i+j<n && s[i-j+1] == s[i+j]; j++) {
                mincnts[i+j+1] = min(mincnts[i+j+1], 1+mincnts[i-j+1]);
            }
        }
        return mincnts[n];
    }
};
```



## 30. LeetCode 85 [Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)

```c++
// Time Complexity: O(mn)
// Space Complexity: O(n)

class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        int m = matrix.size();
        int n = matrix[0].size();
        
        vector<int> left(n, 0);
        vector<int> right(n, n);
        vector<int> height(n, 0);
        int res = 0;
        
        for (int i=0; i<m; i++) {
            int cur_left_bound = 0, cur_right_bound = n; // [l, r); 
            for (int j=0; j<n; j++) {
                if (matrix[i][j] == '1') {
                    left[j] = max(left[j], cur_left_bound);
                } else {
                    left[j] = 0;
                    cur_left_bound = j + 1;
                }
            }
            
            for (int j=n-1; j>=0; j--) {
                if (matrix[i][j] == '1') {
                    right[j] = min(right[j], cur_right_bound);
                } else {
                    right[j] = n;
                    cur_right_bound = j;
                }
            }
            
            for (int j=0; j<n; j++) {
                if (matrix[i][j] == '1') {
                    height[j]++;
                } else {
                    height[j] = 0;
                }
            }
            
            for (int j=0; j<n; j++) {
                res = max(res, (right[j] - left[j]) * height[j]);
            }
        }
        
        return res;
    }
};
```

[solution](https://leetcode.com/problems/maximal-rectangle/discuss/29054/Share-my-DP-solution)



## 31. LeetCode 221 [Maximal Square](https://leetcode.com/problems/maximal-square/)

### Explanation

We initialize another matrix (dp) with the same dimensions as the original one initialized with all 0’s.

dp(i,j) represents the side length of the maximum square whose bottom right corner is the cell with index (i,j) in the original matrix.

Starting from index (0,0), for every 1 found in the original matrix, we update the value of the current element as

->Recurrence:

dp(i,j)=min(dp(i−1,j),dp(i−1,j−1),dp(i,j−1))+1.

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        int m = matrix.size();
        int n = matrix[0].size();
        
        vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
        int maxSide = 0;
        
        for (int i=1; i<=m; i++) {
            for (int j=1; j<=n; j++) {
                if (matrix[i-1][j-1] == '1') {
                    dp[i][j] = min(dp[i-1][j-1], min(dp[i-1][j], dp[i][j-1])) + 1;
                    maxSide = max(maxSide, dp[i][j]);
                }
            }
        }
        
        return maxSide * maxSide;
    }
};
```



### Space Optimization

In the previous approach for calculating dp of ith row we are using only the previous element and the `(i−1)th` row. Therefore, we don't need 2D dp matrix as 1D dp array will be sufficient for this.

Initially the dp array contains all 0's. As we scan the elements of the original matrix across a row, we keep on updating the dp array as per the equation `dp[j]=min(dp[j-1],dp[j],prev)`, where prev refers to the old `dp[j-1]`. For every row, we repeat the same process and update in the same dp array.

```c++
// Time Complexity: O(mn)
// Space Complexity: O(n)

class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        int m = matrix.size();
        int n = matrix[0].size();
        
        vector<int> dp(n+1, 0);
        int maxSide = 0;
        
        for (int i=1; i<=m; i++) {
            int prev = 0;
            for (int j=1; j<=n; j++) {
                int temp = dp[j];
                if (matrix[i-1][j-1] == '1') {
                    dp[j] = min(dp[j-1], min(dp[j], prev)) + 1;
                    maxSide = max(maxSide, dp[j]);
                } else {
                    dp[j] = 0;
                }
                prev = temp;
                
            }
        }
        
        return maxSide * maxSide;
    }
};
```



## 32. LeetCode 764 [Largest Plus Sign](https://leetcode.com/problems/largest-plus-sign/)

**Algorithms**: For each position `(i, j)` of the `grid` matrix, we try to extend in each of the four directions (left, right, up, down) as long as possible, then take the minimum length of `1`'s out of the four directions as the order of the largest axis-aligned plus sign centered at position `(i, j)`.

**Optimizations**: Normally we would need a total of five matrices to make the above idea work -- one matrix for the `grid` itself and four more matrices for each of the four directions. However, these five matrices can be combined into one using two simple tricks:

1. For each position `(i, j)`, we are only concerned with the minimum length of `1`'s out of the four directions. This implies we may combine the four matrices into one by only keeping tracking of the minimum length.

2. For each position `(i, j)`, the order of the largest axis-aligned plus sign centered at it will be `0` if and only if `grid[i][j] == 0`. This implies we may further combine the `grid` matrix with the one obtained above.

**Implementations**:

1. Create an `N-by-N` matrix `grid`, with all elements initialized with value `N`.
2. Reset those elements to `0` whose positions are in the `mines` list.
3. For each position `(i, j)`, find the maximum length of `1`'s in each of the four directions and set `grid[i][j]` to the minimum of these four lengths. Note that there is a simple recurrence relation relating the maximum length of `1`'s at current position with previous position for each of the four directions (labeled as `l`, `r`, `u`, `d`).
4. Loop through the `grid` matrix and choose the maximum element which will be the largest axis-aligned plus sign of `1`'s contained in the grid.

```c++
class Solution {
public:
    int orderOfLargestPlusSign(int N, vector<vector<int>>& mines) {
        vector<vector<int>> dp(N, vector<int>(N, N));
        
        for (auto &mine : mines) {
            dp[mine[0]][mine[1]] = 0;
        }
        
        for (int i = 0; i < N; ++i) {
            for (int j = 0, k = N - 1, l = 0, r = 0, u = 0, d = 0; j < N; ++j, --k) {
                dp[i][j] = min(dp[i][j], l = (dp[i][j] == 0 ? 0 : l + 1));
                dp[i][k] = min(dp[i][k], r = (dp[i][k] == 0 ? 0 : r + 1));
                dp[j][i] = min(dp[j][i], u = (dp[j][i] == 0 ? 0 : u + 1));
                dp[k][i] = min(dp[k][i], d = (dp[k][i] == 0 ? 0 : d + 1));
            }
        }
        
        int res = INT_MIN;
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < N; ++j) {
                res = max(res, dp[i][j]);
            }
        }
        
        return res;
    }
};
```

[solution](https://leetcode.com/problems/largest-plus-sign/discuss/113314/JavaC%2B%2BPython-O(N2)-solution-using-only-one-grid-matrix)



## 33. LeetCode 312 [Burst Balloons](https://leetcode.com/problems/burst-balloons/)

### Intuition

**i. Be Naive First**

When I first get this problem, it is far from dynamic programming to me. I started with the most naive idea the backtracking.

We have n balloons to burst, which mean we have n steps in the game. In the i th step we have n-i balloons to burst, i = 0~n-1. Therefore we are looking at an algorithm of O(n!). Well, it is slow, probably works for n < 12 only.

Of course this is not the point to implement it. We need to identify the redundant works we did in it and try to optimize.

**Well, we can find that for any balloons left the maxCoins does not depends on the balloons already bursted.** This indicate that we can use memorization (top down) or dynamic programming (bottom up) for all the cases from small numbers of balloon until n balloons. How many cases are there? For k balloons there are C(n, k) cases and for each case it need to scan the k balloons to compare. The sum is quite big still. It is better than O(n!) but worse than O(2^n).

**ii. Better idea**

We then think can we apply the divide and conquer technique? After all there seems to be many self similar sub problems from the previous analysis.

Well, the nature way to divide the problem is burst one balloon and separate the balloons into 2 sub sections one on the left and one one the right. However, in this problem the left and right become adjacent and have effects on the maxCoins in the future.

**Then another interesting idea come up. Which is quite often seen in dp problem analysis. That is reverse thinking. Like I said the coins you get for a balloon does not depend on the balloons already burst. Therefore instead of divide the problem by the first balloon to burst, we divide the problem by the last balloon to burst.**

Why is that? **Because only the first and last balloons we are sure of their adjacent balloons before hand!**

For the first we have `nums[i-1]*nums[i]*nums[i+1]` for the last we have `nums[-1]*nums[i]*nums[n]`.

OK. Think about n balloons if i is the last one to burst, what now?

We can see that the balloons is again separated into 2 sections. But this time since the balloon i is the last balloon of all to burst, the left and right section now has well defined boundary and do not affect each other! Therefore we can do either recursive method with memoization or dp.

**iii. Final**

Here comes the final solutions. Note that we put 2 balloons with 1 as boundaries and also burst all the zero balloons in the first round since they won't give any coins. The algorithm runs in O(n^3) which can be easily seen from the 3 loops in dp solution.

### Recurrence Equation

**Define** c(i, j) = maxCoins(nums[i] ~ nums[j])

Then ans = c(1, n)

**c(i, j) = max(c(i, k-1) + nums[i-1] * k * nums[j+1] + c(k+1, j)) (i <= k <= j)**

(i-1, i, i+1, ..., k-1, k, k+1, ..., j-1, j, j+1)



[huahua](https://www.youtube.com/watch?v=z3hu2Be92UA)

### (1) Recursive method with memorization

```c++
// Time Complexity: O(n^3)
// Space Complexity: O(n^2)

class Solution {
public:
    int maxCoins(vector<int>& nums) {
        if (nums.empty()) return 0;
        int n = nums.size();
        vector<int> nums_(n+2);
        for (int i=1; i<=n; i++) {
            nums_[i] = nums[i-1];
        }
        nums_[0] = nums_[n+1] = 1;
        
        vector<vector<int>> memo(n+2, vector<int>(n+2));
        return burst(nums_, memo, 0, n+1);
    }
    
private:
    int burst(vector<int> &nums_, vector<vector<int>> &memo, int i, int j) {
        if (i + 1 == j) return 0;
        if (memo[i][j] > 0) return memo[i][j];
        
        int ans = 0;
        for (int k=i+1; k<j; k++) {
            ans = max(ans, burst(nums_, memo, i, k) + nums_[i] * nums_[k] * nums_[j] + burst(nums_, memo, k, j));
        }
        
        return memo[i][j] = ans;
    }
};
```

[solution](https://leetcode.com/problems/burst-balloons/discuss/76228/Share-some-analysis-and-explanations)



### (2) DP (corresponding to the recursive method)

```c++
// Time Complexity: O(n^3)
// Space Complexity: O(n^2)

class Solution {
public:
    int maxCoins(vector<int>& nums) {
        if (nums.empty()) return 0;
        int n = nums.size();
        vector<int> nums_(n+2);
        
        for (int i=1; i<=n; i++) {
            nums_[i] = nums[i-1];
        }
        nums_[0] = nums_[n+1] = 1;
        
        vector<vector<int>> dp(n+2, vector<int>(n+2, 0));
        
        for (int l=3; l<=n+2; l++) {
            for (int i=0; i<=n+2-l; i++) {
                int j = i + l - 1;
                for (int k=i+1; k<j; k++) {
                    dp[i][j] = max(dp[i][j], nums_[i] * nums_[k] * nums_[j] + dp[i][k] + dp[k][j]);
                }
            }
        }
        
        return dp[0][n+1];
    }
};
```



### (3) DP-2

```c++
// Time Complexity: O(n^3)
// Space Complexity: O(n^2)

class Solution {
public:
    int maxCoins(vector<int>& nums) {
        if (nums.empty()) return 0;
        int n = nums.size();
        nums.insert(nums.begin(), 1);
        nums.push_back(1);
        
        // dp[i][j] = maxCoins(nums[i] ~ nums[j])
        vector<vector<int>> dp(n+2, vector<int>(n+2, 0));
        
        for (int l=1; l<=n; l++) {
            for (int i=1; i<=n+1-l; i++) {
                int j = i + l - 1;
                for (int k=i; k<=j; k++) {
                    dp[i][j] = max(dp[i][j], dp[i][k-1] + nums[i-1] * nums[k] * nums[j+1] + dp[k+1][j]);
                }
            }
        }
        
        return dp[1][n];
    }
};
```



## 34. LeetCode 416 [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)

### Just like 平分背包(0/1 knapsack problem)

### (1) DP

```c++
// Time Complexity: O(nW)
// Space Complexity: O(nW)

class Solution {
public:
    bool canPartition(vector<int>& nums) {
        const int n = nums.size();
        int sum = 0;
        for (auto const &i : nums) {
            sum += i;
        }
        
        if (sum % 2) {
            return false;
        }
        
        int W = sum / 2;
        vector<vector<bool>> dp(n+1, vector<bool>(W+1, 0));
        for (int i=0; i<=n; i++) {
            dp[i][0] = true;
        }
        
        for (int i=1; i<=n; i++) {
            for (int j=1; j<=W; j++) {
                if (nums[i-1] > j) {
                    dp[i][j] = dp[i-1][j];
                } else {
                    dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i-1]];
                }
            }
        }
        
        return dp[n][W];
    }
};
```

[solution](https://leetcode.com/problems/partition-equal-subset-sum/discuss/90592/01-knapsack-detailed-explanation)



### (2) DP + Space Optimization

```c++
// Time Complexity: O(nW)
// Space Complexity: O(W)

class Solution {
public:
    bool canPartition(vector<int>& nums) {
        const int n = nums.size();
        int sum = 0;
        for (auto const &i : nums) {
            sum += i;
        }
        
        if (sum % 2) {
            return false;
        }
        
        int W = sum / 2;
        vector<bool> dp(W+1, false);
        dp[0] = true;
        
        for (int i=0; i<n; i++) {
            // Key!! Traverse from W to nums[i] here to make sure
            // we use the old values to update the new values
            // which means one element cannot be used more than once
            for (int j=W; j>=nums[i]; j--) {
                dp[j] = dp[j] || dp[j-nums[i]];
            }
        }
        
        return dp[W];
    }
};
```



### (3) Backtracking + Pruning

```c++
// Time Complexity: O(2^n)
// Space Complexity: O(n)

class Solution {
public:
    bool canPartition(vector<int>& nums) {
        const int n = nums.size();
        int sum = 0;
        for (auto const &i : nums) {
            sum += i;
        }
        
        if (sum % 2) {
            return false;
        }
        
        int W = sum / 2;
        sort(nums.begin(), nums.end(), greater<int>());
        return backtrack(nums, W, 0);
    }
    
private:
    bool backtrack(vector<int> &nums, int sum, int start) {
        if (sum == 0) return true;
        
        for (int i=start; i<nums.size(); i++) {
            if (nums[i] > sum) continue;
            if (backtrack(nums, sum-nums[i], i+1)) return true;
            while (i+1 < nums.size() && nums[i] == nums[i+1]) i++;
        }
        
        return false;
    }
};
```



### (4) Bit Manipulation

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if (sum % 2) return false;
        const int MAX_NUM = 100;
        const int MAX_ARRAY_SIZE = 200;
        bitset<MAX_NUM * MAX_ARRAY_SIZE / 2 + 1> bits(1);
        for (auto const &i : nums) {
            bits |= bits << i;
        }
        
        return bits[sum / 2];
    }
};
```

[solution](https://leetcode.com/problems/partition-equal-subset-sum/discuss/90590/Simple-C%2B%2B-4-line-solution-using-a-bitset)



## 35. LeetCode 562 [Longest Line of Consecutive One in Matrix](https://leetcode.com/problems/longest-line-of-consecutive-one-in-matrix/)

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



## 36. LeetCode 698 [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)

### (1) Recursive (Simulation)

```c++
// Time Complexity: O(k*2^n)
// Space Complexity: O(n)

class Solution {
public:
    bool canPartitionKSubsets(vector<int>& nums, int k) {
        int n = nums.size();
        
        // If k is 1, then complete array will be our answer
        if (k == 1) return true;
        // If total number of partitions are more than n, then 
        // division is not possible 
        if (k > n) return false;
        
        // if array sum is not divisible by k then we can't divide 
        // array into k partitions 
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if (sum % k) return false;
        
        // the sum of each subset should be subset (= sum / K) 
        int subset = sum / k;
        vector<int> subsetSum(k, 0);
        vector<int> visited(n, 0);
        
        return isKPartitionPossibleRec(nums, subsetSum, visited, subset, k, n, 0, n-1);
    }
    
private:
    bool isKPartitionPossibleRec(vector<int> &nums, vector<int> &subsetSum, vector<int> &visited, int &subset, int &k, int &n, int curIdx, int limitedIdx) {
        /*  current index (K - 2) represents (K - 1) subsets of equal 
            sum last partition will already remain with sum 'subset'*/
        if (subsetSum[curIdx] == subset) {
            if (curIdx == k - 2) return true;
            // recursive call for next subsetition 
            return isKPartitionPossibleRec(nums, subsetSum, visited, subset, k, n, curIdx+1, n-1);
        }
        
        for (int i=limitedIdx; i>=0; i--) {
            //  if already visited, continue 
            if (visited[i]) continue;
            int tmp = subsetSum[curIdx] + nums[i];
            
            // if temp is less than subset then only include the element 
            // and call recursively
            if (tmp <= subset) {
                //  mark the element and include into current partition sum
                visited[i] = 1;
                subsetSum[curIdx] += nums[i];
                
                if (isKPartitionPossibleRec(nums, subsetSum, visited, subset, k, n, curIdx, i-1)) {
                    return true;
                }
                // after recursive call unmark the element and remove from 
            	// subsetition sum 
                visited[i] = 0;
                subsetSum[curIdx] -= nums[i];
            }
        }
        
        return false;
    }
};
```

[solution](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/discuss/108741/Cpp-solution-with-explanation-in-details)

[geeksforgeeks](https://www.geeksforgeeks.org/partition-set-k-subsets-equal-sum/)



### (2) Another Simulation Solution

```c++
// N is the length of nums, and k is as given
// Time Complexity: O(k^(N-k)*k!) without trick => O(k^N)
// Space Complexity: O(N)
// As we skip additional zeroes in groups, naively we will make O(k!) calls to search, then 
// additional O(k^(N−k)) calls after every element of groups is nonzero.

class Solution {
public:
    bool canPartitionKSubsets(vector<int>& nums, int k) {
        int n = nums.size();
        if (k == 1) return true;
        if (k > n) return false;
        
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if (sum % k) return false;
        
        int target = sum / k;
        int limitedIdx = n - 1;
        
        sort(nums.begin(), nums.end());
        if (nums[limitedIdx] > target) return false;
        while (limitedIdx >= 0 && nums[limitedIdx] == target) {
            limitedIdx--;
            k--;
        }
        vector<int> subsetSum(k, 0);
        return search(subsetSum, limitedIdx, nums, target);
    }
    
private:
    bool search(vector<int> &subsetSum, int limitedIdx, vector<int> &nums, int &target) {
        if (limitedIdx < 0) return true;
        for (int i=0; i<subsetSum.size(); i++) {
            int curVal = nums[limitedIdx];
            if (subsetSum[i] + curVal <= target) {
                subsetSum[i] += curVal;
                limitedIdx--;
                if (search(subsetSum, limitedIdx, nums, target)) return true;
                subsetSum[i] -= curVal;
                limitedIdx++;
            }
            if (subsetSum[i] == 0) break;
        }
        return false;
    }
};
```



## 37. LeetCode 887 [Super Egg Drop](https://leetcode.com/problems/super-egg-drop/)

```c++
class Solution {
public:
    int superEggDrop(int K, int N) {
        vector<vector<int>> dp(N+1, vector<int>(K+1, 0));
        
        int m = 0; // number of drops
        while (dp[m][K] < N) {
            m++;
            for (int k = 1; k <= K; k++) {
                dp[m][k] = dp[m-1][k-1] + dp[m-1][k] + 1;
            }
        }
        
        return m;
    }
};
```

[solution](https://leetcode.com/problems/super-egg-drop/discuss/158974/C%2B%2BJavaPython-2D-and-1D-DP-O(KlogN))



## 38. LeetCode 920 [Number of Music Playlists](https://leetcode.com/problems/number-of-music-playlists/)

```c++
class Solution {
public:
    int numMusicPlaylists(int N, int L, int K) {
        int MOD = 1000000007;
        vector<vector<long>> dp(L+1, vector<long>(N+1, 0));
        dp[0][0] = 1;
        
        for (int i=1; i<=L; i++) {
            for (int j=1; j<=N; j++) {
                dp[i][j] += dp[i-1][j-1] * (N - (j - 1));
                dp[i][j] += dp[i-1][j] * max(j-K, 0);
                dp[i][j] %= MOD;
            }
        }
        
        return (int) dp[L][N];
    }
};
```



## 39. LeetCode 877 

```c++

```



## 40. LeetCode 871 [Minimum Number of Refueling Stops](https://leetcode.com/problems/minimum-number-of-refueling-stops/)

```c++
class Solution {
public:
    int minRefuelStops(int target, int startFuel, vector<vector<int>>& stations) {
        int n = stations.size();
        vector<long> dp(n+1, startFuel);
        
        for (int i = 0; i < n; i++) {
            for (int t = i; t >= 0 && dp[t] >= stations[i][0]; t--) {
                dp[t+1] = max(dp[t+1], dp[t] + stations[i][1]);
            }
        }
        
        for (int t = 0; t <= n; t++) {
            if (dp[t] >= target) {
                return t;
            }
        }
        
        return -1;
    }
};
```



## 41. LeetCode 983 [Minimum Cost For Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/)

```c++
class Solution {
public:
    int mincostTickets(vector<int>& days, vector<int>& costs) {
        map<int, int> dp = {{-30,0}};
        auto cost = [&](int d){
            return prev(dp.upper_bound(d))->second;
        };
        
        for(int d : days)
            dp[d] = min({costs[0] + cost(d-1), costs[1] + cost(d-7), costs[2] + cost(d-30)});
        
        return dp.rbegin()->second;
    }
};
```

[dp-solution](https://leetcode.com/problems/minimum-cost-for-tickets/discuss/226670/Java-DP-Solution-with-explanation-O(n))



## 42. LeetCode 982 [Triples with Bitwise AND Equal To Zero](https://leetcode.com/problems/triples-with-bitwise-and-equal-to-zero/)

```c++
class Solution {
public:
    int countTriplets(vector<int>& A) {
        int n = A.size(), ans = 0;
        vector<int> f(1 << 16, -1);

        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++) {
                int x = A[i] & A[j];
                if (f[x] == -1) {
                    f[x] = 0;
                    for (int k = 0; k < n; k++)
                        if ((x & A[k]) == 0)
                            f[x]++;
                }
                ans += f[x];
            }

        return ans;
    }
};
```

[solution](https://leetcode.com/problems/triples-with-bitwise-and-equal-to-zero/discuss/226778/C%2B%2B-20-lines56msenumerate-with-memory-(with-Chinese-translation))



## 43. LeetCode 472 [Concatenated Words](https://leetcode.com/problems/concatenated-words/)

```c++
class Solution {
public:
    vector<string> findAllConcatenatedWordsInADict(vector<string>& words) {
        vector<string> res;
        unordered_set<string> prewords;
        
        sort(words.begin(), words.end(), [](const string &a, const string &b) {
            return a.size() < b.size();
        });
        
        for (string word : words) {
            if (canForm(word, prewords)) {
                res.push_back(word);
            }
            prewords.insert(word);
        }
        
        return res;
    }
    
private:
    bool canForm(string &word, unordered_set<string> &prewords) {
        if (prewords.empty()) return false;
        int n = word.length();
        vector<bool> dp(n+1, false);
        dp[0] = true;
        for (int i = 1; i <= word.length(); i++) {
            for (int j = 0; j < i; j++) {
                if (!dp[j]) continue;
                if (prewords.count(word.substr(j, i-j))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[n];
    }
};
```





















