# DFS (Backtracking) & BFS



## 1. LeetCode 200 [Number of Islands](https://leetcode.com/problems/number-of-islands/)

### (1) BFS

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

class Solution {
public:
    const vector<vector<int>> dirs = {{0, 1}, {0,-1}, {1,0}, {-1,0}};
    
    int numIslands(vector<vector<char>>& grid) {
        if (grid.size() == 0 || grid[0].size() == 0) { return 0;}
        int m = grid.size();
        int n = grid[0].size();
        int cnt = 0;
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (grid[i][j] == '1') {
                    cnt++;
                    // queue is common, but here stack is OK
                    stack<pair<int,int>> st;
                    st.push(make_pair(i,j));
                    grid[i][j] = '0';
                    while (!st.empty()) {
                        pair<int, int> p = st.top();
                        st.pop();
                        for (int k=0; k<4; k++) {
                            int ni = p.first + dirs[k][0];
                            int nj = p.second + dirs[k][1];
                            if (ni >= 0 && ni < m && nj >= 0 && nj < n) {
                                if (grid[ni][nj] == '1') {
                                    st.push(make_pair(ni, nj));
                                    grid[ni][nj] = '0';
                                }
                            }
                        }
                    }
                }
            }
        }
        return cnt;
    }
};
```



### (2) DFS

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        if (grid.empty() || grid[0].empty()) return 0;
        
        int m = grid.size();
        int n = grid[0].size();
        int cnt = 0;
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (grid[i][j] == '1') {
                    cnt++;
                    dfs(grid, i, j, m, n);
                }
            }
        }
        
        return cnt;
    }
    
private:
    void dfs(vector<vector<char>>& grid, int i, int j, int &m, int &n) {
        if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == '0') {
            return;
        }
        
        grid[i][j] = '0';
        dfs(grid, i-1, j, m, n);
        dfs(grid, i, j+1, m, n);
        dfs(grid, i+1, j, m, n);
        dfs(grid, i, j-1, m, n);
    }
};
```



## 2. LeetCode 694 [Number of Distinct Islands](https://leetcode.com/problems/number-of-distinct-islands/)

BFS:

```c++
class Solution {
public:
    int m, n;
    const vector<vector<int>> steps = {{1,0}, {-1,0}, {0,1}, {0,-1}};
        
    bool inside(int i, int j) {
        return i<m && i>=0 && j<n && j>=0;
    }
    
    int numDistinctIslands(vector<vector<int>>& grid) {
        set<vector<pair<int,int>>> S;
        m = grid.size();
        n = grid[0].size();
        for(int i=0; i<m; i++) {
            for(int j=0; j<n; j++) {
                if(grid[i][j]) {
                    vector<pair<int,int>> vp;
                    queue<int> q;
                    q.push(i);
                    q.push(j);
                    grid[i][j] = 0;//key
                    while(!q.empty()) {
                        int x = q.front();
                        q.pop();
                        int y = q.front();
                        q.pop();
                        vp.push_back({x,y});
                        for(vector<int> step:steps) {
                            int nx = x + step[0];
                            int ny = y + step[1];
                            if(inside(nx, ny) && grid[nx][ny]) {
                                grid[nx][ny] = 0;
                                q.push(nx);
                                q.push(ny);
                            }
                        }
                    }
                    //coordinate translation坐标平移
                    int minX = m, minY = n;
                    for(auto& p : vp) {
                        minX = min(minX, p.first);
                        minY = min(minY, p.second);
                    }
                    for(auto& p : vp) {
                        p.first -= minX;
                        p.second -= minY;
                    }
                    sort(vp.begin(), vp.end());
                    S.insert(vp);
                }
            }
        }
        return S.size();
    }
};
```



## 3. Coffee Machine (Mock Interview Problem)

### Description

```
Coffee Machine has a few buttons, [100, 120], [200, 230], [400, 410] (inclusive);

Find if we can get the amount within a given range.

e.g. [90, 120] --> true 
e.g. [300, 360] --> true  (click b1 3 times or b1 + b2)
e.g. [300, 400] --> true  (click b1 3 times)
e.g. [100, 110] --> false
```

### Solution:

