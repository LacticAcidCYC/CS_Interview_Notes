## 1. LeetCode 230 [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

### BST

### (1) Binary Search (DFS)

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



### (2) Inorder-Traversal (DFS)

```c++

```





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



## 6. LeetCode 666 [Path Sum IV](https://leetcode.com/problems/path-sum-iv/)

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

#### (1) Preorder (Iterative)

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

#### Recursive

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





#### (2) Inorder (Iterative)

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

#### Recursive

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



#### (3) Postorder (Iterative)

Processes the root after the traversals of left and right children.

#### Normal

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
        TreeNode* last = NULL;
        
        while (!st.empty() || it) {
            if (it) {
                st.push(it);
                it = it->left;
            } else {
                TreeNode* tmp = st.top();
                // if current node has right child and right child has not been traversed
                if (tmp->right && last != tmp->right) {
                    it = tmp->right;
                } else {
                    post.push_back(tmp->val);
                    last = tmp;
                    st.pop();
                }
            }
        }
        
        return post;
    }
};
```



#### Preorder + reverse

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

#### Recursive

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



## 13. LeetCode 116 [Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

### (1) Iterative

kind of level order traversal (root->left->right->left.left->left.right....)

```c++
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if(!root || !root->left) {
            return;
        }
        TreeLinkNode *iter = NULL;
        while(root->left) {
            iter = root;
            iter->left->next = iter->right;
            while(iter->next) {
                iter->right->next = iter->next->left;
                iter = iter->next;
                iter->left->next = iter->right;
            }
            root = root->left;
        }
    }
};
```



### (2) Recursive

Connect left child and right child;

if next exists, connect right child with next left child; (connect left subtree and right subtree)

Recursively on left subtree and right sub then.

```c++
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (!root || !root->left) return;
        
        root->left->next = root->right;
        if (root->next) {
            root->right->next = root->next->left;
        }
        connect(root->left);
        connect(root->right);
    }
};
```





## 14. LeetCode 117 [Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/)

### (1) Iterative

```c++
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (!root) return;
        TreeLinkNode* cur = root;
        TreeLinkNode* next_itr = NULL;
        TreeLinkNode* next_head = NULL;
        
        while (cur) {
            while (cur) {
                if (cur->left) {
                    if (next_itr) {
                        next_itr->next = cur->left;
                    } else {
                        next_head = cur->left;
                    }
                    next_itr = cur->left;
                }
                
                if (cur->right) {
                    if (next_itr) {
                        next_itr->next = cur->right;
                    } else {
                        next_head = cur->right;
                    }
                    next_itr = cur->right;
                }
                cur = cur->next;
            }
            
            cur = next_head;
            next_itr = NULL;
            next_head = NULL;
        }
    }
};
```

[solution](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/discuss/37828/O(1)-space-O(n)-complexity-Iterative-Solution)



### (2) Recursive

```c++
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (!root) return;
        TreeLinkNode *head = new TreeLinkNode(0);
        TreeLinkNode *it = head;
        TreeLinkNode *cur = root;
        
        while (cur) {
            if (cur->left) {
                it->next = cur->left;
                it = it->next;
            }
            if (cur->right) {
                it->next = cur->right;
                it = it->next;
            }
            cur = cur->next;
        }
        
        connect(head->next);
    }
};
```



## 15. LeetCode 108 [Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

### Binary search

```c++
// Time Complexity: O(n)
// Space Complexity: O(logn)

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
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        if (nums.empty()) return NULL;
        return helper(nums, 0, nums.size()-1);
    }
    
    TreeNode* helper(vector<int>& nums, int start, int end) {
        if (start > end) return NULL;
        int mid = start + (end - start) / 2;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = helper(nums, start, mid-1);;
        root->right = helper(nums, mid+1, end);
        
        return root;
    }
};
```



## 16. LeetCode 235 [Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

### (1) Recursive

```c++
// Time Complexity: O(n) (worse case: a long tree like a list)
// Space Complexity: O(h)

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (p->val > q->val) {
            return lowestCommonAncestor(root, q, p);
        }
        
        if (root->val > q->val) {
            return lowestCommonAncestor(root->left, p, q);
        } else if (root->val < p->val) {
            return lowestCommonAncestor(root->right, p, q);
        } else {
            return root;
        }
    }
};
```



### (2) Iterative

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (p->val > q->val) {
            return lowestCommonAncestor(root, q, p);
        }
        
        TreeNode* cur = root;
        
        while (cur) {
            if (cur->val > q->val) {
                cur = cur->left;
            } else if (cur->val < p->val) {
                cur = cur->right;
            } else {
                return cur;
            }
        }
    }
};
```

