# Stack

## 1. LeetCode 394 [Decode String](https://leetcode.com/problems/decode-string/)

### (1) one stack

```c++
class Solution {
public:
    string decodeString(string s) {
        int len = s.length();
        string ans = "";
        stack<char> st;
        
        for (int i=0; i<len; i++) {
            if (s[i] == ']') {
                string temp = "";
                while (!st.empty() && isalpha(st.top())) {
                    temp = st.top() + temp;
                    st.pop();
                }
                st.pop(); // pop '['
                int repeated = 0;
                int k = 1;
                while (!st.empty() && isdigit(st.top())) {
                    repeated = k * (st.top() - '0') + repeated;
                    k *= 10;
                    st.pop();
                }
                for (int j=0; j<repeated; j++) {
                    for (auto c : temp) {
                        st.push(c);
                    }
                }
            } else {
                st.push(s[i]);
            }
        }
        
        while (!st.empty()) {
            ans = st.top() + ans;
            st.pop();
        }
        
        return ans;
    }
};
```



### (2) double stacks (java)

```java
class Solution {
    public String decodeString(String s) {
        Stack<Integer> intStack = new Stack<>();
        Stack<StringBuilder> strStack = new Stack<>();
        StringBuilder cur = new StringBuilder();
        int k = 0;
        for (char ch : s.toCharArray()) {
            if (Character.isDigit(ch)) {
                k = k * 10 + ch - '0';
            } else if ( ch == '[') {
                intStack.push(k);
                strStack.push(cur);
                cur = new StringBuilder();
                k = 0;
            } else if (ch == ']') {
                StringBuilder tmp = cur;
                cur = strStack.pop();
                for (k = intStack.pop(); k > 0; --k) cur.append(tmp);
            } else cur.append(ch);
        }
        return cur.toString();
    }
}
```

[solution](https://leetcode.com/problems/decode-string/discuss/87534/Simple-Java-Solution-using-Stack)

```c++
// c++ version
class Solution {
public:
    string decodeString(string s) {
        int len = s.length();
        string cur = "";
        stack<string> strSt;
        stack<int> intSt;
        
        int k = 0; // used for building number
        
        for (auto const &c : s) {
            if (isdigit(c)) {
                k = k * 10 + c - '0';
            } else if (c == '[') {
                intSt.push(k);
                strSt.push(cur);
                cur.clear();
                k = 0;
            } else if (c == ']') {
                string tmp = cur; // current repeated string
                cur = strSt.top(); // get the prefix
                strSt.pop();
                for (int i=intSt.top(); i>0; i--) {
                    cur += tmp;
                }
                intSt.pop();
            } else {
                cur.push_back(c);
            }
        }
        
        return cur;
    }
};
```



## 2. LeetCode 150 [Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)

### (1) original

```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> st;
        for (auto const &t : tokens) {
            if (t == "+") {
                int b = st.top();
                st.pop();
                int a = st.top();
                st.pop();
                st.push(a + b);
            } else if (t == "-") {
                int b = st.top();
                st.pop();
                int a = st.top();
                st.pop();
                st.push(a - b);
            } else if (t == "*") {
                int b = st.top();
                st.pop();
                int a = st.top();
                st.pop();
                st.push(a * b);
            } else if (t == "/") {
                int b = st.top();
                st.pop();
                int a = st.top();
                st.pop();
                st.push(a / b);
            } else {
                st.push(stoi(t));
            }
        }
        return st.top();
    }
};
```



### std::function

#include <functional>

[cppreference](https://zh.cppreference.com/w/cpp/utility/functional/function)

### (2) fancy

```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        unordered_map<string, function<int (int, int)>> mp{
            {"+", [] (const int& a, const int& b) { return a + b;}},
            {"-", [] (const int& a, const int& b) { return a - b;}},
            {"*", [] (const int& a, const int& b) { return a * b;}},
            {"/", [] (const int& a, const int& b) { return a / b;}}
        };
        stack<int> st;
        for (auto const &t : tokens) {
            if (mp.count(t)) {
                int b = st.top();
                st.pop();
                int a = st.top();
                st.pop();
                st.push(mp[t](a, b));
            } else {
                st.push(stoi(t));
            }
        }
        return st.top();
    }
};
```

[solution](https://leetcode.com/problems/evaluate-reverse-polish-notation/discuss/47514/Fancy-C%2B%2B-lambda-expression-solution)



## 3. LeetCode 84 [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

[geeksforgeeks](https://www.geeksforgeeks.org/largest-rectangle-under-histogram/)

### Explanation

**For every bar ‘x’, we calculate the area with ‘x’ as the smallest bar in the rectangle. If we calculate such area for every bar ‘x’ and find the maximum of all areas, our task is done.** How to calculate area with ‘x’ as smallest bar? We need to **know index of the first smaller (smaller than ‘x’) bar on left of ‘x’ and index of first smaller bar on right of ‘x’.** Let us call these indexes as ‘left index’ and ‘right index’ respectively.

We traverse all bars from left to right, maintain a stack of bars. Every bar is pushed to stack once. A bar is popped from stack when a bar of smaller height is seen. When a bar is popped, we calculate the area with the popped bar as smallest bar. How do we get left and right indexes of the popped bar – the current index tells us the ‘right index’ and index of previous item in stack is the ‘left index’. Following is the complete algorithm.

**1)** Create an empty stack.

**2)** Start from first bar, and do following for every bar ‘hist[i]’ where ‘i’ varies from 0 to n-1.
……**a)** If stack is empty or hist[i] is higher than the bar at top of stack, then push ‘i’ to stack.
……**b)** If this bar is smaller than the top of stack, then keep removing the top of stack while top of the stack is greater. Let the removed bar be hist[tp]. Calculate area of rectangle with hist[tp] as smallest bar. For hist[tp], the ‘left index’ is previous (previous to tp) item in stack and ‘right index’ is ‘i’ (current index).

