# Disjoint set / Union-Find Forest

[youtube-video-by-huahua](https://www.youtube.com/watch?v=VJnUwsE4fWA)



## 1. LeetCode 399



## 2. LeetCode 547



## 3. LeetCode 684



## 4. LeetCode 685



## 5. LeetCode 952 [Largest Component Size by Common Factor](https://leetcode.com/problems/largest-component-size-by-common-factor/)

[youtube](https://www.youtube.com/watch?v=GTX0kw63Tn0)

### Intuition

Union each number with all its factor;

Count the most frequent parent.

[max_element](http://www.cplusplus.com/reference/algorithm/max_element/?kw=max_element)

```c++
// W: the largest number
// Time Complexity: O(n*sqrt(W))
// Space Complexity: O(W)

class UnionFindSet {
public:
    UnionFindSet(int n) : _parent(n) {
        for (int i=0; i<n; i++) {
            _parent[i] = i;
        }
    }
    
    void Union(int x, int y) {
        _parent[Find(x)] = _parent[Find(y)];
    }
    
    int Find(int x) {
        if (_parent[x] != x) {
            _parent[x] = Find(_parent[x]);
        }
        return _parent[x];
    }
    
private:
    vector<int> _parent;
};

class Solution {
public:
    int largestComponentSize(vector<int>& A) {
        int n = *max_element(A.begin(), A.end());
        UnionFindSet ufs(n+1);
        for (int &a : A) {
            for (int k = 2; k <= sqrt(a); k++) {
                if (a % k == 0) {
                    ufs.Union(a, k);
                    ufs.Union(a, a / k);
                }
            }
        }
        
        unordered_map<int, int> mp;
        int ans = 1;
        for (int &a : A) {
            ans = max(ans, ++mp[ufs.Find(a)]);
        }
        return ans;
    }
};
```

