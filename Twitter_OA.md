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

