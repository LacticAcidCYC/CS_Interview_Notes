## 1. LeetCode 230 [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

### BST

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        int cnt = countNodes(root->left);
        if (k <= cnt) return kthSmallest(root->left, k);
        else if (k > cnt + 1) return kthSmallest(root->right, k-1-cnt);
        else return root->val;
    }
    
    int countNodes(TreeNode* root) {
        if (root == NULL) return 0;
        return 1 + countNodes(root->left) + countNodes(root->right);
    }
};
```

[dc-and-follow-up](https://leetcode.com/problems/kth-smallest-element-in-a-bst/discuss/63743/Java-divide-and-conquer-solution-considering-augmenting-tree-structure-for-the-follow-up)

[another-followup-post](https://leetcode.com/problems/kth-smallest-element-in-a-bst/discuss/63659/What-if-you-could-modify-the-BST-node's-structure)

[other-solutions](https://leetcode.com/problems/kth-smallest-element-in-a-bst/discuss/63734/O(k)-space-O(n)-time-10%2B-short-lines-3-solutions)



## 2. LeetCode 112 [Path Sum](https://leetcode.com/problems/path-sum/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(1) ?

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if (!root) return false;
        if (!root->left && !root->right && root->val == sum) return true;
        return hasPathSum(root->left, sum-root->val) || hasPathSum(root->right, sum-root->val);
    }
};
```



## 3. LeetCode 113 [Path Sum II](https://leetcode.com/problems/path-sum-ii/)

DFS:

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> paths;
        vector<int> path;
        dfs(paths, path, sum, root);
        return paths;
    }
    
    void dfs(vector<vector<int>> &paths, vector<int> &path, int sum, TreeNode* root) {
        if (!root) return;
        if (!root->left && !root->right && root->val == sum) {
            path.push_back(root->val);
            paths.push_back(path);
            path.pop_back();
            return;
        }
        
        path.push_back(root->val);
        dfs(paths, path, sum-root->val, root->left);
        dfs(paths, path, sum-root->val, root->right);
        path.pop_back();
    }
};

// former version
class Solution {
public:
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> paths;
        vector<int> path;
        findPath(root, sum, paths, path);
        return paths;
    }
    
    void findPath(TreeNode* node, int sum, vector<vector<int>>& paths, vector<int>& path) {
        if(node == NULL) return;
        path.push_back(node->val);
        if(node->left == NULL && node->right == NULL && sum == node->val) {
            paths.push_back(path);
        }
        findPath(node->left, sum - node->val, paths, path);
        findPath(node->right, sum - node->val, paths, path);
        path.pop_back();
    }
};
```



## 4. LeetCode 437 [Path Sum III](https://leetcode.com/problems/path-sum-iii/)

### (1) Recursive (DFS)

```c++
// Time Complexity: O(n^2)
// T(n) = T(n-1) + O(n) (worse case) => O(n^2)
// T(n) = 2T(n/2) + O(n) (best case) => O(nlogn)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int pathSum(TreeNode* root, int sum) {
        if (!root) return 0;
        return pathSum(root->left, sum) + pathSum(root->right, sum)
            + helper(root, sum);
    }
    
    int helper(TreeNode* root, int sum) {
        if (!root) return 0;
        return (root->val == sum) + helper(root->left, sum-root->val) 
            + helper(root->right, sum-root->val);
    }
};
```



### (2) Prefix Sum + HashMap

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int pathSum(TreeNode* root, int sum) {
        unordered_map<int, int> mp;
        mp[0]++;
        int cnt = 0;
        int curSum = 0;
        dfs(root, mp, sum, curSum, cnt);
        return cnt;
    }
    
    void dfs(TreeNode* root, unordered_map<int, int> &mp, int sum, int &curSum, int &cnt) {
        if (!root) return;
        
        curSum += root->val;
        cnt += mp[curSum - sum];
        mp[curSum]++;
        
        dfs(root->left, mp, sum, curSum, cnt);
        dfs(root->right, mp, sum, curSum, cnt);
        
        mp[curSum]--; // mp decrease first! correspond to the order above
        curSum -= root->val;
    }
};
```