[solution](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/discuss/64980/C%2B%2B-Recursive-and-Iterative)



## 17. LeetCode 236 [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

[geeksforgeeks](https://www.geeksforgeeks.org/lowest-common-ancestor-binary-tree-set-1/): idea of solution 2,3,4 below

### Idea

A brute-force approach is to see if the nodes are in different subtrees of the root, or if one of the nodes is the root. In this case, the root must be the LCA. If both nodes are in the same subtree, we recurse on that subtree. The time complexity will be O(n^2), where n is the number of the nodes. And the worse case is these two nodes are at the bottom of the tree.

For a better time complexity, we just want to do one-time pass on the tree. If two nodes are in a subtree, we want to directly calculate the LCA, instead of returning a boolean value indicating that both nodes are in that subtree. Thus, the helper function below returns a pair with two value--the first is an integer indicating how many of the two nodes are presented in that subtree, and the second is their LCA, if both of them are present.

The algorithm is structurally similar to a recursive postorder traversal, and the time complexity are the same, which are O(n) and O(h) (h is the height of the tree).

### (1) Recursive (similar to postorder traversal)

```c++
// Time Complexity: O(n)
// Space Complexity: O(logn)

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        return LCAHelper(root, p, q).second;
    }
    
    pair<int, TreeNode*> LCAHelper(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root) {
            return {0, NULL};
        }
        
        auto left_res = LCAHelper(root->left, p, q);
        if (left_res.first == 2) {
            return left_res;
        }
        
        auto right_res = LCAHelper(root->right, p, q);
        if (right_res.first == 2) {
            return right_res;
        }
        
        int numberOfNodes = left_res.first + right_res.first + (root == p) + (root == q);
        return {numberOfNodes, numberOfNodes == 2 ? root : NULL};
    }
};
```

[mypost](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/discuss/190223/c%2B%2B-recursive-solution-with-explanation)



### (2) Recursive (super short)

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root == p || root == q) return root;
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        return !left ? right : !right ? left : root;
    }
};
```

[solution](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/discuss/65225/4-lines-C%2B%2BJavaPythonRuby)



### (3) Iterative (iterative version of method (2))

```c++
class Solution {
    struct Frame {
        TreeNode* node;
        Frame* parent;
        vector<TreeNode*> subs;
    };
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        Frame answer;
        stack<Frame> stack;
        stack.push({root, &answer});
        while (stack.size()) {
            Frame *top = &stack.top(), *parent = top->parent;
            TreeNode *node = top->node;
            if (!node || node == p || node == q) {
                parent->subs.push_back(node);
                stack.pop();
            } else if (top->subs.empty()) {
                stack.push({node->right, top});
                stack.push({node->left, top});
            } else {
                TreeNode *left = top->subs[0], *right = top->subs[1];
                parent->subs.push_back(!left ? right : !right ? left : node);
                stack.pop();
            }
        }
        return answer.subs[0];
    }
};
```

[solution](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/discuss/65245/Iterative-Solutions-in-PythonC%2B%2B)



### (4) Iterative Post-order Traversal Solution

Use post-order traversal to traverse the path and find the path to node p and q, then compare the two paths to find the last matched number (node).

```c++
// Time Complexity: O(n)
// Space Complexity: O(logn)

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root == p || root == q) return root;
        
        TreeNode *it = root, *last = NULL;
        vector<TreeNode*> st; // use for stack and path storage
        vector<TreeNode*> path2p, path2q;
        
        while (path2p.empty() || path2q.empty()) {
            // post-order traversal
            if (it) {
                st.push_back(it);
                if (it == p) path2p = st;
                if (it == q) path2q = st;
                it = it->left;
            } else {
                TreeNode* tmp = st.back();
                if (tmp->right && tmp->right != last) {
                    it = tmp->right;
                } else {
                    last = tmp;
                    st.pop_back();
                }
            }
        }
        
        int n = min(path2p.size(), path2q.size());
        for (int i=0; i<n; i++) {
            if (path2p[i] != path2q[i]) {
                return path2p[i-1];
            }
        }
        
        return path2p[n-1];
    }
};
```



## 18. LeetCode 255 [Verify Preorder Sequence in Binary Search Tree](https://leetcode.com/problems/verify-preorder-sequence-in-binary-search-tree/)

### Explanation

Kinda simulate the traversal, keeping a stack of nodes (just their values) of which we're still in the left subtree. If the next number is smaller than the last stack value, then we're still in the left subtree of all stack nodes, so just push the new one onto the stack. But before that, pop all smaller ancestor values, as we must now be in their right subtrees (or even further, in the right subtree of an ancestor). Also, use the popped values as a lower bound, since being in their right subtree means we must never come across a smaller number anymore.

### (1) O(n) time and O(n) space

```c++
class Solution {
public:
    bool verifyPreorder(vector<int>& preorder) {
        stack<int> path;
        int lower_bound = INT_MIN;
        for (auto const &i : preorder) {
            if (i < lower_bound) {
                return false;
            }
            while (!path.empty() && i > path.top()) {
                lower_bound = path.top();
                path.pop();
            }
            path.push(i);
        }
        
        return true;
    }
};
```

[solution1](https://leetcode.com/problems/verify-preorder-sequence-in-binary-search-tree/discuss/68185/C%2B%2B-easy-to-understand-solution-with-thought-process-and-detailed-explanation)

[solution2](https://leetcode.com/problems/verify-preorder-sequence-in-binary-search-tree/discuss/68142/Java-O(n)-and-O(1)-extra-space)



### (2) O(1) space (abusing the given array)

```c++
class Solution {
public:
    bool verifyPreorder(vector<int>& preorder) {
        int lower_bound = INT_MIN;
        int index = -1;
        for (auto number : preorder) {
            if (number < lower_bound) {
                return false;
            }
            while (index >= 0 && number > preorder[index]) {
                lower_bound = preorder[index--];
            }
            preorder[++index] = number;
        }
        
        return true;
    }
};
```



## 19. LeetCode 270 [Closest Binary Search Tree Value](https://leetcode.com/problems/closest-binary-search-tree-value/)

### (1) Binary Search + iterative

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
    int closestValue(TreeNode* root, double target) {
        int upper_bound = root->val;
        int lower_bound = root->val;
        while (root) {
            if (root->val == target) return root->val;
            else if (root->val > target) {
                upper_bound = root->val;
                root = root->left;
            } else {
                lower_bound = root->val;
                root = root->right;
            }
        }
        return abs(target-upper_bound) > abs(target-lower_bound) ? lower_bound : upper_bound;
    }
};
```



