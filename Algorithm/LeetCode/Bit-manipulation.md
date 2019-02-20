[Bit-Manipulation-Summary](https://leetcode.com/problems/sum-of-two-integers/discuss/84278/A-summary%3A-how-to-use-bit-manipulation-to-solve-problems-easily-and-efficiently)



## 1. LeetCode 136 [Single Number](https://leetcode.com/problems/single-number/)

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int size = nums.size();
        int result = 0;
        for(int i=0; i<size; i++) {
            result = result xor nums[i];
        }
        return result;
    }
};
```



## 2. LeetCode 137 [Single Number II](https://leetcode.com/problems/single-number-ii/)

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int size = nums.size();
        int a = 0, b = 0;
        for(int i=0; i<size; i++) {
            a = (a ^ nums[i]) & ~b;
            b = (b ^ nums[i]) & ~a;
        }
        return a;
    }
};
```



## 3. LeetCode 260 [Single Number III](https://leetcode.com/problems/single-number-iii/)

```c++
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int size = nums.size();
        int a = 0;
        for(int i=0; i<size; i++) {
            a = a ^ nums[i];
        }
        // cout << a << endl;
        a &= (-a);
        // cout << a << endl;
        vector<int> result = {0, 0};
        for(int i=0; i<size; i++) {
            if((nums[i] & a) == 0) {
                result[0] ^= nums[i];
            } else {
                result[1] ^= nums[i];
            }
        }
        return result;
    }
};
```



## 4. LeetCode 191 [Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int cnt = 0;
        for(int i=0; i<32; i++) {
            if(n & 1) cnt++;
            n >>= 1;
        }
        return cnt;
    }
};
```



## 5. LeetCode 78 [Subsets](https://leetcode.com/problems/subsets/)

```c++
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        int n = (int)nums.size();
        int m = (int)pow(2,n);
        vector<vector<int>> subs(m, vector<int>());
        for(int i=0,q=1; i<n; i++,q<<=1) {
            for(int j=q; j<m; j+=q) {
                for(int k=0; k<q; k++) {
                    subs[j++].push_back(nums[i]);
                }
            }
        }
        return subs;
    }
};
```



## 6. LeetCode 190 [Reverse Bits](https://leetcode.com/problems/reverse-bits/)

```c++
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        n = (n >> 16) | (n << 16);
        n = ((n & 0xff00ff00) >> 8) | ((n & 0x00ff00ff) << 8);
        n = ((n & 0xf0f0f0f0) >> 4) | ((n & 0x0f0f0f0f) << 4);
        n = ((n & 0xcccccccc) >> 2) | ((n & 0x33333333) << 2);
        n = ((n & 0xaaaaaaaa) >> 1) | ((n & 0x55555555) << 1);
        return n;
    }
};
```



## 7. LeetCode 338 [Counting Bits](https://leetcode.com/problems/counting-bits/)

```c++
class Solution {
public:
    vector<int> countBits(int num) {
        vector<int> dp(num+1, 0);
        int offset = 1;
        for (int i=1; i<=num; i++) {
            if (i == offset * 2) {
                offset *= 2;
            }
            dp[i] = dp[i-offset] + 1;
        }
        return dp;
    }
};
```



## 8. LeetCode 401 [Binary Watch](https://leetcode.com/problems/binary-watch/)

```c++
class Solution {
public:
    vector<string> readBinaryWatch(int num) {
        vector<string> result;
        for(int h = 0; h < 12; h++) {
            for(int m = 0; m < 60; m++) {
                if(bitset<10>(h << 6 | m).count() == num) {
                    result.emplace_back(to_string(h) + (m < 10 ? ":0" : ":") + to_string(m));
                }
            }
        } 
        return result;
    }
};
```



## 9. LeetCode 461 [Hamming Distance](https://leetcode.com/problems/hamming-distance/)

```c++
// solution 1
class Solution {
public:
    int hammingDistance(int x, int y) {
        int cnt = 0;
        while(x > 0 || y > 0) {
            (x & 1) != (y & 1) ? cnt++ : 0;
            x >>= 1;
            y >>= 1;
        }
        return cnt;
    }
};

