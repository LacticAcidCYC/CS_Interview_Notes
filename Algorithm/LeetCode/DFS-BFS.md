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









































