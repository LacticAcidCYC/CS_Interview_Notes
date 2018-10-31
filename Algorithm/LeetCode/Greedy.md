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

