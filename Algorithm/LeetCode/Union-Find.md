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

```c++
class Solution {
public:
    vector<double> calcEquation(vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries) {
        unordered_map<string, Node*> mp; // <string, corresponding Node>
        vector<double> res;
        
        int n = equations.size();
        for (int i=0; i<n; i++) {
            auto s1 = equations[i].first;
            auto s2 = equations[i].second;
            
            if (mp.count(s1) && mp.count(s2)) {
                unionNode(mp[s1], mp[s2], values[i], mp);
            } else if (mp.count(s1)) {
                mp[s2] = new Node(mp[s1]->val / values[i]);
                mp[s2]->parent = mp[s1];
            } else if (mp.count(s2)) {
                mp[s1] = new Node(mp[s2]->val * values[i]);
                mp[s1]->parent = mp[s2];
            } else {
                mp[s1] = new Node(values[i]);
                mp[s2] = new Node(1);
                mp[s1]->parent = mp[s2];
            }
        }
        
        for (auto &q : queries) {
            if (!mp.count(q.first) || !mp.count(q.second) || findParent(mp[q.first]) != findParent(mp[q.second])) {
                res.push_back(-1);
            } else {
                res.push_back(mp[q.first]->val / mp[q.second]->val);
            }
        }
        
        return res;
    }
    
private:
    struct Node {
        Node* parent;
        double val = 0.0;
        Node(double x) : val(x), parent(this) {}
    };
    
    void unionNode(Node* n1, Node* n2, double value, unordered_map<string, Node*> &mp) {
        Node *p1 = findParent(n1), *p2 = findParent(n2);
        if (p1 != p2) {
            double radio = value * n2->val / n1->val;
            for (auto &pair : mp) {
                if (pair.second->parent == p1) {
                    pair.second->val *= radio;
                }
            }
            p1->parent = p2;
        }
    }
    
    Node* findParent(Node* node) {
        if (node->parent != node) {
            node->parent = findParent(node->parent);
        }
        return node->parent;
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

