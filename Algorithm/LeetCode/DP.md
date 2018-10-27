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





























































