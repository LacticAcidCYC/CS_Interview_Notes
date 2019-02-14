## 1. LeetCode 242 [Valid Anagram](https://leetcode.com/problems/valid-anagram/)

```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        vector<int> mp(26);
        for (auto &c : s) {
            mp[c - 'a']++;
        }
        
        for (auto &c : t) {
            mp[c - 'a']--;
        }
        
        for (auto &i : mp) {
            if (i != 0) return false;
        }
        return true;
    }
};
```



## 2. LeetCode 49 [Group Anagrams](https://leetcode.com/problems/group-anagrams/)

```c++
// Time Complexity: O(NKlogK)
// Space Complexity: O(NK)
// where N is the length of strs, and K is the maximum length of a string in strs. 

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        if (strs.empty()) return {};
        vector<vector<string>> res;
        
        unordered_map<string, vector<string>> mp;
        for (auto &s : strs) {
            auto key = s;
            sort(key.begin(), key.end());
            mp[key].push_back(s);
        }
        
        for (auto &p : mp) {
            res.push_back(p.second);
        }
        
        return res;
    }
};

// Time Complexity: O(NK)
// Space Complexity: O(NK)
// Time Complexity: O(NK), where N is the length of strs, and K is the maximum length of a
// string in strs. Counting each string is linear in the size of the string, and we count
// every string.

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        if (strs.empty()) return {};
        vector<vector<string>> res;
        
        unordered_map<string, vector<string>> mp;
        for (auto &s : strs) {
            string key = "";
            vector<int> cnts(26, 0);
            for (auto &c : s) {
                cnts[c - 'a']++;
            }
            
            for (int i=0; i<26; i++) {
                key += ("#" + to_string(cnts[i]));
            }
            
            mp[key].push_back(s);
        }
        
        for (auto &p : mp) {
            res.push_back(p.second);
        }
        
        return res;
    }
};
```



## 3. LeetCode 266 [Palindrome Permutation](https://leetcode.com/problems/palindrome-permutation/)

```c++
class Solution {
public:
    bool canPermutePalindrome(string s) {
        unordered_map<char, int> mp;
        int odd = 1;
        
        for (auto &c : s) {
            mp[c]++;
        }
        
        for (auto &p : mp) {
            if (p.second % 2) {
                if (odd == 0) return false;
                odd--;
            }
        }
        return true;
    }
};
```



## 4. LeetCode 267 [Palindrome Permutation II](https://leetcode.com/problems/palindrome-permutation-ii/)

### Hashmap + Backtracking

```c++
class Solution {
public:
    vector<string> generatePalindromes(string s) {
        if (s.empty()) return {};
        vector<string> res;
        string mid = "", half = "";
        unordered_map<char, int> mp;
        int odd = 1;
        
        for (auto &c : s) {
            mp[c]++;
        }
        
        for (auto &p : mp) {
            if (p.second % 2) {
                if (odd == 0) return {};
                odd--;
            }
        }
        
        // construct left half
        for (auto &p : mp) {
            if (p.second % 2) mid += p.first;
            half += string(p.second / 2, p.first);
        }
        
        // generate all unique permutation
        res = permutations(half);
        for (auto &s : res) {
            string t(s);
            reverse(t.begin(), t.end());
            if (odd == 0) t = mid + t;
            s = s + t;
        }
        return res;
    }
    
    // O(n^2)
    vector<string> permutations(string &s) {
        vector<string> res;
        string t(s);
        do {
            res.push_back(t);
            next_permutation(t.begin(), t.end());
        } while (t != s);
        
        return res;
    }
};
```



## 5. LeetCode 438 [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)

### HashTable + Sliding Window