[solution](https://leetcode.com/problems/path-sum-iii/discuss/91878/17-ms-O(n)-java-Prefix-sum-method)



## 5. LeetCode 173 [Binary Search Tree Iterator](https://leetcode.com/problems/binary-search-tree-iterator/)

### Inorder Traversal

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



## 6. LeetCode [Path Sum IV](https://leetcode.com/problems/path-sum-iv/)

### (1) Convert to Tree + DFS

[solution](https://leetcode.com/articles/path-sum-iv/)



### (2) Direct Traversal

#### Use Integer / 10 as node's identifier

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int pathSum(vector<int>& nums) {
        if (nums.empty()) return 0;
        unordered_map<int, int> tree;
        int sum = 0;
        int path_sum = 0;
        int root = nums[0] / 10;
        
        for (const auto &i : nums) {
            tree[i / 10] = i % 10;
        }
        
        dfs(tree, root, sum, path_sum);
        
        return sum;
    }
    
    void dfs(unordered_map<int, int> &tree, int root, int &sum, int &path_sum) {
        if (!tree.count(root)) {
            return;
        }
        
        path_sum += tree[root];
        int depth = root / 10;
        int pos = root % 10;
        int left = 10 * (depth + 1) + 2 * pos - 1;
        int right = left + 1;
        
        if (!tree.count(left) && !tree.count(right)) {
            sum += path_sum;
            return;
        }
        
        dfs(tree, left, sum, path_sum);
        dfs(tree, right, sum, path_sum);
        
        path_sum -= tree[root];
    }
};
```



## 7. LeetCode 687 [Longest Univalue Path](https://leetcode.com/problems/longest-univalue-path/)

### Explanation

There are three possible situation to get the path:

(1) from left subtree;

(2) from right subtree;

(3) the path contains the root;

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

// original
class Solution {
public:
    int longestUnivaluePath(TreeNode* root) {
        if (!root) return 0;
        return max(max(longestUnivaluePath(root->right), longestUnivaluePath(root->left))
                  , helper(root->left, root->val) + helper(root->right, root->val));
    }
    
    int helper(TreeNode* root, int val) {
        if (!root || root->val != val) return 0;
        // the path cannot be splitted!
        return 1 + max(helper(root->left, root->val), helper(root->right, root->val));
    }
};
```



### O(n)

#### Bottom-Up Update

[solution](https://leetcode.com/problems/longest-univalue-path/discuss/130315/Java-Solution-With-Explanation)

[solution2](https://leetcode.com/problems/longest-univalue-path/discuss/108136/JavaC%2B%2B-Clean-Code)

**Idea**
What we need is a longest path with same number and this path could pass through any node. So we must visit each node and workout a logic to know the longest path at that node, while we are at the given node, we keep updating max known path so far.

**Visiting each node**
this is just tree traversal

**Logic for longest path at the given node**
Two parts here
One is to update maxLength at this node. There are three possibilites:

1. longest path is in the left subtree but doesn't connect to me
2. longest path is in the right subtree but doesn't connect to me
3. longest path goes through me

All of these are captured by **l+r** in the code.

**Second part is what we need to return to our caller/parent node**
Since this is a simple path problem, for the caller we must tell which path we are choosing from 1 & 2 above. Why? Because path cannot be in Y shape. So we choose the max in left and right paths. But remember the whole path has to carry the same value - so now make use of the parent value to compare our value, if yes add 1 to the max chosen in the previous statement or return 0 (why? As this node cut the streak of longest path with same value)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int longestUnivaluePath(TreeNode* root) {
        if (!root) return 0;
        int len = 0;
        dfs(root, root->val, len);
        return len;
    }
    
    // here we need to use the return value of the dfs function
    int dfs(TreeNode* root, int val, int &len) {
        if (!root) return 0;
        
        // get the length from left and right substree including the root
        int left_len = dfs(root->left, root->val, len);
        int right_len = dfs(root->right, root->val, len);
        
        // combine this two part and update the result
        len = max(len, left_len + right_len);
        
        // if current val is equal to its parent's val, return the result to its parent loop
        if (root->val == val) {
            return 1 + max(left_len, right_len);
        }
        return 0;
    }
};
```



## 8. LeetCode 124 [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)

### Similar to LeetCode 687

update the maxPath bottom-up

There are three possibilites:

1. Largest path is in the left subtree but doesn't connect to root
2. Largest path is in the right subtree but doesn't connect to root
3. Largest path goes through root

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int maxPathSum(TreeNode* root) {
        if (!root) return 0;
        int max_val = INT_MIN;
        dfs(root, max_val);
        return max_val;
    }
    
    int dfs(TreeNode* root, int &max_val) {
        if (!root) return 0;
        // if left_max or right_max < 0, set it to 0! (which means cutting this half)
        int left_max = max(0, dfs(root->left, max_val));
        int right_max = max(0, dfs(root->right, max_val));
        max_val = max(max_val, left_max+right_max+root->val);
        return root->val + max(left_max, right_max);
    }
};
```

[solution](https://leetcode.com/problems/binary-tree-maximum-path-sum/discuss/39775/Accepted-short-solution-in-Java)



## 9. LeetCode 129 [Sum Root to Leaf Numbers](https://leetcode.com/problems/sum-root-to-leaf-numbers/)

### Similar to LeetCode 113

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int sumNumbers(TreeNode* root) {
        if (!root) return 0;
        int sum = 0;
        int pathNum = 0;
        dfs(root, sum, pathNum);
        return sum;
    }
    
    void dfs(TreeNode* root, int &sum, int& pathNum) {
        if (!root) return;
        
        pathNum = 10 * pathNum + root->val;
        if (!root->left && !root->right) {
            sum += pathNum;
            pathNum /= 10;
            return;
        }
        
        dfs(root->left, sum, pathNum);
        dfs(root->right, sum, pathNum);
        pathNum /= 10;
    }
};
```



## 10. LeetCode 144 [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/) | LeetCode 94 [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/) | LeetCode 145 [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)

### Binary Tree Traversal

[summary](https://leetcode.com/problems/binary-tree-postorder-traversal/discuss/45551/Preorder-Inorder-and-Postorder-Iteratively-Summarization)

[geeksforgeeks](https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/)

#### (1) Preorder (BFS)

Processes the root before the traversals of left and right children.

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        if (!root) return {};
        vector<int> pre;
        stack<TreeNode*> st;
        TreeNode* it = root;
        
        while (!st.empty() || it != NULL) {
            if (it != NULL) {
                st.push(it);
                pre.push_back(it->val);
                it = it->left;
            } else {
                it = st.top()->right;
                st.pop();
            }
        }
        
        return pre;
    }
};
```

DFS:

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        if (!root) return {};
        vector<int> pre;
        traverse(root, pre);
        return pre;
    }
    
    void traverse(TreeNode* root, vector<int> &pre) {
        if (!root) return;
        
        pre.push_back(root->val);
        traverse(root->left, pre);
        traverse(root->right, pre);
    }
};
```





#### (2) Inorder (BFS)

Processes the root after the traversal of left child and before the traversal of right child.

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> in;
        if (!root) return in;
        stack<TreeNode*> st;
        TreeNode* it = root;
        
        while (!st.empty() || it != NULL) {
            if (it != NULL) {
                st.push(it);
                it = it->left;
            } else {
                TreeNode* tmp = st.top();
                st.pop();
                in.push_back(tmp->val);
                it = tmp->right;
            }
        }
        
        return in;
    }
};
```

