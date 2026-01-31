# Senior Engineer's Study Guide: Stack Pattern

The **Stack** pattern utilizes the Last-In-First-Out (LIFO) principle. It is the go-to data structure for problems involving nested structures (like parentheses), backtracking, or "monotonic" processing where you need to track the next or previous greater/smaller element in linear time.

---

## 1. Valid Parentheses
[https://leetcode.com/problems/valid-parentheses/](https://leetcode.com/problems/valid-parentheses/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** We use a stack to store expected closing characters; every time we see an opener, we push its corresponding closer onto the stack to ensure the correct order.
* **The 'Talk Track':**
    * "I’ll use a Map to define the pairings, which makes the code cleaner and easier to extend if more bracket types are added."
    * "A stack is ideal here because the most recently opened bracket must be the first one closed."
    * "I'll add an early exit: if the stack is empty when I encounter a closer, or if the popped element doesn't match, the string is invalid."
* **Pseudocode:**
    1. Create a `bracketMap` (opener as key, closer as value).
    2. Initialize an empty `validationStack`.
    3. Iterate through each `currentCharacter`.
    4. If it's an opener, push its closer to the stack.
    5. If it's a closer, pop from the stack and check if it matches `currentCharacter`.
    6. Return true if the stack is empty at the end.
* **JavaScript Solution:**
```javascript
const isValid = (inputString) => {
  const bracketMap = { '(': ')', '{': '}', '[': ']' };
  const validationStack = [];

  for (const currentCharacter of inputString) {
    if (bracketMap[currentCharacter]) {
      validationStack.push(bracketMap[currentCharacter]);
    } else {
      const expectedCloser = validationStack.pop();
      if (currentCharacter !== expectedCloser) {
        return false;
      }
    }
  }

  return validationStack.length === 0;
};
```
* **Dry Run (inputString = "()"):**
    * `char` = '(': `validationStack` gets ')'.
    * `char` = ')': Pop ')'. Match!
    * `stack` is empty. Return `true`.
* **Alternative Approach:** We could use string replacement in a loop, but that would result in $O(n^2)$ time complexity.

---

## 2. Min Stack
[https://leetcode.com/problems/min-stack/](https://leetcode.com/problems/min-stack/)

* **Complexity:** Time: $O(1)$ for all operations | Space: $O(n)$
* **Key Intuition:** To get the minimum in constant time, we store a snapshot of the minimum value at every level of the stack.
* **The 'Talk Track':**
    * "I’ll maintain a secondary stack or store objects to keep track of the minimum value associated with every element."
    * "This ensures that when we pop the current minimum, the previous minimum is still available at the top of our tracking structure."
    * "By trading a bit of space, we achieve the $O(1)$ requirement for `getMin`."
* **Pseudocode:**
    1. Create a `mainStack` to store objects: `{ value, currentMin }`.
    2. `push(val)`: find new min by comparing `val` with the `currentMin` of the top element.
    3. `pop()`: standard array pop.
    4. `top()` and `getMin()`: return properties from the top object.
* **JavaScript Solution:**
```javascript
class MinStack {
  constructor() {
    this.stackStorage = [];
  }

  push(value) {
    const currentMin = this.stackStorage.length === 0 
      ? value 
      : Math.min(value, this.getMin());
    this.stackStorage.push({ value, currentMin });
  }

  pop() {
    this.stackStorage.pop();
  }

  top() {
    return this.stackStorage[this.stackStorage.length - 1].value;
  }

  getMin() {
    return this.stackStorage[this.stackStorage.length - 1].currentMin;
  }
}
```
* **Dry Run (push 1, push 2):**
    * Push 1: `stack` = `[{val:1, min:1}]`.
    * Push 2: `stack` = `[{val:1, min:1}, {val:2, min:1}]`.
    * `getMin()` returns 1.
* **Alternative Approach:** We could use a single stack and store the difference between the value and the minimum, but that is significantly more complex to implement.

---

## 3. Evaluate Reverse Polish Notation
[https://leetcode.com/problems/evaluate-reverse-polish-notation/](https://leetcode.com/problems/evaluate-reverse-polish-notation/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** Post-fix notation is designed for stacks; operands are pushed, and operators trigger a pop of the last two values to calculate a result.
* **The 'Talk Track':**
    * "I'll use a stack to keep track of operands as I process the tokens."
    * "When an operator is encountered, the order of operands matters—especially for division and subtraction (the first pop is the divisor)."
    * "I'll use `Math.trunc()` for division to ensure we truncate toward zero as specified."
* **Pseudocode:**
    1. Initialize `operandStack`.
    2. For each `token`:
       a. If it's a number, push it.
       b. If it's an operator, pop `valueTwo`, then pop `valueOne`.
       c. Apply operator and push result.
    3. Return the last item in `operandStack`.
* **JavaScript Solution:**
```javascript
const evalRPN = (tokens) => {
  const operandStack = [];
  const operators = {
    '+': (first, second) => first + second,
    '-': (first, second) => first - second,
    '*': (first, second) => first * second,
    '/': (first, second) => Math.trunc(first / second),
  };

  for (const token of tokens) {
    if (operators[token]) {
      const valueTwo = operandStack.pop();
      const valueOne = operandStack.pop();
      operandStack.push(operators[token](valueOne, valueTwo));
    } else {
      operandStack.push(Number(token));
    }
  }

  return operandStack[0];
};
```
* **Dry Run (["1", "2", "+"]):**
    * Push 1, push 2.
    * Operator '+': pop 2, pop 1. Push $1+2=3$.
    * Return 3.
* **Alternative Approach:** We could solve this recursively, but the stack-based iterative approach is more direct for RPN.

---

## 4. Generate Parentheses
[https://leetcode.com/problems/generate-parentheses/](https://leetcode.com/problems/generate-parentheses/)

* **Complexity:** Time: $O(\frac{4^n}{\sqrt{n}})$ | Space: $O(n)$
* **Key Intuition:** This is a backtracking problem where the "implicit stack" (recursion) ensures we only add a closing bracket if it doesn't exceed the number of open brackets.
* **The 'Talk Track':**
    * "I'll use a recursive backtracking approach to explore all valid combinations."
    * "The constraints are simple: I can add an opening bracket if I have 'credits' left, and a closing bracket if the count of open brackets is higher."
    * "This pruning prevents us from ever generating an invalid string, optimizing the search space."
* **Pseudocode:**
    1. `backtrack(currentString, openCount, closeCount)`:
    2. If `currentString.length === n * 2`, add to results and return.
    3. If `openCount < n`, `backtrack(currentString + '(', openCount + 1, closeCount)`.
    4. If `closeCount < openCount`, `backtrack(currentString + ')', openCount, closeCount + 1)`.
* **JavaScript Solution:**
```javascript
const generateParenthesis = (pairsCount) => {
  const results = [];

  const backtrack = (currentString, openCount, closeCount) => {
    if (currentString.length === pairsCount * 2) {
      results.push(currentString);
      return;
    }

    if (openCount < pairsCount) {
      backtrack(currentString + '(', openCount + 1, closeCount);
    }
    if (closeCount < openCount) {
      backtrack(currentString + ')', openCount, closeCount + 1);
    }
  };

  backtrack("", 0, 0);
  return results;
};
```
* **Dry Run (n=1):**
    * `open`<1: `(` (1, 0).
    * `close`<1: `()` (1, 1).
    * Length=2: Return `["()"]`.
* **Alternative Approach:** We could use an iterative BFS approach with a queue, but backtracking is the idiomatic way to handle combinatorial generation.

---

## 5. Daily Temperatures
[https://leetcode.com/problems/daily-temperatures/](https://leetcode.com/problems/daily-temperatures/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** Use a **Monotonic Decreasing Stack** to store indices of temperatures; when we find a warmer temperature, we've found the "next greater" for all indices currently in the stack.
* **The 'Talk Track':**
    * "This is a classic 'Next Greater Element' problem, which screams for a monotonic stack."
    * "I’ll store indices in the stack so I can calculate the distance between the current day and the cooler day."
    * "Even though there's a nested `while` loop, each index is pushed and popped exactly once, maintaining linear time."
* **Pseudocode:**
    1. Initialize `answerArray` with zeros.
    2. `indexStack` = [].
    3. Iterate `currentIndex` through `temperatures`.
    4. While `temperatures[currentIndex]` > `temperatures[stackTop]`:
       a. `prevIndex = stack.pop()`.
       b. `answerArray[prevIndex] = currentIndex - prevIndex`.
    5. Push `currentIndex`.
* **JavaScript Solution:**
```javascript
const dailyTemperatures = (temperatures) => {
  const collectionLength = temperatures.length;
  const daysToWait = new Array(collectionLength).fill(0);
  const indexStack = [];

  for (let currentIndex = 0; currentIndex < collectionLength; currentIndex++) {
    const currentTemp = temperatures[currentIndex];

    while (indexStack.length > 0 && currentTemp > temperatures[indexStack[indexStack.length - 1]]) {
      const previousIndex = indexStack.pop();
      daysToWait[previousIndex] = currentIndex - previousIndex;
    }
    indexStack.push(currentIndex);
  }

  return daysToWait;
};
```
* **Dry Run ([1, 2]):**
    * `i`=0: `stack` = [0].
    * `i`=1: 2 > 1. `prev` = 0. `ans[0]` = 1-0 = 1. `stack` = [1].
    * Return [1, 0].
* **Alternative Approach:** A brute force $O(n^2)$ would check every subsequent day for each index, but it would time out on large inputs.

---

## 6. Car Fleet
[https://leetcode.com/problems/car-fleet/](https://leetcode.com/problems/car-fleet/)

* **Complexity:** Time: $O(n \log n)$ (due to sorting) | Space: $O(n)$
* **Key Intuition:** A car becomes a fleet if the time it takes to reach the target is less than or equal to the time taken by the car directly in front of it.
* **The 'Talk Track':**
    * "First, I'll pair position and speed, then sort by position in descending order to process cars from closest-to-target to furthest."
    * "I'll calculate the time to destination: $(target - position) / speed$."
    * "If a car's time is $\le$ the fleet ahead of it, it joins that fleet; if it's slower, it starts a new fleet."
* **Pseudocode:**
    1. Create pairs of `[pos, speed]` and sort descending by `pos`.
    2. Initialize `fleetStack`.
    3. For each car, calculate `timeToTarget`.
    4. If `timeToTarget` is greater than the top of `fleetStack`, push it.
    5. Return `fleetStack.length`.
* **JavaScript Solution:**
```javascript
const carFleet = (target, position, speed) => {
  const cars = position.map((pos, index) => [pos, speed[index]]);
  cars.sort((a, b) => b[0] - a[0]);

  const fleetTimeStack = [];

  for (const [currentPosition, currentSpeed] of cars) {
    const timeToDestination = (target - currentPosition) / currentSpeed;
    
    if (fleetTimeStack.length === 0 || timeToDestination > fleetTimeStack[fleetTimeStack.length - 1]) {
      fleetTimeStack.push(timeToDestination);
    }
  }

  return fleetTimeStack.length;
};
```
* **Dry Run (target=10, pos=[0, 5], speed=[2, 1]):**
    * Sorted: [5, 1], [0, 2].
    * Car 5: `time` = (10-5)/1 = 5. `stack` = [5].
    * Car 0: `time` = (10-0)/2 = 5. 5 is not > 5. Do nothing.
    * Return 1.
* **Alternative Approach:** We could use a single variable to track the `lastFleetTime` instead of a full stack to save space.

---

## 7. Validate Stack Sequences
[https://leetcode.com/problems/validate-stack-sequences/](https://leetcode.com/problems/validate-stack-sequences/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** Simulate the process greedily: push elements one by one and pop as long as the top of the stack matches the next element in the `popped` array.
* **The 'Talk Track':**
    * "I'll use a real stack to mimic the described behavior."
    * "The greedy choice is always correct here: if the top of the stack matches the next expected popped element, we MUST pop it."
    * "If the stack is empty at the end, the sequence is valid."
* **Pseudocode:**
    1. `simulationStack = []`, `poppedIndex = 0`.
    2. For each `item` in `pushed`:
       a. Push `item` to `simulationStack`.
       b. While `stack.top === popped[poppedIndex]`:
          i. Pop from `stack`.
          ii. Increment `poppedIndex`.
    3. Return `stack.length === 0`.
* **JavaScript Solution:**
```javascript
const validateStackSequences = (pushed, popped) => {
  const simulationStack = [];
  let poppedIndex = 0;

  for (const currentItem of pushed) {
    simulationStack.push(currentItem);
    while (
      simulationStack.length > 0 && 
      simulationStack[simulationStack.length - 1] === popped[poppedIndex]
    ) {
      simulationStack.pop();
      poppedIndex++;
    }
  }

  return simulationStack.length === 0;
};
```
* **Dry Run (push=[1], pop=[1]):**
    * Push 1. `stack` = [1].
    * Top (1) === Pop (1). Pop! `stack` = [].
    * Return `true`.
* **Alternative Approach:** We could use the `pushed` array itself as a stack by using a pointer to save space, though this is less readable.

---

## 8. [Latest 2026] Robot Collisions
[https://leetcode.com/problems/robot-collisions/](https://leetcode.com/problems/robot-collisions/)

* **Complexity:** Time: $O(n \log n)$ | Space: $O(n)$
* **Key Intuition:** This is a "collision" problem where we only care about 'R' robots hitting 'L' robots. A stack stores 'R' robots until they meet an 'L' robot coming the other way.
* **The 'Talk Track':**
    * "We sort robots by position to process them from left to right."
    * "The stack will only ever hold robots moving Right ('R'). When a Left ('L') robot appears, it 'battles' the robots in the stack."
    * "I'll handle health reductions and removals within a `while` loop until the 'L' robot is destroyed or the stack is empty."
* **Pseudocode:**
    1. Sort robot indices by position.
    2. `robotStack` = [].
    3. For each `robot`:
       a. If 'R', push to stack.
       b. If 'L', collide with `stack.top`. Update health or remove.
    4. Filter and return remaining healths in original order.
* **JavaScript Solution:**
```javascript
const survivedRobotsHealths = (positions, healths, directions) => {
  const collectionLength = positions.length;
  const indices = Array.from({ length: collectionLength }, (_, index) => index);
  indices.sort((a, b) => positions[a] - positions[b]);

  const movingRightStack = [];
  for (const currentIndex of indices) {
    if (directions[currentIndex] === 'R') {
      movingRightStack.push(currentIndex);
      continue;
    }

    // Process Left-moving robot collision
    while (movingRightStack.length > 0 && healths[currentIndex] > 0) {
      const topRightIndex = movingRightStack[movingRightStack.length - 1];
      if (healths[currentIndex] > healths[topRightIndex]) {
        healths[currentIndex] -= 1;
        healths[topRightIndex] = 0;
        movingRightStack.pop();
      } else if (healths[currentIndex] < healths[topRightIndex]) {
        healths[topRightIndex] -= 1;
        healths[currentIndex] = 0;
      } else {
        healths[currentIndex] = 0;
        healths[topRightIndex] = 0;
        movingRightStack.pop();
      }
    }
  }

  return healths.filter((health) => health > 0);
};
```
* **Dry Run (pos=[1,2], health=[10,10], dir="RL"):**
    * Robot 0 ('R') pushed.
    * Robot 1 ('L') battles. Healths equal. Both 0.
    * Return [].
* **Alternative Approach:** Without a stack, you would need complex interval merging or multiple passes, which are less efficient.

---

## 9. [Latest 2026] Clear Digits
[https://leetcode.com/problems/clear-digits/](https://leetcode.com/problems/clear-digits/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** We use a stack to keep track of characters; whenever we hit a digit, we "clear" the most recent character by popping it.
* **The 'Talk Track':**
    * "This is a simple application of the stack's LIFO property to handle backspace-like behavior."
    * "I'll iterate once, pushing non-digits and popping when a digit is seen."
    * "The final stack, joined, represents the string after all deletions."
* **Pseudocode:**
    1. Initialize `charStack`.
    2. Iterate through string.
    3. If character is a digit, `stack.pop()`.
    4. Else, `stack.push(char)`.
    5. Join and return.
* **JavaScript Solution:**
```javascript
const clearDigits = (inputString) => {
  const characterStack = [];

  for (const currentCharacter of inputString) {
    const isDigit = currentCharacter >= '0' && currentCharacter <= '9';
    if (isDigit) {
      if (characterStack.length > 0) {
        characterStack.pop();
      }
    } else {
      characterStack.push(currentCharacter);
    }
  }

  return characterStack.join('');
};
```
* **Dry Run (inputString = "a1"):**
    * 'a': push 'a'.
    * '1': pop 'a'.
    * Return "".
* **Alternative Approach:** We could use a two-pointer "in-place" modification on a character array to achieve $O(1)$ extra space beyond the output.