```c++
#include <iostream>
#include <vector>
using namespace std;

bool getCoffee(vector<pair<int, int>> &buttons, pair<int, int> target);
bool dfs(vector<pair<int, int>> &buttons, pair<int, int> &target, pair<int, int> &range,
        vector<int> &amounts, int num_btn);

bool getCoffee(vector<pair<int, int>> &buttons, pair<int, int> target) {
  int n = buttons.size();

  vector<int> amounts(n);
  for (int i=0; i<n; i++) {
    amounts[i] = target.second / buttons[i].first;
  }

  pair<int, int> range{0, 0};

  return dfs(buttons, target, range, amounts, 0);
}

bool dfs(vector<pair<int, int>> &buttons, pair<int, int> &target, pair<int, int> &range,
        vector<int> &amounts, int num_btn) {
  if (num_btn == buttons.size()) {
    return false;
  }
  if (range.first >= target.first && range.second <= target.second) {
    return true;
  }

  for (int i=amounts[num_btn]; i>=0; i--) {
    range.first += i * buttons[num_btn].first;
    range.second += i * buttons[num_btn].second;
    // cout << num_btn << "," << i << " || " << range.first << "," << range.second << endl;
    if (range.first > target.second) {
      range.first -= i * buttons[num_btn].first;
      range.second -= i * buttons[num_btn].second;
      continue;
    }
    if (dfs(buttons, target, range, amounts, num_btn+1)) {
      return true;
    }
    range.first -= i * buttons[num_btn].first;
    range.second -= i * buttons[num_btn].second;
  }

  return false;
}

int main() {
	vector<pair<int, int>> buttons = {{100, 120}, {200, 230}, {400, 410}};
  pair<int, int> target = {300, 350};
  cout << getCoffee(buttons, target) << endl;
	return 0;
}
```



## 4. LeetCode 934 [Shortest Bridge](https://leetcode.com/problems/shortest-bridge/)

### Expand the Island

[solution](https://leetcode.com/problems/shortest-bridge/discuss/189293/C%2B%2B-BFS-Island-Expansion)

We first paint islands using DFS, so the first island is identified by color 2, and second - by color 3.
Then we start expanding the second island by paining connected empty area. Each round, we increase the color (4, 5, and so on) so we can keep track of the newly painted area. This ends when we "bump" into the first island.

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn) (recursive stack)

class Solution {
public:
    int shortestBridge(vector<vector<int>>& A) {
        int m = A.size();
        int n = A[0].size();
        // paint the two islands with color 2 & 3
        for (auto i=0, color = 2; i<m; i++) {
            for (auto j=0; j<n; j++) {
                if (paint(A, i, j, color)) {
                    color++;
                }
            }
        }
        
        // expand the island with color 3
        for (auto color=3; ; color++) {
            for (auto i=0; i<m; i++) {
                for (auto j=0; j<n; j++) {
                    if (A[i][j] == color) {
                        if (expand(A, i-1, j, color+1) || expand(A, i, j+1, color+1) ||
                            expand(A, i+1, j, color+1) || expand(A, i, j-1, color+1)) {
                            return color - 3;
                        }
                    }
                }
            }
        }
    }
    
    bool paint(vector<vector<int>>& matrix, int row, int col, int color) {
        if (row < 0 || row == matrix.size() || col < 0 || col == matrix.size() || matrix[row][col] != 1) {
            return false;
        }
        matrix[row][col] = color;
        // paint the 4 adjacent grids
        paint(matrix,row-1,col,color);
        paint(matrix,row,col+1,color);
        paint(matrix,row+1,col,color);
        paint(matrix,row,col-1,color);
        return true;
    }
    