**3)** If the stack is not empty, then one by one remove all bars from stack and do step 2.b for every removed bar.

Following is implementation of the above algorithm.

```c++
// Time Complexity: O(n) Since every bar is pushed and popped only once, the time complexity // of this method is O(n).
// Space Complexity: O(n)

// corner case: repeated bar with the same height
// e.g. 1,3,4,4,4,4,2,3,5
// when we visit the second 4, we can pop the first 4 to make sure the bars stored in stack
// are always in increasing order.
// Thus, we treat the first 4 bar like a higher bar because actually we only need to
// calculate the area with the last '4' as smallest bar.

class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        if (heights.empty()) return 0;
        int n = heights.size();
        stack<int> st; // used for store the indexes of the histograms
        int left = -1, right = 0; // left bound index & right bound index
        int res_area = 0;
        
        for (int i=0; i<n; i++) {
            if (st.empty() || heights[i] > heights[st.top()]) {
                st.push(i);
            } else {
                while (!st.empty() && heights[i] <= heights[st.top()]) {
                    int top = st.top();
                    st.pop();
                    left = !st.empty() ? st.top() : -1;
                    right = i;
                    res_area = max(res_area, heights[top] * (right - left - 1));
                }
                st.push(i);
            }
        }
        
        right = n;
        while (!st.empty()) {
            int top = st.top();
            st.pop();
            left = !st.empty() ? st.top() : -1;
            res_area = max(res_area, heights[top] * (right - left - 1));
        }
        
        return res_area;
    }
};

// shorter version
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        if (heights.empty()) return 0;
        int n = heights.size();
        // Create an empty stack. The stack holds indexes  
    	// of heights array. The bars stored in stack are  
    	// always in increasing order of their heights.
        stack<int> st;
        int top; // To store top of stack 
        int max_area = 0; // Initalize max area 
        
        // Run through all bars of given histogram 
        int i = 0;
        while (i < n) {
            // If this bar is higher than the bar on top  
        	// stack, push it to stack
            if (st.empty() || heights[i] > heights[st.top()]) {
                st.push(i++);
            } else {
                // If this bar is lower than or equal to the top bar of stack,  
                // then calculate area of rectangle with stack  
                // top as the smallest (or minimum height) bar.  
                // 'i' is 'right index' for the top and element  
                // before top in stack is 'left index' 
                while (!st.empty() && heights[i] <= heights[st.top()]) {
                    top = st.top();
                    st.pop();
                    // Calculate the area with hist[tp] stack  
            		// as smallest bar 
                    // and update max area, if needed 
                    max_area = max(max_area, heights[top] * (st.empty() ? i : i - st.top() - 1));
                }
                st.push(i++);
            }
        }
        
        // Now pop the remaining bars from stack and calculate 
    	// area with every popped bar as the smallest bar
        // same as above
        while (!st.empty()) {
            top = st.top();
            st.pop();
            max_area = max(max_area, heights[top] * (st.empty() ? i : i - st.top() - 1));
        }
        
        return max_area;
    }
};
```









































