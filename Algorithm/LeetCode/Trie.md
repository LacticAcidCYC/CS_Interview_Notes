## 1. LeetCode 208 [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)

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



## 2. LeetCode 211 [Add and Search Word - Data structure design](https://leetcode.com/problems/add-and-search-word-data-structure-design/)

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



## 3. LeetCode 212

```c++

```

