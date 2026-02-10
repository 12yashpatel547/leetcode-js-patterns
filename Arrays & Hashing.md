# Senior Engineer's Study Guide: Arrays & Hashing

This guide focuses on the **Arrays & Hashing** pattern. This is the foundation of technical interviews, centered on using Hash Maps and Hash Sets to trade space for time, turning $O(n^2)$ brute-force searches into $O(n)$ linear lookups.

---

## 1. Contains Duplicate
[https://leetcode.com/problems/contains-duplicate/](https://leetcode.com/problems/contains-duplicate/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** We use a `Set` to track unique elements seen so far; if we encounter an element already in the set, we found a duplicate.
* **The 'Talk Track':**
    * "I'll use a `Set` here because it provides $O(1)$ average-time complexity for lookups and insertions."
    * "I'm prioritizing time complexity over space, assuming the input size could be large."
    * "Early exit: as soon as I find a duplicate, I return true to avoid unnecessary iterations."
* **Pseudocode:**
    1. Initialize an empty Set `seenNumbers`.
    2. Iterate through each `currentNumber` in the input array.
    3. If `currentNumber` exists in `seenNumbers`, return `true`.
    4. Otherwise, add `currentNumber` to `seenNumbers`.
    5. Return `false` if the loop completes.
* **JavaScript Solution:**
```javascript
const containsDuplicate = (nums) => {
  const seenNumbers = new Set();
  for (const currentNumber of nums) {
    if (seenNumbers.has(currentNumber)) {
      return true;
    }
    seenNumbers.add(currentNumber);
  }
  return false;
};
```
### Example-Based Dry Run (Example 1)
**Input:** `nums = [1, 2, 3, 1]`  
**Output:** `true`

| Iteration | `currentNumber` | `seenElements` (Before) | `seenElements.has()` | Action |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 1 | `{}` | false | Add 1 |
| 2 | 2 | `{1}` | false | Add 2 |
| 3 | 3 | `{1, 2}` | false | Add 3 |
| 4 | 1 | `{1, 2, 3}` | true | **Return true** |

* **Alternative Approach:** We could sort the array first ($O(n \log n)$ time) and check adjacent elements, which would reduce space complexity to $O(1)$.

---

## 2. Valid Anagram
[https://leetcode.com/problems/valid-anagram/](https://leetcode.com/problems/valid-anagram/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$ (since the character set is bounded by 26)
* **Key Intuition:** Two strings are anagrams if they have the exact same character frequencies.
* **The 'Talk Track':**
    * "First, I'll check if the lengths differ, as that's an immediate disqualifier."
    * "I’ll use a frequency map to count occurrences; incrementing for string A and decrementing for string B."
    * "This approach is robust even if we scale to Unicode, though here we assume lowercase English letters."
* **Pseudocode:**
    1. If `stringA.length !== stringB.length`, return `false`.
    2. Create a `charCount` map.
    3. Loop through `stringA`: increment `charCount[char]`.
    4. Loop through `stringB`: decrement `charCount[char]`.
    5. If any count in `charCount` is not zero, return `false`.
* **JavaScript Solution:**
```javascript
const isAnagram = (stringA, stringB) => {
  if (stringA.length !== stringB.length) return false;

  const charCount = {};
  for (let currentIndex = 0; currentIndex < stringA.length; currentIndex++) {
    const charA = stringA[currentIndex];
    const charB = stringB[currentIndex];
    charCount[charA] = (charCount[charA] || 0) + 1;
    charCount[charB] = (charCount[charB] || 0) - 1;
  }

  return Object.values(charCount).every((count) => count === 0);
};
```
### Example-Based Dry Run (Example 1)
**Input:** `s = "anagram", t = "nagaram"`
**Output:** `true`

| Step | Current Char | `characterCounts` State |
| :--- | :--- | :--- |
| Init | - | `{}` |
| Loop 1 (s) | 'a' | `{a: 3, n: 1, g: 1, r: 1, m: 1}` (after all iterations) |
| Loop 2 (t) | 'n' | `{a: 3, n: 0, g: 1, r: 1, m: 1}` |
| Loop 2 (t) | 'a' | `{a: 2, n: 0, g: 1, r: 1, m: 1}` |
| ... | ... | All counts reach 0 |

* **Alternative Approach:** We could sort both strings and compare them, but that would result in $O(n \log n)$ time complexity.

---

## 3. Two Sum
[https://leetcode.com/problems/two-sum/](https://leetcode.com/problems/two-sum/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** For every number, we calculate its "complement" ($target - current$) and check if we've seen that complement before.
* **The 'Talk Track':**
    * "Using a Hash Map allows me to find the 'needed' value in $O(1)$ time."
    * "I'll store the value as the key and its index as the value in the map."
    * "I process the array in a single pass to maintain efficiency."
* **Pseudocode:**
    1. Initialize an empty Map `visitedNumbers`.
    2. Loop through `nums` using `currentIndex`.
    3. `neededValue = target - nums[currentIndex]`.
    4. If `neededValue` is in `visitedNumbers`, return `[visitedNumbers[neededValue], currentIndex]`.
    5. Else, add `nums[currentIndex]` to `visitedNumbers`.
* **JavaScript Solution:**
```javascript
const twoSum = (nums, target) => {
  const visitedNumbers = new Map();
  for (let currentIndex = 0; currentIndex < nums.length; currentIndex++) {
    const currentNum = nums[currentIndex];
    const complement = target - currentNum;
    if (visitedNumbers.has(complement)) {
      return [visitedNumbers.get(complement), currentIndex];
    }
    visitedNumbers.set(currentNum, currentIndex);
  }
};
```
### Example-Based Dry Run (Example 1)
**Input:** `nums = [2, 7, 11, 15], target = 9`
**Output:** `[0, 1]`

| `currentIndex` | `currentNumber` | `complementValue` | `Map.has(complement)` | Map State (After) |
| :--- | :--- | :--- | :--- | :--- |
| 0 | 2 | 7 | False | `{2: 0}` |
| 1 | 7 | 2 | True | **Return [0, 1]** |

* **Alternative Approach:** If the array were sorted, we could use the Two-Pointer technique to achieve $O(1)$ space.

---

## 4. Group Anagrams
[https://leetcode.com/problems/group-anagrams/](https://leetcode.com/problems/group-anagrams/)

* **Complexity:** Time: $O(n \cdot k \log k)$ (where $k$ is max string length) | Space: $O(n \cdot k)$
* **Key Intuition:** Categorize strings by their sorted version; all anagrams will share the same sorted "key".
* **The 'Talk Track':**
    * "I'll use a Map where the key is the sorted string and the value is an array of original strings."
    * "This ensures that all words with the same characters are grouped together automatically."
    * "For performance at scale, I'd consider a character frequency array as a key instead of sorting."
* **Pseudocode:**
    1. Create a Map `anagramGroups`.
    2. For each `currentString` in input:
       a. Sort the characters of `currentString` to create `sortedKey`.
       b. If `sortedKey` isn't in `anagramGroups`, add it with an empty array.
       c. Push `currentString` to `anagramGroups[sortedKey]`.
    3. Return values of `anagramGroups`.
* **JavaScript Solution:**
```javascript
const groupAnagrams = (strs) => {
  const groups = {};
  for (const currentStr of strs) {
    const sortedKey = currentStr.split('').sort().join('');
    if (!groups[sortedKey]) {
      groups[sortedKey] = [];
    }
    groups[sortedKey].push(currentStr);
  }
  return Object.values(groups);
};
```
### Example-Based Dry Run (Example 1)
**Input:** `strs = ["eat","tea","tan","ate","nat","bat"]`  
**Expected Output:** `[["eat","tea","ate"],["tan","nat"],["bat"]]`

| Iteration | `currentStr` | `sortedKey` | `groups` (Before) | Evaluation `!groups[sortedKey]` | Action / State Change |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | "eat" | "aet" | `{}` | True | Initialize `groups["aet"] = []`; Push "eat" |
| 2 | "tea" | "aet" | `{"aet": ["eat"]}` | False | Push "tea" to `groups["aet"]` |
| 3 | "tan" | "ant" | `{"aet": ["eat", "tea"]}` | True | Initialize `groups["ant"] = []`; Push "tan" |
| 4 | "ate" | "aet" | `{"aet": ["eat", "tea"], "ant": ["tan"]}` | False | Push "ate" to `groups["aet"]` |
| 5 | "nat" | "ant" | `{"aet": ["eat", "tea", "ate"], "ant": ["tan"]}` | False | Push "nat" to `groups["ant"]` |
| 6 | "bat" | "abt" | `{"aet": ["eat", "tea", "ate"], "ant": ["tan", "nat"]}` | True | Initialize `groups["abt"] = []`; Push "bat" |

**Final Return:** `[["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]`
* **Alternative Approach:** We could use a frequency count of 26 characters as a string key to achieve $O(n \cdot k)$ time.

---

## 5. Top K Frequent Elements
[https://leetcode.com/problems/top-k-frequent-elements/](https://leetcode.com/problems/top-k-frequent-elements/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** Use Bucket Sort logic: the maximum frequency is the array length, so we can group numbers by their frequency index.
* **The 'Talk Track':**
    * "I'll avoid a Min-Heap ($O(n \log k)$) and use Bucket Sort to achieve true $O(n)$ time."
    * "The index of my bucket array will represent the frequency of the elements."
    * "I'll iterate from the end of the bucket array to the beginning to pick the most frequent ones."
* **Pseudocode:**
    1. Count frequencies using a Map.
    2. Create an array of arrays `buckets` where `index` is frequency.
    3. Fill `buckets` based on frequency counts.
    4. Iterate `buckets` from end to start, collecting elements until we reach `k`.
* **JavaScript Solution:**
```javascript
const topKFrequent = (nums, k) => {
  const counts = new Map();
  const collectionLength = nums.length;
  const buckets = Array.from({ length: collectionLength + 1 }, () => []);

  for (const num of nums) {
    counts.set(num, (counts.get(num) || 0) + 1);
  }

  for (const [num, freq] of counts) {
    buckets[freq].push(num);
  }

  const result = [];
  for (let freqIndex = buckets.length - 1; freqIndex >= 0 && result.length < k; freqIndex--) {
    result.push(...buckets[freqIndex]);
  }
  return result.slice(0, k);
};
```
### Example-Based Dry Run (Example 1)
**Input:** `nums = [1,1,1,2,2,3], k = 2`  
**Output:** `[1, 2]`

| Step | State |
| :--- | :--- |
| Map | `{1: 3, 2: 2, 3: 1}` |
| Buckets | `[[], [3], [2], [1], [], [], []]` |
| Result (Reverse Scan) | Index 3 -> `[1]`, Index 2 -> `[1, 2]`. Return. |

* **Alternative Approach:** A Min-Heap of size $k$ would solve this in $O(n \log k)$ time if memory was extremely constrained.

---

## 6. Product of Array Except Self
[https://leetcode.com/problems/product-of-array-except-self/](https://leetcode.com/problems/product-of-array-except-self/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$ (output array doesn't count)
* **Key Intuition:** The answer for any index is (Product of all elements to the left) $\times$ (Product of all elements to the right).
* **The 'Talk Track':**
    * "The constraint forbids using the division operator, so I'll use prefix and suffix products."
    * "I can optimize space by storing the prefix products in the result array and then calculating suffix products on the fly."
    * "This maintains $O(n)$ time with only a single result array for space."
* **Pseudocode:**
    1. Initialize `result` array with 1s.
    2. Pass 1 (Prefix): `result[i] = product of everything before i`.
    3. Pass 2 (Suffix): Multiply `result[i]` by `product of everything after i`.
* **JavaScript Solution:**
```javascript
const productExceptSelf = (nums) => {
  const collectionLength = nums.length;
  const result = new Array(collectionLength).fill(1);

  let prefixProduct = 1;
  for (let currentIndex = 0; currentIndex < collectionLength; currentIndex++) {
    result[currentIndex] = prefixProduct;
    prefixProduct *= nums[currentIndex];
  }

  let suffixProduct = 1;
  for (let currentIndex = collectionLength - 1; currentIndex >= 0; currentIndex--) {
    result[currentIndex] *= suffixProduct;
    suffixProduct *= nums[currentIndex];
  }

  return result;
};
```
### Example-Based Dry Run (Example 1)
**Input:** `nums = [1,2,3,4]`  
**Output:** `[24,12,8,6]`

| Pass | Index | Accumulator | `result` array |
| :--- | :--- | :--- | :--- |
| Left | 0, 1, 2, 3 | - | `[1, 1, 2, 6]` |
| Right | 3 | 1 | `[1, 1, 2, 6]` |
| Right | 2 | 4 | `[1, 1, 8, 6]` |
| Right | 1 | 12 | `[1, 12, 8, 6]` |
| Right | 0 | 24 | `[24, 12, 8, 6]` |

* **Alternative Approach:** Using two separate arrays for prefix and suffix products makes the logic clearer but uses $O(n)$ extra space.

---

## 7. Valid Sudoku
[https://leetcode.com/problems/valid-sudoku/](https://leetcode.com/problems/valid-sudoku/)

* **Complexity:** Time: $O(1)$ (fixed $9 \times 9$ grid) | Space: $O(1)$
* **Key Intuition:** Use three sets of hash maps/sets to track duplicates in rows, columns, and $3 \times 3$ sub-boxes simultaneously.
* **The 'Talk Track':**
    * "I'll iterate through the grid once, checking three conditions for every filled cell."
    * "The box index can be calculated using `Math.floor(row/3) * 3 + Math.floor(col/3)`."
    * "If a number is already in the specific row, col, or box set, the board is invalid."
* **Pseudocode:**
    1. Create sets for `rows`, `cols`, and `boxes`.
    2. Loop through every cell (r, c).
    3. If cell is '.', skip.
    4. Calculate `boxIndex`.
    5. If value exists in `rows[r]`, `cols[c]`, or `boxes[boxIndex]`, return `false`.
    6. Add value to all three.
* **JavaScript Solution:**
```javascript
const isValidSudoku = (board) => {
  const rows = Array.from({ length: 9 }, () => new Set());
  const cols = Array.from({ length: 9 }, () => new Set());
  const boxes = Array.from({ length: 9 }, () => new Set());

  for (let rowIndex = 0; rowIndex < 9; rowIndex++) {
    for (let colIndex = 0; colIndex < 9; colIndex++) {
      const value = board[rowIndex][colIndex];
      if (value === ".") continue;

      const boxIndex = Math.floor(rowIndex / 3) * 3 + Math.floor(colIndex / 3);
      if (rows[rowIndex].has(value) || cols[colIndex].has(value) || boxes[boxIndex].has(value)) {
        return false;
      }

      rows[rowIndex].add(value);
      cols[colIndex].add(value);
      boxes[boxIndex].add(value);
    }
  }
  return true;
};
```
### Example-Based Dry Run (Example 1 Snippet)
**Input:** `board = [["5","3",".",".","7",".",".",".","."], ...]`
**Output:** `true`

| Row | Col | Value | Box ID | `seenConstraints` Addition | Result |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | 0 | "5" | "0-0" | `row-0-5`, `column-0-5`, `box-0-0-5` | Continue |
| 0 | 1 | "3" | "0-0" | `row-0-3`, `column-1-3`, `box-0-0-3` | Continue |
| 0 | 4 | "7" | "0-1" | `row-0-7`, `column-4-7`, `box-0-1-7` | Continue |

* **Alternative Approach:** We could use bitmasking to represent the sets for even more memory efficiency.

---

## 8. Encode and Decode Strings
[https://www.lintcode.com/problem/659/](https://www.lintcode.com/problem/659/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$ (ignoring output)
* **Key Intuition:** Use a "Length + Delimiter" (e.g., `4#` for "lint") to handle strings containing any special characters.
* **The 'Talk Track':**
    * "A simple delimiter won't work because the strings themselves could contain that character."
    * "I'll prefix each string with its length followed by a '#' to act as a robust metadata header."
    * "During decoding, I'll read the length first, then jump that many characters forward."
* **Pseudocode:**
    * **Encode:** Join strings with `str.length + "#" + str`.
    * **Decode:** Find the next '#', parse the number before it, extract that many chars, repeat.
* **JavaScript Solution:**
```javascript
const encode = (strs) => {
  return strs.map(str => `${str.length}#${str}`).join('');
};

const decode = (str) => {
  const result = [];
  let currentIndex = 0;
  while (currentIndex < str.length) {
    let delimiterIndex = str.indexOf('#', currentIndex);
    let length = parseInt(str.substring(currentIndex, delimiterIndex));
    currentIndex = delimiterIndex + 1;
    result.push(str.substring(currentIndex, currentIndex + length));
    currentIndex += length;
  }
  return result;
};
```
### Example-Based Dry Run (Example 1)
**Input:** `["lint","code","love","you"]`
**Output:** `["lint","code","love","you"]`

| `currentPointer` | `delimiterIndex` | `stringLength` | `targetString` | New `currentPointer` |
| :--- | :--- | :--- | :--- | :--- |
| 0 | 1 | 4 | "lint" | 6 |
| 6 | 7 | 4 | "code" | 12 |
| 12 | 13 | 4 | "love" | 18 |
| 18 | 19 | 3 | "you" | 23 (End) |

* **Alternative Approach:** We could use non-printable ASCII characters as delimiters, but that is less robust than length-prefixing.

---

## 9. Longest Consecutive Sequence
[https://leetcode.com/problems/longest-consecutive-sequence/](https://leetcode.com/problems/longest-consecutive-sequence/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** Only start counting a sequence if the current number is the "start" of a sequence (i.e., `num - 1` doesn't exist in the set).
* **The 'Talk Track':**
    * "I'll put all numbers in a Set for $O(1)$ lookups."
    * "The key optimization is identifying the start of a sequence so we don't do redundant $O(n)$ work for every number."
    * "Even though there's a nested while loop, each element is visited at most twice, ensuring linear time."
* **Pseudocode:**
    1. Store all numbers in `numSet`.
    2. For each `num` in `numSet`:
       a. If `num - 1` is not in `numSet`:
          i. Count how many consecutive numbers exist (`num + 1`, `num + 2`...).
          ii. Update `maxStreak`.
    3. Return `maxStreak`.
* **JavaScript Solution:**
```javascript
const longestConsecutive = (nums) => {
  const numSet = new Set(nums);
  let maxStreak = 0;

  for (const num of numSet) {
    if (!numSet.has(num - 1)) {
      let currentNum = num;
      let currentStreak = 1;

      while (numSet.has(currentNum + 1)) {
        currentNum += 1;
        currentStreak += 1;
      }
      maxStreak = Math.max(maxStreak, currentStreak);
    }
  }
  return maxStreak;
};
```
* **Dry Run ([1, 2]):**
    * `num` 1: `num-1` (0) not in set. Start sequence. Found 2. `streak` = 2.
    * `num` 2: `num-1` (1) exists. Skip.
    * Return 2.
* **Alternative Approach:** We could use Union-Find to group consecutive numbers, though it's typically more complex to implement.

---

## 10. Sort Colors
[https://leetcode.com/problems/sort-colors/](https://leetcode.com/problems/sort-colors/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Dutch National Flag algorithm: partition the array into three sections (0s, 1s, and 2s) using three pointers.
* **The 'Talk Track':**
    * "I'll use `low` for the 0s boundary, `high` for the 2s, and `currentIndex` to scan."
    * "When I see a 2, I swap it to the end; when I see a 0, I swap it to the front."
    * "This is a one-pass solution that avoids the overhead of a full sort."
* **Pseudocode:**
    1. `low = 0`, `currentIndex = 0`, `high = nums.length - 1`.
    2. While `currentIndex <= high`:
       a. If `nums[currentIndex] === 0`, swap with `low`, increment both.
       b. If `nums[currentIndex] === 2`, swap with `high`, decrement `high`.
       c. If `nums[currentIndex] === 1`, increment `currentIndex`.
* **JavaScript Solution:**
```javascript
const sortColors = (nums) => {
  let low = 0;
  let currentIndex = 0;
  let high = nums.length - 1;

  while (currentIndex <= high) {
    if (nums[currentIndex] === 0) {
      [nums[low], nums[currentIndex]] = [nums[currentIndex], nums[low]];
      low++;
      currentIndex++;
    } else if (nums[currentIndex] === 2) {
      [nums[high], nums[currentIndex]] = [nums[currentIndex], nums[high]];
      high--;
    } else {
      currentIndex++;
    }
  }
};
```
* **Dry Run ([2, 0, 1]):**
    * `currentIndex` 0 is '2'. Swap with `high` (index 2). Array: `[1, 0, 2]`, `high` = 1.
    * `currentIndex` 0 is now '1'. Increment `currentIndex`.
    * `currentIndex` 1 is '0'. Swap with `low`. Array: `[0, 1, 2]`.
* **Alternative Approach:** We could use a Two-Pass counting sort, but that would require two traversals of the array.

---

## 11. [Latest 2026] Smallest Positive Missing Integer
[https://leetcode.com/problems/first-missing-positive/](https://leetcode.com/problems/first-missing-positive/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Use the array itself as a Hash Map by placing each number `x` at `index x-1`.
* **The 'Talk Track':**
    * "The $O(n)$ time and $O(1)$ space requirement suggests we must mutate the input array to store state."
    * "I'll perform a 'cyclic sort' to place all valid integers in their correct spots."
    * "The first index where the value doesn't match the index tells us the missing number."
* **Pseudocode:**
    1. Iterate `currentIndex`: swap `nums[currentIndex]` with its target position `nums[nums[currentIndex] - 1]` if it's within range.
    2. Iterate again: return the first `i + 1` where `nums[i] !== i + 1`.
* **JavaScript Solution:**
```javascript
const firstMissingPositive = (nums) => {
  const collectionLength = nums.length;
  for (let currentIndex = 0; currentIndex < collectionLength; currentIndex++) {
    while (
      nums[currentIndex] > 0 &&
      nums[currentIndex] <= collectionLength &&
      nums[nums[currentIndex] - 1] !== nums[currentIndex]
    ) {
      const targetIndex = nums[currentIndex] - 1;
      [nums[currentIndex], nums[targetIndex]] = [nums[targetIndex], nums[currentIndex]];
    }
  }

  for (let currentIndex = 0; currentIndex < collectionLength; currentIndex++) {
    if (nums[currentIndex] !== currentIndex + 1) return currentIndex + 1;
  }
  return collectionLength + 1;
};
```
* **Dry Run ([1, 2]):**
    * 1 is at index 0 (correct). 2 is at index 1 (correct).
    * Final loop finishes, return $2 + 1 = 3$.
* **Alternative Approach:** We could use a Hash Set for $O(n)$ space, which is much simpler but violates the $O(1)$ constraint.

---

## 12. [Latest 2026] Count Subarrays with Fixed Bounds
[https://leetcode.com/problems/count-subarrays-with-fixed-bounds/](https://leetcode.com/problems/count-subarrays-with-fixed-bounds/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Track the last seen positions of the `minK`, `maxK`, and any value that violates the bounds.
* **The 'Talk Track':**
    * "This is a sliding window variation where the 'validity' of the window is determined by the presence of both `minK` and `maxK`."
    * "I'll use three pointers to track the most recent 'problem' index and the most recent target indices."
    * "The number of valid subarrays ending at the current index is the distance between the smaller of the target indices and the last bad index."
* **Pseudocode:**
    1. Initialize `lastMin = -1`, `lastMax = -1`, `badIndex = -1`, `totalCount = 0`.
    2. For each `num` at `currentIndex`:
       a. If `num` out of range, update `badIndex`.
       b. If `num === minK`, update `lastMin`.
       c. If `num === maxK`, update `lastMax`.
       d. `totalCount += max(0, min(lastMin, lastMax) - badIndex)`.
* **JavaScript Solution:**
```javascript
const countSubarrays = (nums, minK, maxK) => {
  let totalCount = 0;
  let badIndex = -1;
  let lastMin = -1;
  let lastMax = -1;

  for (let currentIndex = 0; currentIndex < nums.length; currentIndex++) {
    const num = nums[currentIndex];
    if (num < minK || num > maxK) badIndex = currentIndex;
    if (num === minK) lastMin = currentIndex;
    if (num === maxK) lastMax = currentIndex;

    totalCount += Math.max(0, Math.min(lastMin, lastMax) - badIndex);
  }
  return totalCount;
};
```
* **Dry Run (nums = [1, 3], minK = 1, maxK = 3):**
    * Index 0: `lastMin` = 0. `total` += `max(0, -1 - (-1))` = 0.
    * Index 1: `lastMax` = 1. `total` += `max(0, min(0, 1) - (-1))` = 1.
    * Return 1.
* **Alternative Approach:** We could use a two-pointer approach for each boundary, but it's significantly more error-prone than this index-tracking method.
