## 1. LeetCode 208 [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)

### (1) Former Solution

```c++
// original solution
class TrieNode {
public:
    TrieNode() {
        children = vector<TrieNode*>(26);
    }
    
    bool containsKey(char ch) {
        return children[ch - 'a'] != NULL;
    }
    
    TrieNode* get(char ch) {
        return children[ch - 'a'];
    }
    
    void put(char ch, TrieNode* node) {
        children[ch - 'a'] = node;
    }
    
    void setEnd() {
        isEnd = true;
    }
    
    bool end() {
        return isEnd;
    }

private:
    vector<TrieNode*> children;
    const int r = 26;
    bool isEnd;
};

class Trie {
public:
    /** Initialize your data structure here. */
    Trie() {
        root = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    void insert(string word) {
        TrieNode* node = root;
        for(auto ch : word) {
            if(!node->containsKey(ch)) {
                node->put(ch, new TrieNode());
            }
            node = node->get(ch);
        }
        node->setEnd();
    }
    
    /** Returns if the word is in the trie. */
    bool search(string word) {
        TrieNode* node = searchPrefix(word);
        return node != NULL && node->end();
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(string prefix) {
        TrieNode* node = searchPrefix(prefix);
        return node != NULL;
    }
    
    TrieNode* searchPrefix(string prefix) {
        TrieNode* node = root;
        for(auto ch : prefix) {
            if(!node->containsKey(ch)) {
                return NULL;
            } else {
                node = node->get(ch);
            }
        }
        return node;
    }
    
private:
    TrieNode* root;
};



/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * bool param_2 = obj.search(word);
 * bool param_3 = obj.startsWith(prefix);
 */
```



### (2) Clearer and Updated Solution (C++11)

