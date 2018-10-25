# String

## *<1>Using window to get maximum or minimum substring*

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



## 6. LeetCode 424

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



## 8. LeetCode 727 

### (1) Sliding Window

```c++
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



## 9. LeetCode 567

```c++

```





