    bool expand(vector<vector<int>>& matrix, int row, int col, int color) {
        if (row < 0 || row == matrix.size() || col < 0 || col == matrix.size()) {
            return false;
        }
        if(matrix[row][col] == 0) {
            matrix[row][col] = color;
        }
        return matrix[row][col] == 2;
    }
};
```



## 5. LeetCode 130 [Surrounded Regions](https://leetcode.com/problems/surrounded-regions/)

### (1) BFS

```c++
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        if (board.empty() || board[0].empty()) return;
        int m = board.size();
        int n = board[0].size();
        
        const vector<vector<int>> dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
        
        vector<vector<bool>> visited(m, vector<bool>(n, false));
        vector<pair<int, int>> area;
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (!visited[i][j]) {
                    visited[i][j] = true;
                    if (board[i][j] == 'O') {
                        area.clear();
                        queue<pair<int, int>> q;
                        q.push({i, j});
                        area.push_back({i, j});
                        bool TOUCH_BORDER = false;
                        
                        while (!q.empty()) {
                            auto front = q.front();
                            q.pop();
                            if (front.first == 0 || front.first == m-1 || front.second == 0 || front.second == n-1) {
                                TOUCH_BORDER = true;
                            }
                            for (int k=0; k<4; k++) {
                                int nx = front.first + dirs[k][0];
                                int ny = front.second + dirs[k][1];
                                if (nx >= 0 && nx < m && ny >= 0 && ny < n && board[nx][ny] == 'O' && !visited[nx][ny]) {
                                    q.push({nx, ny});
                                    area.push_back({nx, ny});
                                    visited[nx][ny] = true;
                                }
                            }
                        }
                        
                        if (!TOUCH_BORDER) {
                            for (auto const &p : area) {
                                board[p.first][p.second] = 'X';
                            }
                        }
                    }
                }
            }
        }
    }
};
```



### (2) BFS-2

#### Explanation

1. First, change all the 'O' on the border and its connected cells to 'S'

2. scan the matrix, change all the 'S' to 'O', all the 'O' to 'X'

```c++
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        if (board.empty() || board[0].empty()) return;
        int m = board.size();
        int n = board[0].size();
        
        const vector<vector<int>> dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
        
        vector<pair<int, int>> borderOs;
        for (int i=0; i<m; i++) {
            if (board[i][0] == 'O') {
                borderOs.push_back({i, 0});
            }
            if (board[i][n-1] == 'O') {
                borderOs.push_back({i, n-1});
            }
        }
        for (int j=1; j<n-1; j++) {
            if (board[0][j] == 'O') {
                borderOs.push_back({0, j});
            }
            if (board[m-1][j] == 'O') {
                borderOs.push_back({m-1, j});
            }
        }
        
        for (auto p : borderOs) {
            queue<pair<int, int>> q;
            if (board[p.first][p.second] == 'O') {
                board[p.first][p.second] = 'S';
                q.push(p);
            }
            while (!q.empty()) {
                auto front = q.front();
                q.pop();
                for (int k=0; k<4; k++) {
                    int nx = front.first + dirs[k][0];
                    int ny = front.second + dirs[k][1];
                    if (nx >= 0 && nx < m && ny >= 0 && ny < n && board[nx][ny] == 'O') {
                        q.push({nx, ny});
                        board[nx][ny] = 'S';
                    }
                }
            }
        }
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (board[i][j] == 'S') {
                    board[i][j] = 'O';
                } else if (board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
            }
        }
    }
};
```

[solution](https://leetcode.com/problems/surrounded-regions/discuss/41630/9-lines-Python-148-ms)



## 6. LeetCode 301 [Remove Invalid Parentheses](https://leetcode.com/problems/remove-invalid-parentheses/)

### Explanation

1. count all the 'unmatched' **(** and **)**;
2. removed the invalid parentheses using dfs.

```c++
// Time Complexity: O(2^n)
// Space Complexity: O(n)

class Solution {
public:
    vector<string> removeInvalidParentheses(string s) {
        int left_cnt = 0, right_cnt = 0;
        
        for (auto c : s) {
            if (c == ')') {
                if (left_cnt == 0) {
                    right_cnt++;
                } else {
                    left_cnt--;
                }
            } else if (c == '(') {
                left_cnt++;
            }
        }
        
        vector<string> res;
        dfs(s, res, left_cnt, right_cnt, 0);
        
        return res;
    }
    
private:
    void dfs(string s, vector<string> &res, int left_cnt, int right_cnt, int index) {
        if (left_cnt == 0 && right_cnt == 0 && isValid(s)) {
            res.push_back(s);
            return;
        }
        
        for (int i=index; i<s.length(); i++) {
            // important!! avoid duplicates e.g. "(((("
            if (i != index && s[i] == s[i-1]) continue;
            if (s[i] == '(' || s[i] == ')') {
                auto tmp = s;
                tmp.erase(i, 1);
                if (left_cnt > 0 && s[i] == '(') {
                    dfs(tmp, res, left_cnt-1, right_cnt, i);
                } else if (right_cnt > 0 && s[i] == ')') {
                    dfs(tmp, res, left_cnt, right_cnt-1, i);
                }
            }
        }
        
    }
    
