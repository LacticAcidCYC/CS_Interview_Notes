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









































