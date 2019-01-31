# <1> Sliding Window (two pointers)

## 1. LeetCode 76 [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

### Explanation

(1) Use two pointers to create a window of letters in **S**, which would have all the characters from **T**.

(2) Since you have to find the minimum window in S which has all the characters from T, you need to expand and contract the window using the two pointers and keep checking the window for all the characters. This approach is also called Sliding Window Approach. 

```latex
L ------------------------ R , Suppose this is the window that contains all characters of T 
                          
        L----------------- R , this is the contracted window. We found a smaller window that still contains all the characters in T When the window is no longer valid, start expanding again using the right pointer.
```



### Solution

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)
// window: [left, right]
// First, find a window containing all the matched letter;
// Then, minimize the window

string minWindow(string s, string t) {
    if (t.empty() || s.empty()) return "";
    int ls = (int) s.length();
    int lt = (int) t.length(); // counter for match
    vector<int> map(128, 0);
    for (auto c : t) {
        map[c]++;
    }
    // left_bound, right_bound, minimum length of window, head of minimum window
    int left = 0, right = 0, min_len = INT_MAX, begin = 0;
    while (right < ls) {
        if (map[s[right++]]-- > 0) {
            lt--;
        }
        while (lt == 0) {
            if (min_len > right - left) {
                min_len = min(min_len, right - left);
                begin = left;
            }
            if (map[s[left++]]++ == 0) { lt++;}
        }
    }
    return min_len == INT_MAX ? "" : s.substr(begin, min_len);
}
```



## 2. LeetCode 30 [Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/)

(1) two-map solution

[Reference](https://leetcode.com/problems/substring-with-concatenation-of-all-words/discuss/13658/Easy-Two-Map-Solution-(C%2B%2BJava))

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(m) (m = length of words)

class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        if (s.empty() || words.empty()) return vector<int>();
        int n = s.length(); // length of s
        int word_len = words[0].size(); // length of the word
        int word_cnt = words.size(); // amount of word
        vector<int> res; // return vector
        unordered_map<string, int> dict; // hash map for storing the words
        for (auto s : words) { dict[s]++;}
        for (int i=0; i<n-word_len*word_cnt+1; i++) {
            unordered_map<string, int> matched; // hash map for storing the matched words
            int cur_matched = 0; // amount of matched string
            string cur_str; // current checked string
            while (dict.count(cur_str = s.substr(i+cur_matched*word_len, word_len))) {
                matched[cur_str]++;
                // !!!if current string is already matched, break!
                if (matched[cur_str] > dict[cur_str]) {
                    break;
                } else {
                    cur_matched++;
                    // find the matched substring!
                    if (cur_matched == word_cnt) {
                        res.push_back(i);
                    }
                }
            }
        }
        return res;
    }
};

// analysis
This is a nice intuitive solution, but unfortunately its runtime is O(n2) in worst case, where n is the length of your String s. (If you want to get an offer from the top tech companies like google, I guess you have to do better than that.)

Here is the analysis. An extreme case would be, words array contains only one string w that is length n/2. So this problem becomes looking for all starting index of occurrence of w from String s. The solution checks substring n/2 times, and each check takes O(n/2) time. (It does not matter whether you are using a hash map or not. Because you have to compare all the characters of w with the corresponding value found in the bucket. If you dont understand this, I suggest you to go to check how hash map works). So the runtime is (n/2 * n/2), which is effectively O(n2)
```



(2) using a window

```c++
// Time Complexity: O(n) (n = length of s)
// Space Complexity: O(m) (m = length of words)

// travel all the words combinations to maintain a window
// there are wl(word len) times travel (because if we check from index wl(or some index larger than wl), it has been already checked during check from 0(or 1,2,...,wl-1))

// each time, n/wl words, mostly 2 times travel for each word
// one left side of the window, the other right side of the window
// so, time complexity O(wl * 2 * N/wl) = O(2N)
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        if (s.empty() || words.empty()) return vector<int>(); // corner check
        vector<int> res; // result
        int n = s.length(); // length of s
        int word_len = words[0].size(); // word length
        int word_cnt = words.size(); // amount of words
        unordered_map<string, int> dict;
        // init word occurence
        for (auto s : words) { dict[s]++;}
        
        // travel all substring combinations
        for (int i=0; i<word_len; i++) {
            unordered_map<string, int> matched; // matched hash map
            int left = i, cur_matched = 0; // substring begin index & current amount of matched words
            // j : right
            for (int j=i; j<=n-word_len; j+=word_len) {
                string cur_str = s.substr(j, word_len); // current checked string
                if (dict.count(cur_str)) {
                    // if matched
                    matched[cur_str]++;
                    if (matched[cur_str] <= dict[cur_str]) {
                        // if matched word is needed to concatenate
                        cur_matched++;
                    } else {
                        // if amount of matched word is larger than the amount in dict
                        // need to cut some strings on the left
                        // a more word, advance the window left side possiablly
                        while (matched[cur_str] > dict[cur_str]) {
                            string del_str = s.substr(left, word_len);
                            if (matched[del_str]-- <= dict[del_str]) {
                                cur_matched--;
                            }
                            left += word_len;
                        }
                    }
                    // if find the substring!
                    if (cur_matched == word_cnt) {
                        res.push_back(left);
                        // advance one word
                        matched[s.substr(left, word_len)]--;
                        cur_matched--;
                        left += word_len;
                    }
                } else {
                    // if current string cannot be found in dict, then cut all
                    // and reset all vars
                    matched.clear();
                    cur_matched = 0;
                    left = j + word_len;
                }
            }
        }
        
        return res;
    }
};
```





## 3. LeetCode 209 [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)

### (1) Sliding Window

```c++
// Time Complexity: O(n) (n = length of nums)
// Space Complexity: O(1)

class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int n = nums.size();
        int begin = 0, end = 0, cur_sum = 0, min_len = INT_MAX;
        while (end < n) {
            cur_sum += nums[end++];
            while (cur_sum >= s) {
                min_len = min(min_len, end - begin);
                cur_sum -= nums[begin++];
            }
        }
        return min_len == INT_MAX ? 0 : min_len;
    }
};
```