// solution 2
class Solution {
public:
    int hammingDistance(int x, int y) {
        int cnt = 0;
        int z = x ^ y;
        while(z > 0) {
            (z & 1) ? cnt++ : 0;
            z >>= 1;
        }
        return cnt;
    }
};
```



## 10. LeetCode 477 [Total Hamming Distance](https://leetcode.com/problems/total-hamming-distance/)

```c++
class Solution {
public:
    int hammingDistance(int x, int y) {
        int cnt = 0;
        int z = x ^ y;
        while(z > 0) {
            (z & 1) ? cnt++ : 0;
            z >>= 1;
        }
        return cnt;
        /*
        while(x > 0 || y > 0) {
            (x & 1) != (y & 1) ? cnt++ : 0;
            x >>= 1;
            y >>= 1;
        }
        return cnt;*/
    }
};
```



## 11. LeetCode 693 [Binary Number with Alternating Bits](https://leetcode.com/problems/binary-number-with-alternating-bits/)

```c++
class Solution {
public:
    bool hasAlternatingBits(int n) {
        vector<int> v;
        while(n) {
            v.push_back(n % 2);
            n >>= 1;
        }
        for(int i=0; i<v.size()-1; i++) {
            if(v[i] == v[i+1]) return false;
        }
        return true;
    }
};
```



## 12. LeetCode 201 [Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/)

```c++
class Solution {
public:
    int rangeBitwiseAnd(int m, int n) {
        return (n > m) ? (rangeBitwiseAnd(m/2, n/2) << 1) : m;
    }
};
```



## 13. LeetCode 371 [Sum of Two Integers](https://leetcode.com/problems/sum-of-two-integers/)

```c++
class Solution {
public:
    int getSum(unsigned int a, unsigned int b) {
        int sum = a;
        
        while (b != 0) {
            sum = a ^ b; //calculate sum of a and b without thinking the carry 
            b = (a & b) << 1; //calculate the carry
            a = sum; //add sum(without carry) and carry
        }
        
        return sum;
    }
};

// recursion
class Solution {
public:
    int getSum(unsigned int a, unsigned int b) {
        return b == 0 ? a : getSum(a ^ b, (a & b) << 1);
    }
};
```



## 14. LeetCode 389 [Find the Difference](https://leetcode.com/problems/find-the-difference/)

```c++
class Solution {
public:
    char findTheDifference(string s, string t) {
        int bits = 0;
        
        for (char &c : s) {
            bits ^= c;
        }
        
        for (char &c : t) {
            bits ^= c;
        }
        
        return bits;
    }
};
```



## 15. LeetCode 405 [Convert a Number to Hexadecimal](https://leetcode.com/problems/convert-a-number-to-hexadecimal/)

```c++
class Solution {
public:
    string toHex(int num) {
        char digits[] = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};
        string hex = "";
        
        unsigned int n = num;
        while (n) {
            hex = digits[n & 0xf] + hex;
            n >>= 4;
        }
        return hex.empty() ? "0" : hex;
    }
};

class Solution {
public:
    string toHex(int num) {
        int count = 0;
        if(!num) return "0";
        string result;
        while (num && count < 8) {
            int temp = num & 15;
            if (temp < 10) result.push_back('0' + temp);
            else result.push_back('a' + temp - 10);
            num = num >> 4;
            count++;
        }
        reverse(result.begin(), result.end());
        return result;
    }
};
```



## 16. LeetCode 476 [Number Complement](https://leetcode.com/problems/number-complement/)

```c++
class Solution {
public:
    int findComplement(int num) {
        unsigned int mask = ~0;
        while (num & mask) mask <<= 1;
        return ~mask & ~num;
    }
};
```



## 17. LeetCode 500 [Keyboard Row](https://leetcode.com/problems/keyboard-row/)

```c++
class Solution {
public:
    vector<string> findWords(vector<string>& words) {
        vector<int> dict(26);
        vector<string> rows = {"QWERTYUIOP", "ASDFGHJKL", "ZXCVBNM"};
        for (int i = 0; i < rows.size(); i++) {
            for (auto &c : rows[i]) dict[c-'A'] = 1 << i;
        }
        
        vector<string> res;
        for (auto w : words) {
            int r = 7;
            for (char c : w) {
                r &= dict[toupper(c)-'A'];
                if (r == 0) break;
            }
            if (r) res.push_back(w);
        }
        return res;
    }
};
```



## 18. LeetCode 898 [Bitwise ORs of Subarrays](https://leetcode.com/problems/bitwise-ors-of-subarrays/)

### Bruteforce + hashset

```c++
class Solution {
public:
    int subarrayBitwiseORs(vector<int>& A) {
        unordered_set<int> res, cur;
        cur.insert(0);
        
        for (int a : A) {
            unordered_set<int> cur2;
            for (int b : cur) {
                cur2.insert(a | b);
            }
            cur2.insert(a);
            swap(cur, cur2);
            
            for (int x : cur) {
                res.insert(x);
            }
        }
        
        return res.size();
    }
};
```



































