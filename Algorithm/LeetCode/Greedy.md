# Greedy

## 1. LeetCode 406 [Queue Reconstruction by Height](https://leetcode.com/problems/queue-reconstruction-by-height/)

-Sorting

-First insert higher person, then we can use the k to insert every lower person

```c++
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
```



## 2. LeetCode 134 [Gas Station](https://leetcode.com/problems/gas-station/)

### (1) Naive Simulation (112ms)

```c++
// Time Complexity: O(n^2)

class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int n = gas.size();
        
        for (int i=0; i<n; i++) {
            if (gas[i] >= cost[i]) {
                int tank = 0;
                int j = 0;
                for (; j<n; j++) {
                    tank += gas[(i + j) % n];
                    if (tank < cost[(i + j) % n]) {
                        break;
                    }
                    tank -= cost[(i + j) % n];
                }
                if (j == n) return i;
            }
        }
        
        return -1;
    }
};
```



### (2) greedy

find the smallest subsum and also store the index

```c++
// Time Complexity: O(n)

class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int n = gas.size();
        
        int min_balance = INT_MAX;
        int begin = -1;
        int balance = 0;
        for (int i=0; i<n; i++) {
            balance += gas[i] - cost[i];
            if (min_balance > balance) {
                begin = i+1;
                min_balance = balance;
            }
        }
        
        //if (balance < 0) return -1;
        //else return begin == n ? 0 : begin;
        return (balance < 0) ? -1 : begin % n;
    }
};
```

