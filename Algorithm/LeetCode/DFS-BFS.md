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



## 3. Coffee Machine

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









































