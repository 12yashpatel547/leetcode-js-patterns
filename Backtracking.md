# Senior Engineer's Study Guide: Backtracking

The **Backtracking** pattern is an algorithmic technique for solving problems recursively by trying to build a solution incrementally, one piece at a time, and removing those pieces that fail to satisfy the constraints of the problem at any point in time. As a senior engineer, the focus is on efficient **state management**, **pruning** (eliminating branches early), and understanding the **decision tree** depth.



---

## 1. Subsets
[https://leetcode.com/problems/subsets/](https://leetcode.com/problems/subsets/)

* **Complexity:** Time: $O(n \cdot 2^n)$ | Space: $O(n)$
* **Key Intuition:** At every index, we make a binary choice: either include the current element in the subset or exclude it.
* **The 'Talk Track':**
    * "I'll use a recursive helper to explore the decision tree where each level corresponds to an element in the input array."
    * "To manage state, I'll push an element into a `currentPath` array, recurse, and then pop it—this 'undo' step is the essence of backtracking."
    * "Since we need all possible subsets, we add a copy of the `currentPath` to our results at every step of the recursion."
* **Pseudocode:**
    1. Initialize `resultList`.
    2. Define `backtrack(startIndex, currentPath)`:
       a. Push a copy of `currentPath` to `resultList`.
       b. Loop from `i = startIndex` to `nums.length`:
          i. Add `nums[i]` to `currentPath`.
          ii. Recurse with `i + 1`.
          iii. Remove `nums[i]` from `currentPath`.
    3. Start `backtrack(0, [])`.
* **JavaScript Solution:**
```javascript
const subsets = (nums) => {
  const allPossibleSubsets = [];

  const findSubsets = (startIndex, currentPath) => {
    allPossibleSubsets.push([...currentPath]);

    for (let currentIndex = startIndex; currentIndex < nums.length; currentIndex++) {
      currentPath.push(nums[currentIndex]);
      findSubsets(currentIndex + 1, currentPath);
      currentPath.pop();
    }
  };

  findSubsets(0, []);
  return allPossibleSubsets;
};
```
* **Dry Run (input = [1, 2]):**
    * `start` 0: add `[]`.
    * Loop `i`=0: path [1], `start` 1. Add [1].
    * Loop `i`=1: path [1, 2], `start` 2. Add [1, 2].
    * Pop 2, Pop 1.
    * Loop `i`=1: path [2], `start` 2. Add [2].
* **Alternative Approach:** We could use a bitmask approach where each bit represents whether to include an element, which is iterative.

---

## 2. Combination Sum
[https://leetcode.com/problems/combination-sum/](https://leetcode.com/problems/combination-sum/)

* **Complexity:** Time: $O(N^{\frac{T}{M} + 1})$ where $T$ is target and $M$ is min value | Space: $O(\frac{T}{M})$
* **Key Intuition:** We explore the sum space by repeatedly choosing elements, including the current one, until the sum exceeds the target.
* **The 'Talk Track':**
    * "Because we can reuse elements, the recursive call will pass the `currentIndex` rather than `currentIndex + 1`."
    * "I'll implement a base case to stop recursion if the `remainingTarget` becomes negative to prune the search space."
    * "I'll use a `remainingTarget` parameter in the recursion to avoid re-summing the array at every step."
* **Pseudocode:**
    1. Define `backtrack(startIndex, currentPath, remainingTarget)`.
    2. If `remainingTarget === 0`, add copy of path to results.
    3. If `remainingTarget < 0`, return.
    4. Loop from `startIndex` to end:
       a. Add `nums[i]` to path.
       b. Recurse with `i` (reuse element) and `remainingTarget - nums[i]`.
       c. Pop.
* **JavaScript Solution:**
```javascript
const combinationSum = (candidates, target) => {
  const validCombinations = [];

  const searchCombinations = (startIndex, currentPath, remainingTarget) => {
    if (remainingTarget === 0) {
      validCombinations.push([...currentPath]);
      return;
    }
    if (remainingTarget < 0) return;

    for (let currentIndex = startIndex; currentIndex < candidates.length; currentIndex++) {
      const currentCandidate = candidates[currentIndex];
      currentPath.push(currentCandidate);
      // Pass currentIndex to allow unlimited reuse of the same element
      searchCombinations(currentIndex, currentPath, remainingTarget - currentCandidate);
      currentPath.pop();
    }
  };

  searchCombinations(0, [], target);
  return validCombinations;
};
```
* **Dry Run (input = [2], target = 4):**
    * `rem` 4: add 2. `rem` 2.
    * `rem` 2: add 2. `rem` 0. Success [2, 2].
    * `rem` 0: pop 2, pop 2.
* **Alternative Approach:** This can be modeled as a Dynamic Programming problem (unbounded knapsack), but backtracking is better for generating the actual paths.

---

## 3. Combinations
[https://leetcode.com/problems/combinations/](https://leetcode.com/problems/combinations/)

* **Complexity:** Time: $O(k \cdot \binom{n}{k})$ | Space: $O(k)$
* **Key Intuition:** This is a fixed-depth search where we select $k$ items out of $n$ without replacement or regard to order.
* **The 'Talk Track':**
    * "I'll terminate the recursion early as soon as the length of my `currentPath` reaches $k$."
    * "I can optimize the loop boundary: if the remaining numbers in the array are fewer than the slots left in my combination, I can stop early."
    * "We pass `i + 1` to the next level to ensure we don't pick the same number twice."
* **Pseudocode:**
    1. Define `backtrack(startNumber, currentPath)`.
    2. If `currentPath.length === k`, save and return.
    3. Loop from `startNumber` to `n`:
       a. Push `i`.
       b. Recurse `i + 1`.
       c. Pop.
* **JavaScript Solution:**
```javascript
const combine = (n, k) => {
  const resultCombinations = [];

  const buildCombination = (startNumber, currentPath) => {
    if (currentPath.length === k) {
      resultCombinations.push([...currentPath]);
      return;
    }

    // Optimization: Only loop if there are enough numbers left to fill k slots
    const numbersNeeded = k - currentPath.length;
    for (let currentNum = startNumber; currentNum <= n - numbersNeeded + 1; currentNum++) {
      currentPath.push(currentNum);
      buildCombination(currentNum + 1, currentPath);
      currentPath.pop();
    }
  };

  buildCombination(1, []);
  return resultCombinations;
};
```
* **Dry Run (n = 2, k = 1):**
    * `start` 1: push 1. Length is 1. Add [1]. Pop 1.
    * `start` 1: push 2. Length is 1. Add [2]. Pop 2.
* **Alternative Approach:** We could generate combinations using Lexicographic (Binary Sorted) combinations, but backtracking is the standard interview expectation.

---

## 4. Permutations
[https://leetcode.com/problems/permutations/](https://leetcode.com/problems/permutations/)

* **Complexity:** Time: $O(n \cdot n!)$ | Space: $O(n!)$
* **Key Intuition:** Unlike combinations, order matters. We use a "used" set or swap logic to ensure every element appears exactly once in different positions.
* **The 'Talk Track':**
    * "I'll maintain a `usedSet` to track which elements of the array are already present in the current permutation to maintain $O(1)$ lookup."
    * "The base case is reached when the `currentPath` length matches the input array length."
    * "This generates $n!$ leaf nodes in our recursion tree."
* **Pseudocode:**
    1. Define `backtrack(currentPath, usedSet)`.
    2. If `currentPath.length === nums.length`, save and return.
    3. Loop through every `num` in `nums`:
       a. If `num` is in `usedSet`, skip.
       b. Add `num` to `path` and `usedSet`.
       c. Recurse.
       d. Remove `num` from `path` and `usedSet`.
* **JavaScript Solution:**
```javascript
const permute = (nums) => {
  const resultPermutations = [];
  const usedElementsSet = new Set();

  const generatePermutation = (currentPath) => {
    if (currentPath.length === nums.length) {
      resultPermutations.push([...currentPath]);
      return;
    }

    for (const currentNumber of nums) {
      if (usedElementsSet.has(currentNumber)) continue;

      currentPath.push(currentNumber);
      usedElementsSet.add(currentNumber);
      
      generatePermutation(currentPath);
      
      currentPath.pop();
      usedElementsSet.delete(currentNumber);
    }
  };

  generatePermutation([]);
  return resultPermutations;
};
```
* **Dry Run (input = [1, 2]):**
    * Path [], Used {}.
    * Try 1: Path [1], Used {1}. Recurse.
    * Try 2: Path [1, 2], Used {1, 2}. Saved!
    * Backtrack: Path [2], Used {2}.
    * Try 1: Path [2, 1], Used {2, 1}. Saved!
* **Alternative Approach:** We could use a swapping algorithm (Heap's Algorithm) to generate permutations in-place, reducing space for the `usedSet`.

---

## 5. Subsets II
[https://leetcode.com/problems/subsets-ii/](https://leetcode.com/problems/subsets-ii/)

* **Complexity:** Time: $O(n \cdot 2^n)$ | Space: $O(n)$
* **Key Intuition:** To handle duplicates, we sort the array and skip the current element if it's the same as the previous element in the same recursive loop.
* **The 'Talk Track':**
    * "Sorting is a prerequisite here so that duplicates are adjacent."
    * "I'll use the condition `i > startIndex && nums[i] === nums[i-1]` to skip duplicates; this ensures we only start a branch with a duplicate value once at each level."
    * "This prevents the generation of identical subset paths."
* **Pseudocode:**
    1. Sort `nums`.
    2. Define `backtrack(startIndex, currentPath)`.
    3. Loop `i` from `startIndex` to end:
       a. If `i > startIndex` and `nums[i] === nums[i-1]`, `continue`.
       b. Push, recurse `i + 1`, pop.
* **JavaScript Solution:**
```javascript
const subsetsWithDup = (nums) => {
  const uniqueSubsets = [];
  nums.sort((valA, valB) => valA - valB);

  const findUniqueSubsets = (startIndex, currentPath) => {
    uniqueSubsets.push([...currentPath]);

    for (let currentIndex = startIndex; currentIndex < nums.length; currentIndex++) {
      // Skip the same element at the same depth to avoid duplicate branches
      if (currentIndex > startIndex && nums[currentIndex] === nums[currentIndex - 1]) {
        continue;
      }

      currentPath.push(nums[currentIndex]);
      findUniqueSubsets(currentIndex + 1, currentPath);
      currentPath.pop();
    }
  };

  findUniqueSubsets(0, []);
  return uniqueSubsets;
};
```
* **Dry Run (input = [1, 1]):**
    * `start` 0: add [].
    * `i`=0: path [1], `start` 1. Add [1].
    * `i`=1: path [1, 1], `start` 2. Add [1, 1].
    * Pop, Pop.
    * `i`=1: 1 === 1 and `i` > `start`, skip.
* **Alternative Approach:** We could generate all subsets and use a stringified Set to filter duplicates, but that's memory-inefficient.

---

## 6. Combination Sum II
[https://leetcode.com/problems/combination-sum-ii/](https://leetcode.com/problems/combination-sum-ii/)

* **Complexity:** Time: $O(2^n)$ | Space: $O(n)$
* **Key Intuition:** Each element can be used once, and the result must not contain duplicate combinations. We combine the "sort and skip" logic from Subsets II with the "remainingTarget" from Combination Sum I.
* **The 'Talk Track':**
    * "Since elements can't be reused, the recursive call moves to `i + 1`."
    * "The sorting and the skip condition `i > startIndex && candidates[i] === candidates[i-1]` are crucial for avoiding duplicate combinations in the output."
    * "I'll prune the search by breaking the loop entirely if the current candidate exceeds the remaining target, as further candidates will also be too large."
* **Pseudocode:**
    1. Sort `candidates`.
    2. Loop `i` from `startIndex`:
       a. If `candidates[i] > remainingTarget`, `break`.
       b. If `i > startIndex` and `candidates[i] === candidates[i-1]`, `continue`.
       c. Push, recurse `i + 1`, pop.
* **JavaScript Solution:**
```javascript
const combinationSum2 = (candidates, target) => {
  const results = [];
  candidates.sort((a, b) => a - b);

  const backtrack = (startIndex, currentPath, remainingTarget) => {
    if (remainingTarget === 0) {
      results.push([...currentPath]);
      return;
    }

    for (let currentIndex = startIndex; currentIndex < candidates.length; currentIndex++) {
      const currentVal = candidates[currentIndex];
      
      if (currentVal > remainingTarget) break; // Pruning
      if (currentIndex > startIndex && currentVal === candidates[currentIndex - 1]) continue;

      currentPath.push(currentVal);
      backtrack(currentIndex + 1, currentPath, remainingTarget - currentVal);
      currentPath.pop();
    }
  };

  backtrack(0, [], target);
  return results;
};
```
* **Dry Run (input = [1, 1], target = 1):**
    * `start` 0, `rem` 1: path [1], `rem` 0. Success [1].
    * `i`=1: 1 === 1 and 1 > 0, skip.
* **Alternative Approach:** We could use a frequency map to count available instances of each number.

---

## 7. Word Search
[https://leetcode.com/problems/word-search/](https://leetcode.com/problems/word-search/)

* **Complexity:** Time: $O(N \cdot M \cdot 3^L)$ where $L$ is word length | Space: $O(L)$
* **Key Intuition:** We use DFS to explore the grid. At each step, we check if the current cell matches the current character of the word, then recurse in four directions.
* **The 'Talk Track':**
    * "I'll use in-place marking by temporarily changing the cell value (e.g., to '#') to track visited cells without using $O(N \cdot M)$ extra space."
    * "If any branch of the recursion returns true, I'll bubble that boolean up to the initial call."
    * "I must remember to restore the original character after the recursion—this is the 'un-choose' part of backtracking."
* **Pseudocode:**
    1. Loop through all cells `(r, c)`.
    2. If `dfs(r, c, 0)` is true, return true.
    3. `dfs(r, c, charIndex)`:
       a. If `charIndex === word.length`, return true.
       b. If out of bounds or `board[r][c] !== word[charIndex]`, return false.
       c. Mark cell.
       d. Check 4 neighbors with `charIndex + 1`.
       e. Unmark cell.
* **JavaScript Solution:**
```javascript
const exist = (board, word) => {
  const totalRows = board.length;
  const totalCols = board[0].length;

  const searchWord = (currentRow, currentCol, wordIndex) => {
    if (wordIndex === word.length) return true;

    if (
      currentRow < 0 || currentCol < 0 || 
      currentRow >= totalRows || currentCol >= totalCols || 
      board[currentRow][currentCol] !== word[wordIndex]
    ) {
      return false;
    }

    const tempStorage = board[currentRow][currentCol];
    board[currentRow][currentCol] = '#'; // Mark as visited

    const foundMatch = (
      searchWord(currentRow + 1, currentCol, wordIndex + 1) ||
      searchWord(currentRow - 1, currentCol, wordIndex + 1) ||
      searchWord(currentRow, currentCol + 1, wordIndex + 1) ||
      searchWord(currentRow, currentCol - 1, wordIndex + 1)
    );

    board[currentRow][currentCol] = tempStorage; // Backtrack
    return foundMatch;
  };

  for (let rowIndex = 0; rowIndex < totalRows; rowIndex++) {
    for (let colIndex = 0; colIndex < totalCols; colIndex++) {
      if (searchWord(rowIndex, colIndex, 0)) return true;
    }
  }

  return false;
};
```
* **Dry Run (board = [['A']], word = "A"):**
    * (0,0) matches 'A'. `wordIndex` 0 -> 1.
    * Next call `wordIndex` 1 matches base case. Return true.
* **Alternative Approach:** Using a Trie can optimize searching for multiple words in the grid simultaneously.

---

## 8. Palindrome Partitioning
[https://leetcode.com/problems/palindrome-partitioning/](https://leetcode.com/problems/palindrome-partitioning/)

* **Complexity:** Time: $O(N \cdot 2^N)$ | Space: $O(N^2)$ for precomputing palindromes
* **Key Intuition:** We split the string at every possible index and only recurse if the prefix is a palindrome.
* **The 'Talk Track':**
    * "This is a 'Partitioning' problem which is a variation of the Subsets pattern."
    * "At each step, I'll take a substring from `startIndex` to `i`; if it's a palindrome, it's a valid path component."
    * "I can optimize this by precomputing a boolean DP table for palindromes to avoid $O(N)$ checks in each recursion."
* **Pseudocode:**
    1. Define `backtrack(startIndex, currentPath)`.
    2. If `startIndex === string.length`, add path to results.
    3. Loop `i` from `startIndex` to end:
       a. If `isPalindrome(substring(startIndex, i+1))`:
          i. Push substring, recurse `i + 1`, pop.
* **JavaScript Solution:**
```javascript
const partition = (inputString) => {
  const resultPartitions = [];

  const isPalindrome = (stringToCheck) => {
    let leftPointer = 0;
    let rightPointer = stringToCheck.length - 1;
    while (leftPointer < rightPointer) {
      if (stringToCheck[leftPointer] !== stringToCheck[rightPointer]) return false;
      leftPointer++;
      rightPointer--;
    }
    return true;
  };

  const findPartitions = (startIndex, currentPath) => {
    if (startIndex === inputString.length) {
      resultPartitions.push([...currentPath]);
      return;
    }

    for (let endIndex = startIndex + 1; endIndex <= inputString.length; endIndex++) {
      const currentSnippet = inputString.slice(startIndex, endIndex);
      
      if (isPalindrome(currentSnippet)) {
        currentPath.push(currentSnippet);
        findPartitions(endIndex, currentPath);
        currentPath.pop();
      }
    }
  };

  findPartitions(0, []);
  return resultPartitions;
};
```
* **Dry Run (input = "aab"):**
    * "a" is pal. Recurse "ab".
    * "a" is pal. Recurse "b".
    * "b" is pal. Success ["a", "a", "b"].
    * Backtrack: "aa" is pal. Recurse "b". Success ["aa", "b"].
* **Alternative Approach:** We can use DP to calculate the palindromes, but the overall time complexity is still dominated by the $2^N$ partitions.

---

## 9. Letter Combinations of a Phone Number
[https://leetcode.com/problems/letter-combinations-of-a-phone-number/](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)

* **Complexity:** Time: $O(4^N \cdot N)$ | Space: $O(N)$
* **Key Intuition:** This is a classic Cartesian product problem where the depth of the tree is the length of the input digits.
* **The 'Talk Track':**
    * "I'll use a static mapping object for the digit-to-letter translation."
    * "Each recursive level handles exactly one digit from the input string."
    * "If the input is empty, I'll handle that as a special case to return an empty array instead of `['']`."
* **Pseudocode:**
    1. Define `mapping = {2: 'abc', ...}`.
    2. Define `backtrack(digitIndex, currentString)`.
    3. If `currentString.length === digits.length`, add to results.
    4. For each `letter` in `mapping[digits[digitIndex]]`:
       a. Recurse `digitIndex + 1`, `currentString + letter`.
* **JavaScript Solution:**
```javascript
const letterCombinations = (digits) => {
  if (digits.length === 0) return [];

  const digitToLettersMap = {
    '2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
    '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'
  };

  const resultCombinations = [];

  const buildString = (currentIndex, currentString) => {
    if (currentString.length === digits.length) {
      resultCombinations.push(currentString);
      return;
    }

    const possibleLetters = digitToLettersMap[digits[currentIndex]];
    for (const char of possibleLetters) {
      buildString(currentIndex + 1, currentString + char);
    }
  };

  buildString(0, "");
  return resultCombinations;
};
```
* **Dry Run (digits = "23"):**
    * `idx` 0 ('2'): try 'a'.
    * `idx` 1 ('3'): try 'd'. Success "ad".
    * Backtrack: try 'e'. Success "ae".
* **Alternative Approach:** This could be solved iteratively using a queue (BFS style), but recursion is more idiomatic for small depths.

---

## 10. [Latest 2026] Find All Possible Recipes from Given Supplies
[https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/](https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/)

* **Complexity:** Time: $O(N + E)$ | Space: $O(N + E)$
* **Key Intuition:** This is a dependency-based backtracking problem (often solved with topological sort, but solvable via DFS + Memoization) where we determine if a recipe can be completed based on its ingredients.
* **The 'Talk Track':**
    * "I'll use a `memo` map to track whether a recipe is 'craftable' to prevent redundant recursive calls in a complex dependency graph."
    * "I need to handle cycles—if a recipe requires itself through a chain, it's uncraftable."
    * "We treat the supplies as base-case ingredients that are always available."
* **Pseudocode:**
    1. Map `recipes` to their `ingredients`.
    2. Define `canCraft(item, visitingSet)`:
       a. If `item` is in `supplies`, return true.
       b. If `item` is in `visitingSet`, return false (cycle).
       c. If `memo` has result, return it.
       d. For each `ingredient`: if `!canCraft(ingredient)`, return false.
       e. Return true.
* **JavaScript Solution:**
```javascript
const findAllRecipes = (recipes, ingredients, supplies) => {
  const supplySet = new Set(supplies);
  const recipeToIngredientsMap = new Map();
  recipes.forEach((recipe, index) => recipeToIngredientsMap.set(recipe, ingredients[index]));

  const craftableMemo = new Map();
  const currentPathSet = new Set();

  const canMake = (targetItem) => {
    if (supplySet.has(targetItem)) return true;
    if (!recipeToIngredientsMap.has(targetItem)) return false;
    if (currentPathSet.has(targetItem)) return false; // Cycle
    if (craftableMemo.has(targetItem)) return craftableMemo.get(targetItem);

    currentPathSet.add(targetItem);
    
    const ingredientsRequired = recipeToIngredientsMap.get(targetItem);
    const result = ingredientsRequired.every(ingredient => canMake(ingredient));
    
    currentPathSet.delete(targetItem);
    craftableMemo.set(targetItem, result);
    return result;
  };

  return recipes.filter(recipe => canMake(recipe));
};
```
* **Dry Run (rec=['bread'], ing=[['yeast']], sup=['yeast']):**
    * `canMake('bread')`: checks 'yeast'.
    * 'yeast' in `supplySet`. Returns true.
    * Result: ['bread'].
* **Alternative Approach:** Topological sort (Kahn's Algorithm) is the standard $O(V+E)$ approach for this.

---

## 11. [Latest 2026] Fair Distribution of Cookies
[https://leetcode.com/problems/fair-distribution-of-cookies/](https://leetcode.com/problems/fair-distribution-of-cookies/)

* **Complexity:** Time: $O(k^n)$ | Space: $O(k)$
* **Key Intuition:** We distribute $n$ cookie bags to $k$ children to minimize the maximum number of cookies any one child receives.
* **The 'Talk Track':**
    * "At each cookie bag, I have $k$ choices (children) to give it to."
    * "I'll use a pruning optimization: if the current `maxCookies` in a branch already exceeds the best `minMax` found so far, I'll stop searching that branch."
    * "Another optimization: if a child has no cookies yet, giving the bag to them is functionally identical to giving it to any other empty-handed child."
* **Pseudocode:**
    1. Define `childrenSums = [0, 0, ... k]`.
    2. Define `backtrack(cookieIndex)`:
       a. If `cookieIndex === n`, return `max(childrenSums)`.
       b. Loop through `k` children:
          i. Add `cookies[cookieIndex]` to `childSums[k]`.
          ii. Recurse next bag.
          iii. Backtrack (subtract).
* **JavaScript Solution:**
```javascript
const distributeCookies = (cookies, childCount) => {
  let minimumUnfairness = Infinity;
  const currentChildTallies = new Array(childCount).fill(0);

  const allocateBags = (cookieIndex) => {
    if (cookieIndex === cookies.length) {
      minimumUnfairness = Math.min(minimumUnfairness, Math.max(...currentChildTallies));
      return;
    }

    // Optimization: Pruning if current max already exceeds the best result
    if (Math.max(...currentChildTallies) >= minimumUnfairness) return;

    for (let childIndex = 0; childIndex < childCount; childIndex++) {
      currentChildTallies[childIndex] += cookies[cookieIndex];
      allocateBags(cookieIndex + 1);
      currentChildTallies[childIndex] -= cookies[cookieIndex];

      // Optimization: Avoid symmetric distributions to empty children
      if (currentChildTallies[childIndex] === 0) break;
    }
  };

  allocateBags(0);
  return minimumUnfairness;
};
```
* **Dry Run (cookies = [10, 20], k = 2):**
    * Bag 10: Child 0 gets 10.
    * Bag 20: Child 0 gets 20 (Total 30) OR Child 1 gets 20 (Max 20).
    * `minMax` becomes 20.
* **Alternative Approach:** This is an NP-hard problem, so while we can use DP with bitmasking, backtracking with aggressive pruning is often the most practical solution.

---
