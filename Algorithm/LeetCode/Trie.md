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
    
    /** Adds a word into the data structure. */
    void addWord(string word) {
        int len = (int)word.size();
        if(!wordList[len].count(word)) {
            wordList[len].insert(word);
        }
    }
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    bool search(string word) {
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

```



## 3. LeetCode 212

```c++

```



## 4. LeetCode 677 [Map Sum Pairs](https://leetcode.com/problems/map-sum-pairs/)

### (1) Trie

```c++

```



### (2) Hashmap (Simulating Trie)

```c++
class MapSum {
public:
    /** Initialize your data structure here. */
    MapSum() {}
    
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