### (2) Binary Search (shorter version)

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
    int closestValue(TreeNode* root, double target) {
        int closest = root->val;
        while (root) {
            if (abs(closest-target) >= abs(root->val-target)) {
                closest = root->val;
            }
            root = target < root->val ? root->left : root->right;
        }
        return closest;
    }
};
```



### (3) Recursive (fancy)

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
    int closestValue(TreeNode* root, double target) {
        auto a = root->val;
        auto child = target < a ? root->left : root->right;
        if (!child) return a;
        auto b = closestValue(child, target);
        return abs(target-a) >= abs(target-b) ? b : a;
    }
};
```





## 20. LeetCode 272 [Closest Binary Search Tree Value II](https://leetcode.com/problems/closest-binary-search-tree-value-ii/)

### (1) Using two stack + recursive

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
    vector<int> closestKValues(TreeNode* root, double target, int k) {
        vector<int> res;
        
        stack<int> sp; // predecessors
        stack<int> ss; // successors
        
        inorder_traverse(root, sp, false, target);
        inorder_traverse(root, ss, true, target);
        
        while (k--) {
            if (sp.empty()) {
                res.push_back(ss.top());
                ss.pop();
            } else if (ss.empty()) {
                res.push_back(sp.top());
                sp.pop();
            } else if (abs(ss.top() - target) < abs(sp.top() - target)) {
                res.push_back(ss.top());
                ss.pop();
            } else {
                res.push_back(sp.top());
                sp.pop();
            }
        }
        
        return res;
    }
    
    void inorder_traverse(TreeNode* root, stack<int> &st, bool reverse, double target) {
        if (!root) return;
        
        inorder_traverse(reverse ? root->right : root->left, st, reverse, target);
        if ((!reverse && root->val > target) || (reverse && root->val <= target)) return;
        st.push(root->val);
        inorder_traverse(reverse ? root->left : root->right, st, reverse, target);
    }
};
```

[solution](https://leetcode.com/problems/closest-binary-search-tree-value-ii/discuss/70511/AC-clean-Java-solution-using-two-stacks)



### (2) Using two stack + successor + predecessor

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
    vector<int> closestKValues(TreeNode* root, double target, int k) {
        vector<int> res;
        stack<TreeNode*> pre, suc;
        TreeNode* it = root;
        
        while (it) {
            if (target <= it->val) {
                suc.push(it);
                it = it->left;
            } else {
                pre.push(it);
                it = it->right;
            }
        }
        
        while (k--) {
            if (pre.empty() && suc.empty()) {
                break;
            } else if (pre.empty() || !suc.empty() && (suc.top()->val - target < target - pre.top()->val)) {
                res.push_back(getSuccessor(suc));
            } else {
                res.push_back(getPredecessor(pre));
            }
        }
        
        return res;
    }
    
    int getSuccessor(stack<TreeNode*> &suc) {
        TreeNode* top = suc.top();
        suc.pop();
        TreeNode* it = top->right;
        while (it) {
            suc.push(it);
            it = it->left;
        }
        return top->val;
    }
    
    int getPredecessor(stack<TreeNode*> &pre) {
        TreeNode* top = pre.top();
        pre.pop();
        TreeNode* it = top->left;
        while (it) {
            pre.push(it);
            it = it->right;
        }
        return top->val;
    }
    
};
```

