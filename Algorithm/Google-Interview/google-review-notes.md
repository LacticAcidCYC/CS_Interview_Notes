# Algorithm Exercises

## 1. LeetCode 904 [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)

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





## 2. LeetCode 681 [Next Closest Time](https://leetcode.com/problems/next-closest-time/)

**String::find**

[cplusplus](http://www.cplusplus.com/reference/string/string/find/)

### (1) Increase the minute one by one

```c++
class Solution {
public:
    string nextClosestTime(string time) {
        if (time[0] == time[1] && time[1] == time[3] &&
           time[3] == time[4] && time[4] == time[5]) {
            return time;
        }
        int colon = time.find(':');
        int cur = stoi(time.substr(0, colon)) * 60 + stoi(time.substr(colon+1));
        string next = "0000";
        const vector<int> v{600, 60, 10, 1};
        
        for (int i=1, j=0; i<=1440; i++) {
            int next_min = (cur + i) % 1440;
            for (j=0; j<4; j++) {
                next[j] = '0' + next_min / v[j];
                next_min %= v[j];
                if (time.find(next[j]) == string::npos) {
                    break;
                }
            }
            if (j == 4) {
                break;
            }
        }
        
        return next.substr(0, 2) + ':' + next.substr(2);
    }
};
```



### (2) DFS

 ```java
class Solution {
    int diff = Integer.MAX_VALUE;
    String result = "";
    
    public String nextClosestTime(String time) {
        Set<Integer> set = new HashSet<>();
        set.add(Integer.parseInt(time.substring(0, 1)));
        set.add(Integer.parseInt(time.substring(1, 2)));
        set.add(Integer.parseInt(time.substring(3, 4)));
        set.add(Integer.parseInt(time.substring(4, 5)));
        
        if (set.size() == 1) return time;
        
        List<Integer> digits = new ArrayList<>(set);
        int minute = Integer.parseInt(time.substring(0, 2)) * 60 + Integer.parseInt(time.substring(3, 5));

        dfs(digits, "", 0, minute);

        return result;
    }

    private void dfs(List<Integer> digits, String cur, int pos, int target) {
        if (pos == 4) {
            int m = Integer.parseInt(cur.substring(0, 2)) * 60 + Integer.parseInt(cur.substring(2, 4));
            if (m == target) return;
            int d = m - target > 0 ? m - target : 1440 + m - target;
            if (d < diff) {
                diff = d;
                result = cur.substring(0, 2) + ":" + cur.substring(2, 4);
            }
            return;
        }

        for (int i = 0; i < digits.size(); i++) {
            if (pos == 0 && digits.get(i) > 2) continue;
            if (pos == 1 && Integer.parseInt(cur) * 10 + digits.get(i) > 23) continue;
            if (pos == 2 && digits.get(i) > 5) continue;
            if (pos == 3 && Integer.parseInt(cur.substring(2)) * 10 + digits.get(i) > 59) continue;
            dfs(digits, cur + digits.get(i), pos + 1, target);
        }
    }
}
 ```

[solution1](https://leetcode.com/problems/next-closest-time/discuss/107783/C%2B%2BJava-Clean-Code)

[dfs](https://leetcode.com/problems/next-closest-time/discuss/107788/Verbose-Java-solution-DFS)

