## Linkedin OA

### 1. Friend Circles (LeetCode 547)

DFS/Union-Find

(1) DFS

```c++
// Time complexity: O(n^2)
// Space complexity: O(n)

// friend circles (DFS)
int findCircleNum2(vector<vector<int>>& M) {
    if (M.empty()) return 0;
    int n = (int) M[0].size();
    int cnt = 0;
//    deque<bool> visited(n, false);
    vector<bool> visited(n, false);
    
    for (int i=0; i<n; i++) {
        cnt += !visited[i] ? dfs(i, M, visited), 1 : 0;
    }
    return cnt;
}

void dfs(int i,vector<vector<int>>& M, vector<bool> & visited) {
    visited[i] = true;
    
    auto row = M[i];
    for (int j = 0; j < (int)row.size(); j++) {
        if (!visited[j] && row[j] == 1) {
            dfs(j, M, visited);
        }
    }
}
```



(2) Union-Find:

```c++
// Time complexity: O(n^3)
// Space complexity: O(n)

// friend circles (UnionFind)
// union by rank
int findCircleNum(vector<vector<int>>& M) {
    if (M.empty()) return 0;
    int n = (int) M[0].size();
    int cnt = n;
    vector<int> parent(n);
    vector<int> rank(n, 0);
    for (int i=0; i<n; i++) {
        parent[i] = i;
    }
    
    for (int i=0; i<n-1; i++) {
        for (int j=i+1; j<n; j++) {
            if (M[i][j]) {
                int rootI = find(i, parent);
                int rootJ = find(j, parent);
                if (rootI == rootJ) continue;
                if (rank[rootI] > rank[rootJ]) {
                    parent[rootJ] = rootI;
                } else {
                    parent[rootI] = rootJ;
                    if (rank[rootI] == rank[rootJ]) {
                        rank[rootJ]++;
                    }
                }
                cnt--;
            }
        }
    }
    return cnt;
}

int find(int x, vector<int> &parent) {
    // Path compression
    if (parent[x] != x) {
        parent[x] = find(parent[x], parent);
    }
    return parent[x];
}
```



### 2. simple queries(binary search)

(1) binary search

```c++
//Time Complexity: O(nlogn)
// Space Complexity: O(1)

// simple queries(binary search)
// Return an array containing how many integers in nums1 are smaller than or equal to each integer in nums2
vector<int> simpleQueries(vector<int> nums1, vector<int> nums2);
int bs(vector<int> v, int n);

vector<int> simpleQueries(vector<int> nums1, vector<int> nums2) {
    sort(nums1.begin(), nums1.end());
    int len = (int) nums2.size();
    vector<int> queries(len, 0);
    for (int i=0; i<len; i++) {
        queries[i] = bs(nums1, nums2[i]);
    }
    return queries;
}

// upper_bound
int bs(vector<int> v, int n) {
    int len = (int) v.size();
    int l = 0, r = len-1;
    while (l < r) {
        int mid = l + (r - l) / 2;
        if (v[mid] > n) {
            r = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```



### 3. monsoon umbrella (coin change)

(1) backtracking (early pruning(termination))

```c++
// Time Complexity: O(len(coins)*amount) 
// Space Complexity: O(amount)

// backtrack
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
```



(2) dp

```c++
// Time Complexity: O(MN) (M:amount, N:number of coins);
// Space Complexity: O(M)

// dp
int coinChange2(vector<int>& coins, int amount) {
    int n = (int) coins.size();
    vector<int> dp(amount+1, INT_MAX);
    dp[0] = 0;
    for (int i=0; i<n; i++) {
        for (int j=coins[i]; j<=amount; j++) {
            if (dp[j-coins[i]] != INT_MAX) {
                dp[j] = min(dp[j], dp[j-coins[i]]+1);
            }
        }
    }
    return dp[amount] == INT_MAX ? -1 : dp[amount];
}
```



Follow up:

### LeetCode 518 Coin Change 2

Backtrack? Dp?

(1) backtrack (top-bottom) (TLE! Too many overlapping problems)

Just like recursive Fibonacci

```c++
// Time Complexity: 
// Space Complexity: 

int change(int amount, vector<int>& coins) {
    int n = coins.size();
    int cnt = 0;
    backtrack(amount, coins, 0, cnt);
    return cnt;
}

void backtrack(int amount, vector<int> &coins, int index, int &cnt) {
    if (amount == 0) {
        cnt++;
        return;
    }
    if (index == coins.size()) return;

    for (int i=amount/coins[index]; i>=0; i--) {
        backtrack(amount-i*coins[index], coins, index+1, cnt);
    } 
}
```



(2) DP (bottom-up)

```c++
// Time Complexity: O(MN) (M:amount, N:number of coins);
// Space Complexity: O(M)

int change(int amount, vector<int>& coins) {
    int n = coins.size();
    vector<int> dp(amount+1, 0);
    dp[0] = 1;
    for (int i=0; i<n; i++) {
        for (int j=coins[i]; j<=amount; j++) {
            dp[j] = dp[j] + dp[j-coins[i]];
        }
    }
    return dp[amount];
}
```



### LeetCode 279 Perfect Squares

[reference](https://leetcode.com/problems/perfect-squares/discuss/71488/Summary-of-4-different-solutions-(BFS-DP-static-DP-and-mathematics))

(1) backtrack

```c++
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
```



(2) dp 

```c++
// 1
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


// 2
class Solution {
public:
    int numSquares(int n) {
        static vector<int> dp{0};
        while(dp.size() <= n) {
            int m = dp.size();
            int cnt = INT_MAX;
            for(int i=1; i*i<=m; i++) {
                cnt = min(cnt, dp[m-i*i] + 1);
            }
            dp.push_back(cnt);
        }
        return dp[n];
    }
};

// 3 using static to store the former results
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
```



(3) math

```c++
// 1
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

// 2
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
```























