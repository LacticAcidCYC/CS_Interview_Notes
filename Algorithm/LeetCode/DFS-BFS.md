## 1. LeetCode 200 [Number of Islands](https://leetcode.com/problems/number-of-islands/)

BFS:

```c++
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
                    // queue is better, but here stack is OK
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





































