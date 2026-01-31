# Senior Engineer's Study Guide: Dynamic Programming (1D & Multi-Dimensional)

The **Dynamic Programming (DP)** pattern is about breaking down a complex problem into overlapping subproblems and storing the results (memoization) to avoid redundant calculations. As a senior engineer, the goal is to identify the **Optimal Substructure** and **State Transition Relation**, moving from a recursive top-down approach to an iterative bottom-up approach to optimize space.

---

## 1. Climbing Stairs
[https://leetcode.com/problems/climbing-stairs/](https://leetcode.com/problems/climbing-stairs/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** To reach step $n$, you must come from either step $n-1$ or $n-2$. This is essentially the Fibonacci sequence.
* **The 'Talk Track':**
    * "I recognize that the number of ways to reach the current step is the sum of the ways to reach the two preceding steps."
    * "Instead of an $O(n)$ space array, I'll use two variables to track the 'previous' and 'two-steps-back' values to achieve $O(1)$ space."
    * "This is a classic 'counting' DP problem where we build the solution from the base cases of 1 and 2 steps."
* **Pseudocode:**
    1. Handle base cases for $n \le 2$.
    2. Initialize `oneStepBack = 2`, `twoStepsBack = 1`.
    3. Loop from 3 to $n$:
       a. `currentWays = oneStepBack + twoStepsBack`.
       b. Update pointers.
    4. Return `oneStepBack`.
* **JavaScript Solution:**
```javascript
const climbStairs = (totalSteps) => {
  if (totalSteps <= 2) return totalSteps;

  let twoStepsBackCount = 1;
  let oneStepBackCount = 2;

  for (let currentStep = 3; currentStep <= totalSteps; currentStep++) {
    const totalWaysAtCurrentStep = oneStepBackCount + twoStepsBackCount;
    twoStepsBackCount = oneStepBackCount;
    oneStepBackCount = totalWaysAtCurrentStep;
  }

  return oneStepBackCount;
};
```
* **Dry Run (input = 3):**
    * `twoStepsBack` = 1, `oneStepBack` = 2.
    * `i` = 3: `ways` = 1 + 2 = 3.
    * Return 3.
* **Alternative Approach:** We could use a recursive approach with memoization, but that would use $O(n)$ space on the call stack.

---

## 2. Min Cost Climbing Stairs
[https://leetcode.com/problems/min-cost-climbing-stairs/](https://leetcode.com/problems/min-cost-climbing-stairs/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** The cost to reach the current step is the step's own cost plus the minimum of the costs to reach the two steps below it.
* **The 'Talk Track':**
    * "I'll modify the input array in-place or use variables to keep track of the cumulative cost to save space."
    * "Since we can start at index 0 or 1, our recurrence relation should look back at the accumulated minimums."
    * "The final answer is the minimum of the last two calculated costs."
* **Pseudocode:**
    1. Iterate from index 2 to the end of the `cost` array.
    2. `cost[i] += Math.min(cost[i-1], cost[i-2])`.
    3. Return `Math.min(lastValue, secondToLastValue)`.
* **JavaScript Solution:**
```javascript
const minCostClimbingStairs = (costArray) => {
  const collectionLength = costArray.length;

  for (let currentIndex = 2; currentIndex < collectionLength; currentIndex++) {
    costArray[currentIndex] += Math.min(
      costArray[currentIndex - 1],
      costArray[currentIndex - 2]
    );
  }

  return Math.min(costArray[collectionLength - 1], costArray[collectionLength - 2]);
};
```
* **Dry Run (input = [1, 2, 3]):**
    * `i` = 2: `cost[2]` = 3 + min(1, 2) = 4. Array is [1, 2, 4].
    * Return min(2, 4) = 2.
* **Alternative Approach:** We could create a separate `dp` array to avoid mutating the input.

---

## 3. House Robber
[https://leetcode.com/problems/house-robber/](https://leetcode.com/problems/house-robber/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** At each house, the max profit is either: (this house + max profit from 2 houses ago) or (max profit from the previous house).
* **The 'Talk Track':**
    * "This is a choice problem: rob or skip. If I rob house $i$, I must have skipped house $i-1$."
    * "I'll maintain two variables to represent the 'robbed last' and 'skipped last' states."
    * "This optimization reduces the space from $O(n)$ to $O(1)$ as we only ever need the last two results."
* **Pseudocode:**
    1. `robPrevious = 0`, `robBeforePrevious = 0`.
    2. For each `currentCash` in `nums`:
       a. `currentMax = Math.max(robPrevious, robBeforePrevious + currentCash)`.
       b. `robBeforePrevious = robPrevious`.
       c. `robPrevious = currentMax`.
    3. Return `robPrevious`.
* **JavaScript Solution:**
```javascript
const rob = (houseValues) => {
  let maximumRobbedTwoHousesAgo = 0;
  let maximumRobbedOneHouseAgo = 0;

  for (const currentHouseValue of houseValues) {
    const currentHouseCalculation = Math.max(
      maximumRobbedOneHouseAgo,
      maximumRobbedTwoHousesAgo + currentHouseValue
    );
    maximumRobbedTwoHousesAgo = maximumRobbedOneHouseAgo;
    maximumRobbedOneHouseAgo = currentHouseCalculation;
  }

  return maximumRobbedOneHouseAgo;
};
```
* **Dry Run (input = [1, 2, 3]):**
    * `h`=1: `max` = max(0, 0+1)=1. `prev2`=0, `prev1`=1.
    * `h`=2: `max` = max(1, 0+2)=2. `prev2`=1, `prev1`=2.
    * `h`=3: `max` = max(2, 1+3)=4. `prev2`=2, `prev1`=4.
* **Alternative Approach:** We could use a DP array where `dp[i]` is the max at house $i$.

---

## 4. House Robber II
[https://leetcode.com/problems/house-robber-ii/](https://leetcode.com/problems/house-robber-ii/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Since the first and last houses are connected, you can either rob the first house (and skip the last) or rob the last house (and skip the first).
* **The 'Talk Track':**
    * "The circular constraint means we just run the House Robber I logic twice: once for the range $[0, n-2]$ and once for $[1, n-1]$."
    * "I'll abstract the core logic into a helper function to keep the code DRY (Don't Repeat Yourself)."
    * "The final answer is the maximum of the two scenarios or the value of a single house if $n=1$."
* **Pseudocode:**
    1. Handle $n=1$ edge case.
    2. `scenarioA = originalRob(nums.slice(0, -1))`.
    3. `scenarioB = originalRob(nums.slice(1))`.
    4. Return `Math.max(scenarioA, scenarioB)`.
* **JavaScript Solution:**
```javascript
const robCircular = (nums) => {
  const collectionLength = nums.length;
  if (collectionLength === 1) return nums[0];

  const linearRobber = (subArray) => {
    let prevTwo = 0, prevOne = 0;
    for (const val of subArray) {
      const current = Math.max(prevOne, prevTwo + val);
      prevTwo = prevOne;
      prevOne = current;
    }
    return prevOne;
  };

  return Math.max(
    linearRobber(nums.slice(0, collectionLength - 1)),
    linearRobber(nums.slice(1))
  );
};
```
* **Dry Run (input = [1, 2, 3]):**
    * `range1` [1, 2]: max is 2.
    * `range2` [2, 3]: max is 3.
    * Return 3.
* **Alternative Approach:** We could handle the logic in a single pass with more complex state variables, but the two-pass approach is much cleaner.

---

## 5. Longest Palindromic Substring
[https://leetcode.com/problems/longest-palindromic-substring/](https://leetcode.com/problems/longest-palindromic-substring/)

* **Complexity:** Time: $O(n^2)$ | Space: $O(1)$
* **Key Intuition:** Instead of checking every substring, we expand outwards from each character (and each gap between characters) as potential centers of a palindrome.
* **The 'Talk Track':**
    * "I'll treat every character as a potential center and expand as long as the left and right characters match."
    * "I need to check both odd-length palindromes (single char center) and even-length palindromes (gap center)."
    * "This 'Expand Around Center' approach is more space-efficient than the $O(n^2)$ DP table approach."
* **Pseudocode:**
    1. Loop through each index `i`.
    2. `expand(i, i)` (odd) and `expand(i, i+1)` (even).
    3. Track the `maxStart` and `maxLength` of the longest palindrome found.
* **JavaScript Solution:**
```javascript
const longestPalindrome = (inputString) => {
  if (inputString.length < 2) return inputString;
  let startResultIndex = 0;
  let maximumLengthFound = 0;

  const expandAroundCenter = (leftPointer, rightPointer) => {
    while (
      leftPointer >= 0 &&
      rightPointer < inputString.length &&
      inputString[leftPointer] === inputString[rightPointer]
    ) {
      const currentPalindromeLength = rightPointer - leftPointer + 1;
      if (currentPalindromeLength > maximumLengthFound) {
        startResultIndex = leftPointer;
        maximumLengthFound = currentPalindromeLength;
      }
      leftPointer--;
      rightPointer++;
    }
  };

  for (let currentIndex = 0; currentIndex < inputString.length; currentIndex++) {
    expandAroundCenter(currentIndex, currentIndex);
    expandAroundCenter(currentIndex, currentIndex + 1);
  }

  return inputString.substring(startResultIndex, startResultIndex + maximumLengthFound);
};
```
* **Dry Run (input = "aba"):**
    * `i`=0: expand(0,0) -> "a".
    * `i`=1: expand(1,1) -> "aba". `maxLen`=3.
    * Return "aba".
* **Alternative Approach:** Manacher's Algorithm can solve this in $O(n)$ time, but it is extremely complex and rarely expected in standard interviews.

---

## 6. Palindromic Substrings
[https://leetcode.com/problems/palindromic-substrings/](https://leetcode.com/problems/palindromic-substrings/)

* **Complexity:** Time: $O(n^2)$ | Space: $O(1)$
* **Key Intuition:** Similar to the previous problem, but we increment a counter every time we successfully expand.
* **The 'Talk Track':**
    * "Every time the characters at our left and right expansion pointers match, we've found a new palindromic substring."
    * "This avoids the $O(n^3)$ brute force of checking every substring by exploiting the property that a palindrome grows from another palindrome."
* **JavaScript Solution:**
```javascript
const countSubstrings = (inputString) => {
  let totalPalindromeCount = 0;

  const countFromCenter = (leftIndex, rightIndex) => {
    let currentCount = 0;
    while (
      leftIndex >= 0 &&
      rightIndex < inputString.length &&
      inputString[leftIndex] === inputString[rightIndex]
    ) {
      currentCount++;
      leftIndex--;
      rightIndex++;
    }
    return currentCount;
  };

  for (let currentIndex = 0; currentIndex < inputString.length; currentIndex++) {
    totalPalindromeCount += countFromCenter(currentIndex, currentIndex);
    totalPalindromeCount += countFromCenter(currentIndex, currentIndex + 1);
  }

  return totalPalindromeCount;
};
```
* **Dry Run (input = "aaa"):**
    * `i`=0: (0,0)->"a", (0,1)->"aa". Count = 2.
    * `i`=1: (1,1)->"a", (0,2)->"aaa", (1,2)->"aa". Count = 2+3 = 5.
    * `i`=2: (2,2)->"a". Count = 5+1 = 6.
* **Alternative Approach:** We could use a DP table where `dp[i][j]` is true if `s[i...j]` is a palindrome.

---

## 7. Decode Ways
[https://leetcode.com/problems/decode-ways/](https://leetcode.com/problems/decode-ways/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** The number of ways to decode a string of length $i$ depends on whether the last character is valid (1-9) and whether the last two characters form a valid number (10-26).
* **The 'Talk Track':**
    * "I'll use a `dp` array where `dp[i]` represents the ways to decode the prefix of length `i`."
    * "A single digit is valid if it's not '0'. A double digit is valid if it's between '10' and '26'."
    * "This is similar to Climbing Stairs, but with conditional constraints on each 'step'."
* **Pseudocode:**
    1. `dp[0] = 1`. If `s[0] === '0'`, return 0.
    2. Loop from index 2 to $n$:
       a. If `s[i-1]` is valid (1-9), `dp[i] += dp[i-1]`.
       b. If `s[i-2...i-1]` is valid (10-26), `dp[i] += dp[i-2]`.
* **JavaScript Solution:**
```javascript
const numDecodings = (inputString) => {
  if (!inputString || inputString[0] === '0') return 0;
  const stringLength = inputString.length;
  const dpTable = new Array(stringLength + 1).fill(0);
  
  dpTable[0] = 1;
  dpTable[1] = 1;

  for (let currentIndex = 2; currentIndex <= stringLength; currentIndex++) {
    const singleDigit = parseInt(inputString.substring(currentIndex - 1, currentIndex));
    const doubleDigit = parseInt(inputString.substring(currentIndex - 2, currentIndex));

    if (singleDigit >= 1 && singleDigit <= 9) {
      dpTable[currentIndex] += dpTable[currentIndex - 1];
    }
    if (doubleDigit >= 10 && doubleDigit <= 26) {
      dpTable[currentIndex] += dpTable[currentIndex - 2];
    }
  }

  return dpTable[stringLength];
};
```
* **Dry Run (input = "12"):**
    * `dp` = [1, 1, 0].
    * `i`=2: single "2" is valid (add dp[1]=1), double "12" is valid (add dp[0]=1).
    * `dp[2]` = 2.
* **Alternative Approach:** Space can be optimized to $O(1)$ by only storing the last two `dp` values.

---

## 8. Coin Change
[https://leetcode.com/problems/coin-change/](https://leetcode.com/problems/coin-change/)

* **Complexity:** Time: $O(amount \cdot n)$ | Space: $O(amount)$
* **Key Intuition:** For every amount, the minimum coins needed is $1 + \min(\text{coins needed for (amount - coin\_value)})$.
* **The 'Talk Track':**
    * "I'll build the solution bottom-up, starting from amount 0 to the target amount."
    * "I initialize the `dp` array with `amount + 1` (a value larger than any possible answer) to represent infinity."
    * "If `dp[target]` is still 'Infinity' at the end, it means the amount is unreachable."
* **JavaScript Solution:**
```javascript
const coinChange = (coins, targetAmount) => {
  const dpTable = new Array(targetAmount + 1).fill(targetAmount + 1);
  dpTable[0] = 0;

  for (let currentAmount = 1; currentAmount <= targetAmount; currentAmount++) {
    for (const coinValue of coins) {
      if (currentAmount - coinValue >= 0) {
        dpTable[currentAmount] = Math.min(
          dpTable[currentAmount],
          1 + dpTable[currentAmount - coinValue]
        );
      }
    }
  }

  return dpTable[targetAmount] > targetAmount ? -1 : dpTable[targetAmount];
};
```
* **Dry Run (coins = [1, 2], target = 3):**
    * `dp` = [0, 4, 4, 4].
    * `amt`=1: min(4, 1+dp[0]) = 1.
    * `amt`=2: min(4, 1+dp[1], 1+dp[0]) = 1.
    * `amt`=3: min(4, 1+dp[2], 1+dp[1]) = 2.
* **Alternative Approach:** Top-down recursion with memoization is also common but usually slightly slower due to recursion overhead.

---

## 9. Maximum Product Subarray
[https://leetcode.com/problems/maximum-product-subarray/](https://leetcode.com/problems/maximum-product-subarray/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Since multiplying by a negative number flips the sign, we must track both the maximum product *and* the minimum product ending at the current position.
* **The 'Talk Track':**
    * "When we encounter a negative number, our current max could become a min, and our current min could become a max."
    * "I'll swap `currentMax` and `currentMin` when the current element is negative to maintain the potential for a large positive product later."
    * "This is a clever variation of Kadane's algorithm."
* **JavaScript Solution:**
```javascript
const maxProduct = (nums) => {
  let globalMax = nums[0];
  let currentMax = nums[0];
  let currentMin = nums[0];

  for (let currentIndex = 1; currentIndex < nums.length; currentIndex++) {
    const value = nums[currentIndex];
    
    if (value < 0) {
      [currentMax, currentMin] = [currentMin, currentMax];
    }

    currentMax = Math.max(value, currentMax * value);
    currentMin = Math.min(value, currentMin * value);

    globalMax = Math.max(globalMax, currentMax);
  }

  return globalMax;
};
```
* **Dry Run (input = [2, -5, -2]):**
    * `i`=0: max=2, min=2, glob=2.
    * `i`=1: swap (2,2), max=max(-5, -10)=-5, min=min(-5, -10)=-10, glob=2.
    * `i`=2: swap (-5, -10), max=max(-2, 20)=20, min=min(-2, 10)=-2, glob=20.
* **Alternative Approach:** We could calculate prefix and suffix products; the answer is the max of either, but handling zeros makes that approach more complex.

---

## 10. Word Break
[https://leetcode.com/problems/word-break/](https://leetcode.com/problems/word-break/)

* **Complexity:** Time: $O(n^3)$ (due to substring and set lookup) | Space: $O(n)$
* **Key Intuition:** A string `s` can be broken if there exists an index `j` such that `s[0...j]` is breakable and `s[j...i]` exists in the dictionary.
* **The 'Talk Track':**
    * "I'll use a `dp` array where `dp[i]` is a boolean indicating if the prefix of length `i` is breakable."
    * "For each length `i`, I'll look back at all previous breakable indices `j` and check if the remainder is in the dictionary."
    * "Using a `Set` for the dictionary makes the lookups $O(1)$ on average."
* **JavaScript Solution:**
```javascript
const wordBreak = (inputString, wordDict) => {
  const dictionarySet = new Set(wordDict);
  const dpTable = new Array(inputString.length + 1).fill(false);
  dpTable[0] = true; // Empty string is "breakable"

  for (let i = 1; i <= inputString.length; i++) {
    for (let j = 0; j < i; j++) {
      if (dpTable[j] && dictionarySet.has(inputString.substring(j, i))) {
        dpTable[i] = true;
        break;
      }
    }
  }

  return dpTable[inputString.length];
};
```
* **Dry Run (input = "leetcode", dict = ["leet", "code"]):**
    * `dp[0]` = true.
    * `i`=4: `j`=0, "leet" in dict, `dp[4]` = true.
    * `i`=8: `j`=4, `dp[4]` is true, "code" in dict, `dp[8]` = true.
* **Alternative Approach:** A Trie combined with DFS + memoization can also be very efficient.

---

## 11. Longest Increasing Subsequence
[https://leetcode.com/problems/longest-increasing-subsequence/](https://leetcode.com/problems/longest-increasing-subsequence/)

* **Complexity:** Time: $O(n \log n)$ | Space: $O(n)$
* **Key Intuition:** We maintain a list of the smallest possible tail for all increasing subsequences of different lengths.
* **The 'Talk Track':**
    * "The standard $O(n^2)$ DP is good, but we can reach $O(n \log n)$ using a 'patience sorting' inspired approach."
    * "I'll maintain an array `sub` where I use binary search to either append the current number or replace the first element that is larger than it."
    * "The length of this `sub` array will be the length of the LIS."
* **JavaScript Solution:**
```javascript
const lengthOfLIS = (nums) => {
  const activeSubsequence = [];

  for (const currentNumber of nums) {
    let leftIndex = 0;
    let rightIndex = activeSubsequence.length;

    // Binary search to find the insertion point
    while (leftIndex < rightIndex) {
      const middleIndex = Math.floor((leftIndex + rightIndex) / 2);
      if (activeSubsequence[middleIndex] < currentNumber) {
        leftIndex = middleIndex + 1;
      } else {
        rightIndex = middleIndex;
      }
    }

    if (leftIndex === activeSubsequence.length) {
      activeSubsequence.push(currentNumber);
    } else {
      activeSubsequence[leftIndex] = currentNumber;
    }
  }

  return activeSubsequence.length;
};
```
* **Dry Run (input = [10, 9, 2, 5]):**
    * `num`=10: `sub`=[10].
    * `num`=9: replace 10 -> `sub`=[9].
    * `num`=2: replace 9 -> `sub`=[2].
    * `num`=5: append -> `sub`=[2, 5]. Length = 2.
* **Alternative Approach:** $O(n^2)$ nested loops where `dp[i] = 1 + max(dp[j])`.

---

## 12. Partition Equal Subset Sum
[https://leetcode.com/problems/partition-equal-subset-sum/](https://leetcode.com/problems/partition-equal-subset-sum/)

* **Complexity:** Time: $O(n \cdot sum)$ | Space: $O(sum)$
* **Key Intuition:** This is a 0/1 Knapsack problem. We need to find if a subset of numbers sums exactly to `totalSum / 2`.
* **The 'Talk Track':**
    * "First, if the total sum is odd, it's impossible to partition it equally."
    * "I'll use a `Set` (or a bitset) to track all possible sums we can create as we iterate through the numbers."
    * "To optimize space, I only need to track the sums possible with the current number and previous numbers."
* **JavaScript Solution:**
```javascript
const canPartition = (nums) => {
  const totalSum = nums.reduce((acc, curr) => acc + curr, 0);
  if (totalSum % 2 !== 0) return false;
  
  const targetSum = totalSum / 2;
  let possibleSums = new Set([0]);

  for (const currentNumber of nums) {
    const nextPossibleSums = new Set(possibleSums);
    for (const sum of possibleSums) {
      const newSum = sum + currentNumber;
      if (newSum === targetSum) return true;
      if (newSum < targetSum) nextPossibleSums.add(newSum);
    }
    possibleSums = nextPossibleSums;
  }

  return false;
};
```
* **Dry Run (input = [1, 5, 5]):**
    * `target` = 5.5 (impossible, return false). Wait, if input is [1, 2, 3], `target` = 3.
    * `num`=1: sums={0, 1}.
    * `num`=2: sums={0, 1, 2, 3}. `newSum` 3 matches target. Return true.
* **Alternative Approach:** Using a 1D boolean array and iterating backwards to avoid using the same element twice.

---

## 13. Triangle
[https://leetcode.com/problems/triangle/](https://leetcode.com/problems/triangle/)

* **Complexity:** Time: $O(n^2)$ | Space: $O(n)$
* **Key Intuition:** To find the minimum path, we work from the bottom of the triangle up to the top. The min path to a cell is its value plus the minimum of the two cells below it.
* **The 'Talk Track':**
    * "Bottom-up DP is much simpler here because the top has only one entry point."
    * "I'll use a 1D array to store the minimum path sums of the row below the current one."
    * "By the time we reach the top, `dp[0]` will contain the answer."
* **JavaScript Solution:**
```javascript
const minimumTotal = (triangle) => {
  const rowCount = triangle.length;
  // Initialize with the bottom row
  const dpTable = [...triangle[rowCount - 1]];

  for (let rowIndex = rowCount - 2; rowIndex >= 0; rowIndex--) {
    for (let colIndex = 0; colIndex <= rowIndex; colIndex++) {
      // Current cell + min of the two values directly below it
      dpTable[colIndex] = triangle[rowIndex][colIndex] + Math.min(
        dpTable[colIndex],
        dpTable[colIndex + 1]
      );
    }
  }

  return dpTable[0];
};
```
* **Dry Run (triangle = [[2], [3, 4]]):**
    * `dp` = [3, 4].
    * `row`=0: `dp[0]` = 2 + min(3, 4) = 5.
    * Return 5.
* **Alternative Approach:** Modifying the triangle in-place to achieve $O(1)$ extra space.

---

## 14. [Latest 2026] Extra Characters in a String
[https://leetcode.com/problems/extra-characters-in-a-string/](https://leetcode.com/problems/extra-characters-in-a-string/)

* **Complexity:** Time: $O(n^3)$ | Space: $O(n)$
* **Key Intuition:** For each character, we can either consider it "extra" or try to find a word in the dictionary that ends at this position.
* **The 'Talk Track':**
    * "I'll define `dp[i]` as the minimum extra characters in the prefix of length `i`."
    * "At each step, the default is `dp[i-1] + 1` (treating the current char as extra)."
    * "Then I'll check all substrings ending at `i` to see if they match a dictionary word to potentially reduce the count."
* **JavaScript Solution:**
```javascript
const minExtraChar = (inputString, wordDictionary) => {
  const stringLength = inputString.length;
  const dictionarySet = new Set(wordDictionary);
  const dpTable = new Array(stringLength + 1).fill(0);

  for (let i = 1; i <= stringLength; i++) {
    // Default: current character is extra
    dpTable[i] = dpTable[i - 1] + 1;
    
    for (let j = 0; j < i; j++) {
      if (dictionarySet.has(inputString.substring(j, i))) {
        dpTable[i] = Math.min(dpTable[i], dpTable[j]);
      }
    }
  }

  return dpTable[stringLength];
};
```
* **Dry Run (input = "leetsc", dict = ["leet"]):**
    * `dp[4]` = 0 (after finding "leet").
    * `i`=5 ('s'): `dp[5]` = `dp[4]`+1 = 1.
    * `i`=6 ('c'): `dp[6]` = `dp[5]`+1 = 2.
    * Return 2.
* **Alternative Approach:** Using a Trie to speed up the substring lookups.

---

## 15. [Latest 2026] Maximum Number of Moves in a Grid
[https://leetcode.com/problems/maximum-number-of-moves-in-a-grid/](https://leetcode.com/problems/maximum-number-of-moves-in-a-grid/)

* **Complexity:** Time: $O(m \cdot n)$ | Space: $O(m \cdot n)$
* **Key Intuition:** We want to find the longest path in a grid where we can only move to a cell in the next column with a strictly greater value.
* **The 'Talk Track':**
    * "This is a grid-based DP where we only move right, so we can process column by column."
    * "I'll use a `dp` table to store whether a cell is reachable; the answer is the furthest column we can reach."
    * "Using a BFS/DFS with memoization is also very intuitive for this 'pathfinding' style of DP."
* **JavaScript Solution:**
```javascript
const maxMoves = (grid) => {
  const rowCount = grid.length;
  const colCount = grid[0].length;
  const memoizationTable = Array.from({ length: rowCount }, () => new Array(colCount).fill(-1));

  const calculateMoves = (currentRow, currentCol) => {
    if (currentCol === colCount - 1) return 0;
    if (memoizationTable[currentRow][currentCol] !== -1) return memoizationTable[currentRow][currentCol];

    let maxFromHere = 0;
    const nextColumn = currentCol + 1;
    
    // Check three possible moves: up-right, right, down-right
    for (let rowOffset = -1; rowOffset <= 1; rowOffset++) {
      const nextRow = currentRow + rowOffset;
      if (nextRow >= 0 && nextRow < rowCount && grid[nextRow][nextColumn] > grid[currentRow][currentCol]) {
        maxFromHere = Math.max(maxFromHere, 1 + calculateMoves(nextRow, nextColumn));
      }
    }

    memoizationTable[currentRow][currentCol] = maxFromHere;
    return maxFromHere;
  };

  let globalMaxMoves = 0;
  for (let startRow = 0; startRow < rowCount; startRow++) {
    globalMaxMoves = Math.max(globalMaxMoves, calculateMoves(startRow, 0));
  }

  return globalMaxMoves;
};
```
* **Dry Run (grid = [[1, 2], [3, 4]]):**
    * Start (0,0): can move to (0,1) because 2 > 1. 1 move.
    * Start (1,0): can move to (1,1) because 4 > 3. 1 move.
    * Result: 1.
* **Alternative Approach:** Iterative DP column by column using two boolean arrays to track reachability.

---
