# Disjoint set / Union-Find Forest

[youtube-video-by-huahua](https://www.youtube.com/watch?v=VJnUwsE4fWA)

```latex
Find(x): find the root/cluster-id of x

Union(x, y): merge two clusters

Check whether two elements are in the same set or not in O(1)*.

Find: O(ɑ(n))* ≈ O(1)

Union: O(ɑ(n))* ≈ O(1)

Space: O(n)

Without optimization: Find: O(n)

Two key optimizations:

1. Path compression: make tree flat 
-> happened during Find()
2. Union by rank: merge low rank tree to high rank one
-> If two sub-tree has the same rank, break tie arbitrarily and increase the merged tree's
rank by 1;
-> Reduce path compression overhead.

*: amortized

ɑ(.): inverse Ackermann function

```



## 1. LeetCode 399 [Evaluate Division](https://leetcode.com/problems/evaluate-division/)

[huahua](http://zxi.mytechroad.com/blog/graph/leetcode-399-evaluate-division/)

```c++
// E: number of edges(equations)
// Q: number of queries
// Time Complexity: O(E + Q)
// Space Complexity: O(E)

class Solution {
public:
    vector<double> calcEquation(vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries) {
        unordered_map<string, pair<string, double>> parents;
        
        for (int i=0; i<equations.size(); i++) {
            const string& A = equations[i].first;
            const string& B = equations[i].second;
            const double k = values[i];
            
            if (parents.count(A) && parents.count(B)) {
                auto& rA = find(A, parents);
                auto& rB = find(B, parents);
                parents[rA.first] = {rB.first, k * rB.second / rA.second}; 
            } else if (parents.count(A)) {
                parents[B] = {A, 1.0 / k};
            } else if (parents.count(B)) {
                parents[A] = {B, k};
            } else {
                parents[A] = {B, k};
                parents[B] = {B, 1.0};
            }
        }
        
        vector<double> ans;
        for (const auto& query : queries) {
            const string& X = query.first;
            const string& Y = query.second;
            if (!parents.count(X) || !parents.count(Y)) {
                ans.push_back(-1.0);
                continue;
            }
            
            auto& rX = find(X, parents);
            auto& rY = find(Y, parents);
            if (rX.first != rY.first) {
                ans.push_back(-1.0);
            } else {
                ans.push_back(rX.second / rY.second);
            }
        }
        
        return ans;
    }
    
private:
    pair<string, double>& find(const string& X, unordered_map<string, pair<string, double>> &parents) {
        if (X != parents[X].first) {
            const auto& p = find(parents[X].first, parents);
            parents[X].first = p.first;
            parents[X].second *= p.second;
        }
        return parents[X];
    }
};
```



## 2. LeetCode 547 [Friend Circles](https://leetcode.com/problems/friend-circles/)

```c++
class UnionFindSet {
public:
    UnionFindSet(int n) {
        _parent = vector<int>(n+1, 0);
        _rank = vector<int>(n+1, 0);
        
        for (int i=1; i<=n; i++) {
            _parent[i] = i;
        }
    }
    
    bool Union(int x, int y) {
        int rx = Find(x);
        int ry = Find(y);
        
        if (rx == ry) return false;
        
        if (_rank[rx] > _rank[ry]) {
            _parent[ry] = rx;
        } else {
            _parent[rx] = ry;
            if (_rank[rx] == _rank[ry]) {
                _rank[ry]++;
            }
        }
        
        return true;
    }
    
    int Find(int x) {
        if (_parent[x] != x) {
            _parent[x] = Find(_parent[x]);
        }
        return _parent[x];
    }
    
private:
    vector<int> _parent;
    vector<int> _rank;
};

class Solution {
public:
    int findCircleNum(vector<vector<int>>& M) {
        int n = M.size();
        UnionFindSet nfs(n);
        
        for (int i=0; i<n; i++) {
            for (int j=i+1; j<n; j++) {
                if (M[i][j] == 1) {
                    nfs.Union(i+1, j+1);
                }
            }
        }
        
        unordered_set<int> circles;
        for (int i=1; i<=n; i++) {
            circles.insert(nfs.Find(i));
        }
        
        return circles.size();
    }
};
```



## 3. LeetCode 684 [Redundant Connection](https://leetcode.com/problems/redundant-connection/)

```c++
class UnionFindSet {
public:
    UnionFindSet(int n) {
        _parent = vector<int>(n+1, 0);
        _rank = vector<int>(n+1, 0);
        
        for (int i=1; i<=n; i++) {
            _parent[i] = i;
        }
    }
    
    bool Union(int x, int y) {
        int rx = Find(x);
        int ry = Find(y);
        
        if (rx == ry) return false;
        if (_rank[rx] > _rank[ry]) {
            _parent[ry] = rx;
        } else {
            _parent[rx] = ry;
            if (_rank[rx] == _rank[ry]) {
                _rank[ry]++;
            }
        }
        
        return true;
    }
    
    int Find(int x) {
        if (_parent[x] != x) {
            _parent[x] = Find(_parent[x]);
        }
        return _parent[x];
    }
    
private:
    vector<int> _parent;
    vector<int> _rank;
};

class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = edges.size();
        UnionFindSet ufs(n);
        vector<int> ans;
        
        for (int i=0; i<n; i++) {
            if (!ufs.Union(edges[i][0], edges[i][1])) {
                ans = {edges[i][0], edges[i][1]};
                break;
            }
        }
        
        return ans;
    }
};
```



## 4. LeetCode 685 [Redundant Connection II](https://leetcode.com/problems/redundant-connection-ii/)

```c++
class Solution {
    vector<int> parent;
public:
    vector<int> findRedundantDirectedConnection(vector<vector<int>>& edges) {
        parent = vector<int>(edges.size()+1, 0);
        vector<int> doubleParentA, doubleParentB;
        
        for(vector<int> &edge : edges) {
            if(!parent[edge[1]])
                parent[edge[1]] = edge[0];
            else {
                //two parent
                doubleParentA = {parent[edge[1]], edge[1]};
                doubleParentB = edge;
                edge[1] = 0;
            }
        }
        
        for(int i=0; i<parent.size(); i++) parent[i] = i;
        
        for(vector<int> &edge : edges) {
            int a = edge[0];
            int b = edge[1];
            int v = find(a);
            if(b == 0) continue;
            if(v == b) {
                if(doubleParentA.empty()) {
                    return edge;
                }
                else return doubleParentA;
            }
            parent[b] = v;
        }
        return doubleParentB;
    }
    
    int find(int x) {
        int root = parent[x];
        if(x != root) {
            root = find(root);
        }
        return root;
    }
};
```

[solution](https://leetcode.com/problems/redundant-connection-ii/discuss/108045/C%2B%2BJava-Union-Find-with-explanation-O(n))



## 5. LeetCode 952 [Largest Component Size by Common Factor](https://leetcode.com/problems/largest-component-size-by-common-factor/)

[youtube](https://www.youtube.com/watch?v=GTX0kw63Tn0)

### Intuition

Union each number with all its factor;

Count the most frequent parent.

[max_element](http://www.cplusplus.com/reference/algorithm/max_element/?kw=max_element)

```c++
// W: the largest number
// Time Complexity: O(n*sqrt(W))
// Space Complexity: O(W)

class UnionFindSet {
public:
    UnionFindSet(int n) : _parent(n) {
        for (int i=0; i<n; i++) {
            _parent[i] = i;
        }
    }
    
    void Union(int x, int y) {
        _parent[Find(x)] = _parent[Find(y)];
    }
    
    int Find(int x) {
        if (_parent[x] != x) {
            _parent[x] = Find(_parent[x]);
        }
        return _parent[x];
    }
    
private:
    vector<int> _parent;
};

class Solution {
public:
    int largestComponentSize(vector<int>& A) {
        int n = *max_element(A.begin(), A.end());
        UnionFindSet ufs(n+1);
        for (int &a : A) {
            for (int k = 2; k <= sqrt(a); k++) {
                if (a % k == 0) {
                    ufs.Union(a, k);
                    ufs.Union(a, a / k);
                }
            }
        }
        
        unordered_map<int, int> mp;
        int ans = 1;
        for (int &a : A) {
            ans = max(ans, ++mp[ufs.Find(a)]);
        }
        return ans;
    }
};
```



## 6. LeetCode 737 

```c++

```



## 7. LeetCode 200 [Number of Islands](https://leetcode.com/problems/number-of-islands/)

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

class UnionFindSet {
public:
    UnionFindSet(vector<vector<char>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        _parents.resize(m*n);
        _ranks.resize(m*n);
        count = 0;
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (grid[i][j] == '1') {
                    _parents[i*n+j] = i * n + j;
                    count++;
                } else {
                    _parents[i*n+j] = -1;
                }
            }
        }
    }
    
    bool Union(int x, int y) {
        int rx = Find(x);
        int ry = Find(y);
        if (rx == ry) {
            return false;
        }
        
        count--;
        if (_ranks[rx] > _ranks[ry]) {
            _parents[ry] = rx;
        } else {
            _parents[rx] = ry;
            if (_ranks[rx] == _ranks[ry]) {
                _ranks[ry]++;
            }
        }
        
        return true;
    }
    
    int Find(int x) {
        if (_parents[x] != x) {
            _parents[x] = Find(_parents[x]);
        }
        return _parents[x];
    }
    
    int getCount() const {
        return count;
    }
    
private:
    vector<int> _parents;
    vector<int> _ranks;
    int count; // number of connected component
};

class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        if (grid.empty() || grid[0].empty()) return 0;
        int m = grid.size();
        int n = grid[0].size();
        
        UnionFindSet ufs(grid);
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (grid[i][j] == '1') {
                    grid[i][j] = '0';
                    if (i - 1 >= 0 && grid[i-1][j] == '1') {
                        ufs.Union(i*n+j, (i-1)*n+j);
                    }
                    if (i + 1 < m && grid[i+1][j] == '1') {
                        ufs.Union(i*n+j, (i+1)*n+j);
                    }
                    if (j - 1 >= 0 && grid[i][j-1] == '1') {
                        ufs.Union(i*n+j, i*n+j-1);
                    }
                    if (j + 1 < n && grid[i][j+1] == '1') {
                        ufs.Union(i*n+j, i*n+j+1);
                    }
                }
            }
        }
        return ufs.getCount();
    }
};
```