    bool isValid(string s) {
        int count = 0;
        
        for (auto c : s) {
            if (c == '(') {
                count++;
            } else if (c == ')') {
                count--;
            }
            if (count < 0) return false;
        }
        
        return count == 0;
    }
};
```

[bfs-solution](https://leetcode.com/problems/remove-invalid-parentheses/discuss/75032/Share-my-Java-BFS-solution)

[solution](https://leetcode.com/problems/remove-invalid-parentheses/discuss/75050/My-C%2B%2B-DFS-Solution-16ms)



## 7. LeetCode 97 [Interleaving String](https://leetcode.com/problems/interleaving-string/)

### BFS

```c++
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int m = s1.length();
        int n = s2.length();
        if (m + n != s3.length()) return false;
        
        vector<int> visited((m+1)*(n+1), 0); // use 1-D vector to simulate the matrix
        queue<pair<int, int>> q; // store the candidate path points
        q.push({0, 0});
        
        while (!q.empty()) {
            auto p = q.front();
            q.pop();
            if (p.first == m && p.second == n) return true;
            
            // move right
            if (p.second < n && s2[p.second] == s3[p.first+p.second] && !visited[p.first * (n+1) + p.second + 1]) {
                visited[p.first*(n+1) + p.second + 1] = true;
                q.push({p.first, p.second+1});
            }
            
            // move down
            if (p.first < m && s1[p.first] == s3[p.first+p.second] && !visited[(p.first + 1) * (n+1) + p.second]) {
                visited[(p.first + 1) * (n+1) + p.second] = true;
                q.push({p.first+1, p.second});
            }
        }
        
        return false;
    }
};
```

[solution](https://leetcode.com/problems/interleaving-string/discuss/31948/8ms-C%2B%2B-solution-using-BFS-with-explanation)



## 8. LeetCode 329 [Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

To get max length of increasing sequences:

1. Do `DFS` from every cell
2. Compare every 4 direction and skip cells that are out of boundary or smaller
3. Get matrix `max` from every cell's `max`
4. Use `matrix[x][y] <= matrix[i][j]` so we don't need a `visited[m][n]` array
5. The key is to `cache` the distance because it's highly possible to revisit a cell

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

class Solution {
public:
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        
        vector<vector<int>> dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
        int m = matrix.size();
        int n = matrix[0].size();
        int max_path = 0;
        vector<vector<int>> memo(m, vector<int>(n, 0));
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                int max_len = dfs(matrix, dirs, memo, i, j);
                max_path = max(max_path, max_len);
            }
        }
        
        return max_path;
    }
    
private:
    int dfs(vector<vector<int>>& matrix, vector<vector<int>> &dirs, vector<vector<int>> &memo, int row, int col) {
        if (memo[row][col] != 0) return memo[row][col];
        
        int m = matrix.size();
        int n = matrix[0].size();
        int max_len = 0;
        
        for (int i=0; i<4; i++) {
            int nr = row + dirs[i][0];
            int nc = col + dirs[i][1];
            
            if (nr >= 0 && nr < m && nc >= 0 && nc < n && matrix[nr][nc] > matrix[row][col]) {
                max_len = max(max_len, 1 + dfs(matrix, dirs, memo, nr, nc));
            } else {
                max_len = max(max_len, 1);
            }
        }
        
        memo[row][col] = max_len;
        return max_len;
    }
};

// other version
class Solution {
public:
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        
        vector<vector<int>> dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
        int m = matrix.size();
        int n = matrix[0].size();
        int max_path = 0;
        vector<vector<int>> memo(m, vector<int>(n, 0));
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                int max_len = dfs(matrix, dirs, memo, i, j, INT_MIN);
                max_path = max(max_path, max_len);
            }
        }
        
        return max_path;
    }
    
private:
    int dfs(vector<vector<int>>& matrix, vector<vector<int>> &dirs, vector<vector<int>> &memo, int i, int j, int prev) {
        if (i < 0 || i >= matrix.size() || j < 0 || j >= matrix[0].size() || matrix[i][j] <= prev) {
            return 0;
        }
        if (memo[i][j] != 0) return memo[i][j];

        int max_len = 0;
        
        for (int k=0; k<4; k++) {
            int ni = i + dirs[k][0];
            int nj = j + dirs[k][1];
            max_len = max(max_len, 1 + dfs(matrix, dirs, memo, ni, nj, matrix[i][j]));
        }
        
        memo[i][j] = max_len;
        return max_len;
    }
};
```

[solution](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/discuss/78308/15ms-Concise-Java-Solution)



## 9. LeetCode 51 [N-Queens](https://leetcode.com/problems/n-queens/)

```c++
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> res;
        vector<string> board(n, string(n, '.'));
        solve(res, board, 0, n);
        return res;
    }
private:
    bool isValid(vector<string> &board, int row, int col, int n) {
        // check column
        for (int i=0; i<row; i++) {
            if (board[i][col] == 'Q') {
                return false;
            }
        }
        // check 45 degree diagonal
        for (int i=row-1, j=col-1; i>=0 && j>=0; --i, --j) {
            if (board[i][j] == 'Q') {
                return false;
            }
        }
        // check 135
        for (int i=row-1, j=col+1; i>=0 && j<n; --i, ++j) {
            if (board[i][j] == 'Q') {
                return false;
            }
        }
        return true;
    }
    
    // backtrack
    void solve(vector<vector<string>> &res, vector<string> &board, int row, int n) {
        if (row == n) {
            res.push_back(board);
            return;
        }
        for (int col = 0; col < n; col++) {
            if (isValid(board, row, col, n)) {
                board[row][col] = 'Q';
                solve(res, board, row+1, n);
                board[row][col] = '.';
            }
        }
    }
};
```