### (2) Prefix Sum + hashmap + TreeSet (similar to (3))

#### set::lower_bound

->An iterator to the the first element in the container which **is not considered to go before *val***

#### set::upper_bound

->An iterator to the the first element in the container which is considered to go after *val*, or [set::end](http://www.cplusplus.com/set::end) if no elements are considered to go after *val*.

```c++
// Time Complexity: O(nlogn) (n = length of nums)
// Space Complexity: O(n)

class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        unordered_map<int, int> map;
        set<int> sum_set;
        map[0] = -1;
        sum_set.insert(0);
        int minLen = INT_MAX;
        int sum = 0, n = nums.size();
        
        for (int i=0; i<n; i++) {
            sum += nums[i];
            if (map.count(sum-s)) {
                minLen = min(minLen, i-map[sum-s]);
            } else if (sum > s) {
                // lower_bound
                minLen = min(minLen, i-map[*(--sum_set.upper_bound(sum-s))]);
            }
            map[sum] = i;
            sum_set.insert(sum);
        }
        
        return minLen == INT_MAX ? 0 : minLen;
    }
};

// shorter
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        unordered_map<int, int> map;
        set<int> sum_set;
        map[0] = -1;
        sum_set.insert(0);
        int minLen = INT_MAX;
        int sum = 0, n = nums.size();
        
        for (int i=0; i<n; i++) {
            sum += nums[i];
            if (sum >= s) {
                // equal or lower_bound
                minLen = min(minLen, i-map[*(--sum_set.upper_bound(sum-s))]);
            }
            map[sum] = i;
            sum_set.insert(sum);
        }
        
        return minLen == INT_MAX ? 0 : minLen;
    }
};
```



### (3) Binary Search (O(nlogn))

```c++
// Time Complexity: O(nlogn) (n = length of nums)
// Space Complexity: O(n)

class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int minLen = INT_MAX;
        int n= nums.size();
        
        vector<int> sums(n+1, 0);
        for (int i=1; i<=n; i++) {
            sums[i] = nums[i-1] + sums[i-1]; // subarray(with length i)'s sum
        }
        
        for (int i=0; i<n; i++) {
            int end = bs(i+1, sums.size()-1, sums, sums[i]+s);
            if (end == -1) break;
            if (end - i < minLen) {
                minLen = end - i;
            }
        }
        
        return minLen == INT_MAX ? 0 : minLen;
    }
    
    // find the equal or higher element with smallest index
    int bs(int low, int high, vector<int> &sums, int target) {
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (sums[mid] >= target) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        
        return sums[low] >= target ? low : -1;
    }
};

// original
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int minLen = INT_MAX;
        int n= nums.size();
        
        vector<int> sums(n+1, 0);
        for (int i=1; i<=n; i++) {
            sums[i] = nums[i-1] + sums[i-1]; // subarray(with length i)'s sum
        }
        
        for (int i=0; i<=n; i++) {
            int end = bs(i+1, sums.size()-1, sums, sums[i]+s);
            if (end == sums.size()) break;
            if (end - i < minLen) {
                minLen = end - i;
            }
        }
        
        return minLen == INT_MAX ? 0 : minLen;
    }
    
    // find the equal or higher element with smallest index
    int bs(int low, int high, vector<int> &sums, int target) {
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (sums[mid] >= target) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        
        return low;
    }
};
```

[solution](https://leetcode.com/problems/minimum-size-subarray-sum/discuss/59090/4ms-O(n)-8ms-O(nlogn)-C%2B%2B)

[solution2](https://leetcode.com/problems/minimum-size-subarray-sum/discuss/59123/O(N)O(NLogN)-solutions-both-O(1)-space)



## 4. LeetCode 159 [Longest Substring with At Most Two Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/)

```c++
// Time Complexity: O(n) (n = length of s)
// Space Complexity: O(n) ?

class Solution {
public:
    int lengthOfLongestSubstringTwoDistinct(string s) {
        int n = s.length();
        unordered_map<char, int> map;
        int begin = 0, end = 0, cnt = 0, max_len = 0;
        while (end < n) {
            if (map[s[end++]]++ == 0) {
                cnt++;
            }
            if (cnt <= 2) {
                max_len = max(max_len, end - begin);
            } else {
                while (cnt > 2) {
                    if (map[s[begin++]]-- == 1) {
                        cnt--;
                    }
                }
            }
        }
        return max_len;
    }
};
```



## 5. LeetCode 683 [K Empty Slots](https://leetcode.com/problems/k-empty-slots/)

### Explanation

Use a window of length `k+2` to contain `k+2` slots.  Initially, set window's start position to 0, end position to k+1. Then, we check the slots from position 0, one by one. What we want is to check whether the flowers in the slots inside the window blossom later than the start slot and the end slot. If not, we need to move the window forward to i->i+k+1 (i is current visited position) because all flowers between start and i blossom later than start or end, while flower in position i blossom earlier than start or end, which means the window cannot be set between start and i. If i == end, it means the current window matches the requirement of there exists 2 flowers (start & end) in the status of blooming and the number of  flowers between them is k and they are not blooming. We update the ans now. Again, we reset the window in i->i+k+1.

```c++
// Time Complexity: O(n)
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



## 6. LeetCode 424 [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)

### Explanation

`right-start+1` = size of the current window
`maxChar` = largest count of a single, unique character in the current window

The main equation is: `right-start+1-maxChar`

When `right-start+1-maxChar` == 0, then then the window is filled with only one character
When `right-start+1-maxChar` > 0, then we have characters in the window that are NOT the character that occurs the most.  `right-start+1-maxChar`is equal to exactly the # of characters that are NOT the character that occurs the most in that window. Example: For a window "xxxyz",  `right-start+1-maxChar` would equal 2. (maxChar is 3 and there are 2 characters here, "y" and "z" that are not "x" in the window.)

We are allowed to have at most k replacements in the window, so when  `right-start+1-maxChar > k`, then there are more characters in the window than we can replace, and we need to shrink the window.

If we have window with "xxxy" and k = 1, that's fine because  `right-start+1-maxChar` = 1, which is not > k. maxLength gets updated to 4.

But if we then find a "z" after, like "xxxyz", then we need to shrink the window because now `right-start+1-maxChar`= 2, and 2 > 1. The window becomes "xxyz".

**maxCount may be invalid at some points, but this doesn't matter, because it was valid earlier in the string, and all that matters is finding the max window that occurred anywhere in the string**. Additionally, it will expand **if and only if** enough repeating characters appear in the window to make it expand. So whenever it expands, it's a valid expansion.

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)
class Solution {
public:
    int characterReplacement(string s, int k) {
        int len = s.length();
        if (len < 2 || len < k) return len;
        vector<int> map(26);
        int left = 0, right = 0, maxChar = 0;
        while (right < len) {
            if (map[s[right++]-'A']++ == maxChar) {
                maxChar = map[s[right-1]-'A'];
            }
            if (right - left - maxChar > k) {
                map[s[left++]-'A']--;
            }
        }
        return right - left;
    }
};
```

[solution](https://leetcode.com/problems/longest-repeating-character-replacement/discuss/91271/Java-12-lines-O(n)-sliding-window-solution-with-explanation)



## 7. LeetCode 340 [Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)

-General version of LeetCode 159

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int lengthOfLongestSubstringKDistinct(string s, int k) {
        int len = s.length();
        if (k >= len) return len;
        unordered_map<char, int> map;
        int left = 0, right = 0, maxLen = 0, cnt = 0;
        while (right < len) {
            if (map[s[right++]]++ == 0) {
                cnt++;
            }
            while (cnt > k) {
                if (map[s[left++]]-- == 1) {
                    cnt--;
                }
            }
            maxLen = max(maxLen, right-left);
        }
        return maxLen;
    }
};
```



## 8. LeetCode 727 [Minimum Window Subsequence](https://leetcode.com/problems/minimum-window-subsequence/)

### (1) Sliding Window

```c++
// Time Complexity: O(2mn) (m: len_S, n: len_T)
// Space Complexity: O(1)

class Solution {
public:
    string minWindow(string S, string T) {
        int len_S = S.length();
        int len_T = T.length();
        if (len_T > len_S) return "";
        if (len_T == len_S) return S == T ? S : "";
        
        int ps = 0, pt = 0, begin = 0, minL = INT_MAX;
        while (ps < len_S) {
            if (S[ps++] == T[pt]) {
                pt++;
            }
            if (pt == len_T) {
                int left = ps - 1;
                while (--pt >= 0) {
                    while (S[left--] != T[pt]) {}
                }
                if (ps - left - 1 < minL) {
                    minL = ps - left - 1;
                    begin = left + 1;
                }
                pt = 0;
                ps = left + 2;
            }
        }
        return minL == INT_MAX ? "" : S.substr(begin, minL);
    }
};
```

[java-solution](https://leetcode.com/problems/minimum-window-subsequence/discuss/109356/JAVA-two-pointer-solution-(12ms-beat-100)-with-explaination)

[solution-explanation-in-comment](https://leetcode.com/problems/minimum-window-subsequence/discuss/109355/C%2B%2B-AC-16ms-O(1)-space-not-DP)



### (2) DP

#### Explanation

dp(i, j) stores the "largest" starting index (make the length smallest) of the valid substring W of S[0, i] such that T[0, j] is a subsequence of W. Otherwise, dp(i, j) = -1; So dp(i, j) would be: 

if S[i] == T[j], this means we could borrow the start index from dp(i-1, j-1) to make the current substring valid;
else, we only need to borrow the start index from dp(i-1, j) which could either exist or not.

Finally, go through the last row to find the substring with min length and appears first.

```c++
// Time Complexity: O(mn)
// Space Complexity: O(mn)

class Solution {
public:
    string minWindow(string S, string T) {
        int len_S = S.length();
        int len_T = T.length();
        
        // initialize all value to -1
        vector<vector<int>> dp(len_S, vector<int>(len_T, -1));
        
        // initialize the first col
        for (int i=0; i<len_S; i++) {
            if (S[i] == T[0]) {
                dp[i][0] = i;
            } else {
                if (i != 0) {
                    dp[i][0] = dp[i-1][0];
                }
            }
        }
        
        // calculate dp[][]
        for (int i=1; i<len_S; i++) {
            for (int j=1; j<len_T; j++) {
                if (S[i] == T[j]) {
                    dp[i][j] = dp[i-1][j-1];
                } else {
                    dp[i][j] = dp[i-1][j];
                }
            }
        }
        
        // Finally, go through the last col to find the substring with min length and appears first.
        int begin = -1, min_len = INT_MAX;
        for (int i=0; i<len_S; i++) {
            int index = dp[i][len_T-1];
            if (index != -1) {
                if (i + 1 - index < min_len) {
                    begin = index;
                    min_len = i + 1 - index;
                }
            }
        }
        return begin == -1 ? "" : S.substr(begin, min_len);
    }
};
```

[dp-solution](https://leetcode.com/problems/minimum-window-subsequence/discuss/109362/Java-Super-Easy-DP-Solution-(O(mn))

[dp-solution-2](https://leetcode.com/problems/minimum-window-subsequence/discuss/109358/C%2B%2B-DP-with-explanation-O(ST)-53ms)

[mypost](https://leetcode.com/problems/minimum-window-subsequence/discuss/185659/Easy-to-understand-C%2B%2B-DP-Solution-O(mn))



## 9. LeetCode 713 [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        int n = nums.size();
        //if (k == 0) { return 0;}
        int left = 0, right = 0, cur_product = 1, cnt = 0;
        while (right < n) {
            cur_product *= nums[right++];
            while (left < right && cur_product >= k) {
                // make sure left < right
                cur_product /= nums[left++];
            }
            cnt += (right - left);
        }
        
        return cnt;
    }
};
```



## 10. LeetCode 904 [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)

### Same as LeetCode 159

### (1) Sliding Window

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    int totalFruit(vector<int>& tree) {
        int n = (int) tree.size();
        if (n < 3) return n;
        unordered_map<int, int> fruits;
        int left = 0, right = 0, cnt = 0, maxF = 0;
        while (right < n) {
            if (fruits[tree[right++]]++ == 0) {
                cnt++;
            }
            while (cnt > 2) {
                if (fruits[tree[left++]]-- == 1) {
                    cnt--;
                }
            }
            maxF = max(maxF, right - left);
        }
        return maxF;
    }
};
```



### (2) Other Solution

```c++
class Solution {
public:
    int totalFruit(vector<int>& tree) {
        if (tree.size() <= 2) {
            return tree.size();
        }
        
        int first_type = -1, second_type = -1, cur_cnt = 0, total = 0, second_type_cnt = 0;
        for (const auto &type : tree) {
            if (type == first_type || type == second_type) {
                cur_cnt++;
                second_type_cnt = (type == second_type) ? second_type_cnt + 1 : 1;
            }
            else {
                cur_cnt = second_type_cnt + 1;
                second_type_cnt = 1;
            }
            
            if (type != second_type) {
                first_type = second_type;
                second_type = type;
            }
            
            total = max(total, cur_cnt);
        }
        
        return total;
    }
};
```



## 11. LeetCode 567 [Permutation in String](https://leetcode.com/problems/permutation-in-string/)

```c++
// Time Complexity: O(n) n: length of l2
// Space Complexity: O(1)

class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        int l1 = s1.length();
        int l2 = s2.length();
        if (l1 > l2) return false;
        vector<int> mp(26, 0);
        for (auto &c : s1) {
            mp[c - 'a']++;
        }
        
        int l = 0, r = 0, cnt = l1;
        
        while (r < l2) {
            if (mp[s2[r++] - 'a']-- > 0) {
                cnt--;
                if (cnt == 0) return true;
            } else {
                while (mp[s2[l++] - 'a']++ != -1) {
                    cnt++;
                }
            }
        }
        
        return false;
    }
};
```



## 12. LeetCode 438 [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)

```c++
// Time Complexity: O(n) n: length of string s
// Space Complexity: O(1)

