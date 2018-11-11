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