[other solution](https://leetcode.com/problems/closest-binary-search-tree-value-ii/discuss/70499/Java-5ms-iterative-following-hint-O(klogn)-time-and-space)



### (3) Using deque

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
    vector<int> closestKValues(TreeNode* root, double target, int k) {
        deque<int> dq;
        
        inorder_traverse(root, dq, target);
        
        while (dq.size() > k) {
            if (abs(dq.front() - target) < abs(dq.back() - target)) {
                dq.pop_back();
            } else {
                dq.pop_front();
            }
        }
        
        return vector<int>(dq.begin(), dq.end());
    }
    
    void inorder_traverse(TreeNode* root, deque<int> &dq, double target) {
        if (!root) return;
        
        inorder_traverse(root->left, dq, target);
        dq.push_back(root->val);
        inorder_traverse(root->right, dq, target);
    }
};
```



## 21. LeetCode 109 [Convert Sorted List to Binary Search Tree](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/)

```c++
// Time Complexity: O(nlogn)
// Space Complexity: O(logn)

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
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
    TreeNode* sortedListToBST(ListNode* head) {
        if (!head) return NULL;
        return helper(head, NULL);
    }
    
    TreeNode* helper(ListNode* head, ListNode* tail) {
        if (head == tail) return NULL;
        ListNode* slow = head;
        ListNode* fast = head;
        
        while (fast != tail && fast->next != tail) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        TreeNode* root = new TreeNode(slow->val);
        root->left = helper(head, slow);
        root->right = helper(slow->next, tail);
        return root;
    }
};
```

[solution-java](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/discuss/35476/Share-my-JAVA-solution-1ms-very-short-and-concise.)

[solution-c++](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/discuss/35483/My-Accepted-C%2B%2B-solution)



## 22. LeetCode 199 [Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)

### BFS (level order traversal)

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
    vector<int> rightSideView(TreeNode* root) {
        vector<int> res;
        if (!root) return res;
        vector<int> level;
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            level.clear();
            int n = q.size();
            for (int i=0; i<n; i++) {
                auto node = q.front();
                q.pop();
                level.push_back(node->val);
                if (node->left) {
                    q.push(node->left);
                }
                if (node->right) {
                    q.push(node->right);
                }
            }
            res.push_back(level.back());
        }
        
        return res;
    }
};
```