class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        vector<int> res;
        int ls = s.length();
        int lp = p.length();
        if (lp > ls) return {};
        
        vector<int> mp(26); // hashmap
        for (auto &c : p) {
            mp[c - 'a']++;
        }
        
        int cnt = lp, l = 0, r = 0;
        
        while (r < ls) {
            if (mp[s[r++] - 'a']-- > 0) {
                // if current character is in p and not reach its limit
                // move right pointer and expand the window
                // decrease cnt variable
                cnt--;
            } else {
                // if current character is not in p or reach its limit in p
                // now the substring in the window is invalid
                // we try to increase left pointer to contract the window
                while (mp[s[l++] - 'a']++ >= 0) {
                    cnt++;
                }
            }
            // if cnt == 0, now the substring in the window match our requirement
            if (cnt == 0) {
                res.push_back(l);
            }
        }
        
        return res;
    }
};
```



# <2> General String Problem

## 1. LeetCode 93 [Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/)

```c++
class Solution {
public:
    vector<string> restoreIpAddresses(string s) {
        int n = s.length();
        if (n > 12 || n < 4) return vector<string>();
        vector<string> ips;
        
        for (int i=0; i<3 && i<n-3; i++) {
            for (int j=i+1; j<i+4 && j<n-2; j++) {
                for (int k=j+1; k<j+4 && k<n-1; k++) {
                    if (n-k-1 > 3) continue;
                    string s1 = s.substr(0, i+1);
                    string s2 = s.substr(i+1, j-i);
                    string s3 = s.substr(j+1, k-j);
                    string s4 = s.substr(k+1);
                    if (isValid(s1) && isValid(s2) && isValid(s3) && isValid(s4)) {
                        ips.push_back(s1+'.'+s2+'.'+s3+'.'+s4);
                    }
                }
            }
        }
        
        return ips;
    }
    
