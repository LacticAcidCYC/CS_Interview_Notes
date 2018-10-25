## 1. LeetCode 338 [Counting Bits](https://leetcode.com/problems/counting-bits/)

```c++

```

[solution](https://leetcode.com/problems/counting-bits/discuss/79557/How-we-handle-this-question-on-interview-Thinking-process-%2B-DP-solution)



## 2. LeetCode 279 [Perfect Squares](https://leetcode.com/problems/perfect-squares/)

## (1) DP

```c++
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

```

[stack-solution](https://leetcode.com/problems/longest-valid-parentheses/discuss/14126/My-O(n)-solution-using-a-stack)



### (2) DP

```c++
class Solution {
public:
   int longestValidParentheses(string s) {
        if(s.length() <= 1) return 0;
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







































































