# String

## *<1>Using window to get maximum or minimum substring*

## 1. LeetCode 76 [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

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

```c++
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

### Explanation:

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







































