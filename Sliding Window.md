# Senior Engineer's Study Guide: Sliding Window Pattern

The **Sliding Window** pattern is used to transform nested loops into a single loop, reducing time complexity from $O(n^2)$ to $O(n)$. It is typically applied to arrays or strings when searching for a sub-range that meets a specific criteria (e.g., longest, shortest, or specific sum).

---

## 1. Best Time to Buy & Sell Stock
[https://leetcode.com/problems/best-time-to-buy-and-sell-stock/](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** We only care about the lowest price seen so far and the maximum difference between that low and any subsequent price.
* **The 'Talk Track':**
    * "I’ll use a single-pass approach to maintain the minimum price encountered while simultaneously calculating the potential profit."
    * "This is a dynamic window where the 'start' is the lowest point and the 'end' is our current traversal index."
    * "I'm avoiding a nested loop by recognizing that we can't sell before we buy, so we only need to track the global minimum as we move forward."
* **Pseudocode:**
    1. Initialize `minPrice` to infinity and `maxProfit` to zero.
    2. Iterate through `prices` using `currentPrice`.
    3. If `currentPrice` is less than `minPrice`, update `minPrice`.
    4. Else if `currentPrice - minPrice` is greater than `maxProfit`, update `maxProfit`.
    5. Return `maxProfit`.
* **JavaScript Solution:**
```javascript
const maxProfit = (prices) => {
  let minimumPriceSoFar = Infinity;
  let maximumProfitFound = 0;

  for (let currentIndex = 0; currentIndex < prices.length; currentIndex++) {
    const currentPrice = prices[currentIndex];

    if (currentPrice < minimumPriceSoFar) {
      minimumPriceSoFar = currentPrice;
    } else {
      const potentialProfit = currentPrice - minimumPriceSoFar;
      if (potentialProfit > maximumProfitFound) {
        maximumProfitFound = potentialProfit;
      }
    }
  }

  return maximumProfitFound;
};
```
* **Dry Run (prices = [1, 2]):**
    * `currentIndex` 0: `currentPrice` is 1. `minimumPriceSoFar` becomes 1.
    * `currentIndex` 1: `currentPrice` is 2. `potentialProfit` is $2 - 1 = 1$. `maximumProfitFound` becomes 1.
    * Result: 1.
* **Alternative Approach:** We could treat this as a Kadane’s Algorithm problem by looking at the differences between adjacent days.

---

## 2. Longest Substring Without Repeating Characters
[https://leetcode.com/problems/longest-substring-without-repeating-characters/](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

* **Complexity:** Time: $O(n)$ | Space: $O(min(m, n))$ where $m$ is the size of the character set.
* **Key Intuition:** Use a `Set` to track characters in the current window; when a duplicate appears, shrink the window from the left until the duplicate is gone.
* **The 'Talk Track':**
    * "I’ll use a `Set` for $O(1)$ lookups to detect character collisions."
    * "When a collision occurs, I'll slide the left boundary of my window forward, effectively 'forgetting' characters until the window is valid again."
    * "This ensures we only visit each character at most twice—once by the leading pointer and once by the trailing pointer."
* **Pseudocode:**
    1. Initialize `windowStart = 0`, `maxLength = 0`, and a `Set` called `uniqueCharacters`.
    2. Iterate through the string with `windowEnd`.
    3. While `string[windowEnd]` is in `uniqueCharacters`:
       a. Remove `string[windowStart]` from the `Set`.
       b. Increment `windowStart`.
    4. Add `string[windowEnd]` to the `Set`.
    5. Update `maxLength` with the current window size (`windowEnd - windowStart + 1`).
* **JavaScript Solution:**
```javascript
const lengthOfLongestSubstring = (inputString) => {
  let windowStart = 0;
  let maximumLength = 0;
  const uniqueCharactersSet = new Set();

  for (let windowEnd = 0; windowEnd < inputString.length; windowEnd++) {
    const currentCharacter = inputString[windowEnd];

    while (uniqueCharactersSet.has(currentCharacter)) {
      const characterAtStart = inputString[windowStart];
      uniqueCharactersSet.delete(characterAtStart);
      windowStart++;
    }

    uniqueCharactersSet.add(currentCharacter);
    const currentWindowSize = windowEnd - windowStart + 1;
    maximumLength = Math.max(maximumLength, currentWindowSize);
  }

  return maximumLength;
};
```
* **Dry Run (inputString = "aab"):**
    * `windowEnd` 0: 'a' added, `max` = 1.
    * `windowEnd` 1: 'a' exists. `windowStart` moves to 1, 'a' removed then re-added. `max` = 1.
    * `windowEnd` 2: 'b' added, `max` = 2.
    * Result: 2.
* **Alternative Approach:** Using a Map to store character indices allows the `windowStart` to "jump" past the duplicate immediately, potentially reducing the number of operations.

---

## 3. Longest Repeating Character Replacement
[https://leetcode.com/problems/longest-repeating-character-replacement/](https://leetcode.com/problems/longest-repeating-character-replacement/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$ (since the frequency map is limited to 26 characters).
* **Key Intuition:** A window is valid if (total characters - count of the most frequent character) $\le k$.
* **The 'Talk Track':**
    * "The core constraint here is identifying how many 'edits' we have left in our current window."
    * "I will track the frequency of the most common character in the current window to determine if the remaining characters can be replaced within the $k$ limit."
    * "Crucially, the `maxFrequency` doesn't strictly need to be decreased when the window shrinks, as a smaller `maxFrequency` wouldn't yield a larger window anyway."
* **Pseudocode:**
    1. Initialize `windowStart = 0`, `maxFrequency = 0`, and a `frequencyMap`.
    2. Iterate with `windowEnd`.
    3. Increment count of `string[windowEnd]` in `frequencyMap` and update `maxFrequency`.
    4. While (`windowEnd - windowStart + 1`) - `maxFrequency` > `k`:
       a. Decrement count of `string[windowStart]`.
       b. Increment `windowStart`.
    5. Update `result` with maximum window size.
* **JavaScript Solution:**
```javascript
const characterReplacement = (inputString, allowedReplacements) => {
  const characterCounts = {};
  let windowStart = 0;
  let maximumFrequencySeen = 0;
  let longestValidSubstring = 0;

  for (let windowEnd = 0; windowEnd < inputString.length; windowEnd++) {
    const charAtEnd = inputString[windowEnd];
    characterCounts[charAtEnd] = (characterCounts[charAtEnd] || 0) + 1;
    maximumFrequencySeen = Math.max(maximumFrequencySeen, characterCounts[charAtEnd]);

    const currentWindowLength = windowEnd - windowStart + 1;
    const charactersToReplace = currentWindowLength - maximumFrequencySeen;

    if (charactersToReplace > allowedReplacements) {
      const charAtStart = inputString[windowStart];
      characterCounts[charAtStart] -= 1;
      windowStart++;
    }

    longestValidSubstring = Math.max(longestValidSubstring, windowEnd - windowStart + 1);
  }

  return longestValidSubstring;
};
```
* **Dry Run (s = "ABAB", k = 1):**
    * `windowEnd` 2 (ABA): `maxFreq` = 2 ('A'). `length`(3) - 2 = 1. $1 \le 1$. Valid. `longest` = 3.
    * `windowEnd` 3 (ABAB): `maxFreq` = 2. `length`(4) - 2 = 2. $2 > 1$. Shrink. `longest` = 3.
    * Result: 3.
* **Alternative Approach:** We could use a binary search on the answer length ($O(n \log n)$), but the sliding window is more optimal.

---

## 4. Permutation in String
[https://leetcode.com/problems/permutation-in-string/](https://leetcode.com/problems/permutation-in-string/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** A permutation means a window in `s2` must have the exact same character frequency counts as `s1`.
* **The 'Talk Track':**
    * "I’ll use a fixed-size sliding window equal to the length of `string1`."
    * "Instead of re-sorting or re-counting everything, I’ll maintain a running frequency count and a `matches` variable to track how many characters meet the required frequency."
    * "This optimization allows me to check for a permutation in $O(1)$ time per window slide."
* **Pseudocode:**
    1. If `s1.length > s2.length`, return `false`.
    2. Create two 26-slot arrays for char counts.
    3. Fill counts for the first `s1.length` characters.
    4. Count how many indices in the arrays match.
    5. Slide the window through `s2`: add one char, remove one char, and update the match count.
    6. If `matches === 26` at any point, return `true`.
* **JavaScript Solution:**
```javascript
const checkInclusion = (searchString, containerString) => {
  const searchLength = searchString.length;
  const containerLength = containerString.length;
  if (searchLength > containerLength) return false;

  const searchCounts = new Array(26).fill(0);
  const currentWindowCounts = new Array(26).fill(0);

  const getCharIndex = (char) => char.charCodeAt(0) - 'a'.charCodeAt(0);

  for (let currentIndex = 0; currentIndex < searchLength; currentIndex++) {
    searchCounts[getCharIndex(searchString[currentIndex])]++;
    currentWindowCounts[getCharIndex(containerString[currentIndex])]++;
  }

  let totalMatches = 0;
  for (let index = 0; index < 26; index++) {
    if (searchCounts[index] === currentWindowCounts[index]) totalMatches++;
  }

  for (let windowEnd = searchLength; windowEnd < containerLength; windowEnd++) {
    if (totalMatches === 26) return true;

    const charToAdd = getCharIndex(containerString[windowEnd]);
    currentWindowCounts[charToAdd]++;
    if (currentWindowCounts[charToAdd] === searchCounts[charToAdd]) {
      totalMatches++;
    } else if (currentWindowCounts[charToAdd] === searchCounts[charToAdd] + 1) {
      totalMatches--;
    }

    const charToRemove = getCharIndex(containerString[windowEnd - searchLength]);
    currentWindowCounts[charToRemove]--;
    if (currentWindowCounts[charToRemove] === searchCounts[charToRemove]) {
      totalMatches++;
    } else if (currentWindowCounts[charToRemove] === searchCounts[charToRemove] - 1) {
      totalMatches--;
    }
  }

  return totalMatches === 26;
};
```
* **Dry Run (s1 = "ab", s2 = "ba"):**
    * Initial: `searchCounts` {a:1, b:1}, `currentWindow` {b:1, a:1}.
    * `totalMatches` = 26.
    * Result: `true`.
* **Alternative Approach:** You could use a simple Hash Map and compare it at every step, but the fixed array is faster for lowercase English letters.

---

## 5. [Latest 2026] Maximum Sum of Distinct Subarrays With Length K
[https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/)

* **Complexity:** Time: $O(n)$ | Space: $O(k)$
* **Key Intuition:** Combine a fixed-size window with a `Map` to ensure all $k$ elements are unique while maintaining a running sum.
* **The 'Talk Track':**
    * "We need a fixed window of size $k$ where the 'distinct' constraint is the primary challenge."
    * "I'll use a `Map` to track frequencies within the window; if the `Map` size equals $k$, I know all elements are unique."
    * "This is a clean example of combining the Sliding Window with a Hash Map for efficient state tracking."
* **Pseudocode:**
    1. Initialize `windowStart = 0`, `currentSum = 0`, `maxSum = 0`, and `elementCounts` Map.
    2. Iterate with `windowEnd`:
       a. Add `nums[windowEnd]` to `currentSum` and update `elementCounts`.
       b. If `windowEnd - windowStart + 1 > k`:
          i. Remove `nums[windowStart]` from `currentSum` and `elementCounts`.
          ii. Increment `windowStart`.
       c. If window size is `k` and `elementCounts.size` is `k`, update `maxSum`.
* **JavaScript Solution:**
```javascript
const maximumSubarraySum = (nums, k) => {
  let maximumSumValue = 0;
  let currentWindowSum = 0;
  let windowStart = 0;
  const frequencyMap = new Map();

  for (let windowEnd = 0; windowEnd < nums.length; windowEnd++) {
    const numToAdd = nums[windowEnd];
    currentWindowSum += numToAdd;
    frequencyMap.set(numToAdd, (frequencyMap.get(numToAdd) || 0) + 1);

    if (windowEnd - windowStart + 1 > k) {
      const numToRemove = nums[windowStart];
      currentWindowSum -= numToRemove;
      
      const count = frequencyMap.get(numToRemove);
      if (count === 1) {
        frequencyMap.delete(numToRemove);
      } else {
        frequencyMap.set(numToRemove, count - 1);
      }
      windowStart++;
    }

    if (windowEnd - windowStart + 1 === k) {
      if (frequencyMap.size === k) {
        maximumSumValue = Math.max(maximumSumValue, currentWindowSum);
      }
    }
  }

  return maximumSumValue;
};
```
* **Dry Run (nums = [1, 2], k = 2):**
    * `windowEnd` 1: `currentSum` = 3. `Map` size = 2.
    * Window size is 2, `Map` size is 2. `maxSum` = 3.
    * Result: 3.
* **Alternative Approach:** We could use a Set to check uniqueness, but a Map is necessary to handle the window sliding logic correctly when duplicate values enter or leave.

---

## 6. [Latest 2026] Count Subarrays Where Max Element Appears at Least K Times
[https://leetcode.com/problems/count-subarrays-where-max-element-appears-at-least-k-times/](https://leetcode.com/problems/count-subarrays-where-max-element-appears-at-least-k-times/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Once a window ending at `windowEnd` is valid, any subarray starting at an index before or at `windowStart` and ending at `windowEnd` is also valid.
* **The 'Talk Track':**
    * "First, I'll identify the global maximum in a single pass."
    * "Then, I'll expand the window until I have at least $k$ instances of that max element."
    * "The critical insight is that if a window from `windowStart` to `windowEnd` is valid, then all windows starting from 0 to `windowStart` are also valid for this specific `windowEnd`."
* **Pseudocode:**
    1. Find `maxElement` in `nums`.
    2. Iterate with `windowEnd`:
       a. If `nums[windowEnd] === maxElement`, increment `count`.
       b. While `count === k`:
          i. If `nums[windowStart] === maxElement`, decrement `count`.
          ii. Increment `windowStart`.
       c. Add `windowStart` to the `totalSubarrays`.
* **JavaScript Solution:**
```javascript
const countSubarrays = (nums, k) => {
  const maxElement = Math.max(...nums);
  let totalValidSubarrays = 0;
  let windowStart = 0;
  let maxElementCountInWindow = 0;

  for (let windowEnd = 0; windowEnd < nums.length; windowEnd++) {
    if (nums[windowEnd] === maxElement) {
      maxElementCountInWindow++;
    }

    while (maxElementCountInWindow === k) {
      if (nums[windowStart] === maxElement) {
        maxElementCountInWindow--;
      }
      windowStart++;
    }

    // Every index from 0 to windowStart - 1 forms a valid subarray ending at windowEnd
    totalValidSubarrays += windowStart;
  }

  return totalValidSubarrays;
};
```
* **Dry Run (nums = [1, 1], k = 1):**
    * `maxElement` = 1.
    * `windowEnd` 0: `count` = 1. `while` loop runs: `windowStart` = 1, `count` = 0. `total` = 1.
    * `windowEnd` 1: `count` = 1. `while` loop runs: `windowStart` = 2, `count` = 0. `total` = 1 + 2 = 3.
    * Result: 3.
* **Alternative Approach:** We could store the indices of the max element in an array and use them to calculate distances, but this sliding window is more memory-efficient.

---
