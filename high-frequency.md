## 1. LeetCode 48 [Rotate Image](https://leetcode.com/problems/rotate-image/)

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(1)

class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        for(int i=0; i<n; i++) {
            for(int j=i+1; j<n; j++) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }
        for(int i=0; i<n; i++) {
            for(int j=0; j<n/2; j++) {
                swap(matrix[i][j], matrix[i][n-1-j]);
            }
        }
    }
};
```





## 2. LeetCode 45 [Jump Game II](https://leetcode.com/problems/jump-game-ii/)

### -BFS

*change this problem to a BFS problem, where nodes in level i are all the nodes that can be reached in i-1th jump. for example.*

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size();
        if (n < 2) return 0;
        int curMax = 0, nextMax = 0, steps = 0; // steps: level-1 (depth)
        int i = 0;
        while (1) { // while (curMax >= i)
            steps++;
            while (i <= curMax) {
                nextMax = max(nextMax, nums[i]+i);
                if (nextMax >= n-1) return steps;
                i++;
            }
            curMax = nextMax;
        }
        
        return steps;
    }
};
```

[reference](https://leetcode.com/problems/jump-game-ii/discuss/18028/O(n)-BFS-solution)



### Related: LeetCode 55 [Jump Game](https://leetcode.com/problems/jump-game/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        int end = 0;
        for (int i=0; i<n; i++) {
            if (end >= n-1) return true;
            if (end < i) return false;
            end = max(end, nums[i] + i);
        }
        return true;
    }
};
```



## 3. LeetCode 347

- auto | auto& | auto&&

https://stackoverflow.com/questions/29859796/c-auto-vs-auto/29860056#29860056

- unordered_map

http://www.cplusplus.com/reference/unordered_map/unordered_map/?kw=unordered_map

- 运算符重载

https://zh.cppreference.com/w/cpp/language/operators

```c++
// n: amount of unique number
// Time Complexity: O(nlogk)
// Space Complexity: O(n)

class Solution {
public:
    
    struct compare {
        // 用户定义类重载(overload)函数调用运算符(function call operator)时 operator() 时，类成为函数对象 (FunctionObject) 类型
        bool operator() (const pair<int, int> &A, const pair<int, int> &B) const {
            return A.second > B.second; // great<>
        }
    };
    
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> map;
        // get frequency_map
        for (auto i : nums) {
            map[i]++;
        }
        
        // build a min-heap (size: k)
        priority_queue<pair<int, int>, vector<pair<int, int>>, compare> pq;
        int i = 0;
        for (auto const &p : map) {
            if (i >= k) {
                if (p.second > pq.top().second) {
                    pq.pop();
                    pq.push(p);
                }
            } else {
                pq.push(p);
            }
            i++;
        }
        
        // get the topKFrequent numbers
        vector<int> ans;
        while (!pq.empty()) {
            ans.push_back(pq.top().first);
            pq.pop();
        }
        
        return ans;
    }
};
```



## 4. LeetCode 91 [Decode Ways](https://leetcode.com/problems/decode-ways/)

- DP

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

// state transition equation
// dp[i]:  decode ways of i-length messages
// current char: s[i-1]
// dp[i] = dp[i-1] (when s[i-1] is not '0')
// dp[i] = dp[i-1] + dp[i-2] (when s[i-2]s[i-1] can become '10-26')

class Solution {
public:
    int numDecodings(string s) {
        if (s[0] == '0') return 0;
        int n = s.length();
        vector<int> dp(n+1, 0); // dp[i]: decode ways of i-length messages
        dp[0] = 1; // init according to the state transition
        dp[1] = 1; // obviously one
        for (int i=2; i<=n; i++) {
            if (s[i-1] != '0') {
                dp[i] += dp[i-1]; // can split before s[i-1](current element)
            }
            int last_two_digits = stoi(s.substr(i-2, 2));
            if (last_two_digits >= 10 && last_two_digits <= 26) {
                dp[i] += dp[i-2]; // can split before s[i-2]
            }
        }
        
        return dp[n];
    }
};
```