[proof](https://leetcode.com/problems/gas-station/discuss/42572/Proof-of-%22if-total-gas-is-greater-than-total-cost-there-is-a-solution%22.-C%2B%2B)



### (3) greedy 2

The basic idea is every time we start from a station, we go as far as possible by increasing `end` until remaining gas is less than 0. If 'end' finally hits `start` we know we can travel around from 'start'. If we haven't traveled around, we know we cannot start from this station. Then we check the station before our start station if we can start from this station. Repeat until we have checked all stations.

Note there is a little trick that every time we try to find the next start station, we always to back to extend the distance from start to end so that we can check all stations on the circuit. Otherwise, if we move start forward to decrease the distance from start to end, we are likely to end up with only checking part of the circuit. Another trick is we start from the end of the array so as to avoid some corner cases.

```c++
// Time Complexity: O(n)
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int n = gas.size();
        int sum = 0;
        int end = 0, start = n - 1;
        while (start >= end) {
            if (sum >= 0) {
                sum += gas[end] - cost[end];
                end++;
            } else {
                sum += gas[start] - cost[start];
                start--;
            }
        }
        return sum < 0 ? -1 : (start+1) % n;
    }
};

// avoid corner case
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int n = gas.size();
        int end = 0, start = n - 1;
        int sum = gas[start] - cost[start];
        while (start > end) {
            if (sum >= 0) {
                sum += gas[end] - cost[end];
                end++;
            } else {
                start--;
                sum += gas[start] - cost[start];
            }
        }
        return sum < 0 ? -1 : start;
    }
};
```

[solution](https://leetcode.com/problems/gas-station/discuss/42565/My-AC-is-O(1)-space-O(n)-running-time-solution.-Does-anybody-have-posted-this-solution)



## 3. LeetCode 135 [Candy](https://leetcode.com/problems/candy/)

First, satisfy left-neighbor relationship, then right, finally combine.

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

// two-array for extra space
class Solution {
public:
    int candy(vector<int>& ratings) {
        int n = ratings.size();
        if (n == 1) return 1;
        
        vector<int> left2right(n, 1);
        vector<int> right2left(n, 1);
        
        for (int i=1; i<n; i++) {
            if (ratings[i] > ratings[i-1]) {
                left2right[i] = left2right[i-1] + 1;
            }
        }
        
        for (int i=n-2; i>=0; i--) {
            if (ratings[i] > ratings[i+1]) {
                right2left[i] = right2left[i+1] + 1;
            }
        }
        
        int res = 0;
        
        for (int i=0; i<n; i++) {
            res += max(left2right[i], right2left[i]);
        }
        
        return res;
    }
};

// improved
// one-array for extra space
class Solution {
public:
    int candy(vector<int>& ratings) {
        int n = ratings.size();
        if (n == 1) return 1;
        
        vector<int> candies(n, 1);
        
        for (int i=1; i<n; i++) {
            if (ratings[i] > ratings[i-1]) {
                candies[i] = candies[i-1] + 1;
            }
        }
        
        int res = candies[n-1];
        for (int i=n-2; i>=0; i--) {
            if (ratings[i] > ratings[i+1]) {
                candies[i] = max(candies[i] ,candies[i+1] + 1);
            }
            res += candies[i];
        }
        
        return res;
    }
};
```

[solution](https://leetcode.com/articles/candy/)



## 4. LeetCode 358 [Rearrange String k Distance Apart](https://leetcode.com/problems/rearrange-string-k-distance-apart/)

### hash map + max-heap + greedy

```c++
// Time Complexity: O(n) (O(n26log26))
// Space Complexity: O(1) (O(26))

class Solution {
public:
    string rearrangeString(string s, int k) {
        if (k <= 0) return s;
        unordered_map<char, int> dict;
        
        for (auto c : s) {
            dict[c]++;
        }
        
        priority_queue<pair<int, char>> pq;
        for (auto p : dict) {
            pq.push({p.second, p.first});
        }
        
        int n = s.length();
        string res = "";
        while (!pq.empty()) {
            vector<pair<int, char>> cache;
            int window = min(k, n);
            
            for (int i=0; i<window; i++) {
                if (pq.empty()) return "";
                auto p = pq.top();
                pq.pop();
                res += p.second;
                n--;
                if (p.first > 1) {
                    cache.push_back({p.first-1, p.second});
                }
            }
            
            for (auto p : cache) {
                pq.push(p);
            }
        }
        
        return res;
    }
};
```



## 5. LeetCode 621 [Task Scheduler](https://leetcode.com/problems/task-scheduler/)

```c++
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        vector<int> mp(26, 0);
        int maxCount = INT_MIN;
        for (auto c : tasks) {
            mp[c - 'A']++; 
            maxCount = max(maxCount, mp[c - 'A']);
        }
        
        int res = (n + 1) * (maxCount - 1);
        for (auto cnt : mp) {
            if (cnt == maxCount) {
                res++;
            }
        }
        
        return max((int)tasks.size(), res);
    }
};
```

[solution](https://leetcode.com/problems/task-scheduler/discuss/104504/C%2B%2B-8lines-O(n)): explanation in comments



## 6. LeetCode 942 [DI String Match](https://leetcode.com/problems/di-string-match/)

### std::count

count (InputIterator first, InputIterator last, const T& val);

**Intuition1**

If we see say `S[0] == 'I'`, we can always put `0` as the first element, then next number will always be greater no matter what is selected; similarly, if we see `S[0] == 'D'`, we can always put `N` as the first element, then next number will always be less no matter what is selected. So for the rest of the numbers, similarly, If we see say `S[i] == 'I'`, we can always put the **smallest** as the `ith` element or if we see `S[i] == 'D'`, we can always put the **largest** as the `ith` element.

**Algorithm**

Keep track of the smallest and largest element we haven't placed. If we see an `'I'`, place the small element; otherwise place the large element.

**Intuition2**

First count how many `D` (or `I`) in the string. If the count is `M`, then put `M` in the result array;

Now there are `M` number (0,1,2,...,M-1) less than `M`, the number of the larger number is equal to the number of `I`;

Then we can split the number set into 2 parts——  left(0,1,2,...,M-1) and right(M+1, N);

If `S[i] == 'I'`, we can insert the least in right, otherwise insert the largest one in left.

### Solution1 (One Pass):

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    vector<int> diStringMatch(string S) {
        int n = S.length();
        int low = 0, high = n;
        vector<int> res(n+1);
        
        for (int i=0; i<n; i++) {
            res[i] = S[i] == 'I' ? low++ : high--;
        }
        res[n] = low;
        
        return res;
    }
};
```