## 23. LeetCode 545 [Boundary of Binary Tree](https://leetcode.com/problems/boundary-of-binary-tree/)

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
    vector<int> boundaryOfBinaryTree(TreeNode* root) {
        vector<int> res;
        if (!root) return res;
        if (!root->left && !root->right) return {root->val};
        
        // add root
        res.push_back(root->val);
        
        // find left-most
        getLeftBoundary(root->left, res);
        
        // find leaves
        getLeaves(root->left, res);
        getLeaves(root->right, res);
        
        // find right-most
        getRightBoundary(root->right, res);
        
        return res;
    }
    
    void getLeftBoundary(TreeNode* root, vector<int>& res) {
        if (!root || !root->left && !root->right) return;
        res.push_back(root->val);
        if (root->left) {
            getLeftBoundary(root->left, res);
        } else { 
            getLeftBoundary(root->right, res);
        }
    }
    
    void getRightBoundary(TreeNode* root, vector<int>& res) {
        if (!root || !root->left && !root->right) return;
        if (root->right) {
            getRightBoundary(root->right, res);
        } else {
            getRightBoundary(root->left, res);
        }
        res.push_back(root->val); // reverse order
    }
    
    void getLeaves(TreeNode* root, vector<int> &res) {
        if (!root) return;
        if (!root->left && !root->right) {
            res.push_back(root->val);
            return;
        }
        getLeaves(root->left, res);
        getLeaves(root->right, res);
    }
};
```

[solution](https://leetcode.com/problems/boundary-of-binary-tree/discuss/101280/Java(12ms)-left-boundary-left-leaves-right-leaves-right-boundary)



## 24. LeetCode 297 [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

### Design

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
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        ostringstream out;
        serializeHelper(root, out);
        return out.str();
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        istringstream in(data);
        return deserializeHelper(in);
    }
    
private:
    void serializeHelper(TreeNode* root, ostringstream &out) {
        if (!root) {
            out << "# ";
        } else {
            out << root->val << " ";
            serializeHelper(root->left, out);
            serializeHelper(root->right, out);
        }
    }
    
    TreeNode* deserializeHelper(istringstream &in) {
        string val;
        in >> val;
        if (val == "#") {
            return NULL;
        }
        TreeNode* root = new TreeNode(stoi(val));
        root->left = deserializeHelper(in);
        root->right = deserializeHelper(in);
        return root;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```

[solution](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/discuss/74259/Recursive-preorder-Python-and-C%2B%2B-O(n))



## 25. LeetCode 404 [Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves/)

### (1) My first solution

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
    int sumOfLeftLeaves(TreeNode* root) {
        if (!root) return 0;
        int sum = 0;
        helper(root, sum);
        return sum;
    }
    
    void helper(TreeNode* root, int &sum) {
        if (!root) return;
        if (!root->left) {
            helper(root->right, sum);
            return;
        }
        if (!root->left->left && !root->left->right) {
            sum += root->left->val;
            helper(root->right, sum);
            return;
        }
        helper(root->left, sum);
        helper(root->right, sum);
    }
};

// improved
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        if (!root) return 0;
        int sum = 0;
        helper(root, sum);
        return sum;
    }
    
    void helper(TreeNode* root, int &sum) {
        if (!root) return;
        if (root->left && !root->left->left && !root->left->right) {
            sum += root->left->val;
        } else {
            helper(root->left, sum);
        }
        helper(root->right, sum);
    }
};
```

[solution](https://leetcode.com/problems/sum-of-left-leaves/discuss/88950/Java-iterative-and-recursive-solutions)



### (2) recursive-2

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
    int sumOfLeftLeaves(TreeNode* root) {
        if (!root) return 0;
        int sum = 0;
        if (root->left && !root->left->left && !root->left->right) {
            sum += root->left->val;
        } else {
            sum += sumOfLeftLeaves(root->left);
        }
        sum += sumOfLeftLeaves(root->right);
        return sum;
    }
};
```



### (3) Iterative (BFS)

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
    int sumOfLeftLeaves(TreeNode* root) {
        if (!root) return 0;
        int sum = 0;
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            auto node = q.front();
            q.pop();
            if (node->left) {
                if (!node->left->left && !node->left->right) {
                    sum += node->left->val;
                } else {
                    q.push(node->left);
                }
            }
            if (node->right) {
                if (node->right->left || node->right->right) {
                    q.push(node->right);
                }
            }
        }
        return sum;
    }
};
```



## 26. LeetCode 938 [Range Sum of BST](https://leetcode.com/contest/weekly-contest-110/problems/range-sum-of-bst/)

### DFS

```c++
// Time Complexity: O(n)
// Space Complexity: O(h)

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
    int rangeSumBST(TreeNode* root, int L, int R) {
        if (!root) return 0;
        if (root->val < L) return rangeSumBST(root->right, L, R);
        if (root->val > R) return rangeSumBST(root->left, L, R);
        return root->val + rangeSumBST(root->left, L, root->val) + rangeSumBST(root->right, root->val, R);
    }
};
```





## 27. LeetCode 298

```c++

```



## 28. LeetCode 549

```c++

```

























