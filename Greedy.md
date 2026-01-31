# Senior Engineer's Study Guide: Greedy Algorithms

The **Greedy** pattern involves making the locally optimal choice at each step with the hope of finding the global optimum. In a senior-level interview, the key is proving that a greedy choice is safe and handling constraints—such as large input sizes—where a Dynamic Programming approach ($O(n^2)$) would be too slow.

---

## 1. Maximum Subarray
[https://leetcode.com/problems/maximum-subarray/](https://leetcode.com/problems/maximum-subarray/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Known as Kadane's Algorithm; we discard the prefix sum if it drops below zero, as it would only decrease the value of any subsequent subarray.
* **The 'Talk Track':**
    * "I'll iterate through the array once, maintaining a running sum of the current subarray."
    * "If my running sum becomes negative, I'll reset it to zero because a negative prefix cannot possibly contribute to a maximum sum."
    * "This linear approach is far superior to a nested loop $O(n^2)$ brute force."
* **Pseudocode:**
    1. Initialize `maxSumFound` to the first element and `currentRunningSum` to 0.
    2. Iterate through each `number` in `nums`.
    3. Add `number` to `currentRunningSum`.
    4. Update `maxSumFound` if `currentRunningSum` is greater.
    5. If `currentRunningSum < 0`, reset `currentRunningSum` to 0.
    6. Return `maxSumFound`.
* **JavaScript Solution:**
```javascript
const maxSubArray = (nums) => {
  let maxSumFound = nums[0];
  let currentRunningSum = 0;

  for (const currentNumber of nums) {
    currentRunningSum += currentNumber;
    if (currentRunningSum > maxSumFound) {
      maxSumFound = currentRunningSum;
    }
    if (currentRunningSum < 0) {
      currentRunningSum = 0;
    }
  }

  return maxSumFound;
};
```
* **Dry Run (input = [-1, 2]):**
    * Init: `maxSum` = -1, `running` = 0.
    * Loop -1: `running` = -1. `maxSum` = -1. `running` resets to 0.
    * Loop 2: `running` = 2. `maxSum` = 2.
    * Return 2.
* **Alternative Approach:** We could use a Divide and Conquer approach, but it increases space complexity to $O(\log n)$ due to recursion.

---

## 2. Jump Game
[https://leetcode.com/problems/jump-game/](https://leetcode.com/problems/jump-game/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Instead of checking if we can reach the end from the start, we work backward to see if the 'goal' can be moved to the first index.
* **The 'Talk Track':**
    * "I'll use a backward-greedy approach by shifting the target goal toward the start."
    * "If a specific index can reach the current goal, that index becomes the new goal."
    * "If the goal reaches index 0, we know a path exists."
* **Pseudocode:**
    1. Set `goalPost` to `nums.length - 1`.
    2. Iterate from the end of the array to the beginning.
    3. If `currentIndex + nums[currentIndex] >= goalPost`, update `goalPost = currentIndex`.
    4. Return `true` if `goalPost === 0`.
* **JavaScript Solution:**
```javascript
const canJump = (nums) => {
  let goalPostIndex = nums.length - 1;

  for (let currentIndex = nums.length - 2; currentIndex >= 0; currentIndex--) {
    const jumpCapability = nums[currentIndex];
    if (currentIndex + jumpCapability >= goalPostIndex) {
      goalPostIndex = currentIndex;
    }
  }

  return goalPostIndex === 0;
};
```
* **Dry Run (input = [1, 0]):**
    * `goalPost` = 1.
    * Loop `i` = 0: 0 + 1 >= 1 is true. `goalPost` = 0.
    * Return `true`.
* **Alternative Approach:** We could use Dynamic Programming (memoization), but that would cost $O(n)$ space.

---

## 3. Jump Game II
[https://leetcode.com/problems/jump-game-ii/](https://leetcode.com/problems/jump-game-ii/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** We use a Level Order Traversal (BFS) logic in a greedy fashion, where we jump to the furthest possible index within our current reach.
* **The 'Talk Track':**
    * "I'll maintain a window `[left, right]` representing the range reachable within the current number of jumps."
    * "At each step, I calculate the absolute furthest I can reach from any point in the current window."
    * "This greedy strategy ensures we find the minimum jumps without exhaustive searching."
* **Pseudocode:**
    1. Initialize `jumpsTaken = 0`, `currentRangeEnd = 0`, `furthestReach = 0`.
    2. Iterate through `nums` (except the last index).
    3. Update `furthestReach = max(furthestReach, currentIndex + nums[currentIndex])`.
    4. If `currentIndex === currentRangeEnd`: increment `jumpsTaken` and set `currentRangeEnd = furthestReach`.
    5. Return `jumpsTaken`.
* **JavaScript Solution:**
```javascript
const jump = (nums) => {
  let totalJumps = 0;
  let currentWindowEnd = 0;
  let absoluteFurthestReach = 0;

  for (let currentIndex = 0; currentIndex < nums.length - 1; currentIndex++) {
    absoluteFurthestReach = Math.max(
      absoluteFurthestReach,
      currentIndex + nums[currentIndex]
    );

    if (currentIndex === currentWindowEnd) {
      totalJumps++;
      currentWindowEnd = absoluteFurthestReach;
    }
  }

  return totalJumps;
};
```
* **Dry Run (input = [2, 3, 1]):**
    * `i` = 0: `reach` = 2. `i` === `end`(0). `jumps` = 1, `end` = 2.
    * `i` = 1: `reach` = 4.
    * Loop ends. Return 1.
* **Alternative Approach:** Recursion with memoization works but is significantly slower than this linear greedy approach.

---

## 4. Gas Station
[https://leetcode.com/problems/gas-station/](https://leetcode.com/problems/gas-station/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** If the total gas is less than the total cost, it's impossible. Otherwise, if you can't reach station B from A, no station between A and B can reach B either.
* **The 'Talk Track':**
    * "First, I'll validate if a solution is even possible by comparing total gas vs total cost."
    * "I'll greedily assume the start is at index 0 and reset the starting point whenever my tank becomes negative."
    * "The uniqueness of the solution guaranteed by the problem allows this one-pass optimization."
* **Pseudocode:**
    1. Check if `sum(gas) < sum(cost)`; if so, return -1.
    2. Initialize `tankBalance = 0`, `startIndex = 0`.
    3. For each station:
       a. `tankBalance += gas[i] - cost[i]`.
       b. If `tankBalance < 0`, set `startIndex = i + 1` and `tankBalance = 0`.
    4. Return `startIndex`.
* **JavaScript Solution:**
```javascript
const canCompleteCircuit = (gas, cost) => {
  const totalGas = gas.reduce((accumulator, value) => accumulator + value, 0);
  const totalCost = cost.reduce((accumulator, value) => accumulator + value, 0);

  if (totalGas < totalCost) return -1;

  let currentTankAmount = 0;
  let startingStationIndex = 0;

  for (let currentIndex = 0; currentIndex < gas.length; currentIndex++) {
    currentTankAmount += gas[currentIndex] - cost[currentIndex];
    
    if (currentTankAmount < 0) {
      currentTankAmount = 0;
      startingStationIndex = currentIndex + 1;
    }
  }

  return startingStationIndex;
};
```
* **Dry Run (gas = [1, 2], cost = [2, 1]):**
    * Totals: Gas 3, Cost 3. OK.
    * `i` = 0: `tank` = -1. Reset: `start` = 1, `tank` = 0.
    * `i` = 1: `tank` = 1.
    * Return 1.
* **Alternative Approach:** A brute force $O(n^2)$ approach checks every starting index, but it's too slow for large inputs.

---

## 5. Hand of Straights
[https://leetcode.com/problems/hand-of-straights/](https://leetcode.com/problems/hand-of-straights/)

* **Complexity:** Time: $O(n \log n)$ | Space: $O(n)$
* **Key Intuition:** We use a frequency map and a sorted list of unique keys. We greedily try to form a group starting from the smallest available card.
* **The 'Talk Track':**
    * "I'll count frequencies using a Map and sort the unique card values to ensure I process the 'heads' of potential straights in order."
    * "If I pick a card, I must be able to pick the next `groupSize - 1` consecutive cards from the map."
    * "This greedy strategy works because the smallest card *must* be part of a group starting with itself."
* **Pseudocode:**
    1. If `hand.length % groupSize !== 0`, return false.
    2. Create a frequency Map of `hand`.
    3. Sort the unique card values.
    4. For each `card` in sorted values:
       a. If `count > 0`: for `i` from 0 to `groupSize - 1`, decrement `map[card + i]` by `count`.
       b. If any `map[card + i]` becomes negative, return false.
* **JavaScript Solution:**
```javascript
const isNStraightHand = (hand, groupSize) => {
  if (hand.length % groupSize !== 0) return false;

  const cardFrequencies = new Map();
  for (const cardValue of hand) {
    cardFrequencies.set(cardValue, (cardFrequencies.get(cardValue) || 0) + 1);
  }

  const sortedUniqueCards = Array.from(cardFrequencies.keys()).sort((a, b) => a - b);

  for (const currentCard of sortedUniqueCards) {
    const currentCardCount = cardFrequencies.get(currentCard);
    
    if (currentCardCount > 0) {
      for (let offset = 0; offset < groupSize; offset++) {
        const targetCard = currentCard + offset;
        const targetCount = cardFrequencies.get(targetCard) || 0;
        
        if (targetCount < currentCardCount) return false;
        cardFrequencies.set(targetCard, targetCount - currentCardCount);
      }
    }
  }

  return true;
};
```
* **Dry Run (hand = [1, 2, 3], size = 3):**
    * `freq`: {1:1, 2:1, 3:1}.
    * Smallest is 1. Count is 1.
    * Decrement 1, 2, 3 by 1. All zero.
    * Return `true`.
* **Alternative Approach:** We could use a Min-Heap to find the smallest element repeatedly, which also results in $O(n \log n)$.

---

## 6. Merge Triplets to Form Target Triplet
[https://leetcode.com/problems/merge-triplets-to-form-target-triplet/](https://leetcode.com/problems/merge-triplets-to-form-target-triplet/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** A triplet is "useful" only if all three of its values are less than or equal to the target's respective values. We greedily collect the maximum possible values from useful triplets.
* **The 'Talk Track':**
    * "I'll filter out any triplet that has even one value exceeding our target, as it would ruin the merged result."
    * "From the remaining safe triplets, I'll track the maximum value found at each of the three positions."
    * "If these maximums eventually match our target triplet exactly, the answer is true."
* **Pseudocode:**
    1. Initialize `achieved = [false, false, false]`.
    2. For each `triplet [a, b, c]`:
       a. If `a <= target[0] && b <= target[1] && c <= target[2]`:
          i. Update `achieved[0]` if `a === target[0]`, etc.
    3. Return `achieved.every(val => val === true)`.
* **JavaScript Solution:**
```javascript
const mergeTriplets = (triplets, target) => {
  const achievedIndices = new Set();

  for (const [valA, valB, valC] of triplets) {
    if (valA <= target[0] && valB <= target[1] && valC <= target[2]) {
      if (valA === target[0]) achievedIndices.add(0);
      if (valB === target[1]) achievedIndices.add(1);
      if (valC === target[2]) achievedIndices.add(2);
    }
  }

  return achievedIndices.size === 3;
};
```
* **Dry Run (triplets = [[2, 5, 3]], target = [2, 5, 3]):**
    * Triplet [2, 5, 3] is <= target.
    * Match indices 0, 1, 2. `size` = 3.
    * Return `true`.
* **Alternative Approach:** We could maintain a single 'best' triplet and merge into it, but using a Set to track "reached" targets is more robust and readable.

---

## 7. Partition Labels
[https://leetcode.com/problems/partition-labels/](https://leetcode.com/problems/partition-labels/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$ (constant 26 characters)
* **Key Intuition:** Each partition must contain all occurrences of every character it includes. We greedily extend the current partition's end until we reach the last occurrence of all included characters.
* **The 'Talk Track':**
    * "First, I'll build a map of the last known index of every character in the string."
    * "As I iterate, I'll update the `currentPartitionEnd` to be the maximum of the 'last indices' of characters seen so far."
    * "When my `currentIndex` catches up to the `currentPartitionEnd`, I've found a valid split point."
* **Pseudocode:**
    1. Map each character to its `lastIndex`.
    2. Initialize `start = 0`, `end = 0`.
    3. For `i` from 0 to `n-1`:
       a. Update `end = max(end, lastIndex[char[i]])`.
       b. If `i === end`: push `end - start + 1` to result and set `start = i + 1`.
* **JavaScript Solution:**
```javascript
const partitionLabels = (inputString) => {
  const lastOccurrenceMap = {};
  for (let currentIndex = 0; currentIndex < inputString.length; currentIndex++) {
    lastOccurrenceMap[inputString[currentIndex]] = currentIndex;
  }

  const partitionSizes = [];
  let currentPartitionStart = 0;
  let currentPartitionEnd = 0;

  for (let currentIndex = 0; currentIndex < inputString.length; currentIndex++) {
    currentPartitionEnd = Math.max(
      currentPartitionEnd, 
      lastOccurrenceMap[inputString[currentIndex]]
    );

    if (currentIndex === currentPartitionEnd) {
      partitionSizes.push(currentIndex - currentPartitionStart + 1);
      currentPartitionStart = currentIndex + 1;
    }
  }

  return partitionSizes;
};
```
* **Dry Run (input = "abab"):**
    * `last`: {a: 2, b: 3}.
    * `i` = 0: `end` = 2.
    * `i` = 1: `end` = max(2, 3) = 3.
    * `i` = 3: Catch up! Size = 3 - 0 + 1 = 4.
    * Return [4].
* **Alternative Approach:** This could be solved by treating characters as "Intervals" and performing a Merge Intervals algorithm ($O(n \log n)$).

---

## 8. Valid Parenthesis String
[https://leetcode.com/problems/valid-parenthesis-string/](https://leetcode.com/problems/valid-parenthesis-string/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** We track the minimum and maximum possible number of open parentheses we could have, treating `*` as `(`, `)`, or empty.
* **The 'Talk Track':**
    * "I'll maintain a range `[minOpen, maxOpen]` to account for the flexibility of the `*` character."
    * "If `maxOpen` drops below zero, we have too many closing parentheses, so it's invalid."
    * "We must ensure `minOpen` never drops below zero (we greedily treat `*` as `)` only if possible) and finally check if zero is in our range."
* **Pseudocode:**
    1. Initialize `low = 0`, `high = 0`.
    2. For each `char`:
       a. If `(`, increment both.
       b. If `)`, decrement both.
       c. If `*`, `low--`, `high++`.
       d. If `high < 0`, return false.
       e. `low = max(low, 0)`.
    3. Return `low === 0`.
* **JavaScript Solution:**
```javascript
const checkValidString = (inputString) => {
  let minimumOpenCount = 0;
  let maximumOpenCount = 0;

  for (const character of inputString) {
    if (character === "(") {
      minimumOpenCount++;
      maximumOpenCount++;
    } else if (character === ")") {
      minimumOpenCount--;
      maximumOpenCount--;
    } else {
      // Character is '*'
      minimumOpenCount--;
      maximumOpenCount++;
    }

    if (maximumOpenCount < 0) return false;
    if (minimumOpenCount < 0) minimumOpenCount = 0;
  }

  return minimumOpenCount === 0;
};
```
* **Dry Run (input = "(*)"):**
    * `(`: `min`=1, `max`=1.
    * `*`: `min`=0, `max`=2.
    * `)`: `min`=-1 -> 0, `max`=1.
    * Return `true`.
* **Alternative Approach:** Using two stacks (one for `(` and one for `*`) is also common, but it uses $O(n)$ space.

---

## 9. [Latest 2026] Maximum Sum of Distinct Subarrays With Length K
[https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/)

* **Complexity:** Time: $O(n)$ | Space: $O(k)$
* **Key Intuition:** A greedy sliding window where we only update the answer if the current window contains exactly $K$ unique elements.
* **The 'Talk Track':**
    * "I'll use a sliding window of size $K$ and a Map to track frequencies and count distinct elements."
    * "By greedily maintaining the running sum of only the window, I avoid re-calculating sums from scratch."
    * "The distinctness check is the primary constraint to manage using the Map size."
* **Pseudocode:**
    1. Sliding window of size `k`.
    2. Maintain `currentSum` and `frequencyMap`.
    3. If `frequencyMap.size === k`, update `maxSum`.
* **JavaScript Solution:**
```javascript
const maximumSubarraySum = (nums, k) => {
  let maxSumFound = 0;
  let runningWindowSum = 0;
  const elementFrequencies = new Map();
  let leftPointer = 0;

  for (let rightPointer = 0; rightPointer < nums.length; rightPointer++) {
    const incomingElement = nums[rightPointer];
    runningWindowSum += incomingElement;
    elementFrequencies.set(incomingElement, (elementFrequencies.get(incomingElement) || 0) + 1);

    if (rightPointer - leftPointer + 1 > k) {
      const outgoingElement = nums[leftPointer];
      runningWindowSum -= outgoingElement;
      elementFrequencies.set(outgoingElement, elementFrequencies.get(outgoingElement) - 1);
      if (elementFrequencies.get(outgoingElement) === 0) {
        elementFrequencies.delete(outgoingElement);
      }
      leftPointer++;
    }

    if (rightPointer - leftPointer + 1 === k) {
      if (elementFrequencies.size === k) {
        maxSumFound = Math.max(maxSumFound, runningWindowSum);
      }
    }
  }

  return maxSumFound;
};
```
* **Dry Run (nums = [1, 1], k = 2):**
    * Window [1, 1]. `sum` = 2. `map.size` = 1.
    * 1 !== 2. `maxSum` = 0.
    * Return 0.
* **Alternative Approach:** A brute force $O(n \cdot k)$ would calculate every window's sum and set, but that fails for large $N$.

---

## 10. [Latest 2026] Minimum Operations to Make Array Equal II
[https://leetcode.com/problems/minimum-operations-to-make-array-equal-ii/](https://leetcode.com/problems/minimum-operations-to-make-array-equal-ii/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Since we can only increment one element and decrement another by $K$ simultaneously, the total net difference must be zero, and every individual difference must be divisible by $K$.
* **The 'Talk Track':**
    * "I'll first check if $K=0$; in that case, the arrays must be identical."
    * "I'll track the 'positive' and 'negative' differences separately; for the transformation to be possible, their absolute values must match."
    * "Each operation 'shifts' $K$ value from one index to another, so the total operations is simply the sum of positive differences divided by $K$."
* **Pseudocode:**
    1. Handle $K=0$ case.
    2. Initialize `positiveDiff = 0`, `negativeDiff = 0`.
    3. Iterate through `nums1` and `nums2`.
    4. Calculate `diff = nums1[i] - nums2[i]`.
    5. If `diff % k !== 0`, return -1.
    6. Accumulate into `positiveDiff` or `negativeDiff`.
    7. Return `abs(diffs) / k` if balanced.
* **JavaScript Solution:**
```javascript
const minOperations = (nums1, nums2, k) => {
  if (k === 0) {
    return nums1.every((val, index) => val === nums2[index]) ? 0 : -1;
  }

  let positiveDifferencesSum = 0;
  let negativeDifferencesSum = 0;

  for (let currentIndex = 0; currentIndex < nums1.length; currentIndex++) {
    const totalDifference = nums1[currentIndex] - nums2[currentIndex];

    if (totalDifference % k !== 0) return -1;

    if (totalDifference > 0) {
      positiveDifferencesSum += totalDifference;
    } else {
      negativeDifferencesSum += Math.abs(totalDifference);
    }
  }

  return positiveDifferencesSum === negativeDifferencesSum 
    ? positiveDifferencesSum / k 
    : -1;
};
```
* **Dry Run (n1=[4,3], n2=[2,5], k=2):**
    * `diff1` = 2. `pos` = 2.
    * `diff2` = -2. `neg` = 2.
    * `pos === neg`. Result: 2/2 = 1.
* **Alternative Approach:** We could attempt to simulate, but that would be far too slow ($O(\text{operations})$).

---