    bool isValid(string s) {
        if (stoi(s) > 255 || (s.length() > 1 && s[0] == '0')) {
            return false;
        }
        return true;
    }
};
```



## 2. LeetCode 482 [License Key Formatting](https://leetcode.com/problems/license-key-formatting/)

### (1) Preprocess and Split

```c++
class Solution {
public:
    string licenseKeyFormatting(string S, int K) {
        string ps = "";
        
        for (const auto &c : S) {
            if (c != '-') {
                ps += toupper(c);
            }
        }
        
        int n = ps.length();
        int parts = n / K;
        int remainder = n % K;
        
        string ans = "";
        if (remainder != 0) {
            ans += ps.substr(0, remainder);
            ans += '-';
        }
        
        for (int i=0; i<parts; i++) {
            ans += ps.substr(remainder+i*K, K);
            ans += '-';
        }
        
        ans.pop_back();
        
        return ans;
    }
};
```



### (2) One pass processing

```c++
class Solution {
public:
    string licenseKeyFormatting(string S, int K) {
        string ans = "";
        string part = "";
        
        int i = S.length() - 1;
        
        while (i >= 0) {
            part.clear();
            
            while (i >= 0 && part.length() < K) {
                if (S[i] != '-') {
                    part += toupper(S[i]);
                }
                i--;
            }
            // corner case: "--a-a-a-a--"
            if (part.size()) {
                if (ans.size()) {
                    ans += '-';
                }
                ans += part;
            }
        }
        reverse(ans.begin(), ans.end());
        
        return ans;
    }
};
```



## 3. LeetCode 214 [Shortest Palindrome](https://leetcode.com/problems/shortest-palindrome/)

### (1) O(n^2)

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n)

class Solution {
public:
    string shortestPalindrome(string s) {
        string r = s;
        reverse(r.begin(), r.end());
        int n = s.length();
        
        for (int i=0; i<n; i++) {
            if (r.substr(i) == s.substr(0, n-i)) {
                return r + s.substr(n-i);
            }
        }
        
        return r + s;
    }
};
```



