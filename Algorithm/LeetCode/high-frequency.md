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
                // don't use this: i+nums[i++] 
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



## 8. LeetCode 683 [K Empty Slots](https://leetcode.com/problems/k-empty-slots/)

### (1) Set (red-black tree)

```c++
// Time Complexiy: O(nlogn)
// Space Complexity: O(n)
class Solution {
public:
    int kEmptySlots(vector<int>& flowers, int k) {
        int len = flowers.size();
        if (k > len-2) return -1;
        set<int> s;
        // make sure the bound exists
        s.insert(0);
        s.insert(len+1);
        for (int i=0; i<len; i++) {
            auto iter = s.upper_bound(flowers[i]);
            if (*iter != len+1 && *iter - flowers[i] - 1 == k) {
                return i+1;
            }
            iter--;
            if (*iter != 0 && flowers[i] - *iter - 1 == k) {
                return i+1;
            }
            s.insert(flowers[i]);
        }
        return -1;
    }
};
```

[set-cpluscplus](http://www.cplusplus.com/reference/set/set/)



### (2) Using window

```c++
// Time Complexiy: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int kEmptySlots(vector<int>& flowers, int k) {
        int len = flowers.size();
        if (k > len-2) return -1;
        vector<int> days(len);
        for (int i=0; i<len; i++) {
            days[flowers[i]-1] = i+1;
        }
        
        int left = 0, right = k+1, i = 0, ans = INT_MAX;
        while (right < len) {
            if (days[i] < days[left] || days[i] <= days[right]) {
                if (i == right) {
                    ans = min(ans, max(days[left], days[right]));
                }
                left = i;
                right = i + k + 1;
            }
            i++;
        }
        
        return ans == INT_MAX ? -1 : ans;
    }
};
```

[solution](https://leetcode.com/problems/k-empty-slots/discuss/107931/JavaC%2B%2B-Simple-O(n)-solution)



## 9. LeetCode 722 [Remove Comments](https://leetcode.com/problems/remove-comments/)

Finite-state machine

### Explanation

We only need to check for two things:

1. If we see '//' we stop reading the current line, and add whatever characters we have seen to the result.
2. If we see '/*' then we start the multiline comment mode and we keep on ignoring characters until we see '*/'.
3. If the current character is neither of the above two and the multiline comment mode is off, then we add that character to the current line.

Once we parse one line (source[i]), then if the mode is off, we add the currently generated line (StringBuilder) to the result and repeat for source[i + 1].

**We need to be careful not to insert empty lines in the result.**

```c++
class Solution {
public:
    vector<string> removeComments(vector<string>& source) {
        int len = source.size();
        bool isComment = false;
        vector<string> ans;
        string code = "";
        
        for (int i=0; i<len; i++) {
            auto line = source[i];
            int len = line.length();
            for (int j=0; j<len; j++) {
                if (!isComment) {
                    if (j < len-1 && line[j] == '/' && line[j+1] == '/') {
                        break;
                    } else if (j < len-1 && line[j] == '/' && line[j+1] == '*') {
                        isComment = true;
                        j++;
                    } else {
                        code += line[j];
                    }
                } else {
                    if (j < len-1 && line[j] == '*' && line[j+1] == '/') {
                        isComment = false;
                        j++;
                    }
                }
            }
            
            if (!isComment && !code.empty()) {
                ans.push_back(code);
                code.clear();
            }
        }
        
        return ans;
    }
};
```

[reference](https://leetcode.com/problems/remove-comments/discuss/109197/One-pass-solution-in-Java)



## 10. LeetCode 187

-Set

### (1) set.insert() (or unordered_set)

```c++
(1)	pair<iterator,bool> insert ( const value_type& val );
(2)	pair<iterator,bool> insert ( value_type&& val );
```

### Return value

In versions (1) and (2), the function returns **a pair object** whose first element is **an iterator** pointing either to the newly inserted element in the container or to the element whose key is equivalent, and **a bool value** indicating whether the element was successfully inserted or not.

[cplusplus](http://www.cplusplus.com/reference/unordered_set/unordered_set/insert/)

[stackoverflow](https://stackoverflow.com/questions/5034211/c-copy-set-to-vector)



### (2) vector's range constructor

```c++
template <class InputIterator>
  vector (InputIterator first, InputIterator last,
          const allocator_type& alloc = allocator_type());

// e.g.
set<int> s;
vector<int> v(s.begin(), s.end());
```



### (3) Solution

```c++
// original
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        int len = s.length();
        if (len < 10) return vector<string>();
        unordered_set<string> seqs;
        unordered_set<string> dups;
        vector<string> ans;
        
        for (int i=0; i<=len-10; i++) {
            string cur_seq = s.substr(i, 10);
            if (!seqs.count(cur_seq)) {
                seqs.insert(cur_seq);
            } else {
                if (!dups.count(cur_seq)) {
                    ans.push_back(cur_seq);
                    dups.insert(cur_seq);
                }
            }
        }
        return ans;
    }
};

// improved
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        int len = s.length();
        if (len < 10) return vector<string>();
        unordered_set<string> seqs;
        unordered_set<string> dups;
        vector<string> ans;
        
        for (int i=0; i<=len-10; i++) {
            string cur_seq = s.substr(i, 10);
            if (!seqs.insert(cur_seq).second) {
               if (dups.insert(cur_seq).second) {
                   ans.push_back(cur_seq);
               } 
            }
        }
        return ans;
    }
};

// shortest using range constructor
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        int len = s.length();
        if (len < 10) return vector<string>();
        unordered_set<string> seqs;
        unordered_set<string> dups;
        
        for (int i=0; i<=len-10; i++) {
            string cur_seq = s.substr(i, 10);
            if (!seqs.insert(cur_seq).second) {
               dups.insert(cur_seq); 
            }
        }
        return vector<string>(dups.begin(), dups.end());
    }
};
```

[solution](https://leetcode.com/problems/repeated-dna-sequences/discuss/53855/7-lines-simple-Java-O(n))



## 11. LeetCode 406 [Queue Reconstruction by Height](https://leetcode.com/problems/queue-reconstruction-by-height/)

-Sorting

-First insert higher person, then we can use the k to insert every lower person

### *(1) Define Comparsion Functions*

[3 Ways to Define Comparison Functions in C++](http://fusharblog.com/3-ways-to-define-comparison-functions-in-cpp/)

[c++ custom compare function for std::sort()](https://stackoverflow.com/questions/16894700/c-custom-compare-function-for-stdsort)

[cppreference](https://en.cppreference.com/w/cpp/algorithm/sort)



### (2) Other Concept

[const member function](https://www.geeksforgeeks.org/const-member-functions-c/)

[invalid use of non-static member function](https://blog.csdn.net/cinderella_niu/article/details/43227221)

[non-member-function-cannot-have-cv-qualifier](https://stackoverflow.com/questions/10982628/non-member-function-cannot-have-cv-qualifier)

[why-static-member-function-cant-be-const](https://stackoverflow.com/questions/7035356/c-why-static-member-function-cant-be-created-with-const-qualifier)

### (3) Solution

```c++
class Solution {
public:
    vector<pair<int, int>> reconstructQueue(vector<pair<int, int>>& people) {
        sort(people.begin(), people.end(), compare);
        vector<pair<int, int>> ans;
        
        for (auto p : people) {
            ans.insert(ans.begin()+p.second, p);
        }
        
        return ans;
    }
   
    // Use a custom comparison function.
    
    // Make sure it's a static function, because std::sort is global, it cannot call
    // non-static function in this class;
    // A non-static function is dependent on the class itself.
    // also not adding const to this function because it
private:
    static bool compare(const pair<int, int> &a, const pair<int, int> &b) {
        return a.first > b.first || (a.first == b.first && a.second < b.second);
    }
};

class Solution {
public:
    // Use a custom C++14 polymorphic lambda
    vector<pair<int, int>> reconstructQueue(vector<pair<int, int>>& people) {
        sort(people.begin(), people.end(), [](pair<int, int> &a, pair<int, int> &b) {
            return a.first > b.first || (a.first == b.first && a.second < b.second);
        });
        vector<pair<int,int>> res;
        for(auto p : people) {
            res.insert(res.begin()+p.second, p);
        }
        return res;
    }
};

// Use a custom function object
class Solution {
private:
    struct {
        bool operator()(const pair<int, int> &a, const pair<int, int> &b) const {
            return a.first > b.first || (a.first == b.first && a.second < b.second);
        }
    } compare;
    
public:
    vector<pair<int, int>> reconstructQueue(vector<pair<int, int>>& people) {
        sort(people.begin(), people.end(), compare);
        vector<pair<int, int>> ans;
        
        for (auto p : people) {
            ans.insert(ans.begin()+p.second, p);
        }
        
        return ans;
    }
};
```

[solution](https://leetcode.com/problems/queue-reconstruction-by-height/discuss/89348/6-lines-Concise-C%2B%2B)



## 12. LeetCode 402 [Remove K Digits](https://leetcode.com/problems/remove-k-digits/)

Monotone Stack

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

// k keeps track of how many characters we can remove
// if the previous character in stk is larger than the current one
// then removing it will get a smaller number
// but we can only do so when k is larger than 0
class Solution {
public:
    string removeKdigits(string num, int k) {
        int len = num.size();
        if (len == k) return "0";
        string ans = "";
        int i=0;
        while(i<len) {
            if (ans.empty() || ans.back() <= num[i]) {
                ans += num[i++];
            } else {
                while (k > 0 && !ans.empty() && ans.back() > num[i]) {
                    ans.pop_back();
                    k--;
                }
                if (k == 0) break;
            }
        }
        
        if (k > 0) {
            while (k > 0) {
                ans.pop_back();
                k--;
            }
        } else {
            ans = ans + num.substr(i);
        }
        
        i = 0;
        while (ans[i] == '0') {
            i++;
        }
        return ans.substr(i).empty() ? "0" : ans.substr(i);
    }
};

// shorter version
string removeKdigits(string num, int k) {
       string ans = ""; // treat ans as a stack in below for loop
       
       for (char c : num) {
           while (ans.length() && ans.back() > c && k) {
               ans.pop_back(); // make sure digits in ans are in ascending order
               k--; // remove one char
           }
           
           if (ans.length() || c != '0') { ans.push_back(c); } // can't have leading '0'
       }
       
       while (ans.length() && k--) { ans.pop_back(); } // make sure remove k digits in total
       return ans.empty() ? "0" : ans;
}
```

[solution1](https://leetcode.com/problems/remove-k-digits/discuss/88660/A-greedy-method-using-stack-O(n)-time-and-O(n)-space)

[solution2](https://leetcode.com/problems/remove-k-digits/discuss/88743/C%2B%2B-6ms-10-lines-solution-with-comments)



## 13. LeetCode 22 [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)

DFS

-> Use L, R to control the number of brackets we can use

```c++
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        string s;
        int L = n;
        int R = 0;
        dfs(ans, s, L, R);
        
        return ans;
    }
    
    void dfs(vector<string> &ans, string s, int L, int R) {
        if (L == 0 && R == 0) {
            ans.push_back(s);
        }
        
        if (L > 0) dfs(ans, s + '(', L-1, R+1);
        if (R > 0) dfs(ans, s + ')', L, R-1);
    }
};
```



## 14. LeetCode 151 [Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)

```c++
class Solution {
public:
    void reverseWords(string &s) {
        reverse(s.begin(), s.end());
        int i=0, j=0, l=0;
        int len = s.length();
        
        while (j < len) {
            if (s[j] == ' ') {
                j++;
            } else {
                if (i != 0) i++;
                // pay attention to overflow!
                while (j < len && s[j] != ' ') {
                    swap(s[i++], s[j++]);
                    l++;
                }
                reverse(s.begin()+i-l, s.begin()+i);
                l = 0;
            }
        }
        s.resize(i);
    }
};

// other version
class Solution {
public:
    void reverseWords(string &s) {
        int pi = 0, pj = 0, left = 0;
        int len = (int)s.size();
        int wordCnt = 0;
        while (true) {
            while (pi < len && s[pi] == ' ') { pi++;}
            if (pi == len) { break;}
            if (wordCnt != 0) { s[pj++] = ' ';}
            left = pj;
            while (pi < len && s[pi] != ' ') { s[pj++] = s[pi++];}
            reverse(s.begin()+left, s.begin()+pj);
            wordCnt++;
        }
        s.resize(pj);
        reverse(s.begin(), s.end());
    }
};
```



## 15. LeetCode 253 [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)

### (1) min_heap

Greedy Arrangment Strategy

```c++
// Time Complexity: O(NlogN)
// Space Complexity: O(N)
// using min_heap
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    int minMeetingRooms(vector<Interval>& intervals) {
        sort(intervals.begin(), intervals.end(), [](Interval &a, Interval &b) {
            return a.start < b.start;
        });
        priority_queue<int, vector<int>, greater<int>> pq; // store the end time
        for (auto const &i : intervals) {
            if (!pq.empty()) {
                if (pq.top() <= i.start) {
                    pq.pop();
                }
            }
            pq.push(i.end);
        }
        return pq.size();
    }
};
```



### (2) Chronological Ordering

**Intuition**

The meeting timings given to us define a chronological order of events throughout the day. We are given the start and end timings for the meetings which can help us define this ordering.

Arranging the meetings according to their start times helps us know the natural order of meetings throughout the day. However, simply knowing when a meeting starts doesn't tell us much about its duration.

We also need the meetings sorted by their ending times because an ending event essentially tells us that there must have been a corresponding starting event and more importantly, an ending event tell us that a previously occupied room has now become free.

A meeting is defined by its start and end times. However, for this specific algorithm, we need to treat the start and end times `individually`. This might not make sense right away because a meeting is defined by its start and end times. If we separate the two and treat them individually, then the identity of a meeting goes away. This is fine because:

> When we encounter an ending event, that means that some meeting that started earlier has ended now. We are not really concerned with which meeting has ended. All we need is that **some** meeting ended thus making a room available.

```c++
// Time Complexity: O(NlogN)
// Space Complexity: O(N)

/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    int minMeetingRooms(vector<Interval>& intervals) {
        vector<int> starts;
        vector<int> ends;
        
        for (auto const &i : intervals) {
            starts.push_back(i.start);
            ends.push_back(i.end);
        }
        sort(starts.begin(), starts.end());
        sort(ends.begin(), ends.end());
        
        int ps = 0, pe = 0; // ptr_start or ptr_end
        //int minRooms = 0;
        while (ps < intervals.size()) {
            if (ends[pe] <= starts[ps]) {
                pe++;
                //minRooms--;
            }
            ps++;
            //minRooms++;
        }
        return ps - pe;
    }
};
```

[solution](https://leetcode.com/articles/meeting-rooms-ii/)



## 16. LeetCode 771 [Jewels and Stones](https://leetcode.com/problems/jewels-and-stones/)

```c++
// Time Complexity: O(m+n) (m = length of S)
// Space Complexity: O(n) (n = length of J)

// use hashmap
class Solution {
public:
    int numJewelsInStones(string J, string S) {
        unordered_map<char, int> jewels;
        int jCnt = 0;
        
        for (auto const &j : J) {
            jewels[j]++;
        }
        for (auto const &s : S) {
            if (jewels.count(s)) {
                jCnt++;
            }
        }
        return jCnt;
    }
};

// use hashset
class Solution {
public:
    int numJewelsInStones(string J, string S) {
        unordered_set<char> jewels(J.begin(), J.end());
        int jCnt = 0;
        
        for (auto const &s : S) {
            if (jewels.count(s)) {
                jCnt++;
            }
        }
        return jCnt;
    }
};
```



## 17. LeetCode 336 [Palindrome Pairs](https://leetcode.com/problems/palindrome-pairs/)

```c++

```

[solution](https://leetcode.com/problems/palindrome-pairs/discuss/79215/Easy-to-understand-AC-C%2B%2B-solution-O(n*k2)-using-map)



## 18. LeetCode 564 [Find the Closest Palinfrome](https://leetcode.com/problems/find-the-closest-palindrome/)

```c++

```

[solution](https://leetcode.com/problems/find-the-closest-palindrome/discuss/102391/Python-Simple-with-Explanation)



## 19. LeetCode 460 [LFU Cache](https://leetcode.com/problems/lfu-cache/)

```c++

```

[solution](https://leetcode.com/problems/lfu-cache/discuss/94516/Concise-C%2B%2B-O(1)-solution-using-3-hash-maps-with-explanation)

