```c++
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        vector<int> res;
        int ls = s.length();
        int lp = p.length();
        if (lp > ls) return {};
        
        vector<int> mp(26);
        for (auto &c : p) {
            mp[c - 'a']++;
        }
        
        int cnt = lp, l = 0, r = 0;
        
        while (r < ls) {
            if (mp[s[r++] - 'a']-- > 0) {
                cnt--;
            } else {
                while (mp[s[l++] - 'a']++ >= 0) {
                    cnt++;
                }
            }
            if (cnt == 0) {
                res.push_back(l);
            }
        }
        
        return res;
    }
};
```



## 6. LeetCode 734 [Sentence Similarity](https://leetcode.com/problems/sentence-similarity/)

```c++
// N : length of words1(words2)
// P : length of pairs
// Time Complexity: O(N + P)
// Space Complexity: O(P)

class Solution {
public:
    bool areSentencesSimilar(vector<string>& words1, vector<string>& words2, vector<pair<string, string>> pairs) {
        int l1 = words1.size();
        int l2 = words2.size();
        if (l1 != l2) return false;
        
        unordered_map<string, unordered_set<string>> mp;
        for (auto &p : pairs) {
            mp[p.first].insert(p.second);
            mp[p.second].insert(p.first);
        }
        
        for (int i=0; i<l1; i++) {
            if (words1[i] != words2[i] && !mp[words1[i]].count(words2[i])) {
                return false;
            }
        }
        
        return true;
    }
};
```



## 7. LeetCode 981 [Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store/)

```c++
class TimeMap {
public:
    /** Initialize your data structure here. */
    TimeMap() {
        
    }
    
    void set(string key, string value, int timestamp) {
        m[key].insert({ timestamp, value });
    }
    
    string get(string key, int timestamp) {
        auto it = m[key].upper_bound(timestamp);
        return it == m[key].begin() ? "" : prev(it)->second;
    }
    
private:
    unordered_map<string, map<int, string>> m;
};

/**
 * Your TimeMap object will be instantiated and called as such:
 * TimeMap* obj = new TimeMap();
 * obj->set(key,value,timestamp);
 * string param_2 = obj->get(key,timestamp);
 */
```



## 8. LeetCode 953 [Verifying an Alien Dictionary](https://leetcode.com/problems/verifying-an-alien-dictionary/)

```c++
class Solution {
public:
    bool isAlienSorted(vector<string>& words, string order) {
        vector<int> mapping(26);
        for (int i = 0; i < 26; i++)
            mapping[order[i] - 'a'] = i;
        for (string &w : words)
            for (char &c : w)
                c = mapping[c - 'a'];
        return is_sorted(words.begin(), words.end());
    }
};
```



## 9. LeetCode 594 [Longest Harmonious Subsequence](https://leetcode.com/problems/longest-harmonious-subsequence/)

```c++
class Solution {
public:
    int findLHS(vector<int>& nums) {
        unordered_map<int, int> mp;
        int res = 0;
        
        for (int &num : nums) {
            mp[num]++;
            
            if (mp.count(num + 1)) {
                res = max(res, mp[num] + mp[num+1]);
            }
            if (mp.count(num - 1)) {
                res = max(res, mp[num] + mp[num-1]);
            }
        }
        return res;
    }
};
```



## 10. LeetCode 599 [Minimum Index Sum of Two Lists](https://leetcode.com/problems/minimum-index-sum-of-two-lists/)

```c++
class Solution {
public:
    vector<string> findRestaurant(vector<string>& list1, vector<string>& list2) {
        unordered_map<string, int> mp;
        
        for (int i=0; i<list1.size(); i++) {
            mp[list1[i]] = i;
        }
        
        vector<string> res;
        int min_sum = INT_MAX, sum;
        
        for (int j=0; j<list2.size() && j<=min_sum; j++) {
            if (mp.count(list2[j])) {
                sum = j + mp[list2[j]];
                if (sum < min_sum) {
                    res.clear();
                    res.push_back(list2[j]);
                    min_sum = sum;
                } else if (sum == min_sum) {
                    res.push_back(list2[j]);
                }
            }
        }
        
        return res;
    }
};
```