### Solution2 (Two Pass):

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    vector<int> diStringMatch(string S) {
        int n = S.length();
        int left = count(S.begin(), S.end(), 'D');
        int right = left;
        vector<int> res(n+1);
        res[0] = left;
        
        for (int i=0; i<n; i++) {
            res[i+1] = S[i] == 'I' ? ++right : --left;
        }
        
        return res;
    }
};
```



## 7. LeetCode 936 [Stamping The Sequence](https://leetcode.com/problems/stamping-the-sequence/)

```c++
class Solution {
public:
    vector<int> movesToStamp(string stamp, string target) {
        vector<int> ans;
        int len_t = target.length();
        string questions(len_t, '?');
        
        while (target != questions) {
            int index = match(stamp, target);
            if (index == len_t) return {};
            ans.push_back(index);
        }
        
        reverse(ans.begin(), ans.end());
        return ans;
    }
    
private:
    int match(string &stamp, string &target) {
        int len_t = target.length();
        int len_s = stamp.length();
        
        for (int start = 0; start < len_t; start++) {
            int i = start; // iterator for target
            int j = 0; // iterator for stamp
            
            bool isStamped = false;
            while (i < len_t && j < len_s && (target[i] == '?' || target[i] == stamp[j])) {
                if (target[i] == stamp[j]) isStamped = true;
                i++;
                j++;
            }
            
            if (j == len_s && isStamped) {
                for (int k = start; k < start+len_s; k++) {
                    target[k] = '?';
                }
                return start;
            }
        }
        
        return len_t;
    }
};
```



## 8. LeetCode 984 [String Without AAA or BBB](https://leetcode.com/problems/string-without-aaa-or-bbb/)

```c++
class Solution {
public:
    string strWithout3a3b(int A, int B) {
        string ans;
        
        while (A > 0 || B > 0) {
            bool printA = false;
            int l = ans.length();
            
            if (l >= 2 && ans[l-1] == ans[l-2]) {
                if (ans[l-1] == 'b') {
                    printA = true;
                }
            } else {
                if (A >= B) {
                    printA = true;
                }
            }
            
            if (printA) {
                A--;
                ans += 'a';
            } else {
                B--;
                ans += 'b';
            }
        }
        
        return ans;
    }
};
```



## 9. LeetCode 659 [Split Array into Consecutive Subsequences](https://leetcode.com/problems/split-array-into-consecutive-subsequences/)

```c++
class Solution {
public:
    bool isPossible(vector<int>& nums) {
        unordered_map<int,int> cnt, tails;
        for(int &i : nums) cnt[i]++;
        for(int &i : nums){
            if(!cnt[i]) continue;
            cnt[i]--;
            if(tails[i-1] > 0){
                tails[i-1]--;
                tails[i]++;
            }
            else if(cnt[i+1] && cnt[i+2]){
                cnt[i+1]--;
                cnt[i+2]--;
                tails[i+2]++;
            }
            else return false;
        }
        return true;
    }
};
```

[solution](https://leetcode.com/problems/split-array-into-consecutive-subsequences/discuss/106493/C%2B%2B-O(n)-solution-two-pass)



## 10. LeetCode 763 [Partition Labels](https://leetcode.com/problems/partition-labels/)

```c++
class Solution {
public:
    vector<int> partitionLabels(string S) {
        vector<int> lastpos(26);
        
        for (int i=0; i<S.length(); i++) {
            lastpos[S[i] - 'a'] = i;
        }
        
        int j = 0, anchor = 0;
        vector<int> ans;
        for (int i=0; i<S.length(); i++) {
            j = max(j, lastpos[S[i] - 'a']);
            if (i == j) {
                ans.push_back(i - anchor + 1);
                anchor = i + 1;
            }
        }
        
        return ans;
    }
};
```



## 11. LeetCode 767 [Reorganize String](https://leetcode.com/problems/reorganize-string/)

### Similar: 358, 621

```c++
class Solution {
public:
    string reorganizeString(string S) {
        int N = S.size();
        vector<int> counts(26);
        
        for (char c: S) counts[c - 'a'] += 100;
        for (int i = 0; i < 26; ++i) counts[i] += i;
        //Encoded counts[i] = 100*(actual count) + (i)
        sort(counts.begin(), counts.end());

        string ans(N, '.');
        int t = 1;
        for (int code : counts) {
            int ct = code / 100;
            char ch = 'a' + (code % 100);
            if (ct > (N+1) / 2) return "";
            
            for (int i = 0; i < ct; ++i) {
                if (t >= N) t = 0;
                ans[t] = ch;
                t += 2;
            }
        }

        return ans;
    }
};
```



## 12. LeetCode 910 [Smallest Range II](https://leetcode.com/problems/smallest-range-ii/)

### **Intuition**:

For each integer `A[i]`,
we may choose either `x = -K` or `x = K`.

If we add `K` to all `B[i]`, the result won't change.

It's the same as:
For each integer `A[i]`, we may choose either `x = 0` or `x = 2 * K`.

### **Explanation**:

We sort the `A` first, and we choose to add `x = 0` to all `A[i]`.
Now we have `res = A[n - 1] - A[0]`.
Starting from the smallest of `A`, we add `2 * K` to `A[i]`,
hoping this process will reduce the difference.

Update the new `mx = max(mx, A[i] + 2 * K)`
Update the new `mn = min(A[i + 1], A[0] + 2 * K)`
Update the `res = min(res, mx - mn)`

```c++
class Solution {
public:
    int smallestRangeII(vector<int>& A, int K) {
        sort(A.begin(), A.end());
        int n = A.size(), mx = A[n - 1], mn = A[0], res = mx - mn;
        
        for (int i = 0; i < n - 1; ++i) {
            mx = max(mx, A[i] + 2 * K);
            mn = min(A[i + 1], A[0] + 2 * K);
            res = min(res, mx - mn);
        }
        return res;
    }
};
```

[solution](https://leetcode.com/problems/smallest-range-ii/discuss/173377/C%2B%2BJavaPython-Add-0-or-2-*-K)

















