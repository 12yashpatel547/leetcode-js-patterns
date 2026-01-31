# Senior Engineer's Study Guide: Math & Geometry (Matrix) Patterns

The **Math & Geometry** algorithmic category focuses on numerical logic, spatial manipulation, and coordinate systems. In a senior-level interview, the expectation is not just finding the answer, but handling edge cases (like integer overflow in other languages, or floating point precision), optimizing for in-place modifications to save space, and demonstrating a deep understanding of cyclical detection (Floyd's Cycle-Finding).

---

## 1. Happy Number
[https://leetcode.com/problems/happy-number/](https://leetcode.com/problems/happy-number/)

* **Complexity:** Time: $O(\log n)$ | Space: $O(1)$ (using Floyd's) or $O(\log n)$ (using a Set)
* **Key Intuition:** A number is "unhappy" if the process of squaring digits enters a cycle. We can detect this cycle using a Hash Set or the "Tortoise and Hare" (Floyd's) algorithm.
* **The 'Talk Track':**
    * "I recognize this as a cycle detection problem; if the sequence doesn't reach 1, it will eventually repeat a previously seen number."
    * "I'll use Floyd's Cycle-Finding algorithm to keep space at $O(1)$, treating the sequence as a linked list where the 'next' node is the sum of squared digits."
    * "I'll abstract the digit-squaring logic into a helper function for better readability and maintainability."
* **Pseudocode:**
    1. Define helper `getSumOfSquares(num)`.
    2. Initialize `slowPointer = n`, `fastPointer = getSumOfSquares(n)`.
    3. While `fastPointer` is not 1 and `slowPointer` is not `fastPointer`:
       a. Advance `slowPointer` once.
       b. Advance `fastPointer` twice.
    4. Return `true` if `fastPointer` reached 1.
* **JavaScript Solution:**
```javascript
const isHappy = (inputNumber) => {
  const getSumOfSquares = (num) => {
    let totalSum = 0;
    while (num > 0) {
      const digit = num % 10;
      totalSum += digit * digit;
      num = Math.floor(num / 10);
    }
    return totalSum;
  };

  let slowPointer = inputNumber;
  let fastPointer = getSumOfSquares(inputNumber);

  while (fastPointer !== 1 && slowPointer !== fastPointer) {
    slowPointer = getSumOfSquares(slowPointer);
    fastPointer = getSumOfSquares(getSumOfSquares(fastPointer));
  }

  return fastPointer === 1;
};
```
* **Dry Run (inputNumber = 7):**
    * `slow` = 7, `fast` = 49.
    * Loop 1: `slow` = 49, `fast` = 97 -> 130 -> 10.
    * Loop 2: `slow` = 97, `fast` = 1 -> 1.
    * `fast` is 1. Return `true`.
* **Alternative Approach:** We could use a `Set` to store every result and return `false` if we encounter a number already in the `Set`.

---

## 2. Plus One
[https://leetcode.com/problems/plus-one/](https://leetcode.com/problems/plus-one/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$ (ignoring output)
* **Key Intuition:** We iterate backwards; if a digit is 9, it becomes 0 and carries over. If we reach the front and still have a carry, we prepend a 1.
* **The 'Talk Track':**
    * "I'll iterate from the end of the array, which is the least significant digit."
    * "The moment I find a digit less than 9, I can increment it and return immediately, as there's no more carry."
    * "If the loop finishes, it means the number was all nines (e.g., 999), so I'll create a new array starting with 1."
* **Pseudocode:**
    1. Loop from `digits.length - 1` down to 0.
    2. If `digits[currentIndex] < 9`:
       a. Increment `digits[currentIndex]`.
       b. Return `digits`.
    3. Set `digits[currentIndex] = 0`.
    4. If loop completes, return `[1, ...digits]`.
* **JavaScript Solution:**
```javascript
const plusOne = (digits) => {
  for (let currentIndex = digits.length - 1; currentIndex >= 0; currentIndex--) {
    if (digits[currentIndex] < 9) {
      digits[currentIndex]++;
      return digits;
    }
    digits[currentIndex] = 0;
  }

  return [1, ...digits];
};
```
* **Dry Run (digits = [1, 9]):**
    * `i` = 1: `digits[1]` is 9. Set to 0.
    * `i` = 0: `digits[0]` is 1. Increment to 2. Return [2, 0].
* **Alternative Approach:** We could use BigInt for conversion, but that is less efficient for extremely large arrays.

---

## 3. Rotate Image
[https://leetcode.com/problems/rotate-image/](https://leetcode.com/problems/rotate-image/)

* **Complexity:** Time: $O(n^2)$ | Space: $O(1)$
* **Key Intuition:** A 90-degree rotation is equivalent to a matrix **Transpose** followed by a **Reverse** of each row.
* **The 'Talk Track':**
    * "To perform this in-place, I'll first swap elements across the main diagonal (transpose)."
    * "Then, I'll reverse each row. This linear algebra property effectively rotates the matrix 90 degrees clockwise."
    * "This avoids the $O(n^2)$ space complexity of creating a new matrix."
* **Pseudocode:**
    1. Transpose: Swap `matrix[row][col]` with `matrix[col][row]` for all `row < col`.
    2. Reverse: For each row in `matrix`, call `row.reverse()`.
* **JavaScript Solution:**
```javascript
const rotate = (matrix) => {
  const matrixSize = matrix.length;

  // Step 1: Transpose
  for (let rowIndex = 0; rowIndex < matrixSize; rowIndex++) {
    for (let columnIndex = rowIndex + 1; columnIndex < matrixSize; columnIndex++) {
      const temporaryValue = matrix[rowIndex][columnIndex];
      matrix[rowIndex][columnIndex] = matrix[columnIndex][rowIndex];
      matrix[columnIndex][rowIndex] = temporaryValue;
    }
  }

  // Step 2: Reverse each row
  for (let rowIndex = 0; rowIndex < matrixSize; rowIndex++) {
    matrix[rowIndex].reverse();
  }
};
```
* **Dry Run (matrix = [[1,2],[3,4]]):**
    * Transpose: [[1,3],[2,4]].
    * Reverse rows: [[3,1],[4,2]]. Correct.
* **Alternative Approach:** We could rotate in rings/layers, but the transpose + reverse method is much easier to implement and less error-prone.

---

## 4. Spiral Matrix
[https://leetcode.com/problems/spiral-matrix/](https://leetcode.com/problems/spiral-matrix/)

* **Complexity:** Time: $O(m \cdot n)$ | Space: $O(1)$ (ignoring output)
* **Key Intuition:** Maintain four boundaries (top, bottom, left, right) and shrink them as we traverse the matrix in a specific order.
* **The 'Talk Track':**
    * "I'll define boundaries for the perimeter and move inward after each full cycle."
    * "I must add safety checks inside the loop after moving the boundaries to ensure I don't re-traverse rows or columns in uneven matrices."
    * "This is a simulation problem where boundary management is the primary challenge."
* **Pseudocode:**
    1. Initialize `topRow = 0, bottomRow = m-1, leftCol = 0, rightCol = n-1`.
    2. While boundaries haven't crossed:
       a. Traverse `leftCol` to `rightCol` at `topRow`. Increment `topRow`.
       b. Traverse `topRow` to `bottomRow` at `rightCol`. Decrement `rightCol`.
       c. (Check if `top <= bottom`) Traverse `rightCol` to `leftCol` at `bottomRow`. Decrement `bottomRow`.
       d. (Check if `left <= right`) Traverse `bottomRow` to `topRow` at `leftCol`. Increment `leftCol`.
* **JavaScript Solution:**
```javascript
const spiralOrder = (matrix) => {
  const result = [];
  if (matrix.length === 0) return result;

  let topBoundary = 0;
  let bottomBoundary = matrix.length - 1;
  let leftBoundary = 0;
  let rightBoundary = matrix[0].length - 1;

  while (topBoundary <= bottomBoundary && leftBoundary <= rightBoundary) {
    // 1. Move Right
    for (let col = leftBoundary; col <= rightBoundary; col++) {
      result.push(matrix[topBoundary][col]);
    }
    topBoundary++;

    // 2. Move Down
    for (let row = topBoundary; row <= bottomBoundary; row++) {
      result.push(matrix[row][rightBoundary]);
    }
    rightBoundary--;

    // 3. Move Left (Safety check)
    if (topBoundary <= bottomBoundary) {
      for (let col = rightBoundary; col >= leftBoundary; col--) {
        result.push(matrix[bottomBoundary][col]);
      }
      bottomBoundary--;
    }

    // 4. Move Up (Safety check)
    if (leftBoundary <= rightBoundary) {
      for (let row = bottomBoundary; row >= topBoundary; row--) {
        result.push(matrix[row][leftBoundary]);
      }
      leftBoundary++;
    }
  }

  return result;
};
```
* **Dry Run (matrix = [[1, 2], [3, 4]]):**
    * Right: [1, 2]. `top` = 1.
    * Down: [4]. `right` = 0.
    * Left: [3]. `bottom` = 0.
    * Result: [1, 2, 4, 3].
* **Alternative Approach:** We could keep a "seen" matrix of booleans and a direction vector, but it uses $O(m \cdot n)$ extra space.

---

## 5. Set Matrix Zeroes
[https://leetcode.com/problems/set-matrix-zeroes/](https://leetcode.com/problems/set-matrix-zeroes/)

* **Complexity:** Time: $O(m \cdot n)$ | Space: $O(1)$
* **Key Intuition:** Use the first row and first column of the matrix itself as flags to store which rows/columns should be zeroed.
* **The 'Talk Track':**
    * "To achieve $O(1)$ space, I'll use the matrix's header row and column to track zeros."
    * "I need to handle the overlap at `matrix[0][0]` carefully by using an additional variable to track if the first column needs zeroing."
    * "I'll process the inner matrix first using these flags, and finally handle the first row and column separately."
* **Pseudocode:**
    1. Use `isFirstColZero` boolean.
    2. Check if first column has any zeros.
    3. Iterate through rest of matrix: if `matrix[i][j] === 0`, set `matrix[i][0]` and `matrix[0][j]` to 0.
    4. Iterate matrix again (starting at index 1) and set zeros based on flags.
    5. Finalize by zeroing first row/column if needed.
* **JavaScript Solution:**
```javascript
const setZeroes = (matrix) => {
  const rowCount = matrix.length;
  const colCount = matrix[0].length;
  let shouldFirstColumnBeZero = false;

  for (let rowIndex = 0; rowIndex < rowCount; rowIndex++) {
    if (matrix[rowIndex][0] === 0) shouldFirstColumnBeZero = true;
    for (let columnIndex = 1; columnIndex < colCount; columnIndex++) {
      if (matrix[rowIndex][columnIndex] === 0) {
        matrix[rowIndex][0] = 0;
        matrix[0][columnIndex] = 0;
      }
    }
  }

  for (let rowIndex = 1; rowIndex < rowCount; rowIndex++) {
    for (let columnIndex = 1; columnIndex < colCount; columnIndex++) {
      if (matrix[rowIndex][0] === 0 || matrix[0][columnIndex] === 0) {
        matrix[rowIndex][columnIndex] = 0;
      }
    }
  }

  if (matrix[0][0] === 0) {
    for (let columnIndex = 0; columnIndex < colCount; columnIndex++) {
      matrix[0][columnIndex] = 0;
    }
  }

  if (shouldFirstColumnBeZero) {
    for (let rowIndex = 0; rowIndex < rowCount; rowIndex++) {
      matrix[rowIndex][0] = 0;
    }
  }
};
```
* **Dry Run (matrix = [[1,0],[1,1]]):**
    * `matrix[0][1]` is 0 -> `matrix[0][0]`=0, `matrix[0][1]`=0.
    * Inner loop: `matrix[1][1]` stays 1.
    * `matrix[0][0]` is 0 -> first row becomes zero.
    * Result: [[0,0],[1,0]].
* **Alternative Approach:** We could use two separate arrays (one for rows, one for columns) which would be $O(m + n)$ space.

---

## 6. Pow(x, n)
[https://leetcode.com/problems/powx-n/](https://leetcode.com/problems/powx-n/)

* **Complexity:** Time: $O(\log n)$ | Space: $O(\log n)$ (recursion stack)
* **Key Intuition:** We use **Binary Exponentiation**; if we know $x^{n/2}$, then $x^n$ is just $(x^{n/2})^2$.
* **The 'Talk Track':**
    * "I'll use a divide-and-conquer strategy to cut the power in half at each step."
    * "I must handle negative exponents by setting $x = 1/x$ and $n = -n$."
    * "By calculating the half-power once and storing it, I reduce the number of multiplications from $n$ to $\log n$."
* **Pseudocode:**
    1. Base case: If `n === 0`, return 1.
    2. Handle negative `n`: return `1 / pow(x, -n)`.
    3. `half = pow(x, Math.floor(n / 2))`.
    4. If `n` is even, return `half * half`.
    5. If `n` is odd, return `half * half * x`.
* **JavaScript Solution:**
```javascript
const myPow = (baseValue, exponentValue) => {
  const calculatePower = (x, n) => {
    if (n === 0) return 1;
    
    const halfPower = calculatePower(x, Math.floor(n / 2));
    
    if (n % 2 === 0) {
      return halfPower * halfPower;
    } else {
      return halfPower * halfPower * x;
    }
  };

  if (exponentValue < 0) {
    return 1 / calculatePower(baseValue, Math.abs(exponentValue));
  }
  return calculatePower(baseValue, exponentValue);
};
```
* **Dry Run (x = 2, n = 2):**
    * `half` = pow(2, 1) -> `half` = pow(2, 0) -> returns 1.
    * `n=1` (odd): 1 * 1 * 2 = 2.
    * `n=2` (even): 2 * 2 = 4.
* **Alternative Approach:** We could use an iterative approach with a while loop to achieve $O(1)$ space.

---

## 7. Multiply Strings
[https://leetcode.com/problems/multiply-strings/](https://leetcode.com/problems/multiply-strings/)

* **Complexity:** Time: $O(m \cdot n)$ | Space: $O(m + n)$
* **Key Intuition:** We simulate the manual long multiplication process, placing the result of $num1[i] \cdot num2[j]$ at the index $i + j + 1$ in a result array.
* **The 'Talk Track':**
    * "I'll use a result array of size `m + n` initialized with zeros to handle the maximum possible length of the product."
    * "The key observation is that for digits at indices $i$ and $j$, their product impacts positions $i+j$ and $i+j+1$."
    * "I'll perform a final pass to join the digits, skipping leading zeros unless the result is '0'."
* **Pseudocode:**
    1. `res = array(m + n).fill(0)`.
    2. Loop `i` from `m-1` to 0.
    3. Loop `j` from `n-1` to 0.
    4. `mul = num1[i] * num2[j]`.
    5. `sum = mul + res[i+j+1]`.
    6. `res[i+j+1] = sum % 10`.
    7. `res[i+j] += Math.floor(sum / 10)`.
* **JavaScript Solution:**
```javascript
const multiply = (num1, num2) => {
  if (num1 === "0" || num2 === "0") return "0";

  const productArray = new Array(num1.length + num2.length).fill(0);

  for (let i = num1.length - 1; i >= 0; i--) {
    for (let j = num2.length - 1; j >= 0; j--) {
      const multiplicationResult = Number(num1[i]) * Number(num2[j]);
      const sumWithCarry = multiplicationResult + productArray[i + j + 1];

      productArray[i + j + 1] = sumWithCarry % 10;
      productArray[i + j] += Math.floor(sumWithCarry / 10);
    }
  }

  const resultString = productArray.join("");
  return resultString.startsWith("0") ? resultString.slice(1) : resultString;
};
```
* **Dry Run (num1 = "2", num2 = "3"):**
    * `res` = [0, 0].
    * `mul` = 6. `sum` = 6 + 0.
    * `res[1]` = 6. `res[0]` = 0.
    * Return "6".
* **Alternative Approach:** We could convert to BigInt if the environment allows, but it doesn't demonstrate the logic required for the algorithm.

---

## 8. Detect Squares
[https://leetcode.com/problems/detect-squares/](https://leetcode.com/problems/detect-squares/)

* **Complexity:** Time: $O(n)$ for `count` (where $n$ is unique points) | Space: $O(p)$ (total points)
* **Key Intuition:** For a point $P1(x, y)$, any point $P2(x2, y2)$ with $|x-x2| = |y-y2|$ forms a diagonal of a square. We then just check if the other two corners $(x, y2)$ and $(x2, y)$ exist.
* **The 'Talk Track':**
    * "I'll use a Hash Map to store point frequencies for $O(1)$ coordinate lookup."
    * "To count squares, I'll iterate through all points that share the same diagonal property as the query point."
    * "I'll ensure the side length is non-zero to avoid counting the query point itself as a square."
* **Pseudocode:**
    1. `add(point)`: Increment frequency in map.
    2. `count(point)`:
       a. For every `p2` in points list:
       b. If `abs(x1-x2) === abs(y1-y2)` AND `side > 0`:
       c. `total += count(x1, y2) * count(x2, y1) * count(p2)`.
* **JavaScript Solution:**
```javascript
class DetectSquares {
  constructor() {
    this.pointFrequency = new Map();
    this.pointsList = [];
  }

  add(point) {
    const key = point.join(",");
    this.pointFrequency.set(key, (this.pointFrequency.get(key) || 0) + 1);
    this.pointsList.push(point);
  }

  count(queryPoint) {
    const [queryX, queryY] = queryPoint;
    let squareCount = 0;

    for (const [existingX, existingY] of this.pointsList) {
      const deltaX = Math.abs(queryX - existingX);
      const deltaY = Math.abs(queryY - existingY);

      if (deltaX !== 0 && deltaX === deltaY) {
        const cornerOne = `${queryX},${existingY}`;
        const cornerTwo = `${existingX},${queryY}`;

        const frequencyOne = this.pointFrequency.get(cornerOne) || 0;
        const frequencyTwo = this.pointFrequency.get(cornerTwo) || 0;

        squareCount += frequencyOne * frequencyTwo;
      }
    }

    return squareCount;
  }
}
```
* **Dry Run (Add [0,0],[0,1],[1,0],[1,1]; Count [0,0]):**
    * Iterates list. Finds [1,1] (deltaX=1, deltaY=1).
    * Checks [0,1] and [1,0]. Both exist.
    * `count` += 1.
* **Alternative Approach:** We could group points by X-coordinate in a nested map to optimize the search space during the `count` operation.

---

## 9. [Latest 2026] Maximum Area of a Longest Diagonal Rectangle
[https://leetcode.com/problems/maximum-area-of-a-longest-diagonal-rectangle/](https://leetcode.com/problems/maximum-area-of-a-longest-diagonal-rectangle/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** We need to find the rectangle with the largest diagonal ($l^2 + w^2$); if there's a tie, we pick the one with the largest area ($l \cdot w$).
* **The 'Talk Track':**
    * "I'll track the `maxDiagonalSquared` to avoid using `Math.sqrt()` and maintain precision."
    * "When I find a new maximum diagonal, I'll update both the diagonal and the area."
    * "If I find a diagonal equal to the current max, I'll update the area only if the new rectangle's area is larger."
* **Pseudocode:**
    1. Initialize `maxDiagSq = 0`, `maxArea = 0`.
    2. For each `[length, width]` in `rectangles`:
       a. `diagSq = length**2 + width**2`.
       b. If `diagSq > maxDiagSq`: update `maxDiagSq` and `maxArea`.
       c. If `diagSq === maxDiagSq`: `maxArea = Math.max(maxArea, length * width)`.
    3. Return `maxArea`.
* **JavaScript Solution:**
```javascript
const areaOfMaxDiagonal = (rectangles) => {
  let maximumDiagonalSquared = 0;
  let maximumAreaFound = 0;

  for (const [length, width] of rectangles) {
    const currentDiagonalSquared = length * length + width * width;
    const currentArea = length * width;

    if (currentDiagonalSquared > maximumDiagonalSquared) {
      maximumDiagonalSquared = currentDiagonalSquared;
      maximumAreaFound = currentArea;
    } else if (currentDiagonalSquared === maximumDiagonalSquared) {
      if (currentArea > maximumAreaFound) {
        maximumAreaFound = currentArea;
      }
    }
  }

  return maximumAreaFound;
};
```
* **Dry Run (rects = [[3, 4], [4, 3]]):**
    * Rect 1: `diag` = 25. `area` = 12.
    * Rect 2: `diag` = 25. `area` = 12.
    * Return 12.
* **Alternative Approach:** We could sort the rectangles, but that would be $O(n \log n)$ instead of this $O(n)$ single-pass solution.

---

## 10. [Latest 2026] Count Lattice Points Inside a Circle
[https://leetcode.com/problems/count-lattice-points-inside-a-circle/](https://leetcode.com/problems/count-lattice-points-inside-a-circle/)

* **Complexity:** Time: $O(N \cdot R^2)$ | Space: $O(N \cdot R^2)$ (for the Set)
* **Key Intuition:** For each circle, check every point within its bounding box $(X-R, Y-R)$ to $(X+R, Y+R)$ and see if it satisfies the distance formula $(x-X)^2 + (y-Y)^2 \le R^2$.
* **The 'Talk Track':**
    * "I'll use a `Set` to store coordinates as strings to ensure each lattice point is only counted once, even if multiple circles overlap it."
    * "The search space for each circle is limited to its bounding box, making it feasible for the given constraints."
    * "I'll optimize by using the squared distance to avoid expensive square root operations."
* **Pseudocode:**
    1. Create a `uniquePoints` Set.
    2. For each circle `[centerX, centerY, radius]`:
       a. Loop `x` from `centerX - radius` to `centerX + radius`.
       b. Loop `y` from `centerY - radius` to `centerY + radius`.
       c. If `(x - centerX)**2 + (y - centerY)**2 <= radius**2`, add `"x,y"` to Set.
    3. Return `Set.size`.
* **JavaScript Solution:**
```javascript
const countLatticePoints = (circles) => {
  const uniqueLatticePoints = new Set();

  for (const [centerX, centerY, radius] of circles) {
    const radiusSquared = radius * radius;
    
    for (let xCoord = centerX - radius; xCoord <= centerX + radius; xCoord++) {
      for (let yCoord = centerY - radius; yCoord <= centerY + radius; yCoord++) {
        const distanceSquared = Math.pow(xCoord - centerX, 2) + Math.pow(yCoord - centerY, 2);
        
        if (distanceSquared <= radiusSquared) {
          uniqueLatticePoints.add(`${xCoord},${yCoord}`);
        }
      }
    }
  }

  return uniqueLatticePoints.size;
};
```
* **Dry Run (circle = [2, 2, 1]):**
    * Bounding box (1,1) to (3,3).
    * (2,2) dist=0. Add.
    * (1,2) dist=1. Add. (3,2) dist=1. Add.
    * Result: 5 points (center and 4 compass points).
* **Alternative Approach:** We could sort circles by radius to process larger ones first and potentially skip points already in the Set, though this doesn't change worst-case complexity.

---
