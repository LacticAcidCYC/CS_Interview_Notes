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



























