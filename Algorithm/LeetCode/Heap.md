## 1. LeetCode 347 [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

### (1) [C++ priority_queue with lambda comparator error](https://stackoverflow.com/questions/5807735/c-priority-queue-with-lambda-comparator-error)

I. `priority_queue` takes the comparator as a template argument. Lambda functions are objects, and thus can't be used as template arguments.

II. A lambda-expression shall not appear in an unevaluated operand.

[cppreference-decltype](https://en.cppreference.com/w/cpp/language/decltype)

### (2) auto

[auto](https://stackoverflow.com/questions/29859796/c-auto-vs-auto/29860056#29860056)

[auto&&](https://stackoverflow.com/questions/13230480/what-does-auto-tell-us)

```c++

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



## 2. LeetCode 632

```c++

```

[solution](https://leetcode.com/problems/smallest-range/discuss/104893/Java-Code-using-PriorityQueue.-similar-to-merge-k-array)



## 3. LeetCode 373

```c++

```

[solution](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/discuss/84551/simple-Java-O(KlogK)-solution-with-explanation)



## 4. LeetCode 378

```c++

```





## 5. LeetCode 692

```c++

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