### (2) KMP

[youtube](https://www.youtube.com/watch?v=GTJr8OvyEVQ&feature=youtu.be)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    string shortestPalindrome(string s) {
        string r = s;
        reverse(r.begin(), r.end());
        string t = s + '#' + r; // add "#" to force match begin at the first index of r
        int len = t.length();
        
        vector<int> p(len, 0);
        for (int i=1; i<len; i++) {
            int j = p[i-1];
            while (j > 0 && t[i] != t[j]) {
                j = p[j-1];
            }
            p[i] = (j + (t[i] == t[j]));
        }
        
        return r.substr(0, r.length() - p[len-1]) + s;
    }
};
```

[leetcode-solution](https://leetcode.com/problems/shortest-palindrome/discuss/60141/C%2B%2B-8-ms-KMP-based-O(n)-time-and-O(n)-memory-solution)

[solution2](https://leetcode.com/problems/shortest-palindrome/discuss/60113/Clean-KMP-solution-with-super-detailed-explanation)

[KMP](http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html)



## 4. LeetCode 5 [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)

### (1) Expand the palindrome as possible

#### Two-directions 

#### [reference](https://leetcode.com/problems/longest-palindromic-substring/discuss/2928/Very-simple-clean-java-solution)

[geeksforgeeks](https://www.geeksforgeeks.org/longest-palindromic-substring-set-2/)

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(1)
// worse case: "aaaaaaaaaaaaaaaa"

class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.length();
        if (n < 2) return s;
        int begin = 0;
        int max_len = 1;
        
        for (int i=0; i<n; i++) {
            expandPalindrome(s, i, i, begin, max_len);
            expandPalindrome(s, i, i+1, begin, max_len);
        }
        
        return s.substr(begin, max_len);
    }
    
    void expandPalindrome(string &s, int l, int r, int &begin, int &max_len) {
        while (l >= 0 && r < s.length() && s[l] == s[r]) {
            l--;
            r++;
        }
        
        // the substring between r and l is palindrome
        if (r - l - 1 > max_len) {
            begin = l + 1;
            max_len = r - l - 1;
        }
    }
};
```

#### Left-Expansion

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(1)
// worse case: "aaaaaaaaaaaaaaaa"

class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.length();
        if (n < 2) return s;
        int begin = 0;
        int max_len = 1;
        
        for (int i=0; i<n; i++) {
            if (isPalindrome(s, i-max_len-1, i)) {
                begin = i - max_len - 1;
                max_len += 2;
            } else if (isPalindrome(s, i-max_len, i)) {
                begin = i - max_len;
                max_len += 1;
            }
        }
        
        return s.substr(begin, max_len);
    }
    
    bool isPalindrome(string &s, int l, int r) {
        if (l < 0) return false;
        while (l < r) {
            if (s[l++] != s[r--]) {
                return false;
            }
        }
        return true;
    }
};
```



### (2) DP

`dp(i, j)` represents whether `s(i ... j)` can form a palindromic substring, `dp(i, j)` is true when `s(i)` equals to `s(j)` and `s(i+1 ... j-1)` is a palindromic substring. When we found a palindrome, check if it's the longest one. Time complexity O(n^2).

```c++
// Time Complexity: O(n^2)
// Space Complexity: O(n^2)

class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.length();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        int begin = 0;
        int max_len = 1;
        
        for (int j=0; j<n; j++) {
            for (int i=j; i>=0; i--) {
                // j - i < 3 means: "a" || "aa"
                dp[i][j] = (s[i] == s[j]) && (j - i < 3 || dp[i+1][j-1]);
                
                if (dp[i][j] && j-i+1 > max_len) {
                    begin = i;
                    max_len = j - i + 1;
                }
            }
        }
        
        return s.substr(begin, max_len);
    }
};
```

[geeksforgeeks](https://www.geeksforgeeks.org/longest-palindrome-substring-set-1/)

[leetcode-solution](https://leetcode.com/problems/longest-palindromic-substring/discuss/2921/Share-my-Java-solution-using-dynamic-programming)



### (3) Manacher (马拉车)

[csdn](https://blog.csdn.net/hk2291976/article/details/51107886)

[segmentfault](https://segmentfault.com/a/1190000008484167)

```c++
// Time Complexity: O(n)
// Space Complexity: O(n)

class Solution {
public:
    string longestPalindrome(string s) {
        if (s.length() <= 1) { return s;}
        int length = s.length();
        
        // build a new string T
        int new_len = 2 * length + 1;
        string new_str = string(new_len, '#');
        for (int i=0; i<length; i++) {
            new_str[2*i+1] = s[i];
        }
        // avoid overflow
        new_str = '$' + new_str;
        
        // right_bound: the largest index among palindrome substring we already get
        // pos: the center the substring above
        // max_pos: record the longest palindrome substring's center
        int right_bound = 0, pos = 0, max_pos = 0;
        vector<int> Len(new_len, 1);
        for (int i=1; i<new_len; i++) {
            if (i < right_bound) {
                Len[i] = min(right_bound-i, Len[2*pos-i]);
            }
            while (new_str[i+Len[i]] == new_str[i-Len[i]]) {
                // already avoid overflow
                Len[i]++;
            }
            if (i+Len[i] > right_bound) {
                right_bound = i + Len[i];
                pos = i;
            }
            max_pos = Len[i] > Len[max_pos] ? i : max_pos;
        }
        return s.substr((max_pos - Len[max_pos]) / 2, Len[max_pos] - 1);
    }
};
```



## 5. LeetCode 936 [Stamping The Sequence](https://leetcode.com/problems/stamping-the-sequence/)

### Explanation

What I basiclly did here is trying to reverse the whole operations.
The operation token later will be more apparent than the ot

peration token before. The letters which stamped later will cover the letters stamped before and we really don't care about the letters which are covered.

```latex
 *  *  *  *  *  *  *
 *  *  * |a  b  c  a|