## 10. LeetCode 52 [N-Queens II](https://leetcode.com/problems/n-queens-ii/)

```c++
class Solution {
public:
    int totalNQueens(int n) {
        int res = 0;
        vector<string> board(n, string(n, '.'));
        solve(res, board, 0, n);
        return res;
    }
    
private:
    bool isValid(vector<string> &board, int row, int col, int n) {
        // check column
        for (int i=0; i<row; i++) {
            if (board[i][col] == 'Q') {
                return false;
            }
        }
        // check 45 degree diagonal
        for (int i=row-1, j=col-1; i>=0 && j>=0; --i, --j) {
            if (board[i][j] == 'Q') {
                return false;
            }
        }
        // check 135
        for (int i=row-1, j=col+1; i>=0 && j<n; --i, ++j) {
            if (board[i][j] == 'Q') {
                return false;
            }
        }
        return true;
    }
    
    // backtrack
    void solve(int &res, vector<string> &board, int row, int n) {
        if (row == n) {
            res++;
            return;
        }
        for (int col = 0; col < n; col++) {
            if (isValid(board, row, col, n)) {
                board[row][col] = 'Q';
                solve(res, board, row+1, n);
                board[row][col] = '.';
            }
        }
    }
};
```



## 11. LeetCode 79 [Word Search](https://leetcode.com/problems/word-search/)

```c++
// m,n : board
// k: length of word
// Time Complexity: O(mn * 3^k) or O(m^2n^2)
// Space Complexity: O(4mn)

class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty()) return false;
        if (word.empty()) return true;
        
        int m = board.size();
        int n = board[0].size();
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (dfs(i, j, board, word, 0)) {
                    return true;
                }
            }
        }
        
        return false;
    }
    
private:
    bool dfs(int x, int y, vector<vector<char>> &board, string &word, int index) {
        if (index == word.size()) return true;
        if (x < 0 || x >= board.size() || y < 0 || y >= board[0].size() || board[x][y] != word[index]) {
            return false;
        }
        
        board[x][y] = '.';
        
        if (dfs(x+1, y, board, word, index+1) || dfs(x-1, y, board, word, index+1)
           || dfs(x, y+1, board, word, index+1) || dfs(x, y-1, board, word, index+1)) {
            return true;
        }
        
        board[x][y] = word[index];
        
        return false;
    }
};

// original
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty()) return false;
        if (word.empty()) return true;
        
        int m = board.size();
        int n = board[0].size();
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (dfs(i, j, board, word, 0)) {
                    return true;
                }
            }
        }
        
        return false;
    }
    
private:
    bool dfs(int x, int y, vector<vector<char>> &board, string &word, int index) {
        if (index == word.size()) return true;
        if (x < 0 || x >= board.size() || y < 0 || y >= board[0].size() || board[x][y] != word[index]) {
            return false;
        }
        
        board[x][y] = '.';
        
        const vector<vector<int>> dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
        
        for (int k=0; k<4; k++) {
            int nx = x + dirs[k][0];
            int ny = y + dirs[k][1];
            if (dfs(nx, ny, board, word, index+1)) {
                return true;
            }
        }
        
        board[x][y] = word[index];
        
        return false;
    }
};
```

