# Senior Engineer's Study Guide: 2D Dynamic Programming

The **2D Dynamic Programming** pattern involves solving problems where the state depends on two varying parameters, typically represented by a grid $(m \times n)$ or two strings/sequences. As a senior engineer, the focus is on identifying the recurrence relation, optimizing space from $O(m \times n)$ to $O(n)$ when only the previous row/state is needed, and handling boundary conditions gracefully.

---

## 1. Unique Paths
[https://leetcode.com/problems/unique-paths/](https://leetcode.com/problems/unique-paths/)

* **Complexity:** Time: $O(m \times n)$ | Space: $O(n)$
* **Key Intuition:** To reach cell $(r, c)$, you must have come from either $(r-1, c)$ or $(r, c-1)$. The total paths is the sum of paths to those two neighbors.
* **The 'Talk Track':**
    * "I'll use a 1D array to store path counts, effectively collapsing the 2D grid since the current row only depends on the row immediately above it."
    * "The base case is the first row and first column, which are all 1s because there's only one way to move strictly right or strictly down."
    * "By iterating row by row and updating the array in place, I reduce space complexity from quadratic to linear."
* **Pseudocode:**
    1. Initialize a `rowArray` of size `n` with 1s.
    2. Loop from `rowIndex = 1` to `m - 1`:
       a. Loop from `colIndex = 1` to `n - 1`:
          i. `rowArray[colIndex] = rowArray[colIndex] + rowArray[colIndex - 1]`.
    3. Return `rowArray[n - 1]`.
* **JavaScript Solution:**
```javascript
const uniquePaths = (rowCount, columnCount) => {
  // Space optimized: only keep track of one row
  const pathCounts = new Array(columnCount).fill(1);

  for (let currentRow = 1; currentRow < rowCount; currentRow++) {
    for (let currentColumn = 1; currentColumn < columnCount; currentColumn++) {
      // current cell = top cell (already in array) + left cell (just updated)
      pathCounts[currentColumn] += pathCounts[currentColumn - 1];
    }
  }

  return pathCounts[columnCount - 1];
};
```
* **Dry Run (m=2, n=2):**
    * `paths` starts as [1, 1].
    * `row` = 1, `col` = 1: `paths[1] = paths[1] (1) + paths[0] (1) = 2`.
    * Result: 2.
* **Alternative Approach:** We could use the Combinatorics formula $\binom{m+n-2}{n-1}$ to solve this in $O(\min(m, n))$ time and $O(1)$ space.

---

## 2. Longest Common Subsequence
[https://leetcode.com/problems/longest-common-subsequence/](https://leetcode.com/problems/longest-common-subsequence/)

* **Complexity:** Time: $O(m \times n)$ | Space: $O(m \times n)$
* **Key Intuition:** If characters match, the LCS is $1 +$ the LCS of the remaining prefixes. If they don't, we take the maximum LCS by skipping a character from either string.
* **The 'Talk Track':**
    * "I'll build a 2D grid where `dp[i][j]` represents the LCS of `text1[0...i]` and `text2[0...j]`."
    * "A match allows us to extend the diagonal value, while a mismatch requires taking the max of the top or left neighbors."
    * "I'll add an extra row and column of zeros to handle the base cases of empty strings cleanly."
* **Pseudocode:**
    1. Create a 2D `dpTable` of size `(len1 + 1) x (len2 + 1)`.
    2. Nested loop through both strings.
    3. If `text1[i-1] === text2[j-1]`: `dp[i][j] = 1 + dp[i-1][j-1]`.
    4. Else: `dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1])`.
    5. Return `dp[len1][len2]`.
* **JavaScript Solution:**
```javascript
const longestCommonSubsequence = (text1, text2) => {
  const length1 = text1.length;
  const length2 = text2.length;
  const dpTable = Array.from({ length: length1 + 1 }, () => new Array(length2 + 1).fill(0));

  for (let index1 = 1; index1 <= length1; index1++) {
    for (let index2 = 1; index2 <= length2; index2++) {
      if (text1[index1 - 1] === text2[index2 - 1]) {
        dpTable[index1][index2] = 1 + dpTable[index1 - 1][index2 - 1];
      } else {
        dpTable[index1][index2] = Math.max(
          dpTable[index1 - 1][index2],
          dpTable[index1][index2 - 1]
        );
      }
    }
  }

  return dpTable[length1][length2];
};
```
* **Dry Run (t1="ab", t2="ac"):**
    * `i=1, j=1` ('a'=='a'): `dp[1][1]` = 1 + 0 = 1.
    * `i=1, j=2` ('a'!='c'): `dp[1][2]` = max(dp[0][2], dp[1][1]) = 1.
    * `i=2, j=1` ('b'!='a'): `dp[2][1]` = max(dp[1][1], dp[2][0]) = 1.
    * `i=2, j=2` ('b'!='c'): `dp[2][2]` = max(dp[1][2], dp[2][1]) = 1.
* **Alternative Approach:** We could optimize space to $O(\min(m, n))$ by only storing the current and previous rows.

---

## 3. Best Time to Buy and Sell Stock with Cooldown
[https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** We track three states: `buying` (can buy or skip), `selling` (can sell or skip), and the result of a sale which forces a cooldown.
* **The 'Talk Track':**
    * "This is a state machine problem. We have three states: Held, Sold, and Reset."
    * "The key constraint is that a 'Sold' state must be followed by a 'Reset' state before we can buy again."
    * "I'll use memoization to cache results for each `(dayIndex, isBuying)` pair to avoid exponential recursion."
* **JavaScript Solution:**
```javascript
const maxProfit = (prices) => {
  const cacheMap = new Map();

  const calculateProfit = (currentIndex, isBuying) => {
    if (currentIndex >= prices.length) return 0;
    
    const stateKey = `${currentIndex}-${isBuying}`;
    if (cacheMap.has(stateKey)) return cacheMap.get(stateKey);

    let maxProfitAtState = 0;
    if (isBuying) {
      // Choice: Buy today or Skip
      const buyToday = calculateProfit(currentIndex + 1, false) - prices[currentIndex];
      const skipToday = calculateProfit(currentIndex + 1, true);
      maxProfitAtState = Math.max(buyToday, skipToday);
    } else {
      // Choice: Sell today (triggers cooldown) or Skip
      const sellToday = calculateProfit(currentIndex + 2, true) + prices[currentIndex];
      const skipToday = calculateProfit(currentIndex + 1, false);
      maxProfitAtState = Math.max(sellToday, skipToday);
    }

    cacheMap.set(stateKey, maxProfitAtState);
    return maxProfitAtState;
  };

  return calculateProfit(0, true);
};
```
* **Dry Run (prices = [1, 2]):**
    * `day 0, buy`: Max( (buy: `day 1, sell` - 1), (skip: `day 1, buy`) ).
    * `day 1, sell`: Max( (sell: 2 + 0), (skip: 0) ) = 2.
    * `day 0, buy` returns 2 - 1 = 1.
* **Alternative Approach:** We could use iterative DP with three arrays for each state to achieve $O(1)$ space.

---

## 4. Coin Change II
[https://leetcode.com/problems/coin-change-ii/](https://leetcode.com/problems/coin-change-ii/)

* **Complexity:** Time: $O(n \times \text{amount})$ | Space: $O(\text{amount})$
* **Key Intuition:** This is a variation of the unbounded knapsack problem. For each coin, we update our DP array by adding the number of ways to form `currentAmount - coinValue`.
* **The 'Talk Track':**
    * "I'll use a 1D DP array where `dp[i]` is the number of ways to make the amount `i`."
    * "By processing one coin at a time, we ensure that each combination is counted exactly once, avoiding permutations."
    * "The base case is `dp[0] = 1`, representing one way to make zero amount: using no coins."
* **JavaScript Solution:**
```javascript
const change = (targetAmount, coins) => {
  const waysToMakeAmount = new Array(targetAmount + 1).fill(0);
  waysToMakeAmount[0] = 1;

  for (const coinValue of coins) {
    for (let currentAmount = coinValue; currentAmount <= targetAmount; currentAmount++) {
      // Add the ways we found using the current coin
      waysToMakeAmount[currentAmount] += waysToMakeAmount[currentAmount - coinValue];
    }
  }

  return waysToMakeAmount[targetAmount];
};
```
* **Dry Run (amount = 3, coins = [1, 2]):**
    * `coin 1`: `dp` becomes [1, 1, 1, 1].
    * `coin 2`: `dp[2] += dp[0]` (1+1=2), `dp[3] += dp[1]` (1+1=2).
    * Result: 2.
* **Alternative Approach:** We could use a 2D DP table if we needed to restrict the number of each coin used.

---

## 5. Target Sum
[https://leetcode.com/problems/target-sum/](https://leetcode.com/problems/target-sum/)

* **Complexity:** Time: $O(n \times \text{sum})$ | Space: $O(n \times \text{sum})$
* **Key Intuition:** This problem can be reduced to a Subset Sum problem. We need to find a subset of numbers (positive) such that `subsetSum = (target + totalSum) / 2`.
* **The 'Talk Track':**
    * "I'll use memoization to store `(index, currentSum)` results to handle the overlapping subproblems."
    * "The total number of ways to reach the target is the sum of ways by adding the current number and ways by subtracting it."
    * "Mathematically, this is equivalent to finding a partition of the array with a specific sum."
* **JavaScript Solution:**
```javascript
const findTargetSumWays = (nums, target) => {
  const memoizationMap = new Map();

  const calculateWays = (currentIndex, currentTotal) => {
    const stateKey = `${currentIndex},${currentTotal}`;
    if (memoizationMap.has(stateKey)) return memoizationMap.get(stateKey);

    if (currentIndex === nums.length) {
      return currentTotal === target ? 1 : 0;
    }

    const waysByAddition = calculateWays(currentIndex + 1, currentTotal + nums[currentIndex]);
    const waysBySubtraction = calculateWays(currentIndex + 1, currentTotal - nums[currentIndex]);

    memoizationMap.set(stateKey, waysByAddition + waysBySubtraction);
    return waysByAddition + waysBySubtraction;
  };

  return calculateWays(0, 0);
};
```
* **Dry Run (nums = [1], target = 1):**
    * `idx 0, sum 0`: `(idx 1, sum 1)` + `(idx 1, sum -1)`.
    * `idx 1, sum 1`: Matches target, returns 1.
    * `idx 1, sum -1`: Returns 0. Total = 1.
* **Alternative Approach:** We could use the 0/1 Knapsack iterative DP approach to solve for the derived subset sum target.

---

## 6. Interleaving String
[https://leetcode.com/problems/interleaving-string/](https://leetcode.com/problems/interleaving-string/)

* **Complexity:** Time: $O(m \times n)$ | Space: $O(m \times n)$
* **Key Intuition:** We use a 2D grid to track if `s3[0...i+j]` can be formed by interleaving `s1[0...i]` and `s2[0...j]`.
* **The 'Talk Track':**
    * "A cell `dp[i][j]` is true if either the character from `s1` matches `s3` and `dp[i-1][j]` was true, or the character from `s2` matches and `dp[i][j-1]` was true."
    * "If the sum of lengths of `s1` and `s2` doesn't equal the length of `s3`, I can return `false` immediately."
    * "The base case is `dp[0][0] = true`, an empty interleaving of empty strings."
* **JavaScript Solution:**
```javascript
const isInterleave = (string1, string2, targetString) => {
  if (string1.length + string2.length !== targetString.length) return false;

  const dpTable = Array.from({ length: string1.length + 1 }, () => 
    new Array(string2.length + 1).fill(false)
  );

  for (let index1 = 0; index1 <= string1.length; index1++) {
    for (let index2 = 0; index2 <= string2.length; index2++) {
      if (index1 === 0 && index2 === 0) {
        dpTable[index1][index2] = true;
      } else if (index1 === 0) {
        dpTable[index1][index2] = dpTable[index1][index2 - 1] && string2[index2 - 1] === targetString[index1 + index2 - 1];
      } else if (index2 === 0) {
        dpTable[index1][index2] = dpTable[index1 - 1][index2] && string1[index1 - 1] === targetString[index1 + index2 - 1];
      } else {
        dpTable[index1][index2] = (dpTable[index1 - 1][index2] && string1[index1 - 1] === targetString[index1 + index2 - 1]) ||
                                (dpTable[index1][index2 - 1] && string2[index2 - 1] === targetString[index1 + index2 - 1]);
      }
    }
  }

  return dpTable[string1.length][string2.length];
};
```
* **Dry Run (s1="a", s2="b", s3="ab"):**
    * `dp[0][0]` = true.
    * `dp[1][0]` (s1 matches 'a'): true.
    * `dp[1][1]` (s2 matches 'b' and `dp[1][0]` is true): true.
* **Alternative Approach:** We could use a 1D DP array to reduce space to $O(n)$.

---

## 7. Unique Paths II
[https://leetcode.com/problems/unique-paths-ii/](https://leetcode.com/problems/unique-paths-ii/)

* **Complexity:** Time: $O(m \times n)$ | Space: $O(n)$
* **Key Intuition:** Identical to Unique Paths I, but if a cell contains an obstacle, we set its path count to 0.
* **The 'Talk Track':**
    * "I'll initialize the first cell as 1 if there's no obstacle, then propagate that through the grid."
    * "If I encounter a '1' in the obstacle grid, I simply reset that `dp` entry to 0 because no paths can pass through it."
    * "Using a 1D array, I need to be careful with the first column of each row to handle starting obstacles."
* **JavaScript Solution:**
```javascript
const uniquePathsWithObstacles = (obstacleGrid) => {
  const columnCount = obstacleGrid[0].length;
  const pathCounts = new Array(columnCount).fill(0);

  // Starting point base case
  pathCounts[0] = obstacleGrid[0][0] === 0 ? 1 : 0;

  for (let rowIndex = 0; rowIndex < obstacleGrid.length; rowIndex++) {
    for (let colIndex = 0; colIndex < columnCount; colIndex++) {
      if (obstacleGrid[rowIndex][colIndex] === 1) {
        pathCounts[colIndex] = 0;
      } else if (colIndex > 0) {
        // Current cell = top neighbor (already in paths[col]) + left neighbor
        pathCounts[colIndex] += pathCounts[colIndex - 1];
      }
    }
  }

  return pathCounts[columnCount - 1];
};
```
* **Dry Run (grid = [[0, 1], [0, 0]]):**
    * `row 0`: `paths[0]=1`, `paths[1]=0` (obstacle). `paths` = [1, 0].
    * `row 1`: `col 0` (no-op), `col 1`: `paths[1] += paths[0]` (0+1=1).
    * Result: 1.
* **Alternative Approach:** We could use recursion with memoization, but the iterative 1D approach is the gold standard for space.

---

## 8. Minimum Path Sum
[https://leetcode.com/problems/minimum-path-sum/](https://leetcode.com/problems/minimum-path-sum/)

* **Complexity:** Time: $O(m \times n)$ | Space: $O(1)$ (if modifying input) or $O(n)$
* **Key Intuition:** Each cell's minimum path is its own value plus the minimum of the cell above it or the cell to its left.
* **The 'Talk Track':**
    * "For the first row and column, there's only one way to arrive, so I'll pre-calculate their prefix sums."
    * "For all other cells, I'll take the minimum of the two possible neighbors to find the optimal path."
    * "To achieve $O(1)$ extra space, I'll perform the calculations in-place on the input grid."
* **JavaScript Solution:**
```javascript
const minPathSum = (grid) => {
  const rowCount = grid.length;
  const columnCount = grid[0].length;

  for (let rowIndex = 0; rowIndex < rowCount; rowIndex++) {
    for (let colIndex = 0; colIndex < columnCount; colIndex++) {
      if (rowIndex === 0 && colIndex === 0) continue;

      if (rowIndex === 0) {
        grid[rowIndex][colIndex] += grid[rowIndex][colIndex - 1];
      } else if (colIndex === 0) {
        grid[rowIndex][colIndex] += grid[rowIndex - 1][colIndex];
      } else {
        grid[rowIndex][colIndex] += Math.min(
          grid[rowIndex - 1][colIndex],
          grid[rowIndex][colIndex - 1]
        );
      }
    }
  }

  return grid[rowCount - 1][columnCount - 1];
};
```
* **Dry Run (grid = [[1, 2], [1, 1]]):**
    * `row 0, col 1`: 2 + 1 = 3.
    * `row 1, col 0`: 1 + 1 = 2.
    * `row 1, col 1`: 1 + min(3, 2) = 3.
    * Result: 3.
* **Alternative Approach:** We could use Dijkstra's Algorithm, but since we can only move right and down, DP is significantly more efficient.

---

## 9. [Latest 2026] Maximum Strength of a Group
[https://leetcode.com/problems/maximum-strength-of-a-group/](https://leetcode.com/problems/maximum-strength-of-a-group/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** To find the maximum product of a subsequence, we must track both the current maximum and current minimum at each step (since two negatives make a positive).
* **The 'Talk Track':**
    * "This is similar to 'Maximum Product Subarray' but for non-contiguous subsequences."
    * "For each number, we have two choices: include it in our product or skip it."
    * "I'll maintain `maxProduct` and `minProduct` to capture the 'flipping' effect of negative numbers."
* **JavaScript Solution:**
```javascript
const maxStrength = (nums) => {
  if (nums.length === 1) return nums[0];

  let maxProduct = nums[0];
  let minProduct = nums[0];
  let hasSelection = false;
  let globalMax = nums[0];

  for (let currentIndex = 1; currentIndex < nums.length; currentIndex++) {
    const currentVal = nums[currentIndex];
    const temporaryMax = maxProduct;

    maxProduct = Math.max(maxProduct, currentVal, temporaryMax * currentVal, minProduct * currentVal);
    minProduct = Math.min(minProduct, currentVal, temporaryMax * currentVal, minProduct * currentVal);
    
    globalMax = Math.max(globalMax, maxProduct);
  }

  return globalMax;
};
```
* **Dry Run (nums = [3, -1]):**
    * `i=0`: max=3, min=3.
    * `i=1`: max = max(3, -1, -3, -3) = 3. min = min(3, -1, -3, -3) = -3.
    * Result: 3.
* **Alternative Approach:** Sorting the array and handling negatives in pairs is another valid $O(n \log n)$ strategy.

---

## 10. [Latest 2026] Length of the Longest Common Subsequence Between Two Arrays II
[https://leetcode.com/problems/longest-common-subsequence-between-two-arrays-ii/](https://leetcode.com/problems/longest-common-subsequence-between-two-arrays-ii/)

* **Complexity:** Time: $O(m \times n)$ | Space: $O(n)$
* **Key Intuition:** A 2026 variation focusing on memory constraints and array-based LCS rather than strings.
* **The 'Talk Track':**
    * "I'll implement the space-optimized LCS algorithm using only two rows to handle potentially large input arrays."
    * "By swapping the `previousRow` and `currentRow` pointers, I keep the space complexity strictly linear relative to the smaller array."
    * "This demonstrates an understanding of memory locality and cache efficiency in a senior engineering context."
* **JavaScript Solution:**
```javascript
const longestCommonSubsequenceArrays = (array1, array2) => {
  // Ensure array2 is the shorter one for space optimization
  if (array1.length < array2.length) return longestCommonSubsequenceArrays(array2, array1);

  let previousRow = new Array(array2.length + 1).fill(0);

  for (let index1 = 1; index1 <= array1.length; index1++) {
    const currentRow = new Array(array2.length + 1).fill(0);
    for (let index2 = 1; index2 <= array2.length; index2++) {
      if (array1[index1 - 1] === array2[index2 - 1]) {
        currentRow[index2] = 1 + previousRow[index2 - 1];
      } else {
        currentRow[index2] = Math.max(previousRow[index2], currentRow[index2 - 1]);
      }
    }
    previousRow = currentRow;
  }

  return previousRow[array2.length];
};
```
* **Dry Run (a1=[1, 2], a2=[1, 3]):**
    * `idx1=1, idx2=1` (1==1): `curr[1]` = 1.
    * `idx1=2, idx2=1` (2!=1): `curr[1]` = max(1, 0) = 1.
    * `idx1=2, idx2=2` (2!=3): `curr[2]` = max(0, 1) = 1.
    * Result: 1.
* **Alternative Approach:** For sparse arrays with many unique elements, we could potentially convert this to a Longest Increasing Subsequence problem in $O(n \log n)$.

---