DFS:

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        if (!root) return {};
        vector<int> in;
        traverse(root, in);
        return in;
    }
    
    void traverse(TreeNode* root, vector<int> &in) {
        if (!root) return;
        
        traverse(root->left, in);
        in.push_back(root->val);
        traverse(root->right, in);
    }
};
```



#### (3) Postorder (BFS)

Processes the root after the traversals of left and right children.

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        if (!root) return {};
        vector<int> post;
        stack<TreeNode*> st;
        TreeNode* it = root;
        
        while (!st.empty() || it != NULL) {
            if (it != NULL) {
                st.push(it);
                post.push_back(it->val);
                it = it->right;
            } else {
                it = st.top()->left;
                st.pop();
            }
        }
        reverse(post.begin(), post.end());
        return post;
    }
};
```

DFS:

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        if (!root) return {};
        vector<int> post;
        traverse(root, post);
        return post;
    }
    
    void traverse(TreeNode* root, vector<int> &post) {
        if (!root) return;
        
        traverse(root->left, post);
        traverse(root->right, post);
        post.push_back(root->val);
    }
};
```



## 11. LeetCode 99 [Recover Binary Search Tree](https://leetcode.com/problems/recover-binary-search-tree/)

### Inorder Traversal and Find the Decreasing numbers (1 or 2)

After Inorder traversal, the numbe should have been in ascending order.

e.g. 

1,3,2,4

3,2,1

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    void recoverTree(TreeNode* root) {
        TreeNode *first = NULL, *second = NULL;
        TreeNode *prev = new TreeNode(INT_MIN);
        
        inorder_dfs(root, first, second, prev);
        
        swap(first->val, second->val);
    }
    
    void inorder_dfs(TreeNode* root, TreeNode* &first, TreeNode* &second, TreeNode* &prev) {
        if (!root) return;
        
        inorder_dfs(root->left, first, second, prev);
        
        if (prev->val > root->val) {
            if (!first) {
                // There may be just one time current elment smaller than prev one!!
                first = prev;
                second = root;
            } else {
                second = root;
                return;
            }
        }
        
        /*
        if (!first && prev->val > root->val) {
            first = prev;
        }
        if (first && prev->val > root->val) {
            second = root;
        }*/
        
        prev = root;
        
        inorder_dfs(root->right, first, second, prev);
    }
};
```

