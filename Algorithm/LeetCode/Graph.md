# Topological Sort

## 1. LeetCode 207 [Course Schedule](https://leetcode.com/problems/course-schedule/)

[solution](https://leetcode.com/problems/course-schedule/discuss/58509/18-22-lines-C%2B%2B-BFSDFS-Solutions)

### (1) DFS

```c++
class Solution {
public:
    bool canFinish(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<vector<int>> graph = make_graph(numCourses, prerequisites);
        vector<int> visited(numCourses, 0); // 0:unvisited; -1:visiting; 1:visited
        for (int i=0; i<numCourses; i++) {
            if (dfs_find_cycle(graph, visited, i)) {
                return false;
            }
        }
        
        return true;
    }

private:
    vector<vector<int>> make_graph(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<vector<int>> graph(numCourses);
        for (const auto &pre : prerequisites) {
            graph[pre.second].push_back(pre.first);
        }
        return graph;
    }
    
    bool dfs_find_cycle(vector<vector<int>> &graph, vector<int> &visited, int course) {
        if (visited[course] == 1) return false;
        if (visited[course] == -1) return true;
        visited[course] = -1; // intermediate status
        for (const auto &neigh: graph[course]) {
            if (dfs_find_cycle(graph, visited, neigh)) {
                return true;
            }
        }
        
        visited[course] = 1;
        return false;
    }
};
```



### (2) BFS

```c++
class Solution {
public:
    bool canFinish(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<vector<int>> graph = make_graph(numCourses, prerequisites);
        vector<int> indegrees(numCourses);
        
        //compute indegree
        for (const auto &nexts : graph) {
            for (const auto &next : nexts) {
                indegrees[next]++;
            }
        }
        
        //total number of courses is numCourses, so the number of process to delete the edges is also numCourses
        for (int i=0; i<numCourses; i++) {
            int j = 0;
            for (; j<numCourses; j++) {
                if (indegrees[j] == 0) break;
            }
            if (j == numCourses) return false; // find a cycle
            indegrees[j] = -1;
            for (const auto &next : graph[j]) {
                indegrees[next]--;
            }
        }
        
        return true;
    }

private:
    vector<vector<int>> make_graph(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<vector<int>> graph(numCourses);
        for (const auto &pre : prerequisites) {
            graph[pre.second].push_back(pre.first);
        }
        return graph;
    }
};
```





## 2. LeetCode 210 [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)

### Almost same as LeetCode 207

### (1) BFS

```c++
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<int> orders;
        vector<vector<int>> graph = make_graph(numCourses, prerequisites);
        vector<int> indegrees(numCourses);
        
        //compute indegree
        for (const auto &nexts : graph) {
            for (const auto &next : nexts) {
                indegrees[next]++;
            }
        }
        
        //total number of courses is numCourses, so the number of process to delete the edges is also numCourses
        for (int i=0; i<numCourses; i++) {
            int j = 0;
            for (; j<numCourses; j++) {
                if (indegrees[j] == 0) break;
            }
            if (j == numCourses) return {}; // find a cycle
            orders.push_back(j);
            indegrees[j] = -1;
            for (const auto &next : graph[j]) {
                indegrees[next]--;
            }
        }
        
        return orders;
    }

private:
    vector<vector<int>> make_graph(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<vector<int>> graph(numCourses);
        for (const auto &pre : prerequisites) {
            graph[pre.second].push_back(pre.first);
        }
        return graph;
    }
};
```



### (2) DFS

```c++
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<int> orders;
        vector<vector<int>> graph = make_graph(numCourses, prerequisites);
        vector<int> visited(numCourses, 0); // 0:unvisited; -1:visiting; 1:visited
        for (int i=0; i<numCourses; i++) {
            if (dfs_find_cycle(graph, visited, i, orders)) {
                return {};
            }
        }
        reverse(orders.begin(), orders.end());
        return orders;
    }

private:
    vector<vector<int>> make_graph(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<vector<int>> graph(numCourses);
        for (const auto &pre : prerequisites) {
            graph[pre.second].push_back(pre.first);
        }
        return graph;
    }
    
    bool dfs_find_cycle(vector<vector<int>> &graph, vector<int> &visited, int course, vector<int> &orders) {
        if (visited[course] == 1) return false; // already visited before
        if (visited[course] == -1) return true; // is visiting in the dfs
        visited[course] = -1; // intermediate status
        for (const auto &neigh: graph[course]) {
            if (dfs_find_cycle(graph, visited, neigh, orders)) {
                return true;
            }
        }
        visited[course] = 1;
        orders.push_back(course);
        return false;
    }
};
```



## 3. LeetCode 269 [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/)

```c++
class Solution {
public:
    string alienOrder(vector<string>& words) {
        string res = "";
        if (words.empty()) return res;
        
        unordered_map<char, unordered_set<char>> graph;
        unordered_map<char, int> indegrees;
        unordered_set<char> alphabet;
        
        for (const string &word : words) {
            for (const char &c : word) {
                alphabet.insert(c);
            }
        }
        
        // make graph
        int n = words.size();
        for (int i=0; i<n-1; i++) {
            string s1 = words[i];
            string s2 = words[i+1];
            int ptr = 0;
            while (ptr < s1.size() && ptr < s2.size()) {
                if (s1[ptr] == s2[ptr]) {
                    ptr++;
                } else {
                    // avoid duplicate edges!!!
                    if (graph[s1[ptr]].insert(s2[ptr]).second) {
                        indegrees[s2[ptr]]++;
                    }
                    break;
                }
            }
        }
        
        // topological sort
        int l = alphabet.size();
        for (int i=0; i<l; i++) {
            auto it = alphabet.begin();
            for (; it != alphabet.end(); it++) {
                //cout << *it << ": " << indegrees[*it] << endl;
                if (indegrees[*it] == 0) {
                    //cout << *it << endl;
                    break;
                }
            }
            if (it == alphabet.end()) return "";
            res.push_back(*it);
            indegrees[*it] = -1;
            for (const char &c : graph[*it]) {
                indegrees[c]--;
            }
            alphabet.erase(it);
        }
        return res;
    }
};
```

[solution](https://leetcode.com/problems/alien-dictionary/discuss/70119/Java-AC-solution-using-BFS)



## 4. LeetCode 802 [Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/)

### Reversed Edges

```c++
// Time Complexity: O(E + V)
// Space Complexity: O(V)

class Solution {
public:
    vector<int> eventualSafeNodes(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<bool> safe(n);
        
        vector<unordered_set<int>> _graph;
        vector<unordered_set<int>> _rgraph;
        
        for (int i=0; i<n; ++i) {
            _graph.push_back({});
            _rgraph.push_back({});
        }
        
        queue<int> q;
        
        for (int i=0; i<n; ++i) {
            if (graph[i].empty()) {
                q.push(i);
            } 
            
            for (int j : graph[i]) {
                _graph[i].insert(j);
                _rgraph[j].insert(i);
            }
        }
        
        while (!q.empty()) {
            int j = q.front();
            q.pop();
            safe[j] = true;
            for (int i : _rgraph[j]) {
                _graph[i].erase(j);
                if (_graph[i].empty()) {
                    q.push(i);
                }
            }
        }
        
        vector<int> res;
        for (int i=0; i<n; ++i) {
            if (safe[i]) {
                res.push_back(i);
            }
        }
        
        return res;
    }
};
```



# DFS / BFS

## 1. LeetCode 133 [Clone Graph](https://leetcode.com/problems/clone-graph/)

### (1) DFS (with memorization)

[article](https://articles.leetcode.com/clone-graph-part-i/)

```c++
/**
 * Definition for undirected graph.
 * struct UndirectedGraphNode {
 *     int label;
 *     vector<UndirectedGraphNode *> neighbors;
 *     UndirectedGraphNode(int x) : label(x) {};
 * };
 */
class Solution {
public:
    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
        if (!node) return NULL;
        unordered_map<UndirectedGraphNode*, UndirectedGraphNode*> mp;
        return dfs_clone(node, mp);
    }
    
private:
    UndirectedGraphNode *dfs_clone(UndirectedGraphNode* node,
              unordered_map<UndirectedGraphNode*, UndirectedGraphNode*> &mp) {
        if (!node) return NULL;
        if (!mp.count(node)) {
            // must set mp[node] here!! otherwise it would cause
            // runtime error (cycle!)
            mp[node] = new UndirectedGraphNode(node->label);
            for (UndirectedGraphNode *neigh : node->neighbors) {
                mp[node]->neighbors.push_back(dfs_clone(neigh, mp));
            }
            // don't set mp[node] here
        }
        
        return mp[node];
    }
};
```

[solution](https://leetcode.com/problems/clone-graph/discuss/42313/7-17-lines-C%2B%2B-BFSDFS-Solutions)



### (2) BFS

```c++
/**
 * Definition for undirected graph.
 * struct UndirectedGraphNode {
 *     int label;
 *     vector<UndirectedGraphNode *> neighbors;
 *     UndirectedGraphNode(int x) : label(x) {};
 * };
 */
class Solution {
public:
    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
        if (!node) return NULL;
        unordered_map<UndirectedGraphNode*, UndirectedGraphNode*> mp;
        UndirectedGraphNode *copy = new UndirectedGraphNode(node->label);
        mp[node] = copy;
        queue<UndirectedGraphNode*> q;
        q.push(node);
        
        while (!q.empty()) {
            UndirectedGraphNode* cur = q.front();
            q.pop();
            
            for (const auto &neigh : cur->neighbors) {
                if (!mp.count(neigh)) {
                    mp[neigh] = new UndirectedGraphNode(neigh->label);
                    q.push(neigh);
                }
                mp[cur]->neighbors.push_back(mp[neigh]);
            }
        }
        
        return copy;
    }
};

// improved version with C++ 11
/**
 * Definition for undirected graph.
 * struct UndirectedGraphNode {
 *     int label;
 *     vector<UndirectedGraphNode *> neighbors;
 *     UndirectedGraphNode(int x) : label(x) {};
 * };
 */
class Solution {
public:
    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
        if (!node) return nullptr;
        
        unordered_map<UndirectedGraphNode*, UndirectedGraphNode*> node_mp;
        queue<UndirectedGraphNode*> q;
        node_mp.emplace(node, new UndirectedGraphNode(node->label));
        q.emplace(node);
        
        while (!q.empty()) {
            auto v = q.front();
            q.pop();
            
            for (UndirectedGraphNode* nb : v->neighbors) {
                // try to copy node nb
                if (!node_mp.count(nb)) {
                    node_mp.emplace(nb, new UndirectedGraphNode(nb->label));
                    q.emplace(nb);
                }
                // copy edge v->nb
                node_mp[v]->neighbors.emplace_back(node_mp[nb]);
            }
        }
        
        return node_mp[node];
    }
};
```



### Similar LeetCode 138

#### (1) HashMap

```c++

```



#### (2) Three times Traversal Copy

```c++

```



## 2. LeetCode 399 [Evaluate Division](https://leetcode.com/problems/evaluate-division/)

### (1) DFS

```c++
// E: number of edges(equations)
// Q: number of queries
// Time Complexity: O(E + E*Q)
// Space Complexity: O(E)

class Solution {
public:
    vector<double> calcEquation(vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries) {
        unordered_map<string, unordered_map<string, double>> graph;
        vector<double> res;
        
        for (int i=0; i<equations.size(); i++) {
            graph[equations[i].first][equations[i].second] = values[i];
            if (values[i] != 0) {
                graph[equations[i].second][equations[i].first] = 1 / values[i];
            }
        }
        
        for (auto p : queries) {
            unordered_set<string> memo; // used for visited avoiding cycle
            double tmp = dfs(p.first, p.second, graph, memo);
            res.push_back(tmp ? tmp : -1.0);
        }
        
        return res;
    }
    
private:
    double dfs(string s, string t, unordered_map<string, unordered_map<string, double>> &graph, unordered_set<string> &memo) {
        if (graph[s].count(t)) return graph[s][t];
        for (auto edge : graph[s]) {
            if (!memo.count(edge.first)) {
                memo.insert(edge.first);
                double tmp = dfs(edge.first, t, graph, memo);
                if (tmp) {
                    return edge.second * tmp;
                }
            }
        }
        
        return 0;
    }
};
```

[graph](https://leetcode.com/problems/evaluate-division/discuss/88168/c%2B%2B-0ms-Hash%2BDFS-solution)

[graph+dfs](https://leetcode.com/problems/evaluate-division/discuss/88169/Java-AC-Solution-using-graph)



### (2) Union-Find

After optimization: can reach O((n+m) * a(n))

[solution](https://leetcode.com/problems/evaluate-division/discuss/88343/Union-find-algorithm%3A-c%2B%2B-0ms-solution-(almost-linear-complexity))

[geeksforgeeks](https://www.geeksforgeeks.org/union-find-algorithm-set-2-union-by-rank/)

```c++
// N: number of equations; M: number of queries
// Time Complexity: O(N^2 + M)
// Space Complexity: O(N)

// path compression (can be optimized by using 'union by rank')
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

[union-find](https://leetcode.com/problems/evaluate-division/discuss/88170/0ms-C%2B%2B-Union-Find-Solution-EASY-to-UNDERSTAND)



### Improved Union-Find

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





## 3. LeetCode 785 [Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/)

### (1) DFS

`Our goal` is trying to use two colors to color the graph and see if there are any adjacent nodes having the same color.
Initialize a color[] array for each node. Here are three states for `colors[]` array:
`0: Haven't been colored yet.`
`1: Red.`
`-1: Blue.`
For each node,

1. If it hasn't been colored, use a color to color it. Then use the other color to color all its adjacent nodes (DFS).
2. If it has been colored, check if the current color is the same as the color that is going to be used to color it. 

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    bool isBipartite(vector<vector<int>>& graph) {
        int n = graph.size(); // number of nodes
        vector<int> colors(n, 0); // 0: uncolored 1: red -1: blue
        
        for (int i=0; i<n; i++) {
            if (!colors[i] && !dfs_isValid(colors, graph, 1, i)) {
                return false;
            }
        }
        
        return true;
    }
    
    bool dfs_isValid(vector<int> &colors, vector<vector<int>>& graph, int color, int node) {
        if (colors[node] != 0) {
            return colors[node] == color;
        }
        colors[node] = color;
        
        for (const auto &neigh : graph[node]) {
            if (!dfs_isValid(colors, graph, -color, neigh)) {
                return false;
            }
        }
        
        return true;
    }
};
```

[solution](https://leetcode.com/problems/is-graph-bipartite/discuss/115487/Java-Clean-DFS-solution-with-Explanation)



### (2) BFS

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    bool isBipartite(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<int> colors(n, 0); // 0: uncolored 1: red -1: blue
        
        for (int i=0; i<n; i++) {
            if (colors[i] == 0 && !graph[i].empty()) {
                colors[i] = 1;
                queue<int> q;
                q.push(i);
                while (!q.empty()) {
                    int cur = q.front();
                    q.pop();
                    
                    for (const auto &neigh : graph[cur]) {
                        if (!colors[neigh]) {
                            colors[neigh] = -colors[cur];
                            q.push(neigh);
                        } else {
                            if (colors[neigh] == colors[cur]) {
                                return false;
                            }
                        }
                    }
                }
            }
        }
        
        return true;
    }
};
```

[solution](https://leetcode.com/problems/is-graph-bipartite/discuss/115503/java-BFS)



## 4. LeetCode 797 [All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/)

### (1) Recursive

```c++
// Time Complexity: O(2^n*N^2)
// Space Complexity: O(2^n*N^2)

class Solution {
public:
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        return dfs(graph, 0);
    }
    
private:
    vector<vector<int>> dfs(vector<vector<int>>& graph, int node) {
        int n = graph.size();
        vector<vector<int>> ans;
        if (node == n - 1) {
            ans.push_back({n-1});
            return ans;
        }
        
        for (int neigh : graph[node]) {
            for (vector<int> path : dfs(graph, neigh)) {
                path.insert(path.begin(), node);
                ans.push_back(path);
            }
        }
        return ans;
    }
};
```



### (2) dfs-2

```c++
class Solution {
public:
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        vector<vector<int>> paths;
        vector<int> path;
        dfs(graph, paths, path, 0);
        
        return paths;
    }
    
private:
    void dfs(vector<vector<int>>& graph, vector<vector<int>>& paths, vector<int>& path, int node) {
        path.push_back(node);
        if (node == graph.size() - 1) {
            paths.push_back(path);
            path.pop_back();
            return;
        }
        
        for (auto neigh : graph[node]) {
            dfs(graph, paths, path, neigh);
        }
        path.pop_back();
    }
};
```



## 5. LeetCode 990 [Satisfiability of Equality Equations](https://leetcode.com/problems/satisfiability-of-equality-equations/)

### DFS / Union-Find

### (1) Union-Find

```c++
class Solution {
public:
    bool equationsPossible(vector<string>& equations) {
        vector<int> parents(26);
        
        for (int i = 0; i < 26; ++i) 
            parents[i] = i;
        
        for (string e : equations)
            if (e[1] == '=')
                parents[find(e[0] - 'a', parents)] = find(e[3] - 'a', parents);
        
        for (string e : equations)
            if (e[1] == '!' && find(e[0] - 'a', parents) == find(e[3] - 'a', parents))
                return false;
        return true;
    }

private:
    int find(int x, vector<int> &parents) {
        if (x != parents[x]) 
            parents[x] = find(parents[x], parents);
        return parents[x];
    }
};
```

