## 5. LeetCode 547 [Friend Circles](https://leetcode.com/problems/friend-circles/)

(1) backtrack (dfs)

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

class Solution {
public:
    int findCircleNum(vector<vector<int>>& M) {
        int n = M.size();
        vector<bool> visited(n, false); //memorization
        int cnt = 0;
        
        for (int i=0; i<n; i++) {
            if (!visited[i]) {
                cnt++; // circle cnt
                dfs(M, visited, i);
            }
        }
        return cnt;
    }
    
    void dfs(vector<vector<int>>& M, vector<bool> &visited, int i) {
        visited[i] = true;
        
        auto row = M[i];
        for (int j=0; j<row.size(); j++) {
            if (!visited[j] && row[j] == 1) {
                dfs(M, visited, j);
            }
        }
    }
};
```



(2) union-find

```c++
// Time complexity: O(n^3)
// Improved version's time complexity: O(n^2*a(n))? (a(n) < 5)
// Space complexity: O(n)

// friend circles (UnionFind)
// union by rank
class Solution {
public:
    int findCircleNum(vector<vector<int>>& M) {
        int n = M.size();
        vector<int> parent(n);
        vector<int> rank(n, 0);
        int cnt = n;
        
        for (int i=0; i<n; i++) {
            parent[i] = i;
        }
        
        for (int i=0; i<n; i++) {
            for (int j=i+1; j<n; j++) {
                if (M[i][j]) {
                    int pi = find(parent, i);
                    int pj = find(parent, j);
                    if (pi != pj) {
                        cnt--;
                        if (rank[pi] > rank[pj]) {
                            parent[pj] = pi;
                        } else {
                            parent[pi] = pj;
                            if (rank[pi] == rank[pj]) {
                                rank[pj]++;
                            }
                        }
                    }
                }
            }
        }
        
        return cnt;
    }
    
    int find(vector<int> &parent, int i) {
        // Path compression
        if (i != parent[i]) {
            parent[i] = find(parent, parent[i]);
        }
        return parent[i];
    }
};
```



## 6. LeetCode 173 [Binary Search Tree Iterator](https://leetcode.com/problems/binary-search-tree-iterator/)

- System design
- Stack

```latex
Implement an iterator over a binary search tree (BST). Your iterator will be initialized with the root node of a BST.

Calling next() will return the next smallest number in the BST.

Note: next() and hasNext() should run in average O(1) time and uses O(h) memory, where h is the height of the tree.
```

Solution:

```c++
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class BSTIterator {
public:
    BSTIterator(TreeNode *root) {
        pushNodes(root);
    }

    /** @return whether we have a next smallest number */
    bool hasNext() {
        return !bstStack.empty();
    }

    /** @return the next smallest number */
    int next() {
        if (hasNext()) {
            TreeNode* tmpNode = bstStack.top();
            bstStack.pop();
            pushNodes(tmpNode->right);
            return tmpNode->val;
        }
    }
    
private:
    stack<TreeNode*> bstStack;
    
    void pushNodes(TreeNode* node) {
        while (node != NULL) {
            bstStack.push(node);
            node = node->left;
        }
    }

};


/**
 * Your BSTIterator will be called like this:
 * BSTIterator i = BSTIterator(root);
 * while (i.hasNext()) cout << i.next();
 */
```



## 7. LeetCode 73 [Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)

```c++
// Time Complexity: O(mn)
// Space Complexity: O(1)

// use first row and first col to be labels
// use another col0 to label the first col

// It doesn't have to be reversed order as long as the first element of each row is the last one to be changed for that row & the first element of each column is the last one to be changed for that column, since they keep labels. So if we don't use such reversed order, we need another loop to change the first row/column.

class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        
        int col0 = 1;
        
        for (int i=0; i<m; i++) {
            if (matrix[i][0] == 0) col0 = 0;
            for (int j=1; j<n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }
        
        for (int i=m-1; i>=0; i--) {
            for (int j=n-1; j>=1; j--) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
            if (col0 == 0) {
                matrix[i][0] = 0;
            }
        }
    }
};
```

















