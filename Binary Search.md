# Senior Engineer's Study Guide: Binary Search

The **Binary Search** pattern is about more than just finding an element in a sorted array; it’s a powerful optimization technique for any monotonic search space. As a senior engineer, the focus is on identifying "search space" boundaries and handling tricky edge cases (like duplicates or rotations) while maintaining $O(\log n)$ efficiency.



---

## 1. Binary Search
[https://leetcode.com/problems/binary-search/](https://leetcode.com/problems/binary-search/)

* **Complexity:** Time: $O(\log n)$ | Space: $O(1)$
* **Key Intuition:** By comparing the target to the middle element, we can discard half of the search space in each iteration, leading to logarithmic time.
* **The 'Talk Track':**
    * "I'll use two pointers, `leftIndex` and `rightIndex`, to define the boundaries of our current search space."
    * "I calculate the `middleIndex` using `Math.floor(leftIndex + (rightIndex - leftIndex) / 2)` to prevent potential integer overflow in languages with fixed-width integers."
    * "Since the array is sorted, if the value at `middleIndex` is less than our target, I know the target must reside in the right half."
* **Pseudocode:**
    1. Initialize `leftIndex = 0` and `rightIndex = length - 1`.
    2. While `leftIndex <= rightIndex`:
       a. Calculate `middleIndex`.
       b. If `nums[middleIndex] === target`, return `middleIndex`.
       c. If `nums[middleIndex] < target`, `leftIndex = middleIndex + 1`.
       d. Else, `rightIndex = middleIndex - 1`.
    3. Return -1 if not found.
* **JavaScript Solution:**
```javascript
const search = (nums, targetValue) => {
  let leftIndex = 0;
  let rightIndex = nums.length - 1;

  while (leftIndex <= rightIndex) {
    const middleIndex = Math.floor(leftIndex + (rightIndex - leftIndex) / 2);
    const middleValue = nums[middleIndex];

    if (middleValue === targetValue) {
      return middleIndex;
    } else if (middleValue < targetValue) {
      leftIndex = middleIndex + 1;
    } else {
      rightIndex = middleIndex - 1;
    }
  }

  return -1;
};
```
* **Dry Run (input = [1, 2], target = 2):**
    * `left` = 0, `right` = 1. `mid` = 0. `nums[0]` is 1.
    * 1 < 2, so `left` = 1.
    * `left` = 1, `right` = 1. `mid` = 1. `nums[1]` is 2.
    * Match! Return 1.
* **Alternative Approach:** We could use recursion, but it would increase space complexity to $O(\log n)$ due to the call stack.

---

## 2. Search a 2D Matrix
[https://leetcode.com/problems/search-a-2d-matrix/](https://leetcode.com/problems/search-a-2d-matrix/)

* **Complexity:** Time: $O(\log(m \times n))$ | Space: $O(1)$
* **Key Intuition:** Since the matrix is sorted such that the first element of a row is greater than the last of the previous, we can treat the entire 2D structure as a single flattened sorted array.
* **The 'Talk Track':**
    * "I'll treat the $M \times N$ matrix as a virtual 1D array to keep the complexity at a strict $O(\log(m \times n))$."
    * "To map a virtual index back to 2D coordinates, I use `row = index / columnCount` and `col = index % columnCount`."
    * "This is more efficient than first searching for the row and then the column, though both result in the same asymptotic complexity."
* **Pseudocode:**
    1. `rowCount = matrix.length`, `columnCount = matrix[0].length`.
    2. `leftIndex = 0`, `rightIndex = (rowCount * columnCount) - 1`.
    3. Perform standard binary search on this virtual range.
    4. Convert `middleIndex` to `row` and `col` to access the value.
* **JavaScript Solution:**
```javascript
const searchMatrix = (matrix, targetValue) => {
  const rowCount = matrix.length;
  const columnCount = matrix[0].length;
  let leftIndex = 0;
  let rightIndex = (rowCount * columnCount) - 1;

  while (leftIndex <= rightIndex) {
    const middleIndex = Math.floor(leftIndex + (rightIndex - leftIndex) / 2);
    const currentRow = Math.floor(middleIndex / columnCount);
    const currentColumn = middleIndex % columnCount;
    const middleValue = matrix[currentRow][currentColumn];

    if (middleValue === targetValue) {
      return true;
    } else if (middleValue < targetValue) {
      leftIndex = middleIndex + 1;
    } else {
      rightIndex = middleIndex - 1;
    }
  }

  return false;
};
```
* **Dry Run (matrix = [[1, 3]], target = 3):**
    * `row` = 1, `col` = 2. `left` = 0, `right` = 1.
    * `mid` = 0. `val` = matrix[0][0] = 1. 1 < 3, `left` = 1.
    * `mid` = 1. `val` = matrix[0][1] = 3. Match!
* **Alternative Approach:** One could use binary search on the first column to find the row, then binary search within that row.

---

## 3. Koko Eating Bananas
[https://leetcode.com/problems/koko-eating-bananas/](https://leetcode.com/problems/koko-eating-bananas/)

* **Complexity:** Time: $O(n \log(\max(piles)))$ | Space: $O(1)$
* **Key Intuition:** We are binary searching for the *answer* (speed $K$) rather than an element in an array. The range of $K$ is monotonic: if she can finish at speed $X$, she can definitely finish at speed $X+1$.
* **The 'Talk Track':**
    * "The search space for the eating speed $K$ ranges from 1 to the largest pile size."
    * "For each candidate speed in our binary search, I'll calculate the total hours required to consume all piles."
    * "I'm looking for the minimum $K$ such that `totalHours <= h`, which makes this a 'Find First Occurrence' style binary search."
* **Pseudocode:**
    1. `lowSpeed = 1`, `highSpeed = max(piles)`.
    2. While `lowSpeed < highSpeed`:
       a. `midSpeed = (low + high) / 2`.
       b. Calculate `hoursSpent` by summing `Math.ceil(pile / midSpeed)` for all piles.
       c. If `hoursSpent <= h`, `highSpeed = midSpeed` (this might be the answer).
       d. Else, `lowSpeed = midSpeed + 1`.
    3. Return `lowSpeed`.
* **JavaScript Solution:**
```javascript
const minEatingSpeed = (piles, availableHours) => {
  let lowSpeed = 1;
  let highSpeed = Math.max(...piles);

  while (lowSpeed < highSpeed) {
    const candidateSpeed = Math.floor(lowSpeed + (highSpeed - lowSpeed) / 2);
    let totalHoursSpent = 0;

    for (const pileSize of piles) {
      totalHoursSpent += Math.ceil(pileSize / candidateSpeed);
    }

    if (totalHoursSpent <= availableHours) {
      highSpeed = candidateSpeed;
    } else {
      lowSpeed = candidateSpeed + 1;
    }
  }

  return lowSpeed;
};
```
* **Dry Run (piles = [3, 6], h = 3):**
    * `low` = 1, `high` = 6. `mid` = 3.
    * Hours: `ceil(3/3) + ceil(6/3)` = 1 + 2 = 3. 3 <= 3, `high` = 3.
    * `low` = 1, `high` = 3. `mid` = 2.
    * Hours: `ceil(3/2) + ceil(6/2)` = 2 + 3 = 5. 5 > 3, `low` = 3.
    * Loop ends. Return 3.
* **Alternative Approach:** We could check every speed from 1 upwards, but that would be $O(n \cdot \max(piles))$, which is inefficient.

---

## 4. Search in Rotated Sorted Array
[https://leetcode.com/problems/search-in-rotated-sorted-array/](https://leetcode.com/problems/search-in-rotated-sorted-array/)

* **Complexity:** Time: $O(\log n)$ | Space: $O(1)$
* **Key Intuition:** In any rotation of a sorted array, at least one half (left or right) of the `middleIndex` must be perfectly sorted. We identify the sorted half and check if the target lies within it.
* **The 'Talk Track':**
    * "Even with a rotation, binary search is possible because one side of the pivot is always monotonic."
    * "I'll first check if the left side `[leftIndex...middleIndex]` is sorted by comparing the boundary values."
    * "If the target is within the sorted side's range, I'll search there; otherwise, I'll jump to the unsorted side."
* **Pseudocode:**
    1. Standard `leftIndex`, `rightIndex` setup.
    2. Check if `nums[leftIndex] <= nums[middleIndex]` (Left half is sorted).
       a. If target is between `nums[leftIndex]` and `nums[middleIndex]`, search left.
       b. Else, search right.
    3. Else (Right half is sorted):
       a. If target is between `nums[middleIndex]` and `nums[rightIndex]`, search right.
       b. Else, search left.
* **JavaScript Solution:**
```javascript
const searchRotated = (nums, targetValue) => {
  let leftIndex = 0;
  let rightIndex = nums.length - 1;

  while (leftIndex <= rightIndex) {
    const middleIndex = Math.floor(leftIndex + (rightIndex - leftIndex) / 2);

    if (nums[middleIndex] === targetValue) return middleIndex;

    // Left half is sorted
    if (nums[leftIndex] <= nums[middleIndex]) {
      if (targetValue >= nums[leftIndex] && targetValue < nums[middleIndex]) {
        rightIndex = middleIndex - 1;
      } else {
        leftIndex = middleIndex + 1;
      }
    } 
    // Right half is sorted
    else {
      if (targetValue > nums[middleIndex] && targetValue <= nums[rightIndex]) {
        leftIndex = middleIndex + 1;
      } else {
        rightIndex = middleIndex - 1;
      }
    }
  }

  return -1;
};
```
* **Dry Run (input = [3, 1], target = 1):**
    * `left` = 0, `right` = 1. `mid` = 0. `val` = 3.
    * Left side [3] is sorted (`nums[0] <= nums[0]`).
    * Target 1 is not between 3 and 3. `left` = 1.
    * `mid` = 1. `val` = 1. Match!
* **Alternative Approach:** We could find the pivot index first and then perform binary search on the appropriate half.

---

## 5. Find Minimum in Rotated Sorted Array
[https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

* **Complexity:** Time: $O(\log n)$ | Space: $O(1)$
* **Key Intuition:** We compare `nums[middleIndex]` to `nums[rightIndex]`. If the middle value is greater than the rightmost value, the minimum must be in the right half because the rotation point is there.
* **The 'Talk Track':**
    * "The minimum element is the only element that is smaller than its predecessor, marking the rotation point."
    * "If `nums[middleIndex] > nums[rightIndex]`, it implies the array is 'broken' in the right half, so the minimum is there."
    * "Otherwise, the minimum is either at `middleIndex` or to its left."
* **Pseudocode:**
    1. `leftIndex = 0`, `rightIndex = length - 1`.
    2. While `leftIndex < rightIndex`:
       a. If `nums[middleIndex] > nums[rightIndex]`, the minimum is in the right: `leftIndex = middleIndex + 1`.
       b. Else, the minimum is in the left: `rightIndex = middleIndex`.
    3. Return `nums[leftIndex]`.
* **JavaScript Solution:**
```javascript
const findMin = (nums) => {
  let leftIndex = 0;
  let rightIndex = nums.length - 1;

  while (leftIndex < rightIndex) {
    const middleIndex = Math.floor(leftIndex + (rightIndex - leftIndex) / 2);

    if (nums[middleIndex] > nums[rightIndex]) {
      leftIndex = middleIndex + 1;
    } else {
      rightIndex = middleIndex;
    }
  }

  return nums[leftIndex];
};
```
* **Dry Run (input = [2, 3, 1]):**
    * `left` = 0, `right` = 2. `mid` = 1. `val` = 3.
    * 3 > 1, so `left` = 2.
    * Loop ends. Return `nums[2]` which is 1.
* **Alternative Approach:** Sorting the array would find the minimum but would be $O(n \log n)$.

---

## 6. Time Based Key-Value Store
[https://leetcode.com/problems/time-based-key-value-store/](https://leetcode.com/problems/time-based-key-value-store/)

* **Complexity:** Time: $O(\log n)$ per `get`, $O(1)$ per `set` | Space: $O(n)$
* **Key Intuition:** Since timestamps are added in strictly increasing order, we can store values in an array per key and use binary search to find the largest timestamp $\le$ the requested one.
* **The 'Talk Track':**
    * "I'll use a Hash Map where keys map to an array of `[timestamp, value]` pairs."
    * "Since the `set` operations are strictly increasing, each array is naturally sorted by timestamp."
    * "For the `get` operation, I'll perform a binary search to find the 'rightmost' element whose timestamp is less than or equal to the target."
* **Pseudocode:**
    1. `set`: Push `[timestamp, value]` to the map's list for that key.
    2. `get`:
       a. Retrieve list. Perform binary search for `targetTimestamp`.
       b. If `currentTimestamp <= target`, update `result` and search right.
       c. Else, search left.
* **JavaScript Solution:**
```javascript
class TimeMap {
  constructor() {
    this.storeMap = new Map();
  }

  set(keyLabel, stringValue, timestampValue) {
    if (!this.storeMap.has(keyLabel)) {
      this.storeMap.set(keyLabel, []);
    }
    this.storeMap.get(keyLabel).push([timestampValue, stringValue]);
  }

  get(keyLabel, timestampValue) {
    const recordHistory = this.storeMap.get(keyLabel) || [];
    let latestValidValue = "";
    let leftIndex = 0;
    let rightIndex = recordHistory.length - 1;

    while (leftIndex <= rightIndex) {
      const middleIndex = Math.floor(leftIndex + (rightIndex - leftIndex) / 2);
      const [currentTimestamp, currentValue] = recordHistory[middleIndex];

      if (currentTimestamp <= timestampValue) {
        latestValidValue = currentValue;
        leftIndex = middleIndex + 1;
      } else {
        rightIndex = middleIndex - 1;
      }
    }

    return latestValidValue;
  }
}
```
* **Dry Run (set: "foo", "bar", 1; get: "foo", 1):**
    * `left` = 0, `right` = 0. `mid` = 0.
    * 1 <= 1. `latest` = "bar", `left` = 1.
    * Loop ends. Return "bar".
* **Alternative Approach:** Linear search would be $O(n)$, failing performance requirements for high-frequency stores.

---

## 7. [Latest 2026] Most Beautiful Item for Each Query
[https://leetcode.com/problems/most-beautiful-item-for-each-query/](https://leetcode.com/problems/most-beautiful-item-for-each-query/)

* **Complexity:** Time: $O((n+q) \log n)$ | Space: $O(n)$
* **Key Intuition:** We sort items by price and pre-process them to store the maximum beauty seen so far at or below each price. We then binary search for each query price.
* **The 'Talk Track':**
    * "After sorting by price, I'll update each item's beauty to be the `max(currentBeauty, previousBeauty)` to ensure the beauty values are non-decreasing."
    * "This transformation allows me to use binary search on the prices to find the most expensive item we can afford."
    * "The pre-processing step turns this into a standard 'Find Rightmost Index' binary search problem."
* **Pseudocode:**
    1. Sort `items` by price.
    2. Iterate through items: `items[i].beauty = max(items[i].beauty, items[i-1].beauty)`.
    3. For each `queryPrice`:
       a. Binary search for the largest price $\le$ `queryPrice`.
       b. Append the beauty of that item to result.
* **JavaScript Solution:**
```javascript
const maximumBeauty = (itemsCollection, queries) => {
  itemsCollection.sort((itemA, itemB) => itemA[0] - itemB[0]);

  let maximumBeautySeen = 0;
  for (let currentIndex = 0; currentIndex < itemsCollection.length; currentIndex++) {
    maximumBeautySeen = Math.max(maximumBeautySeen, itemsCollection[currentIndex][1]);
    itemsCollection[currentIndex][1] = maximumBeautySeen;
  }

  const resultBeautyList = [];
  for (const queryPriceLimit of queries) {
    let bestBeauty = 0;
    let leftIndex = 0;
    let rightIndex = itemsCollection.length - 1;

    while (leftIndex <= rightIndex) {
      const middleIndex = Math.floor(leftIndex + (rightIndex - leftIndex) / 2);
      if (itemsCollection[middleIndex][0] <= queryPriceLimit) {
        bestBeauty = itemsCollection[middleIndex][1];
        leftIndex = middleIndex + 1;
      } else {
        rightIndex = middleIndex - 1;
      }
    }
    resultBeautyList.push(bestBeauty);
  }

  return resultBeautyList;
};
```
* **Dry Run (items=[[1,2]], queries=[2]):**
    * Pre-process: item[0] beauty = 2.
    * Binary search for 2: `mid` index 0. Price 1 <= 2. `best` = 2.
    * Result: [2].
* **Alternative Approach:** Sorting queries and using a two-pointer approach would also be $O(n \log n + q \log q)$.

---

## 8. [Latest 2026] Minimum Possible Maximum Value of Array
[https://leetcode.com/problems/minimize-maximum-of-array/](https://leetcode.com/problems/minimize-maximum-of-array/)

* **Complexity:** Time: $O(n \log(\max(nums)))$ | Space: $O(1)$
* **Key Intuition:** We binary search for the minimum "maximum value" possible. For a candidate value $X$, we check if we can redistribute the array values from right to left such that no element exceeds $X$.
* **The 'Talk Track':**
    * "The core challenge is that we can only move 'value' from index $i$ to $i-1$."
    * "By binary searching the answer space, we can verify if a target value is feasible by checking if the prefix sums exceed the total allowed capacity (prefix length $\times$ target)."
    * "This follows the 'Binary Search on Answer' pattern where we check a property over the entire array."
* **Pseudocode:**
    1. `low = nums[0]`, `high = max(nums)`.
    2. `check(mid)`: Iterate through array, if `totalSum > mid * (i + 1)`, return false.
    3. Standard binary search to find the minimum `mid` that returns true.
* **JavaScript Solution:**
```javascript
const minimizeArrayValue = (nums) => {
  let lowCandidate = 0;
  let highCandidate = Math.max(...nums);
  let resultMinimumMax = highCandidate;

  const isFeasible = (targetMaximum) => {
    let accumulatedPrefixSum = 0;
    for (let currentIndex = 0; currentIndex < nums.length; currentIndex++) {
      accumulatedPrefixSum += nums[currentIndex];
      if (accumulatedPrefixSum > targetMaximum * (currentIndex + 1)) {
        return false;
      }
    }
    return true;
  };

  while (lowCandidate <= highCandidate) {
    const middleCandidate = Math.floor(lowCandidate + (highCandidate - lowCandidate) / 2);
    if (isFeasible(middleCandidate)) {
      resultMinimumMax = middleCandidate;
      highCandidate = middleCandidate - 1;
    } else {
      lowCandidate = middleCandidate + 1;
    }
  }

  return resultMinimumMax;
};
```
* **Dry Run (nums = [3, 7]):**
    * `low` = 0, `high` = 7. `mid` = 3.
    * Check 3: sum at index 1 is 10. 10 > 3 * 2 (6). False.
    * `low` = 4, `high` = 7. `mid` = 5.
    * Check 5: index 0 (3 <= 5), index 1 (10 <= 10). True.
    * Result: 5.
* **Alternative Approach:** This can be solved in $O(n)$ by calculating prefix sums and finding the maximum of `ceil(prefixSum / (i + 1))`.

---