|a  b  c  a| b  c  a
 a |a  b  c  a| c  a
```

We just try to match the stamp with the target. Since we do not care about the letters which are coverd by others, so we can apply a `*` match any letters. For example:

aabcaca -> a * * * * ca -> * * * * * ca->* * * * * * *



### Codes:

```c++
// Time Complexity: O(mn) m=len_s, n=len_t
// Space Complexity: O(n)

class Solution {
public:
    vector<int> movesToStamp(string stamp, string target) {
        vector<int> stamp_indexes;
        int len_t = target.length();
        string stars(len_t, '*');
        
        while (target != stars) {
            int index = match(stamp, target);
            if (index == len_t) return {};
            stamp_indexes.push_back(index);
        }
        reverse(stamp_indexes.begin(), stamp_indexes.end());
        return stamp_indexes;
    }
    
    int match(string &stamp, string &target) {
        int len_t = target.length();
        int len_s = stamp.length();
        
        for (int left=0; left<len_t; left++) {
            int i = left; // iterator for target
            int j = 0; // iterator for stamp
            
            bool find = false;
            while (i < len_t && j < len_s && (target[i] == '*' || target[i] == stamp[j])) {
                if (target[i] == stamp[j]) find = true;
                i++;
                j++;
            }
            
            if (j == len_s && find) {
                for (int k=left; k<left+len_s; k++) {
                    target[k] = '*';
                }
                return left;
            }
        }
        
        return len_t;
    }
};
```

[solution](https://leetcode.com/problems/stamping-the-sequence/discuss/189258/C%2B%2B-Reverse-Operation-30-ms-better-than-DFS)



## 6. LeetCode 115 [Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)

### DP

```c++
// Time Complexity: O(mn) m=len_s, n=len_t
// Space Complexity: O(mn)

class Solution {
public:
    int numDistinct(string s, string t) {
        int len_s = s.length();
        int len_t = t.length();
        vector<vector<int>> dp(len_s+1, vector<int>(len_t+1, 0));
        
        for (int i=0; i<=len_s; i++) {
            dp[i][0] = 1;
        }
        
        for (int i=1; i<=len_s; i++) {
            for (int j=1; j<=len_t; j++) {
                if (s[i-1] != t[j-1]) {
                    dp[i][j] = dp[i-1][j];
                } else {
                    dp[i][j] = dp[i-1][j] + dp[i-1][j-1];
                }
            }
        }
        
        return dp[len_s][len_t];
    }
};
```

[solution](https://leetcode.com/problems/distinct-subsequences/discuss/37316/7-10-lines-C%2B%2B-Solutions-with-Detailed-Explanations-(O(m*n)-time-and-O(m)-space)



## 7. LeetCode 10 [Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)

### DP

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        int len1 = s.length();
        int len2 = p.length();
        vector<vector<bool>> match(len1+1, vector<bool>(len2+1, false));
        match[0][0] = true;
        for(int i=0; i<=len1; i++) {
            for(int j=1; j<=len2; j++) {
                if(p[j-1] == '*') {
                    match[i][j] = (j > 1 && match[i][j-2]) || ((i > 0 && (p[j-2] == s[i-1] || p[j-2] == '.')  && match[i-1][j]));
                } else {
                    match[i][j] = i > 0 && (s[i-1] == p[j-1] || p[j-1] == '.') && match[i-1][j-1];
                }
            }
        }
        return match[len1][len2];
    }
};
```

