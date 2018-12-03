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

```



































