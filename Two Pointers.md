# Senior Engineer's Study Guide: Two Pointers

The **Two Pointers** pattern is a classic optimization technique used on linear data structures (arrays, strings, linked lists). By maintaining two indices that move toward each other or in the same direction, we can often reduce $O(n^2)$ nested loops into a single $O(n)$ pass.

---

## 1. Valid Palindrome
[https://leetcode.com/problems/valid-palindrome/](https://leetcode.com/problems/valid-palindrome/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Use two pointers starting at opposite ends; as they move inward, every character must match after filtering non-alphanumeric values.
* **The 'Talk Track':**
    * "I'll use a `while` loop with pointers at the `left` and `right` boundaries to avoid the overhead of creating new strings or arrays."
    * "I'll implement a helper or a regex check to skip non-alphanumeric characters on the fly, keeping space complexity at $O(1)$."
    * "This is more efficient than reversing the string, which would require $O(n)$ extra space."
* **Pseudocode:**
    1. Initialize `leftIndex = 0` and `rightIndex = string.length - 1`.
    2. While `leftIndex < rightIndex`:
       a. If `leftIndex` is not alphanumeric, increment `leftIndex`.
       b. Else if `rightIndex` is not alphanumeric, decrement `rightIndex`.
       c. Else if characters don't match (case-insensitive), return `false`.
       d. Else, move both pointers inward.
    3. Return `true`.
* **JavaScript Solution:**
```javascript
const isPalindrome = (inputString) => {
  let leftIndex = 0;
  let rightIndex = inputString.length - 1;

  const isAlphanumeric = (char) => /[a-z0-9]/i.test(char);

  while (leftIndex < rightIndex) {
    if (!isAlphanumeric(inputString[leftIndex])) {
      leftIndex++;
    } else if (!isAlphanumeric(inputString[rightIndex])) {
      rightIndex--;
    } else {
      if (inputString[leftIndex].toLowerCase() !== inputString[rightIndex].toLowerCase()) {
        return false;
      }
      leftIndex++;
      rightIndex--;
    }
  }
  return true;
};
```
* **Dry Run (inputString = "A man, a plan, a canal: Panama"):**
    * `left` starts at 'A', `right` starts at 'a'. Match.
    * `left` hits ' ', skips. `right` hits 'a', waits.
    * Continues until pointers meet. Return `true`.
* **Alternative Approach:** We could use `inputString.replace(/[^a-z0-9]/gi, '').split('').reverse().join('')`, but that uses $O(n)$ space.

---

## 2. Two Sum II - Input Array Is Sorted
[https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Since the array is sorted, if the sum is too small, we *must* move the left pointer right; if it's too large, we *must* move the right pointer left.
* **The 'Talk Track':**
    * "Because the input is sorted, I can leverage the Two-Pointer approach to achieve $O(1)$ space, unlike the Hash Map approach for the unsorted version."
    * "The logic is greedy: increasing the left pointer always increases the sum, and decreasing the right always decreases it."
    * "I'll return 1-based indices as per the problem requirement."
* **Pseudocode:**
    1. `leftIndex = 0`, `rightIndex = numbers.length - 1`.
    2. While `leftIndex < rightIndex`:
       a. `currentSum = numbers[leftIndex] + numbers[rightIndex]`.
       b. If `currentSum === target`, return `[leftIndex + 1, rightIndex + 1]`.
       c. If `currentSum < target`, `leftIndex++`.
       d. Else, `rightIndex--`.
* **JavaScript Solution:**
```javascript
const twoSumSorted = (numbers, target) => {
  let leftIndex = 0;
  let rightIndex = numbers.length - 1;

  while (leftIndex < rightIndex) {
    const currentSum = numbers[leftIndex] + numbers[rightIndex];
    if (currentSum === target) {
      return [leftIndex + 1, rightIndex + 1];
    } else if (currentSum < target) {
      leftIndex++;
    } else {
      rightIndex--;
    }
  }
};
```
* **Dry Run (numbers = [1, 2], target = 3):**
    * `left` = 0 (val 1), `right` = 1 (val 2).
    * `sum` = 3. `sum === target`. Return `[1, 2]`.
* **Alternative Approach:** We could use binary search for each element $O(n \log n)$, but Two Pointers is faster at $O(n)$.

---

## 3. 3Sum
[https://leetcode.com/problems/3sum/](https://leetcode.com/problems/3sum/)

* **Complexity:** Time: $O(n^2)$ | Space: $O(1)$ or $O(n)$ (depending on sorting implementation)
* **Key Intuition:** Sort the array, then iterate through each number and solve the "Two Sum" problem for the remainder of the array using Two Pointers.
* **The 'Talk Track':**
    * "Sorting is essential here to handle duplicates and enable the Two-Pointer logic."
    * "I'll skip duplicate values for the outer loop and the inner pointers to ensure the result set contains only unique triplets."
    * "By fixing one number $i$, we reduce the problem to finding $j$ and $k$ such that $nums[j] + nums[k] = -nums[i]$."
* **Pseudocode:**
    1. Sort `nums`.
    2. Loop `i` from 0 to `nums.length - 1`:
       a. If `nums[i] > 0`, break (no more possible triplets).
       b. If `nums[i] === nums[i-1]`, skip (avoid duplicates).
       c. Set `left = i + 1`, `right = nums.length - 1`.
       d. While `left < right`: logic similar to Two Sum II.
* **JavaScript Solution:**
```javascript
const threeSum = (nums) => {
  const result = [];
  nums.sort((a, b) => a - b);

  for (let i = 0; i < nums.length - 2; i++) {
    if (nums[i] > 0) break;
    if (i > 0 && nums[i] === nums[i - 1]) continue;

    let left = i + 1;
    let right = nums.length - 1;

    while (left < right) {
      const sum = nums[i] + nums[left] + nums[right];
      if (sum === 0) {
        result.push([nums[i], nums[left], nums[right]]);
        while (nums[left] === nums[left + 1]) left++;
        while (nums[right] === nums[right - 1]) right--;
        left++;
        right--;
      } else if (sum < 0) {
        left++;
      } else {
        right--;
      }
    }
  }
  return result;
};
```
* **Dry Run (nums = [-1, 0, 1]):**
    * Sorted: `[-1, 0, 1]`. `i` = 0 (val -1).
    * `left` = 1 (val 0), `right` = 2 (val 1). Sum = 0.
    * Push `[-1, 0, 1]`.
* **Alternative Approach:** Using a Hash Map for the inner loop is possible, but managing triplets' uniqueness becomes much harder.

---

## 4. Container With Most Water
[https://leetcode.com/problems/container-with-most-water/](https://leetcode.com/problems/container-with-most-water/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** The area is limited by the shorter bar. To find a larger area, we *must* move the pointer pointing to the shorter bar inward.
* **The 'Talk Track':**
    * "The width always decreases as we move pointers inward, so we only gain area if we find a significantly taller bar."
    * "I'll use a greedy approach: always move the pointer that is currently the 'bottleneck'."
    * "I'll track the `maxArea` seen so far and return it at the end."
* **Pseudocode:**
    1. `left = 0`, `right = height.length - 1`, `maxArea = 0`.
    2. While `left < right`:
       a. `currentArea = min(height[left], height[right]) * (right - left)`.
       b. Update `maxArea`.
       c. If `height[left] < height[right]`, `left++`, else `right--`.
* **JavaScript Solution:**
```javascript
const maxArea = (height) => {
  let left = 0;
  let right = height.length - 1;
  let maxAreaValue = 0;

  while (left < right) {
    const currentWidth = right - left;
    const currentHeight = Math.min(height[left], height[right]);
    maxAreaValue = Math.max(maxAreaValue, currentWidth * currentHeight);

    if (height[left] < height[right]) {
      left++;
    } else {
      right--;
    }
  }
  return maxAreaValue;
};
```
* **Dry Run (height = [1, 2]):**
    * `left` = 0 (1), `right` = 1 (2). Width = 1.
    * `Area` = 1 * 1 = 1.
    * `height[0] < height[1]`, so `left++`. Loop ends.
* **Alternative Approach:** A brute force $O(n^2)$ would check every pair of bars, but it would time out on large inputs.

---

## 5. [Latest 2026] Trap Rain Water (Two Pointer Variant)
[https://leetcode.com/problems/trapping-rain-water/](https://leetcode.com/problems/trapping-rain-water/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Water trapped at any point is determined by the minimum of the maximum heights to its left and right, minus its own height.
* **The 'Talk Track':**
    * "I'll maintain `leftMax` and `rightMax` variables to track the highest walls seen from either side."
    * "By moving the pointer with the smaller 'max' wall, I can guarantee how much water is trapped at that index without knowing the other side's full profile."
    * "This constant-space solution is the gold standard for this problem."
* **Pseudocode:**
    1. `left = 0, right = n-1, leftMax = 0, rightMax = 0, total = 0`.
    2. While `left < right`:
       a. If `height[left] < height[right]`:
          i. If `height[left] >= leftMax`, update `leftMax`.
          ii. Else, `total += leftMax - height[left]`.
          iii. `left++`.
       b. Else: (Same logic for `right` side).
* **JavaScript Solution:**
```javascript
const trap = (height) => {
  let left = 0;
  let right = height.length - 1;
  let leftMax = 0;
  let rightMax = 0;
  let result = 0;

  while (left < right) {
    if (height[left] < height[right]) {
      if (height[left] >= leftMax) {
        leftMax = height[left];
      } else {
        result += leftMax - height[left];
      }
      left++;
    } else {
      if (height[right] >= rightMax) {
        rightMax = height[right];
      } else {
        result += rightMax - height[right];
      }
      right--;
    }
  }
  return result;
};
```
* **Dry Run (height = [0, 1, 0, 2]):**
    * `left` 0: `leftMax` = 0.
    * `left` 1: `leftMax` = 1.
    * `left` 2: `height[2]` (0) < `leftMax` (1). `result` += 1.
* **Alternative Approach:** We could use a Stack ($O(n)$ space) or Pre-compute prefix/suffix maximums ($O(n)$ space).

---

## 6. [Latest 2026] Append Characters to String to Make Subsequence
[https://leetcode.com/problems/append-characters-to-string-to-make-subsequence/](https://leetcode.com/problems/append-characters-to-string-to-make-subsequence/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Use two pointers to find the longest prefix of `target` that already exists as a subsequence in `source`.
* **The 'Talk Track':**
    * "I'll iterate through `source` and try to match as many characters of `target` as possible in order."
    * "The number of characters left in `target` after the loop is the minimum number of characters we need to append."
    * "This is a greedy two-pointer strategy where one pointer always advances and the other only advances on a match."
* **Pseudocode:**
    1. `sourcePtr = 0`, `targetPtr = 0`.
    2. While `sourcePtr < source.length` and `targetPtr < target.length`:
       a. If characters match, `targetPtr++`.
       b. `sourcePtr++`.
    3. Return `target.length - targetPtr`.
* **JavaScript Solution:**
```javascript
const appendCharacters = (source, target) => {
  let sourcePtr = 0;
  let targetPtr = 0;
  const sourceLength = source.length;
  const targetLength = target.length;

  while (sourcePtr < sourceLength && targetPtr < targetLength) {
    if (source[sourcePtr] === target[targetPtr]) {
      targetPtr++;
    }
    sourcePtr++;
  }

  return targetLength - targetPtr;
};
```
* **Dry Run (source = "abc", target = "abcd"):**
    * Loop finds "a", "b", "c". `targetPtr` reaches 3.
    * Loop ends. Return `4 - 3 = 1`.
* **Alternative Approach:** There isn't a significantly better way; this is the optimal $O(n)$ approach.