[solution](https://leetcode.com/problems/regular-expression-matching/discuss/5684/9-lines-16ms-C%2B%2B-DP-Solutions-with-Explanations)



## 8. LeetCode 97 [Interleaving String](https://leetcode.com/problems/interleaving-string/)

### (1) DP

`dp[i][j]`: whether the `first i+j elements of s3` is the interleaving string of `first i elements of s1` and `first j elements of s2`. So, the transition formula is:
`dp[i][j] = (dp[i-1][j] && s3[i+j-1] == s1[i-1]) || (dp[i][j-1] && s2[j-1] == s3[i+j-1])`

Initialization:

`dp[0][0] = true`

`dp[0][j] = dp[0][j-1] && (s2[j-1] == s3[j-1])`

`dp[i][0] = dp[i-1][0] && (s1[i-1] == s3[i-1])`

```c++
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int m = s1.length();
        int n = s2.length();
        if (m + n != s3.length()) return false;
        
        vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
        
        // initialization
        dp[0][0] = 1;
        for (int i=1; i<=m; i++) {
            dp[i][0] = dp[i-1][0] && (s1[i-1] == s3[i-1]);
        }
        
        for (int j=1; j<=n; j++) {
            dp[0][j] = dp[0][j-1] && (s2[j-1] == s3[j-1]);
        }
        
        for (int i=1; i<=m; i++) {
            for (int j=1; j<=n; j++) {
                dp[i][j] = (dp[i-1][j] && (s1[i-1] == s3[i+j-1])) || (dp[i][j-1] && (s2[j-1] == s3[i+j-1]));
            }
        }
        
        return dp[m][n];
    }
};
```

[solution](https://leetcode.com/problems/interleaving-string/discuss/31879/My-DP-solution-in-C%2B%2B)



### (2) BFS

kind of finding path in a binary tree!!!

```c++
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int m = s1.length();
        int n = s2.length();
        if (m + n != s3.length()) return false;
        
        vector<int> visited((m+1)*(n+1), 0); // use 1-D vector to simulate the matrix
        queue<pair<int, int>> q; // store the candidate path points
        q.push({0, 0});
        
        while (!q.empty()) {
            auto p = q.front();
            q.pop();
            if (p.first == m && p.second == n) return true;
            
            // move right
            if (p.second < n && s2[p.second] == s3[p.first+p.second] && !visited[p.first * (n+1) + p.second + 1]) {
                visited[p.first*(n+1) + p.second + 1] = true;
                q.push({p.first, p.second+1});
            }
            
            // move down
            if (p.first < m && s1[p.first] == s3[p.first+p.second] && !visited[(p.first + 1) * (n+1) + p.second]) {
                visited[(p.first + 1) * (n+1) + p.second] = true;
                q.push({p.first+1, p.second});
            }
        }
        
        return false;
    }
};
```

[solution](https://leetcode.com/problems/interleaving-string/discuss/31948/8ms-C%2B%2B-solution-using-BFS-with-explanation)



## 9. LeetCode 68 [Text Justification](https://leetcode.com/problems/text-justification/)

```c++
class Solution {
public:
    vector<string> fullJustify(vector<string>& words, int maxWidth) {
        vector<string> res;
        int n = words.size();
        for (int i=0, words_cnt, words_len; i<n; i += words_cnt) {
            words_cnt = 0;
            words_len = 0;
            for (; i + words_cnt < n && words_len + words[i + words_cnt].length() <= maxWidth - words_cnt; words_cnt++) {
                words_len += words[i + words_cnt].length();
            }
            
            string line = words[i];
            for (int j=1; j<words_cnt; j++) {
                if (i + words_cnt == n) line += ' ';
                else {
                    line += string((maxWidth - words_len) / (words_cnt - 1) + 
                                   (j <= (maxWidth - words_len) % (words_cnt - 1)), ' ');
                }
                line += words[i + j];
            }
            
            line += string(maxWidth - line.length(), ' ');
            res.push_back(line);
        }
        return res;
    }
};
```

[solution](https://leetcode.com/problems/text-justification/discuss/24873/Share-my-concise-c%2B%2B-solution-less-than-20-lines)



## 10. LeetCode 940 [Distinct Subsequences II](https://leetcode.com/problems/distinct-subsequences-ii/)

### DP

[geeksforgeeks](https://www.geeksforgeeks.org/count-distinct-subsequences/)

```c++
class Solution {
public:
    int distinctSubseqII(string S) {
        int n = S.length();
        const int mod = 1000000007;
        unordered_map<char, int> mp;
        
        vector<long> dp(n+1, 0);
        dp[0] = 1;
        
        for (int i=1; i<=n; i++) {
            dp[i] = 2 * dp[i-1];
            
            if (mp.count(S[i-1])) {
                dp[i] = (dp[i] - dp[mp[S[i-1]]] + mod) % mod;
            }
            mp[S[i-1]] = i-1;
        }
        
        return dp[n]-1;
    }
};

// improved
class Solution {
public:
    int distinctSubseqII(string S) {
        int n = S.length();
        const int mod = 1000000007;
        unordered_map<char, int> mp;
        
        vector<int> dp(n+1, 0);
        dp[0] = 1;
        
        for (int i=1; i<=n; i++) {
            dp[i] = (2 * dp[i-1]) % mod;
            
            if (mp.count(S[i-1])) {
                dp[i] = (dp[i] - dp[mp[S[i-1]]] + mod) % mod;
            }
            mp[S[i-1]] = i-1;
        }
        
        return dp[n]-1;
    }
};
```



## 11. LeetCode 44 [Wildcard Matching](https://leetcode.com/problems/wildcard-matching/)

### (1) Greedy

### Analysis:

For each element in s

If *s==*p or *p == ? which means this is a match, then goes to next element s++ p++.

If p=='*', this is also a match, but one or many chars may be available, so let us save this *'s position and the matched s position.

If not match, then we check if there is a * previously showed up,

​       if there is no *,  return false;

​       if there is an *,  we set current p to the next element of *, and set current s to the next saved s position.

e.g.

abed

?b*d**

a=?, go on, b=b, go on,

e=*, save * position star=3, save s position ss = 3, p++

e!=d,  check if there was a *, yes, ss++, s=ss; p=star+1

d=d, go on, meet the end.

check the rest element in p, if all are *, true, else false;

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        int prevStar = -1; // last position of '*'
        int ss = -1; // last saved position of s (candidate for matching the '*')
        int si = 0; // use for s pointer
        int pi = 0; // use for p pointer
        int ls = s.length();
        int lp = p.length();
        
        while (si < ls) {
            if (s[si] == p[pi] || p[pi] == '?') {
                si++;
                pi++;
            } else if (p[pi] == '*') {
                prevStar = pi++;
                ss = si;
            } else if (prevStar != -1) {
                pi = prevStar + 1;
                si = ++ss;
            } else {
                return false;
            }
        }
        
        while (pi < lp && p[pi] == '*') pi++;
        return pi == lp;
    }
};
```

[solution](http://yucoding.blogspot.com/2013/02/leetcode-question-123-wildcard-matching.html)



### (2) DP

Let's briefly summarize the idea of DP. We define the state `P[i][j]` to be whether `s[0..i)` matches `p[0..j)`. The state equations are as follows:

1. `P[i][j] = P[i - 1][j - 1] && (s[i - 1] == p[j - 1] || p[j - 1] == '?')`, if `p[j - 1] != '*'`;
2. `P[i][j] = P[i][j - 1] || P[i - 1][j]`, if `p[j - 1] == '*'`.

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        int lenS = s.length();
        int lenP = p.length();
        vector<vector<bool>> d(lenS+1, vector<bool>(lenP+1, false));
        d[0][0] = true;
        for(int i=1; i<=lenP; i++) {
            d[0][i] = (p[i-1] == '*') ? d[0][i-1] : false;
        }
        
        for(int i=1; i<=lenS; i++) {
            for(int j=1; j<=lenP; j++) {
                if(p[j-1] == '*') {
                    d[i][j] = d[i-1][j] || d[i][j-1];
                } else {
                    d[i][j] = (s[i-1] == p[j-1] || p[j-1] == '?') && d[i-1][j-1];
                }
            }
        }
        
        return d[lenS][lenP];
    }
};
```

[solution](https://leetcode.com/problems/wildcard-matching/discuss/17833/Accepted-C%2B%2B-DP-Solution-with-a-Trick)



## 12. LeetCode 49 [Group Anagrams](https://leetcode.com/problems/group-anagrams/)

### Hash Table

### (1) Categorize by Sorted String

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
```



### (2) Categorize by Count String

```c++
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



## 13. LeetCode 848 [Shifting Letters](https://leetcode.com/problems/shifting-letters/)

```c++
class Solution {
public:
    string shiftingLetters(string S, vector<int>& shifts) {
        for (int i = shifts.size() - 2; i >= 0; i--)
            shifts[i] = (shifts[i] + shifts[i + 1]) % 26;
        for (int i = 0; i < shifts.size(); i++)
            S[i] = (S[i] - 'a' + shifts[i]) % 26 + 'a';
        return S;
    }
};
```



## 14. LeetCode 966 [Vowel Spellchecker](https://leetcode.com/problems/vowel-spellchecker/)

```c++
class Solution {
public:
    vector<string> spellchecker(vector<string>& wordlist, vector<string>& queries) {
        set<string> words(wordlist.begin(), wordlist.end());
        unordered_map<string, string> cap, vowel;
        for (string w : wordlist) {
            string lower = tolow(w), devowel = todev(w);
            cap.insert({lower, w});
            vowel.insert({devowel, w});
        }
        for (int i = 0; i < queries.size(); ++i) {
            if (words.count(queries[i])) continue;
            string lower = tolow(queries[i]), devowel = todev(queries[i]);
            if (cap.count(lower)) {
                queries[i] = cap[lower];
            } else if (vowel.count(devowel)) {
                queries[i] = vowel[devowel];
            } else {
                queries[i] = "";
            }
        }
        return queries;
    }
    
private:
    string tolow(string w) {
        for (auto & c: w)
            c = tolower(c);
        return w;
    }

    string todev(string w) {
        w = tolow(w);
        for (auto & c: w)
            if (c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u')
                c = '#';
        return w;
    }
};
```

[solution-lee](https://leetcode.com/problems/vowel-spellchecker/discuss/211189/JavaC%2B%2BPython-Two-HashMap)



## 15. LeetCode 758 [Bold Words in String](https://leetcode.com/problems/bold-words-in-string/)

### Same as 616

```c++
class Solution {
public:
    string boldWords(vector<string>& words, string S) {
        // the mask
        vector<bool> mask(S.size(), false);

        for (const auto& str : words) {
            // find the occurence and update the mask
            size_t pos = 0;
            while ((pos = S.find(str, pos)) != string::npos) {
                for (int i = 0; i < str.size(); ++i) {
                    mask[pos + i] = true;
                }

                pos += 1;
            }
            
        }

        string result;
        bool status = 0;   // 0 means we are outside of the mask region, 1 means we are inside.
        for (int i = 0; i < mask.size(); ++i) {

            if (status == 0) {
                if (mask[i]) {
                    result += "<b>";
                    status = 1;
                }

            } else if (status == 1) {
                if (!mask[i]) {
                    result += "</b>";
                    status = 0;
                }
            }

            result += S[i];
        }

        if (status == 1) {
            result += "</b>";
        }

        return result;
    }
};
```



## 16. LeetCode 890 [Find and Replace Pattern](https://leetcode.com/problems/find-and-replace-pattern/)

```c++
class Solution {
public:
    vector<string> findAndReplacePattern(vector<string>& words, string pattern) {
        vector<string> res;
        for (string word : words) if (F(word) == F(pattern)) res.push_back(word);
        return res;
    }

private:
    string F(string w) {
        unordered_map<char, int> m;
        for (char c : w) if (!m.count(c)) m[c] = m.size();
        for (int i = 0; i < w.length(); ++i) w[i] = 'a' + m[w[i]];
        return w;
    }
};
```



## 17. LeetCode 833 [Find And Replace in String](https://leetcode.com/problems/find-and-replace-in-string/)

### (1) Replace S from right to left

```c++
class Solution {
public:
    string findReplaceString(string S, vector<int>& indexes, vector<string>& sources, vector<string>& targets) {
        vector<pair<int, int>> sorted;
        for (int i = 0 ; i < indexes.size(); i++) sorted.push_back({ indexes[i], i });
        sort(sorted.rbegin(), sorted.rend());
        for (auto ind : sorted) {
            int i = ind.first;
            string s = sources[ind.second], t = targets[ind.second];
            if (S.substr(i, s.length()) == s) S = S.substr(0, i) + t + S.substr(i + s.length());
        }
        return S;
    }
};
```

[solution](https://leetcode.com/problems/find-and-replace-in-string/discuss/130587/C%2B%2BJavaPython-Replace-S-from-right-to-left)



### (2) Bucket Sort

```c++
class Solution {
public:
    string findReplaceString(string S, vector<int>& indexes, vector<string>& sources, vector<string>& targets) {
        vector<pair<int, string>> m(S.size());
        
        for (int i = 0; i < indexes.size(); ++i) {
            if (S.find(sources[i], indexes[i]) == indexes[i]) m[indexes[i]] = {sources[i].size(), targets[i]};
        }
        
        for (int i = S.size() - 1; i >= 0; i--) {
            S.replace(i, m[i].first, m[i].second);
        }
        
        return S;
    }
};
```

[solution](https://leetcode.com/problems/find-and-replace-in-string/discuss/130577/C%2B%2B-5-lines-6-ms-bucket-sort-O(n))



























