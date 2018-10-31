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



## 11. LeetCode 567

```c++

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