[solution](https://leetcode.com/problems/word-search/discuss/27658/Accepted-very-short-Java-solution.-No-additional-space.)



## 12. LeetCode 399 [Evaluate Division](https://leetcode.com/problems/evaluate-division/)

```c++
// E: number of edges(equations)
// Q: number of queries
// Time Complexity: O(E + E*Q)
// Space Complexity: O(E)

class Solution {
public:
    vector<double> calcEquation(vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries) {
        unordered_map<string, unordered_map<string, double>> graph;
        vector<double> ans;
        int n = equations.size();
        
        for (int i=0; i<n; i++) {
            const string& A = equations[i].first;
            const string& B = equations[i].second;
            const double& k = values[i];
            graph[A][B] = k;
            if (k != 0) {
                graph[B][A] = 1.0 / k;
            }
        }
        
        for (const auto& query : queries) {
            const string &X = query.first;
            const string &Y = query.second;
            if (!graph.count(X) || !graph.count(Y)) {
                ans.push_back(-1.0);
                continue;
            }
            unordered_set<string> visited;
            ans.push_back(dfs(graph, X, Y, visited));
        }
        
        return ans;
    }
    
private:
    double dfs(unordered_map<string, unordered_map<string, double>> &graph, 
               const string &X, const string &Y, unordered_set<string> &visited) {
        if (X == Y) return 1.0;
        visited.insert(X);
        
        for (const auto& pair : graph[X]) {
            const string& Z = pair.first;
            if (!visited.count(Z)) {
                double d = dfs(graph, Z, Y, visited);
                if (d >= 0) {
                    return d * pair.second;
                }
            }
        }
        
        return -1.0;
    }
};
```



## 13. LeetCode 22 [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)

[solution](https://leetcode.com/problems/generate-parentheses/discuss/10100/Easy-to-understand-Java-backtracking-solution)

[catalan-number](https://zh.wikipedia.org/wiki/%E5%8D%A1%E5%A1%94%E5%85%B0%E6%95%B0)

### (1) backtracking-1

```c++
// Time Complexity: O(4^n / n ^ 0.5)
// Space Complexity: O(4^n / n ^ 0.5)

class Solution {
public:
    vector<string> generateParenthesis(int n) {
        if (n <= 0) return {};
        vector<string> res;
        
        string g_str = "";
        backtrack(res, g_str, 0, 0, n);
        
        return res;
    }
    
    void backtrack(vector<string> &res, string &str, int l_cnt, int r_cnt, int &max) {
        if (l_cnt == max && r_cnt == max) {
            res.push_back(str);
            return;
        }
        
        if (l_cnt < max) {
            str += '(';
            backtrack(res, str, l_cnt+1, r_cnt, max);
            str.pop_back();
        }
        if (r_cnt < l_cnt) {
            str += ')';
            backtrack(res, str, l_cnt, r_cnt+1, max);
            str.pop_back();
        }
    }
};
```



### (2) backtracking-2 (Different Initialization and end condition)

```c++
// Time Complexity: O(4^n / n ^ 0.5)
// Space Complexity: O(4^n / n ^ 0.5)

class Solution {
public:
    vector<string> generateParenthesis(int n) {
        if (n <= 0) return {};
        vector<string> res;
        
        string g_str = "";
        backtrack(res, g_str, n, 0);
        
        return res;
    }
    
    void backtrack(vector<string> &res, string &str, int l_cnt, int r_cnt) {
        if (l_cnt == 0 && r_cnt == 0) {
            res.push_back(str);
            return;
        }
        
        if (l_cnt > 0) {
            str += '(';
            backtrack(res, str, l_cnt-1, r_cnt+1);
            str.pop_back();
        }
        if (r_cnt > 0) {
            str += ')';
            backtrack(res, str, l_cnt, r_cnt-1);
            str.pop_back();
        }
    }
};
```



## 14. LeetCode 78 [Subsets](https://leetcode.com/problems/subsets/)

### (1) Recursive

```c++
// Time Complexity: O(2^n)
// Space Complexity: O(1)

class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> subs;
        vector<int> sub;
        addSubset(nums, 0, sub, subs);
        return subs;
    }
    
    void addSubset(vector<int>& nums, int start, vector<int> sub, vector<vector<int>>& subs) {
        subs.push_back(sub);
        for(int i=start; i<nums.size(); i++) {
            sub.push_back(nums[i]);
            addSubset(nums, i+1, sub, subs);
            sub.pop_back();
        }
    }
};
```



### (2) Iterative

```c++
// Time Complexity: O(2^n)
// Space Complexity: O(1)

class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int n = (int) nums.size();
        vector<vector<int>> subs(1, vector<int>());
        for(int i=0; i<n; i++) {
            int m = subs.size();
            for(int j=0; j<m; j++) {
                subs.push_back(subs[j]);
                subs.back().push_back(nums[i]);
            }
        }
        return subs;
    }
};
```



### (3) Bit Manipulation

```c++
// Time Complexity: O(n*2^n)
// Space Complexity: O(1)

class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        int n = (int)nums.size();
        int m = (int)pow(2,n);
        vector<vector<int>> subs(m, vector<int>());
        for(int i=0,q=1; i<n; i++,q<<=1) {
            for(int j=q; j<m; j+=q) {
                for(int k=0; k<q; k++) {
                    subs[j++].push_back(nums[i]);
                }
            }
        }
        return subs;
    }
};
```

[solution](https://leetcode.com/problems/subsets/discuss/27278/C%2B%2B-RecursiveIterativeBit-Manipulation)



## 15. LeetCode 90 [Subsets II](https://leetcode.com/problems/subsets-ii/)

```c++
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        backtrack(res, nums, {}, 0);
        return res;
    }
    
    void backtrack(vector<vector<int>>& res, vector<int>& nums, vector<int> sub, int begin) {
        res.push_back(sub);
        for (int i=begin; i<nums.size(); i++) {
            if (i == begin || nums[i] != nums[i-1]) {
                sub.push_back(nums[i]);
                backtrack(res, nums, sub, i+1);
                sub.pop_back();
            }
        }
    }
};
```



## 16. LeetCode 140 [Word Break II](https://leetcode.com/problems/word-break-ii/)

### Original 139

```c++
class Solution {
public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> hashset(wordDict.begin(), wordDict.end());
        unordered_map<int, vector<string>> mp;
        return dfs(s, hashset, 0, mp);
    }
    
    vector<string> dfs(string &s, unordered_set<string> &wordSet, int start, unordered_map<int, vector<string>> &mp) {
        if (mp.count(start)) {
            return mp[start];
        }
        
        vector<string> res;
        int n = s.length();
        if (start == n) {
            res.push_back("");
        }
        
        for (int i=start+1; i<=n; i++) {
            if (wordSet.count(s.substr(start, i-start))) {
                vector<string> subres = dfs(s, wordSet, i, mp);
                for (auto ss : subres) {
                    res.push_back(s.substr(start, i-start) + (ss.empty() ? "" : (" " + ss)));
                }
            }
        }
        
        mp[start] = res;
        
        return res;
    }
};
```



## 17. LeetCode 733 [Flood Fill](https://leetcode.com/problems/flood-fill/)

```c++
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int newColor) {
        int color = image[sr][sc];
        if (color != newColor) dfs(image, sr, sc, color, newColor);
        return image;
    }
    
private:
    void dfs(vector<vector<int>>& image, int r, int c, int color, int newColor) {
        if (image[r][c] == color) {
            image[r][c] = newColor;
            if (r >= 1) dfs(image, r-1, c, color, newColor);
            if (c >= 1) dfs(image, r, c-1, color, newColor);
            if (r + 1 < image.size()) dfs(image, r+1, c, color, newColor);
            if (c + 1 < image[0].size()) dfs(image, r, c+1, color, newColor);
        }
    }
};
```



## 18. LeetCode 127 [Word Ladder](https://leetcode.com/problems/word-ladder/)

```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> start, end, *pstart, *ptail;
        unordered_set<string> words;
        for(auto w : wordList) words.insert(w);
        start.insert(beginWord);
        if(words.find(endWord) != words.end()) end.insert(endWord);
        else return 0;
        int dist = 2;
        while(!start.empty() && !end.empty()) {
            if(start.size() < end.size()) {
                pstart = &start;
                ptail = &end;
            } else {
                pstart = &end;
                ptail = &start;
            }
            unordered_set<string> temp;
            for(auto word : *pstart) words.erase(word);
            for(auto iter = pstart->begin(); iter != pstart->end(); iter++) {
                string word = *iter;
                //words.erase(word);
                for(int i=0; i<word.length(); i++) {
                    char changeLetter = word[i];
                    for(int j=0; j<26; j++) {
                        word[i] = 'a' + j;
                        if(ptail->find(word) != ptail->end()) {
                            return dist;
                        }
                        if(words.find(word) != words.end()) {
                            temp.insert(word);
                            //words.erase(word);
                        }
                    }
                    word[i] = changeLetter;
                }
            }
            dist++;
            //cout << temp.size() << endl;
            swap(*pstart, temp);
        }
        return 0;
    }
};
```



## 19. LeetCode 128 [Word Ladder II](https://leetcode.com/problems/word-ladder-ii/)

```c++
class Solution {
public:
    vector<vector<string>> findLadders(string beginWord, string endWord, vector<string>& wordList) {
        vector<vector<string>> ladders;
        vector<string> ladder;
        ladder.push_back(beginWord);
        unordered_set<string> words(wordList.begin(), wordList.end());
        unordered_set<string> beginWords, endWords;
        beginWords.insert(beginWord);
        if(words.find(endWord) != words.end()) endWords.insert(endWord);
        else return ladders;
        unordered_map<string, vector<string>> children;
        bool forward = true;
        if(searchLadders(beginWords, endWords, words, children, forward))
            //cout << "success" << endl;
            genLadders(beginWord, endWord, children, ladder, ladders);
        return ladders;
    }
private:
    bool searchLadders(unordered_set<string>& beginWords, unordered_set<string>& endWords,
                   unordered_set<string>& words, unordered_map<string, vector<string>>& children, bool forward) {
        //BFS
        if(beginWords.empty()) return false;
        if(beginWords.size() > endWords.size()) {
            return searchLadders(endWords, beginWords, words, children, !forward);
        }
        for(string word : beginWords) words.erase(word);
        for(string word : endWords) words.erase(word);
        unordered_set<string> intermediate;
        bool finished = false;
        for(string word : beginWords) {
            int n = (int)word.length();
            string tmp = word;
            for(int i=0; i<n; i++) {
                char changeLetter = word[i];
                for(int j=0; j<26; j++) {
                    word[i] = 'a' + j;
                    if(endWords.find(word) != endWords.end()) {
                        finished = true;
                        forward ? children[tmp].push_back(word) : children[word].push_back(tmp);
                    } else if(!finished && words.find(word) != words.end()) {
                        intermediate.insert(word);
                        forward ? children[tmp].push_back(word) : children[word].push_back(tmp);
                    }
                }
                word[i] = changeLetter;
            }
        }
        return finished || searchLadders(endWords, intermediate, words, children, !forward);
    }
    
    void genLadders(string& begin, string& end, unordered_map<string, vector<string> >& children,
                vector<string>& ladder, vector<vector<string>>& ladders) {
        //DFS
        if(begin == end) {
            ladders.push_back(ladder);
            return;
        }
        for(string child : children[begin]) {
            ladder.push_back(child);
            genLadders(child, end, children, ladder, ladders);
            ladder.pop_back();
        }
        return;
    }
};
```



## 20. LeetCode 752 [Open the Lock](https://leetcode.com/problems/open-the-lock/)

### BFS

```c++
class Solution {
public:
    int openLock(vector<string>& deadends, string target) {
        unordered_set<string> dds(deadends.begin(), deadends.end());
        unordered_set<string> q1, q2, pass, visited;
        string init = "0000";
        if (dds.find(init) != dds.end() || dds.find(target) != dds.end()) return -1;
        visited.insert("0000");
        q1.insert("0000"), q2.insert(target);
        int res = 0;
        while (!q1.empty() && !q2.empty()) {
            if (q1.size() > q2.size()) swap(q1, q2);
            pass.clear();
            for (auto ss : q1) {
                vector<string> nbrs = nbrStrs(ss);
                for (auto s : nbrs) {
                    if (q2.find(s) != q2.end()) return res + 1;
                    if (visited.find(s) != visited.end()) continue;
                    if (dds.find(s) == dds.end()) {
                        pass.insert(s);
                        visited.insert(s);
                    }
                }
            }
            swap(q1, pass);
            res++;
        }
        return -1;
    }
    
private:
    vector<string> nbrStrs(string key) {
        vector<string> res;
        for (int i = 0 ; i < 4; i++) {
            string tmp = key;
            tmp[i] = (key[i] - '0' + 1) % 10 + '0';
            res.push_back(tmp);
            tmp[i] = (key[i] - '0' - 1 + 10) % 10 + '0';
            res.push_back(tmp);
         }
        return res;
    }
};
```



## 21. LeetCode 675 [Cut Off Trees for Golf Event](https://leetcode.com/problems/cut-off-trees-for-golf-event/)

```c++
class Solution {
public:
    int cutOffTree(vector<vector<int>>& forest) {
        if (forest.empty() || forest[0].empty()) return 0;
        
        const vector<int> dr = {-1, 1, 0, 0};
        const vector<int> dc = {0, 0, -1, 1};
        
        // get all the tree positions and sort based on height
        // trees[i][0] is height. The default comparison of vector compare first element before other elements.
        int m = forest.size(), n = forest[0].size();
        vector<vector<int>> trees;
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (forest[i][j] > 1) {
                    trees.push_back({forest[i][j], i, j});
                }
            }
        }
        
        sort(trees.begin(), trees.end());
        
        int ans = 0;
        // accumulate all the paths
        for (int i = 0, cur_row = 0, cur_col = 0; i < trees.size(); i++) {
            int step = bfs(forest, cur_row, cur_col, trees[i][1], trees[i][2], dr, dc);
            // if next tree cannot be reached, step = -1;
            if (step == -1) return -1;
            ans += step;
            cur_row = trees[i][1];
            cur_col = trees[i][2];
        }
        return ans;
    }
    
private:
    int bfs(vector<vector<int>>& forest, int sr, int sc, int tr, int tc, const vector<int> &dr, const vector<int> &dc) {
        if (sr == tr && sc == tc) return 0;
        
        int R = forest.size(), C = forest[0].size();
        queue<pair<int, int>> q;
        q.push({sr, sc});
        vector<vector<int>> visited(R, vector<int>(C));
        visited[sr][sc] = 1;
        int step = 0;
        while (!q.empty()) {
            step++;
            
            int N = q.size();
            for (int i = 0; i < N; i++) {
                int row = q.front().first, col = q.front().second;
                q.pop();
                for (int i = 0; i < 4; i++) {
                    int r = row + dr[i], c = col + dc[i];
                    if (r < 0 || r >= R || c < 0 || c >= C || visited[r][c] == 1 || forest[r][c] == 0) continue;
                    if (r == tr && c == tc) return step;
                    visited[r][c] = 1;
                    q.push({r, c});
                }
            }
        }
        return -1;
    }
};
```



