[unique_ptr](http://www.cplusplus.com/reference/memory/unique_ptr/)

[nullptr-vs-NULL](https://www.google.com/search?q=nullptr+vs+NULL+c%2B%2B&oq=nullptr+vs+NULL+c%2B%2B&aqs=chrome..69i57j0.5029j0j7&sourceid=chrome&ie=UTF-8)

[huahua](https://www.youtube.com/watch?v=f48wGD-MuQw)

```c++
class Trie {
public:
    /** Initialize your data structure here. */
    Trie() : _root(new TrieNode()) {}
    
    /** Inserts a word into the trie. */
    void insert(string word) {
        TrieNode* p = _root.get(); // Returns the stored pointer.
        for (const char c : word) {
            if (!p->children[c - 'a']) {
                p->children[c - 'a'] = new TrieNode();
            }
            p = p->children[c - 'a'];
        }
        p->is_word = true;
    }
    
    /** Returns if the word is in the trie. */
    bool search(string word) const {
        const auto p = find(word);
        return p != nullptr && p->is_word;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(string prefix) const {
        return find(prefix) != nullptr;
    }
    
private:
    struct TrieNode {
        TrieNode() : is_word(false), children(26, nullptr) {}
        
        // important! avoid memory leak!
        ~TrieNode() {
            for (TrieNode* child : children) {
                if (child) delete child;
            }
        }
        
        vector<TrieNode*> children;
        bool is_word;
    };
    
    const TrieNode* find(const string &prefix) const {        
        const TrieNode* p = _root.get();
        for (const char c : prefix) {
            p = p->children[c - 'a'];
            if (p == nullptr) break;
        }
        return p;
    }
    
    unique_ptr<TrieNode> _root;
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * bool param_2 = obj.search(word);
 * bool param_3 = obj.startsWith(prefix);
 */
```





## 2. LeetCode 211 [Add and Search Word - Data structure design](https://leetcode.com/problems/add-and-search-word-data-structure-design/)

### (1) Former Solution

```c++
class TrieNode {
public:
    TrieNode() {
        children = vector<TrieNode*>(26);
    }
    
    bool containsKey(char ch) {
        return children[ch - 'a'] != NULL;
    }
    
    TrieNode* get(char ch) {
        return children[ch - 'a'];
    }
    
    void put(char ch, TrieNode* node) {
        children[ch - 'a'] = node;
    }
    
    void setEnd() {
        isEnd = true;
    }
    
    bool end() {
        return isEnd;
    }

private:
    vector<TrieNode*> children;
    const int r = 26;
    bool isEnd = false;
};

class WordDictionary {
public:
    /** Initialize your data structure here. */
    WordDictionary() {
        root = new TrieNode();
    }
    
    /** Adds a word into the data structure. */
    void addWord(string word) {
        TrieNode* node = root;
        for(auto ch : word) {
            if(!node->containsKey(ch)) {
                node->put(ch, new TrieNode());
            }
            node = node->get(ch);
        }
        node->setEnd();
    }
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    bool search(string word) {
        return dfsSearchPrefix(root, word.c_str());
    }
    
    bool dfsSearchPrefix(TrieNode* node, const char* prefix) {
        if(!prefix[0]) {
            return node->end();
        }
        if(prefix[0] == '.') {
            for(int i=0; i<26; i++) {
                if(node->containsKey('a' + i) && dfsSearchPrefix(node->get('a' + i), prefix+1)) {
                    return true;
                }
            }
            return false;
        } else {
            if(!node->containsKey(prefix[0])) {
                return false;
            }
            return dfsSearchPrefix(node->get(prefix[0]), prefix+1);
        }
    }
    
private:
    TrieNode* root;
};

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary obj = new WordDictionary();
 * obj.addWord(word);
 * bool param_2 = obj.search(word);
 */
```



### (2) Former Solution2 using Hashmap

```c++
class WordDictionary {
public:
    unordered_map<int, unordered_set<string>> wordList;
    /** Initialize your data structure here. */
    WordDictionary() {
        
    }
    
    // O(1)
    /** Adds a word into the data structure. */
    void addWord(string word) {
        int len = (int)word.size();
        if(!wordList[len].count(word)) {
            wordList[len].insert(word);
        }
    }
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    /*bool search(string word) {
        int index = word.find_first_of(".", 0);
        int len = (int) word.size();
        if(index == string :: npos) {
            return wordList[len].count(word) > 0;
        }
        for(auto str : wordList[len]) {
            int found = true;
            for(int i=0; i<len; i++) {
                if(word[i] != str[i] && word[i] != '.') {
                    found = false;
                    break;
                }
            }
            if(found) return true;
        }
        return false;
    }*/
    
    // O(mn)
    // m : number of different string
    // n : length of longest string
    bool search(string word) {
        int index = word.find_first_of(".", 0);
        int len = (int) word.size();
        if(index == string :: npos) {
            return wordList[len].count(word) > 0;
        }
        for(auto str : wordList[len]) {
            int i;
            for(i=0; i<len; i++) {
                if(word[i] != str[i] && word[i] != '.') {
                    break;
                }
            }
            if(i == len) return true;
        }
        return false;
    }
    
};

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary obj = new WordDictionary();
 * obj.addWord(word);
 * bool param_2 = obj.search(word);
 */
```



### (3) Rewrited Trie Solution

```c++
class WordDictionary {
public:
    /** Initialize your data structure here. */
    WordDictionary() : _root(new TrieNode()) {}
    
    /** Adds a word into the data structure. */
    // O(n)
    // n : length of new word
    void addWord(string word) {
        TrieNode* p = _root.get();
        for (const char &c : word) {
            if (!p->children[c - 'a']) {
                p->children[c - 'a'] = new TrieNode();
            }
            p = p->children[c - 'a'];
        }
        p->is_word = true;
    }
    
    // O(m)
    // m : total number of character in Trie
    // worse case: search word only contains '.' except one letter
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    bool search(string word) {
        return dfs_search(_root.get(), word, 0);
    }
    
private:
    struct TrieNode {
        TrieNode() : is_word(false), children(26, nullptr) {}
        
        ~TrieNode() {
            for (TrieNode* child : children) {
                if (child) delete child;
            }
            children.clear();
        }
        
        bool is_word;
        vector<TrieNode*> children;
    };
    
    bool dfs_search(TrieNode* p, string &word, int idx) {
        if (idx == word.size()) {
            return p->is_word;
        }
        
        if (word[idx] == '.') {
            for (TrieNode* tn : p->children) {
                if (tn && dfs_search(tn, word, idx+1)) {
                    return true;
                }
            }
        } else {
            if (p->children[word[idx] - 'a']) {
                return dfs_search(p->children[word[idx] - 'a'], word, idx+1);
            }
        }
        return false;
    }
    
    std::unique_ptr<TrieNode> _root;
};

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary obj = new WordDictionary();
 * obj.addWord(word);
 * bool param_2 = obj.search(word);
 */
```



## 3. LeetCode 212 [Word Search II](https://leetcode.com/problems/word-search-ii/)

### (1) Trie + DFS

```c++
// If searching word like Word Search I, then time is
// O(kmn*3^(maxlen(words)))
// k : number of words
// m,n : size of board
// Time Complexity: O(mn*3^(maxlen(words)))
// Space Complexity: O(k*maxlen(words))

class Solution {
private:
    struct TrieNode {
        TrieNode() : word(""), children(26, nullptr) {}
        
        ~TrieNode() {
            for (TrieNode* child : children) {
                if (child) delete child;
                children.clear();
            }
        }
        
        string word; // replace the boolean value
        vector<TrieNode*> children;
    };
    
    TrieNode* buildTrie(const vector<string> &words) {
        TrieNode* root = new TrieNode();
        for (const string &word : words) {
            TrieNode* p = root;
            for (const char &c : word) {
                int i = c - 'a';
                if (!p->children[i]) {
                    p->children[i] = new TrieNode();
                }
                p = p->children[i];
            }
            p->word = word;
        }
        
        return root;
    }

public:
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        if (board.empty() || board[0].empty()) return {};
        int m = board.size();
        int n = board[0].size();
        
        TrieNode* trie_root = buildTrie(words);
        const vector<vector<int>> dirs = {{0, -1}, {1, 0}, {0, 1}, {-1, 0}};
        vector<string> res;
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                dfs(board, res, trie_root->children[board[i][j] - 'a'], i, j, dirs);
            }
        }
        
        return res;
    }
    
    void dfs(vector<vector<char>>& board, vector<string> &res, TrieNode* root, int r, int c, 
            const vector<vector<int>> &dirs) {
        if (root == nullptr) return;
        
        if (!root->word.empty()) { // find a string
            res.push_back(root->word);
            root->word = ""; // deduplicate (or use a hashset for result)
        }
        
        char temp = board[r][c];
        board[r][c] = '.'; // use for visit flag
        
        for (int k=0; k<dirs.size(); k++) {
            int nr = r + dirs[k][0];
            int nc = c + dirs[k][1];
            if (nr >= 0 && nc >= 0 && nr < board.size() && nc < board[0].size() && board[nr][nc] != '.') {
                dfs(board, res, root->children[board[nr][nc] - 'a'], nr, nc, dirs);
            }
        }
        
        board[r][c] = temp;
    }
};
```

[solution&explanation](https://leetcode.com/problems/word-search-ii/discuss/59780/Java-15ms-Easiest-Solution-(100.00))



### (2) Another Version

```c++
class Solution {
private:
    struct TrieNode {
        TrieNode() : word(""), children(26, nullptr) {}
        
        ~TrieNode() {
            for (TrieNode* child : children) {
                if (child) delete child;
                children.clear();
            }
        }
        
        string word;
        vector<TrieNode*> children;
    };
    
    TrieNode* buildTrie(const vector<string> &words) {
        TrieNode* root = new TrieNode();
        for (const string &word : words) {
            TrieNode* p = root;
            for (const char &c : word) {
                int i = c - 'a';
                if (!p->children[i]) {
                    p->children[i] = new TrieNode();
                }
                p = p->children[i];
            }
            p->word = word;
        }
        
        return root;
    }

public:
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        if (board.empty() || board[0].empty()) return {};
        int m = board.size();
        int n = board[0].size();
        
        TrieNode* trie_root = buildTrie(words);
        vector<string> res;
        
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                dfs(board, res, trie_root, i, j);
            }
        }
        
        return res;
    }
    
    void dfs(vector<vector<char>>& board, vector<string> &res, TrieNode* root, int r, int c) {
        char temp = board[r][c];
        
        if (temp == '.' || root->children[temp - 'a'] == nullptr) return;
        
        root = root->children[temp - 'a'];
        if (!root->word.empty()) {
            res.push_back(root->word);
            root->word = "";
        }
        
        
        board[r][c] = '.';
        
        if (r > 0) dfs(board, res, root, r-1, c);
        if (c > 0) dfs(board, res, root, r, c-1);
        if (r < board.size()-1) dfs(board, res, root, r+1, c);
        if (c < board[0].size()-1) dfs(board, res, root, r, c+1);
        
        board[r][c] = temp;
    }
};
```



## 4. LeetCode 677 [Map Sum Pairs](https://leetcode.com/problems/map-sum-pairs/)

[huahua](http://zxi.mytechroad.com/blog/tree/leetcode-677-map-sum-pairs/)

### (1) Trie

```c++
// string only contains lowercase alphabetic char
class MapSum {
public:
    /** Initialize your data structure here. */
    MapSum() : _root(new TrieNode()) {}
    
    // O(l)
    void insert(string key, int val) {
        TrieNode* p = _root.get();
        int inc = val - _vals[key];
        _vals[key] = val;
        for (const char &c : key) {
            if (!p->children[c - 'a']) {
                p->children[c - 'a'] = new TrieNode();
            }
            p->children[c - 'a']->val += inc;
            p = p->children[c - 'a'];
        }
    }
    
    // O(l)
    int sum(string prefix) const {
        TrieNode* p = _root.get();
        for (const char &c : prefix) {
            p = p->children[c - 'a'];
            if (p == nullptr) return 0;
        }
        
        return p->val;
    }
    
private:
    struct TrieNode {
        TrieNode() : val(0), children(26, nullptr) {}
        
        ~TrieNode() {
            for (TrieNode* child : children) {
                if (child) delete child;
            }
            children.clear();
        }
        
        int val;
        vector<TrieNode*> children; 
    };
    
    unique_ptr<TrieNode> _root; // dummy root
    unordered_map<string, int> _vals; // key -> val
};

// ASCII string
/**
 * Your MapSum object will be instantiated and called as such:
 * MapSum obj = new MapSum();
 * obj.insert(key,val);
 * int param_2 = obj.sum(prefix);
 */

class MapSum {
public:
    /** Initialize your data structure here. */
    MapSum() : _root(new TrieNode()) {}
    
    void insert(string key, int val) {
        TrieNode* p = _root.get();
        int inc = val - _vals[key];
        _vals[key] = val;
        for (const char &c : key) {
            if (!p->children[c]) {
                p->children[c] = new TrieNode();
            }
            p->children[c]->val += inc;
            p = p->children[c];
        }
    }
    
    int sum(string prefix) const {
        TrieNode* p = _root.get();
        for (const char &c : prefix) {
            p = p->children[c];
            if (p == nullptr) return 0;
        }
        
        return p->val;
    }
    
private:
    struct TrieNode {
        TrieNode() : val(0), children(128, nullptr) {}
        
        ~TrieNode() {
            for (TrieNode* child : children) {
                if (child) delete child;
            }
            children.clear();
        }
        
        int val;
        vector<TrieNode*> children; 
    };
    
    unique_ptr<TrieNode> _root;
    unordered_map<string, int> _vals;
};

/**
 * Your MapSum object will be instantiated and called as such:
 * MapSum obj = new MapSum();
 * obj.insert(key,val);
 * int param_2 = obj.sum(prefix);
 */
```



### (2) Hashmap (Simulating Trie)

```c++
class MapSum {
public:
    /** Initialize your data structure here. */
    MapSum() {}
    
    // O(l) => O(1) or O(l^2)
    void insert(string key, int val) {
        int inc = val;
        if (_vals.count(key)) {
            inc -= _vals[key];
        }
        
        _vals[key] = val;
        for (int i=1; i<=key.size(); i++) {
            _sums[key.substr(0, i)] += inc;
        }
    }
    
    // O(1) or O(l) (if length of prefix needs to be considered)
    int sum(string prefix) {
        return _sums[prefix];
    }
    
private:
    unordered_map<string, int> _vals;
    unordered_map<string, int> _sums;
};

/**
 * Your MapSum object will be instantiated and called as such:
 * MapSum obj = new MapSum();
 * obj.insert(key,val);
 * int param_2 = obj.sum(prefix);
 */
```



## 5. leetCode 745 [Prefix and Suffix Search](https://leetcode.com/problems/prefix-and-suffix-search/)

[huahua](https://www.youtube.com/watch?v=a-4WbFqalIA)

### (1) Hashmap

If query is more frequent than WordFilter():

```c++
// Time Complexity: O(N*l^3) + O(Ql)
// Space Complexity: O(N*l^3)

class WordFilter {
public:
    // O(N*l^3)
    WordFilter(vector<string> words) {
        int index = 0;
        for (const auto &word : words) { // N
            int l = word.length();
            vector<string> prefixes(l+1, "");
            vector<string> suffixes(l+1, "");
            
            for (int i=0; i<l; i++) {
                prefixes[i+1] = prefixes[i] + word[i];
                suffixes[i+1] = word[l-1-i] + suffixes[i];
            }
            
            for (const string &prefix : prefixes) {
                for (const string &suffix : suffixes) {
                    _filtermap[prefix+'#'+suffix] = index;
                }
            }
            
            index++;
        }
    }
    
    // O(l)
    int f(string prefix, string suffix) {
        const string key = prefix + '#' + suffix;
        auto it = _filtermap.find(key);
        if (it == _filtermap.end()) return -1;
        return it->second;
    }
    
private:
    unordered_map<string, int> _filtermap; // prefex + '#' + suffix -> weight
};

/**
 * Your WordFilter object will be instantiated and called as such:
 * WordFilter obj = new WordFilter(words);
 * int param_1 = obj.f(prefix,suffix);
 */
```

[solution](https://leetcode.com/problems/prefix-and-suffix-search/discuss/110044/Three-ways-to-solve-this-problem-in-Java)



### (2) Trie

Trick: Use '{' as split character, because its ASCII number is next to character 'z'

```c++
// Time Complexity: O(N*l^2) + O(Ql)
// Space Complexity: O(N*l^2)

class Trie {
public:
    /** Initialize your data structure here. */
    Trie() : _root(new TrieNode()) {}
    
    /** Inserts a word into the trie. */
    void insert(const string &word, int index) {
        TrieNode* p = _root.get();
        for (const char &c : word) {
            int i = c - 'a';
            if (!p->children[i]) {
                p->children[i] = new TrieNode();
            }
            p = p->children[i];
            p->index = index;
        }
    }
    
    /** Returns the index of word that starts with the given prefix. */
    int startsWith(const string &prefix) const {
        auto node = find(prefix);
        if (!node) return -1;
        return node->index;
    }
    
private:
    struct TrieNode {
        TrieNode() : index(-1), children(27, nullptr) {}
        
        ~TrieNode() {
            for (TrieNode* child : children) {
                if (child) delete child;
            }
            children.clear();
        }
        
        int index; // weight of node
        vector<TrieNode*> children; 
    };
    
    const TrieNode* find(const string &prefix) const {
        const TrieNode* p = _root.get();
        for (const char &c : prefix) {
            p = p->children[c - 'a'];
            if (!p) return nullptr;
        }
        return p;
    }
    
    std::unique_ptr<TrieNode> _root;
};

class WordFilter {
public:
    WordFilter(vector<string> words) {
        int n = words.size();
        for (int i=0; i<n; i++) {
            const string& w = words[i];
            string key = "{" + w;
            _trie.insert(key, i);
            for (int j=0; j<w.size(); ++j) {
                key = w[w.size()-1-j] + key;
                _trie.insert(key, i);
            }
        }
    }
    
    int f(string prefix, string suffix) {
        return _trie.startsWith(suffix + "{" + prefix);
    }
    
private:
    Trie _trie;
};

/**
 * Your WordFilter object will be instantiated and called as such:
 * WordFilter obj = new WordFilter(words);
 * int param_1 = obj.f(prefix,suffix);
 */
```



### (3) Trie + Set Intersection

Use two tries to separately find all words that match the prefix, plus all words that match the suffix. Then, we try to find the highest weight element in the intersection of these sets.



## 6. LeetCode 676 [Implement Magic Dictionary](https://leetcode.com/problems/implement-magic-dictionary/)

### (1) Brute Force with Bucket-By-Length

```c++

```



### (2) Trie

```c++

```































