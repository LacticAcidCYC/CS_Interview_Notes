## 1. Missing Words

### - Split a string in C++

- refrences:

  A. [cplusplus](http://www.cplusplus.com/faq/sequences/strings/split/)

  B. [stackoverflow](https://stackoverflow.com/questions/236129/how-do-i-iterate-over-the-words-of-a-string#)

- functions & class

  [istringstream](http://www.cplusplus.com/reference/sstream/istringstream/)

```c++
#include <sstream>

string s;
istringstream iss( s ); 

// std::ios::eof
// Check whether eofbit is set
// Returns true if the eofbit error state flag is set for the stream.

// This flag is set by all standard input operations when the End-of-File is reached in the // sequence associated with the stream.
bool eof() const;
```

​	[std::getline](http://www.cplusplus.com/reference/string/string/getline/?kw=getline)

```c++
istream& getline (istream&  is, string& str, char delim);
```



(1) iostreams and getline()

```c++
#include <sstream>
#include <iostream>

using namespace std;

string s = "string,to,split";
istringstream ss( s );
while (!ss.eof())         // See the WARNING above for WHY we're doing this!
{
  string x;               // here's a nice, empty string
  getline( ss, x, ',' );  // try to read the next field into it
  cout << x << endl;      // print it out, EVEN IF WE ALREADY HIT EOF
}
```



(2) string::find_first_of

```c++
string s = "string,to,split";
string delimiters = ",";
size_t current;
size_t next = -1;
do
{
  current = next + 1;
  next = s.find_first_of( delimiters, current );
  cout << s.substr( current, next - current ) << endl;
}
while (next != string::npos);
```



- Solution:

```c++
#include <iostream>
#include <vector>
#include <sstream>

using namespace std;

// Missing Words
vector<string> missingWords(string &s, string &t);
vector<string> split(string &s);

vector<string> missingWords(string &s, string &t) {
    vector<string> ans;
    vector<string> ss = split(s);
    vector<string> st = split(t);
    
    int s_ptr = 0, t_ptr = 0;
    while (s_ptr < ss.size()) {
        if (t_ptr < st.size() && ss[s_ptr] == st[t_ptr]) {
            s_ptr++;
            t_ptr++;
        } else {
            ans.push_back(ss[s_ptr++]);
        }
    }
    return ans;
}

vector<string> split(string &s) {
    vector<string> splited;
    istringstream iss(s);
    while (!iss.eof()) {
        string x;
        getline(iss, x, ' ');
        splited.push_back(x);
    }
    return splited;
}

int main(int argc, const char * argv[]) {
    string s = "I am using HackerRank to improve programming";
    string t = "am HackerRank to improve";

    for (auto m : missingWords(s, t)) {
        cout << m << endl;
    }
    
    return 0;
}
```



## 2. Huffman Decoding

```c++
// Huffman Decoder
string huffmanDecoder(vector<string> dict, string input);
vector<string> splitByDelimiter(string &s, char delimiter);

vector<string> splitByDelimiter(string &s, char delimiter) {
    vector<string> splited;
    istringstream iss(s);
    while (!iss.eof()) {
        string x;
        getline(iss, x, delimiter);
        splited.push_back(x);
    }
    return splited;
}

string huffmanDecoder(vector<string> dict, string input) {
    string ans = "";
    unordered_map<string, string> map;
    
    for (auto s : dict) {
        vector<string> splited = splitByDelimiter(s, ' ');
        if (splited[0] == "newline") {
            map[splited[1]] = "\n";
        } else {
            map[splited[1]] = splited[0];
        }
    }
    
    int begin = 0, end = 1;
    while (end <= input.length()) {
        string cur = input.substr(begin, end-begin);
        if (map.count(cur)) {
            ans += map[cur];
            begin = end++;
        } else {
            end++;
        }
    }
    
    return ans;
}
```





## 3. HackerlandElection

https://www.geeksforgeeks.org/find-winner-election-votes-represented-candidate-names/

https://www.geeksforgeeks.org/frequent-word-array-strings/

https://stackoverflow.com/questions/9370945/c-help-finding-the-max-value-in-a-map

http://www.cplusplus.com/reference/algorithm/max_element/?kw=max_element

### - Hashmap

```c++
// HackerlandElection
string hackerlandElection(vector<string> votes);

// using hashmap (Java-hashmap)
string hackerlandElection(vector<string> votes) {
    unordered_map<string, int> map;
    for (auto vote : votes) {
        map[vote]++;
    }
    
    string winner;
    int max_election = 0;
    
    for (auto m : map) {
        if ((m.second > max_election) || (m.second == max_election && m.first > winner)) {
            max_election = m.second;
            winner = m.first;
        }
    }
    
    return winner;
}

// using map (Java-TreeMap/Red-Black-Tree)
bool compare(const pair<string, int> &A, const pair<string, int> &B) {
    return A.second < B.second; // same as using min_element
}

string hackerlandElection(vector<string> votes) {
	string winner;
    map<string, int, greater<string>> m;
    
    for (auto vote : votes) {
        m[vote]++;
    }
    
    return max_element(m.begin(), m.end(), compare)->first;
}
```



## 4. Rover Control

```c++
// Rover Control
int roverMove(int size, vector<string> cmds);

int roverMove(int size, vector<string> cmds) {
    int pi = 0, pj = 0;
    unordered_map<string, vector<int>> moves{
        {"UP", {-1, 0}},
        {"RIGHT", {0, 1}},
        {"DOWN", {1, 0}},
        {"LEFT", {0, -1}}
    };
    
    for (auto cmd : cmds) {
        int ni = pi + moves[cmd][0];
        int nj = pj + moves[cmd][1];
        if (ni >= 0 && ni < size && nj >= 0 && nj < size) {
            pi = ni;
            pj = nj;
        }
    }
    return pi*size+pj;
}
```



## 5. Simple Text Queries

```c++
// SimpleTextQueries
vector<string> textQueries(vector<string> &sentences, vector<string> &phrases);
vector<string> split(string &s);

vector<string> split(string &s) {
    vector<string> splited;
    istringstream iss(s);
    while (!iss.eof()) {
        string x;
        getline(iss, x, ' ');
        splited.push_back(x);
    }
    return splited;
}

vector<string> textQueries(vector<string> &sentences, vector<string> &phrases) {
    vector<string> ans;
    vector<unordered_map<string, int>> maps;
    
    for (auto sentence : sentences) {
        vector<string> splited = split(sentence);
        unordered_map<string, int> wordcnts;
        for (auto s : splited) {
            wordcnts[s]++;
        }
        maps.push_back(wordcnts);
    }
    
    for (auto p : phrases) {
        int index = 0;
        vector<string> words = split(p);
        string output =  "";
        for (auto m : maps) {
            int min_cnt = INT_MAX;
            for (auto word : words) {
                if (m[word] == 0) {
                    min_cnt = INT_MAX;
                    break;
                }
                min_cnt = min(min_cnt, m[word]);
            }
            if (min_cnt != INT_MAX && min_cnt > 0) {
                output += to_string(index) + " ";
            }
            index++;
        }
        if (output.length() == 0) {
            ans.push_back("-1");
        } else {
            output.pop_back();
            ans.push_back(output);
        }
    }
    
    return ans;
}
```



## 6. Who's the closest

```c++
// who's the closest (map + binary search)
vector<int> closest(string s, vector<int> indexes);

vector<int> closest(string s, vector<int> indexes) {
    vector<int> ans;
    unordered_map<char, vector<int>> map;
    
    for (int i=0; i<s.length(); i++) {
        map[s[i]].push_back(i);
    }
    
    for (auto index : indexes) {
        if (map[s[index]].size() == 1) {
            ans.push_back(-1);
        } else {
            vector<int> v(map[s[index]].begin(), map[s[index]].end());
            int left = 0, right = (int) v.size() - 1;
            if (index == v[left]) {
                ans.push_back(v[left+1]);
                continue;
            } else if (index == v[right]) {
                ans.push_back(v[right-1]);
                continue;
            }
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (v[mid] == index) {
                    ans.push_back((v[mid] - v[mid-1]) <= (v[mid+1] - v[mid]) ? v[mid-1] : v[mid+1]);
                    break;
                } else if (v[mid] > index) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }
        }
    }
    
    return ans;
}
```



## 7. Image Matching

LeetCode 694 Number of Distinct Island

```c++
// Image Matching (bfs/dfs)
int countMatches(vector<vector<int>> &grid1, vector<vector<int>> &grid2);
vector<pair<int, int>> getImage(int row, int col, vector<vector<int>> &grid);
bool isMatch(vector<pair<int, int>> &image1, vector<pair<int, int>> &image2);

int countMatches(vector<vector<int>> &grid1, vector<vector<int>> &grid2) {
    int ans = 0;
    
    int r = (int) grid1.size();
    int c = (int) grid1[0].size();
    for (int i=0; i<r; i++) {
        for (int j=0; j<c; j++) {
            vector<pair<int, int>> image1 = getImage(i, j, grid1);
            // cout << "size: " << image1.size() << endl;
            vector<pair<int, int>> image2 = getImage(i, j, grid2);
            
            if (!image1.empty() && !image2.empty() && isMatch(image1, image2)) {
                ans++;
            }
        }
    }
    return ans;
}

// bfs
vector<pair<int, int>> getImage(int row, int col, vector<vector<int>> &grid) {
    vector<pair<int, int>> image;
    const vector<vector<int>> dirs{{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
    
    if (grid[row][col] == 1) {
        queue<pair<int, int>> q;
        q.push({row, col});
        while (!q.empty()) {
            pair<int, int> cur = q.front();
            image.push_back(cur);
            q.pop();
            int i = cur.first;
            int j = cur.second;
            grid[i][j] = 0;
            for (int k=0; k<4; k++) {
                int ni = i + dirs[k][0];
                int nj = j + dirs[k][1];
                if (ni >=0 && ni < grid.size() && nj >= 0 && nj < grid[0].size() && grid[ni][nj] == 1) {
                    q.push({ni, nj});
                }
            }
        }
    }
    
    return image;
}

// match two images
bool isMatch(vector<pair<int, int>> &image1, vector<pair<int, int>> &image2) {
    set<vector<pair<int, int>>> us;
    sort(image1.begin(), image1.end());
    sort(image2.begin(), image2.end());
    us.insert(image1);
    us.insert(image2);
    return us.size() == 1;
}
```



## 8. No pairs Allowed

```c++
vector<int> minimalOperations(vector<string> input);

vector<int> minimalOperations(vector<string> input) {
    vector<int> ans;
    
    for (auto s : input) {
        int cnt = 0;
        for (int i=0; i<s.length()-1;) {
            if (s[i] == s[i+1]) {
                int cur_dup = 2;
                i++;
                while (i < s.length()-1 && s[i+1] == s[i]) {
                    i++;
                    cur_dup++;
                }
                cnt += cur_dup / 2;
            }
            i++;
        }
        ans.push_back(cnt);
    }
    
    return ans;
}
```



## 9. Parenthesis

LeetCode 20

```c++
bool isValid(string s) {
    stack<char> st;
    for (auto c : s) {
        if (st.empty() || c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (isMatch(st.top(), c)) {
                st.pop();
            } else {
                return false;
            } 
        }
    }
    return st.empty();
}

bool isMatch(char a, char b) {
    if (a == '(') {
        return b == ')';
    } else if (a == '[') {
        return b == ']';
    } else if (a == '{') {
        return b == '}';
    }
    return false;
}
```



## 10. Primes in Subtree

```c++
// Primes in Subtree
vector<int> primeQueries(int n, vector<int> starts, vector<int> ends, vector<int> values, vector<int> queries);
bool isPrime(int x);

vector<int> primeQueries(int n, vector<int> starts, vector<int> ends, vector<int> values, vector<int> queries) {
    vector<int> ans;
    vector<int> indexes(n);
    vector<int> cnts(n);
    
    // initialize cnts
    for (int i=0; i<values.size(); i++) {
        cnts[i] = isPrime(values[i]) ? 1 : 0;
    }
    
    // initialize indexes
    for (int i=0; i<indexes.size(); i++) {
        indexes[i] = i;
    }
    for (int i=0; i<starts.size(); i++) {
        indexes[ends[i]-1] = starts[i]-1;
    }
    
    // calculate primes in subtree
    for (int i=0; i<cnts.size(); i++) {
        int temp = i;
        while (temp != indexes[temp]) {
            temp = indexes[temp]; // get the parent of node[temp]
            cnts[temp] += cnts[i];
        }
    }
    
    // queries
    for (int i=0; i<queries.size(); i++) {
        ans.push_back(cnts[queries[i]-1]);
    }
    
    return ans;
}

bool isPrime(int x) {
    if (x <= 3) return true;
    if (x % 2 == 0) return false;
    
    for (int i=3; i*i<=x; i++) {
        if (x % i == 0) return false;
    }
    
    return true;
}
```



```c++
/*
 * Complete the 'primeQuery' function below.
 *
 * The function is expected to return an INTEGER_ARRAY.
 * The function accepts following parameters:
 *  1. INTEGER n
 *  2. INTEGER_ARRAY first
 *  3. INTEGER_ARRAY second
 *  4. INTEGER_ARRAY values
 *  5. INTEGER_ARRAY queries
 */

// 3 test cases TLE
bool isPrime(int x) {
    if (x == 1) return false;
    if (x <= 3) return true;
    if (x % 2 == 0) return false;
    for (int i=3; i*i<=x; i++) {
        if (x % i == 0) return false;
    }
    return true;
}

vector<int> primeQuery(int n, vector<int> first, vector<int> second, vector<int> values, vector<int> queries) {
    vector<int> ans;
    vector<int> indexes(n);
    unordered_map<int, set<int>> map;
    vector<int> parents(n);
    
    for (int i=0; i<first.size(); i++) {
        map[first[i]].insert(second[i]);
        //map[second[i]].insert(first[i]);
    }
    
    for (int i=0; i<n; i++) {
        parents[i] = i;
    }
    
    queue<int> q;
    q.push(1);
    while (!q.empty()) {
        int parent = q.front();
        q.pop();
        
        for (auto& pair : map) {
            if (pair.first == parent) {
                for (auto child : pair.second) {
                    q.push(child);
                    parents[child-1] = parent-1;
                }
            } else {
                int isChild = false;
                for (auto cand : pair.second) {
                    if (cand == parent && (pair.first-1) != parents[cand-1]) {
                        q.push(pair.first);
                        parents[pair.first-1] = parent-1;
                        isChild = true;
                        break;
                    }
                }
                if (isChild) {
                    pair.second.erase(parent);
                }
            }
        }
    }    
    
    vector<int> cnts(n);
    for (int i=0; i<n; i++) {
        cnts[i] = isPrime(values[i]) ? 1 : 0; 
    }
    
    for (int i=0; i<n; i++) {
        int temp = i;
        while (temp != parents[temp]) {
            temp = parents[temp];
            cnts[temp] += isPrime(values[i]) ? 1 : 0;
        }
    }
    
    for (auto i : queries) {
        ans.push_back(cnts[i-1]);
    }
    
    return ans;
}
```































