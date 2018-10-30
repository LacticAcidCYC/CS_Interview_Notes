## 1. LeetCode 347 [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

### (1) [C++ priority_queue with lambda comparator error](https://stackoverflow.com/questions/5807735/c-priority-queue-with-lambda-comparator-error)

I. `priority_queue` takes the comparator as a template argument. Lambda functions are objects, and thus can't be used as template arguments.

II. A lambda-expression shall not appear in an unevaluated operand.

[cppreference-decltype](https://en.cppreference.com/w/cpp/language/decltype)

### (2) auto

[auto](https://stackoverflow.com/questions/29859796/c-auto-vs-auto/29860056#29860056)

[auto&&](https://stackoverflow.com/questions/13230480/what-does-auto-tell-us)

```c++
// Time Complexity: O(nlogk)
// Space Complexity: O(n)

class Solution {
private:
    // custom function object
    struct compare {
        bool operator() (const pair<int, int> &A, const pair<int, int> &B) const {
            return A.second > B.second;
        }
    };
    
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> cnts;
        for (auto const &i : nums) {
            cnts[i]++;
        }
        
        priority_queue<pair<int, int>, vector<pair<int, int>>, compare> pq;
        
        int i = 0;
        for (auto const &p : cnts) {
            if (i < k) {
                pq.push(p);
            } else {
                if (pq.top().second < p.second) {
                    pq.pop();
                    pq.push(p);
                }
            }
            i++;
        }
        
        vector<int> ans;
        while (!pq.empty()) {
            ans.push_back(pq.top().first);
            pq.pop();
        }
        
        return ans;
    }
};


class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> cnts;
        for (auto i : nums) {
            cnts[i]++;
        }
        
        // use lambda function comparator
        auto compare = [](pair<int, int> &A, pair<int, int> &B) {
            return A.second > B.second;
        };
        // cannot 
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(compare)> pq(compare);
        
        int i = 0;
        for (auto p : cnts) {
            if (i < k) {
                pq.push(p);
            } else {
                if (pq.top().second < p.second) {
                    pq.pop();
                    pq.push(p);
                }
            }
            i++;
        }
        
        vector<int> ans;
        while (!pq.empty()) {
            ans.push_back(pq.top().first);
            pq.pop();
        }
        
        return ans;
    }
};
```



## 2. LeetCode 632 [Smallest Range](https://leetcode.com/problems/smallest-range/)

```c++
// Time Complexity: O(nlogk) (k = number of vectors)
// Space Complexity: O(k)

class Solution {
public:
    vector<int> smallestRange(vector<vector<int>>& nums) {
        int n = nums.size();
        int max_val = INT_MIN;
        int range = INT_MAX;
        vector<int> ans(2);
        
        auto compare = [](const pair<int, pair<int, int>> &A, const pair<int, pair<int, int>> &B) {
            return A.first > B.first;
        };
        priority_queue<pair<int, pair<int, int>>, vector<pair<int,pair<int, int>>>, decltype(compare)> pq(compare);
        
        for (int i=0; i<n; i++) {
            pq.push({nums[i][0], {i, 0}});
            if (nums[i][0] > max_val) {
                max_val = nums[i][0];
            }
        }
        
        while (true) {
            auto top = pq.top();
            if (max_val - top.first < range) {
                range = max_val - top.first;
                ans[0] = top.first;
                ans[1] = max_val;
            }
            pq.pop();
            if (top.second.second == nums[top.second.first].size() - 1) {
                break;
            }
            int new_insert = nums[top.second.first][top.second.second+1];
            pq.push({new_insert, {top.second.first, top.second.second+1}});
            if (new_insert > max_val) {
                max_val = new_insert;
            }
        }
        
        return ans;
    }
};
```

[solution](https://leetcode.com/problems/smallest-range/discuss/104893/Java-Code-using-PriorityQueue.-similar-to-merge-k-array)



## 3. LeetCode 373 [Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/)

```c++
// Time Complexity: O(klogk)
// Space Complexity: O(k)

class Solution {
public:
    vector<pair<int, int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        vector<pair<int, int>> ans;
        int l1 = nums1.size();
        int l2 = nums2.size();
        if (l1 == 0 || l2 == 0 || k <= 0) return ans;
        
        auto compare = [](pair<pair<int, int>, int> &A, pair<pair<int, int>, int> &B) {
            return A.second > B.second;
        };
        priority_queue<pair<pair<int, int>, int>, vector<pair<pair<int, int>, int>>, decltype(compare)> pq(compare);
        for (int j=0; j<l2 && j<k; j++) {
            pq.push({{0, j}, nums1[0]+nums2[j]});
        }
        
        while (!pq.empty() && ans.size() < k) {
            pair<int, int> min = pq.top().first;
            pq.pop();
            ans.push_back({nums1[min.first], nums2[min.second]});
            if (min.first+1 < l1) {
                pq.push({{min.first+1, min.second}, nums1[min.first+1]+nums2[min.second]});
            }
        }
        
        return ans;
    }
};

// improved version
class Solution {
public:
    vector<pair<int, int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        vector<pair<int, int>> ans;
        int l1 = nums1.size();
        int l2 = nums2.size();
        if (l1 == 0 || l2 == 0 || k <= 0) return ans;
        
        auto compare = [&nums1, &nums2](pair<int, int> &A, pair<int, int> &B) {
            return nums1[A.first] + nums2[A.second] > nums1[B.first] + nums2[B.second];
        };
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(compare)> pq(compare);
        for (int j=0; j<l2 && j<k; j++) {
            pq.push({0, j});
        }
        
        while (!pq.empty() && ans.size() < k) {
            pair<int, int> min = pq.top();
            pq.pop();
            ans.push_back({nums1[min.first], nums2[min.second]});
            if (min.first+1 < l1) {
                pq.push({min.first+1, min.second});
            }
        }
        
        return ans;
    }
};
```

[solution](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/discuss/84551/simple-Java-O(KlogK)-solution-with-explanation)



## 4. LeetCode 378 [Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)

### (1) max-heap (priority_queue)

```c++
// max-heap
// Time Complexity: O(n^2logk)
// Space Complexity: O(1)

int kthSmallest(vector<vector<int>>& matrix, int k) {
    int n = (int) matrix.size();
    priority_queue<int, vector<int>> pq;
    int l = n * n;
    for (int i=0; i<k; i++) {
        pq.push(matrix[i/n][i%n]);
    }
    for (int i=k; i<l; i++) {
        int cur = matrix[i/n][i%n];
        if (pq.top() > cur) {
            pq.pop();
            pq.push(cur);
        }
    }
    return pq.top();
}
```



### (2) min-heap (priority_queue)

```c++
// min-heap
// Time Complexity: O(klogn) (n: row of the matrix)
// Space Complexity: O(n)

struct compare {
    bool operator()(const pair<int, pair<int, int>> &A, const pair<int, pair<int, int>> &B) {
        return A.first > B.first;
    }
};

int kthSmallest2(vector<vector<int>>& matrix, int k) {
    int n = (int) matrix.size();
    priority_queue<pair<int, pair<int, int>>, vector<pair<int, pair<int, int>>>, greater<pair<int, pair<int, int>>>> pq;
    
    for (int i=0; i<n; i++) {
//        pq.push(make_pair(matrix[i][0], make_pair(i, 0)));
        pq.push({ matrix[i][0], { i, 0 }});
    }
    
    while (k-- > 1) {
        int i = pq.top().second.first;
        int j = pq.top().second.second;
        pq.pop();
        if (j < n-1) {
//            pq.push(make_pair(matrix[i][j+1], make_pair(i, j+1)));
            pq.push({ matrix[i][j+1], { i, j+1 }});
        }
    }
    return pq.top().first;
}
```



## 5. LeetCode 692 [Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/)

```c++
// Time Complexity: O(nlogk)
// Space Complexity: O(n)

// original
class Solution {
private:
struct compare {
    bool operator() (const pair<string, int>& a, const pair<string, int>& b) const {
        return a.second == b.second ? a.first < b.first : a.second > b.second;
    }
};
    
public:
    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> um;
        vector<string> res;
        for (auto word : words) {
            um[word]++;
        }
        priority_queue<pair<string,int>, vector<pair<string, int>>, compare> pq;
        for (auto p : um) {
            if (pq.size() < k) {
                pq.emplace(p);
            } else {
                if (p.second > pq.top().second || (p.second == pq.top().second && p.first < pq.top().first)) {
                    pq.pop();
                    pq.emplace(p);
                }
            }
        }
        while (!pq.empty()) {
            res.push_back(pq.top().first);
            pq.pop();
        }
        reverse(res.begin(), res.end());
        return res;
    }
};

// improved version (using lambda, some optimization)
class Solution {
public:
    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> um;
        vector<string> res;
        for (const auto &word : words) {
            um[word]++;
        }
        
        auto compare = [](pair<string, int>& a, pair<string, int>& b) {
            return a.second == b.second ? a.first < b.first : a.second > b.second;
        };
        priority_queue<pair<string,int>, vector<pair<string, int>>, decltype(compare)> pq(compare);
        for (const auto &p : um) {
            if (pq.size() < k) {
                pq.push(p);
            } else {
                if (p.second > pq.top().second || (p.second == pq.top().second && p.first < pq.top().first)) {
                    pq.pop();
                    pq.push(p);
                }
            }
        }
        while (!pq.empty()) {
            res.push_back(pq.top().first);
            pq.pop();
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```



## 6. LeetCode 23 [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
    struct comparison {
        bool operator() (const ListNode* l1, const ListNode* l2) const {
            return l1->val > l2->val;
    }
};
    
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        priority_queue<ListNode*, vector<ListNode*>, comparison> pq;
        for(auto l : lists) {
            if(l) pq.push(l);
        }
        ListNode* res = new ListNode(0);
        ListNode* iter = res;
        while(!pq.empty()) {
            iter->next = pq.top();
            pq.pop();
            iter = iter->next;
            if(iter->next) {
                pq.push(iter->next);
            }
        }
        return res->next;
    }
};
```



## 7. LeetCode 703

```c++

```

