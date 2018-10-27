## 1. LeetCode 230 [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

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
// Space Complexity: O(logn) ?

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



## 4. LeetCode 173 [Binary Search Tree Iterator](https://leetcode.com/problems/binary-search-tree-iterator/)

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















