# Senior Engineer's Study Guide: Tries (Prefix Trees)

The **Trie** (pronounced "try") pattern is a specialized tree structure used for efficient retrieval of keys in a large dataset of strings. It is the optimal choice for features like autocomplete, spell checkers, and prefix matching, as it allows us to search for words or prefixes in $O(L)$ time, where $L$ is the length of the word, independent of the total number of words stored.

---

## 1. Implement Trie (Prefix Tree)
[https://leetcode.com/problems/implement-trie-prefix-tree/](https://leetcode.com/problems/implement-trie-prefix-tree/)



* **Complexity:**
    * **Insert:** Time: $O(L)$ | Space: $O(L \cdot N)$ (where $L$ is word length and $N$ is number of words)
    * **Search/StartsWith:** Time: $O(L)$ | Space: $O(1)$
* **Key Intuition:** Instead of storing whole strings, we store character transitions in a nested object structure; a boolean flag `isEndOfWord` marks where a complete word finishes.
* **The 'Talk Track':**
    * "I'll represent each node as a JavaScript object where keys are characters, providing $O(1)$ access to child nodes."
    * "For the `startsWith` method, the logic is identical to `search`, except we don't care about the `isEndOfWord` flag at the final node."
    * "Using a class structure here makes the data encapsulation clean and mimics how we'd build an autocomplete service in production."
* **Pseudocode:**
    1. **TrieNode:** A constructor with a `children` map and an `isEndOfWord` boolean.
    2. **Insert:** Iterate through each `character` in the word. If the `character` doesn't exist in current node's `children`, create a new node. Move to the child node. After the loop, set `isEndOfWord = true`.
    3. **Search:** Traverse the trie character by character. If a character is missing, return `false`. After the loop, return the value of `isEndOfWord`.
* **JavaScript Solution:**
```javascript
class TrieNode {
  constructor() {
    this.children = {};
    this.isEndOfWord = false;
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  insert(word) {
    let currentNode = this.root;
    for (const character of word) {
      if (!currentNode.children[character]) {
        currentNode.children[character] = new TrieNode();
      }
      currentNode = currentNode.children[character];
    }
    currentNode.isEndOfWord = true;
  }

  search(word) {
    let currentNode = this.root;
    for (const character of word) {
      if (!currentNode.children[character]) {
        return false;
      }
      currentNode = currentNode.children[character];
    }
    return currentNode.isEndOfWord;
  }

  startsWith(prefix) {
    let currentNode = this.root;
    for (const character of prefix) {
      if (!currentNode.children[character]) {
        return false;
      }
      currentNode = currentNode.children[character];
    }
    return true;
  }
}
```
* **Dry Run (insert "a", search "a"):**
    * `insert("a")`: `root.children['a']` is created. `node('a').isEndOfWord` becomes `true`.
    * `search("a")`: Find `root.children['a']`. Check `isEndOfWord`, which is `true`. Return `true`.
* **Alternative Approach:** We could use an array of size 26 for `children` if we were strictly using lowercase English letters, which might be slightly faster but less flexible for Unicode.

---

## 2. Design Add and Search Words Data Structure
[https://leetcode.com/problems/design-add-and-search-words-data-structure/](https://leetcode.com/problems/design-add-and-search-words-data-structure/)

* **Complexity:**
    * **Add:** Time: $O(L)$ | Space: $O(L \cdot N)$
    * **Search:** Time: $O(M)$ (where $M$ is total nodes in Trie in worst case of "...") | Space: $O(L)$ (recursion stack)
* **Key Intuition:** This extends the standard Trie search with a backtracking (DFS) component to handle the '.' wildcard character.
* **The 'Talk Track':**
    * "When encountering the wildcard '.', I need to branch out and check every possible child node at that current level."
    * "I'll use a helper function to perform a Depth-First Search so I can pass the current Trie node and the remaining substring index."
    * "This is a great example of combining a specialized data structure with a recursive algorithm to solve non-trivial search patterns."
* **Pseudocode:**
    1. **addWord:** Standard Trie insertion.
    2. **search:** Call a recursive helper `searchInNode(wordIndex, node)`.
    3. **searchInNode:**
       a. If `wordIndex` equals word length, return `node.isEndOfWord`.
       b. If `word[wordIndex]` is '.', loop through all keys in `node.children` and recursively call `searchInNode` for each. Return `true` if any return `true`.
       c. If character is standard, move to child and recurse.
* **JavaScript Solution:**
```javascript
class WordDictionary {
  constructor() {
    this.root = {};
  }

  addWord(word) {
    let currentNode = this.root;
    for (const character of word) {
      if (!currentNode[character]) {
        currentNode[character] = {};
      }
      currentNode = currentNode[character];
    }
    currentNode.isEndOfWord = true;
  }

  search(word) {
    const searchInNode = (currentIndex, node) => {
      let currentNode = node;
      for (let i = currentIndex; i < word.length; i++) {
        const character = word[i];
        if (character === '.') {
          // Check all possible paths for wildcard
          for (const key of Object.keys(currentNode)) {
            if (key === 'isEndOfWord') continue;
            if (searchInNode(i + 1, currentNode[key])) return true;
          }
          return false;
        } else {
          if (!currentNode[character]) return false;
          currentNode = currentNode[character];
        }
      }
      return currentNode.isEndOfWord === true;
    };

    return searchInNode(0, this.root);
  }
}
```
* **Dry Run (add "a", search "."):**
    * `addWord("a")`: `root['a'] = { isEndOfWord: true }`.
    * `search(".")`: Hit wildcard. Loop keys of `root`. Key is 'a'. Call `searchInNode(1, root['a'])`.
    * `currentIndex` (1) === `word.length`. Return `root['a'].isEndOfWord` (true).
* **Alternative Approach:** For very short words, you could store words in a Map keyed by length, but the Trie-DFS approach is the intended solution for generalized patterns.

---

## 3. [Latest 2026] Longest Common Suffix Queries
[https://leetcode.com/problems/longest-common-suffix-queries/](https://leetcode.com/problems/longest-common-suffix-queries/)

* **Complexity:** Time: $O((N + M) \cdot L)$ | Space: $O(N \cdot L)$
* **Key Intuition:** A common suffix is just a common prefix of reversed strings; we store the index of the "best" (smallest/shortest) word at every node in the Trie.
* **The 'Talk Track':**
    * "The key to 'Longest Common Suffix' is realizing it's a 'Longest Common Prefix' problem if we reverse all strings."
    * "At each Trie node, I'll pre-calculate and store the index of the string that has the smallest length (and smallest original index as a tie-breaker)."
    * "This allows us to answer each query in $O(LengthOfQuery)$ time because the 'best' answer is already cached at the deepest possible matching node."
* **Pseudocode:**
    1. For each `word` in `wordsContainer`, reverse it and insert into Trie.
    2. At each node, maintain `bestIndex`. Update it if the current word is shorter than the word at `bestIndex`.
    3. For each query, reverse it and traverse the Trie.
    4. Return the `bestIndex` of the last node reached.
* **JavaScript Solution:**
```javascript
class SuffixTrieNode {
  constructor(index) {
    this.children = {};
    this.bestIndex = index;
  }
}

const stringIndices = (wordsContainer, wordsQuery) => {
  const root = new SuffixTrieNode(0);
  
  // Find global best for root (shortest word in container)
  for (let i = 1; i < wordsContainer.length; i++) {
    if (wordsContainer[i].length < wordsContainer[root.bestIndex].length) {
      root.bestIndex = i;
    }
  }

  for (let wordIndex = 0; wordIndex < wordsContainer.length; wordIndex++) {
    let currentNode = root;
    const word = wordsContainer[wordIndex];
    // Traverse reversed
    for (let charIndex = word.length - 1; charIndex >= 0; charIndex--) {
      const char = word[charIndex];
      if (!currentNode.children[char]) {
        currentNode.children[char] = new SuffixTrieNode(wordIndex);
      }
      currentNode = currentNode.children[char];
      
      // Update best index at this node
      const currentBestWord = wordsContainer[currentNode.bestIndex];
      if (word.length < currentBestWord.length) {
        currentNode.bestIndex = wordIndex;
      }
    }
  }

  return wordsQuery.map(query => {
    let currentNode = root;
    for (let charIndex = query.length - 1; charIndex >= 0; charIndex--) {
      const char = query[charIndex];
      if (!currentNode.children[char]) break;
      currentNode = currentNode.children[char];
    }
    return currentNode.bestIndex;
  });
};
```
* **Dry Run (wordsContainer = ["ab", "b"], query = ["b"]):**
    * Insert reversed "ba" (idx 0) and "b" (idx 1).
    * `root.children['b']` will have `bestIndex` 1 because "b" is shorter than "ab".
    * Query reversed "b" ends at node 'b'. Return 1.
* **Alternative Approach:** You could compare every query against every word, but that's $O(N \cdot M \cdot L)$, which is far too slow for 2026-level interview constraints.

---

## 4. [Latest 2026] Count Prefix and Suffix Pairs II
[https://leetcode.com/problems/count-prefix-and-suffix-pairs-ii/](https://leetcode.com/problems/count-prefix-and-suffix-pairs-ii/)

* **Complexity:** Time: $O(N \cdot L)$ | Space: $O(N \cdot L)$
* **Key Intuition:** A word is both a prefix and a suffix of another if `word[i]` and `word[length-1-i]` match the target's characters at those same positions; we can store this as a pair-key in a Trie.
* **The 'Talk Track':**
    * "To check if a string is both a prefix and a suffix simultaneously, I'll use a Trie where each node is keyed by a pair of characters: `(char, corresponding_suffix_char)`."
    * "This reduces the problem to a single Trie traversal for each word, counting how many previous words ended on the path we are currently walking."
    * "By using a pair-key like `char1 + "," + char2`, we effectively traverse two versions of the string (forward and backward) at the same time."
* **Pseudocode:**
    1. Initialize `root = {}` and `totalPairs = 0`.
    2. For each `word`:
       a. Traverse Trie. For each `i`, the key is `word[i] + "_" + word[len-1-i]`.
       b. As you move, add any `count` found at intermediate nodes to `totalPairs`.
       c. At the final node, increment its `count`.
* **JavaScript Solution:**
```javascript
const countPrefixSuffixPairs = (words) => {
  let root = {};
  let totalPairsCount = 0;

  for (const word of words) {
    let currentNode = root;
    const wordLength = word.length;

    for (let currentIndex = 0; currentIndex < wordLength; currentIndex++) {
      // Pair prefix character with corresponding suffix character
      const charKey = `${word[currentIndex]}_${word[wordLength - 1 - currentIndex]}`;
      
      if (!currentNode[charKey]) {
        currentNode[charKey] = { count: 0 };
      }
      currentNode = currentNode[charKey];
      
      // If a previous word ended at this node, it's a prefix/suffix match
      totalPairsCount += currentNode.count;
    }
    // Mark this word as completed in the trie
    currentNode.count++;
  }

  return totalPairsCount;
};
```
* **Dry Run (words = ["a", "aba"]):**
    * "a": `root["a_a"]` count becomes 1.
    * "aba": `i=0` key "a_a". `total` += `node.count` (1). `i=1` key "b_b". `i=2` key "a_a".
    * Final count: 1.
* **Alternative Approach:** You could use KMP or Z-algorithm for each pair, but that would be $O(N^2 \cdot L)$.

---