[solution](https://leetcode.com/problems/recover-binary-search-tree/discuss/32535/No-Fancy-Algorithm-just-Simple-and-Powerful-In-Order-Traversal)



## 12. LeetCode 103 [Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        if (!root) return {};
        vector<vector<int>> ans;
        
        queue<pair<TreeNode*, int>> q;
        q.push({root, 1});
        
        while (!q.empty()) {
            auto top = q.front();
            q.pop();
            
            if (ans.size() < top.second) {
                ans.push_back({});
                if (top.second > 1 && top.second % 2) {
                    reverse(ans[top.second-2].begin(), ans[top.second-2].end());
                }
            }
            ans[top.second-1].push_back(top.first->val);
            
            if (top.first->left) {
                q.push({top.first->left, top.second+1});
            }
            if (top.first->right) {
                q.push({top.first->right, top.second+1});
            }
        }
        
        if (ans.size() % 2 == 0) {
            reverse(ans[ans.size()-1].begin(), ans[ans.size()-1].end());
        }
        
        return ans;
    }
};

// improved version
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        if (!root) return {};
        vector<vector<int>> orders;
        vector<int> level;
        queue<TreeNode*> q;
        q.push(root);
        int depth = 0;
        
        while (!q.empty()) {
            int level_num = q.size();
            level.clear();
            for (int i=0; i<level_num; i++) {
                TreeNode* cur = q.front();
                q.pop();
                level.push_back(cur->val);
                if (cur->left) {
                    q.push(cur->left);
                }
                if (cur->right) {
                    q.push(cur->right);
                }
            }
            
            if (depth++ % 2) {
                reverse(level.begin(), level.end());
            }
            orders.push_back(level);
        }
        
        return orders;
    }
};
```





















































