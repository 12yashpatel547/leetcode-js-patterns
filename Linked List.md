# Senior Engineer's Study Guide: Linked Lists

The **Linked List** pattern focuses on pointer manipulation and memory management. At a senior level, the goal is to demonstrate "in-place" manipulation to achieve $O(1)$ space complexity whenever possible and to handle edge cases like null heads, single-node lists, and cycles without breaking pointer references.

---

## 1. Reverse Linked List
[https://leetcode.com/problems/reverse-linked-list/](https://leetcode.com/problems/reverse-linked-list/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** We iterate through the list once, flipping the `next` pointer of each node to point to its predecessor.
* **The 'Talk Track':**
    * "I'll maintain a `previousNode` pointer initialized to null to serve as the new tail's next reference."
    * "Before reassigning `currentNode.next`, I must cache the 'actual' next node to ensure I don't lose the rest of the list."
    * "This is a fundamental operation that serves as a building block for more complex list problems like Palindrome Linked List."
* **Pseudocode:**
    1. Initialize `previousNode` as null.
    2. While `currentNode` is not null:
       a. Save `currentNode.next` into `nextNode`.
       b. Set `currentNode.next` to `previousNode`.
       c. Move `previousNode` to `currentNode`.
       d. Move `currentNode` to `nextNode`.
    3. Return `previousNode`.
* **JavaScript Solution:**
```javascript
const reverseList = (headNode) => {
  let previousNode = null;
  let currentNode = headNode;

  while (currentNode !== null) {
    const nextNodeReference = currentNode.next;
    currentNode.next = previousNode;
    previousNode = currentNode;
    currentNode = nextNodeReference;
  }

  return previousNode;
};
```
* **Dry Run (input = [1, 2]):**
    * Start: `curr`=1, `prev`=null.
    * Loop 1: `nextRef`=2. 1.next=null. `prev`=1. `curr`=2.
    * Loop 2: `nextRef`=null. 2.next=1. `prev`=2. `curr`=null.
    * Result: 2 -> 1 -> null.
* **Alternative Approach:** We could use recursion, but it would increase space complexity to $O(n)$ due to the call stack.

---

## 2. Merge Two Sorted Lists
[https://leetcode.com/problems/merge-two-sorted-lists/](https://leetcode.com/problems/merge-two-sorted-lists/)

* **Complexity:** Time: $O(n + m)$ | Space: $O(1)$
* **Key Intuition:** Use a "dummy" head to simplify the initial node assignment and a single pointer to stitch the two lists together in order.
* **The 'Talk Track':**
    * "I'll use a `dummyNode` to avoid writing conditional logic for the initial head assignment."
    * "I'll compare the heads of both lists, attach the smaller one, and advance that list's pointer."
    * "At the end, if one list still has nodes, I'll simply attach the remainder in one $O(1)$ operation since it's already sorted."
* **Pseudocode:**
    1. Create `dummyHead`. Set `tailNode` to `dummyHead`.
    2. While both `listOne` and `listTwo` are not null:
       a. If `listOne.val` < `listTwo.val`, attach `listOne` to `tailNode.next` and move `listOne`.
       b. Else, attach `listTwo` and move `listTwo`.
       c. Advance `tailNode`.
    3. Attach any remaining nodes from either list to `tailNode.next`.
    4. Return `dummyHead.next`.
* **JavaScript Solution:**
```javascript
const mergeTwoLists = (listOne, listTwo) => {
  const dummyHead = new ListNode(0);
  let currentTail = dummyHead;

  while (listOne !== null && listTwo !== null) {
    if (listOne.val <= listTwo.val) {
      currentTail.next = listOne;
      listOne = listOne.next;
    } else {
      currentTail.next = listTwo;
      listTwo = listTwo.next;
    }
    currentTail = currentTail.next;
  }

  currentTail.next = listOne || listTwo;
  return dummyHead.next;
};
```
* **Dry Run (L1 = [1], L2 = [2]):**
    * `dummy`->0.
    * 1 < 2: `tail.next` = 1. `listOne` = null. `tail` = 1.
    * Loop ends. `tail.next` = `listTwo` (2).
    * Result: 1 -> 2.
* **Alternative Approach:** Recursion works well here but trades $O(1)$ space for $O(n+m)$ stack space.

---

## 3. Reorder List
[https://leetcode.com/problems/reorder-list/](https://leetcode.com/problems/reorder-list/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Find the middle, reverse the second half, and then interleave the two halves.
* **The 'Talk Track':**
    * "I'll use the slow and fast pointer technique to find the midpoint in a single pass."
    * "Once I split the list, I'll reverse the second half to allow for easy interleaving from the tail inwards."
    * "The merging step requires careful pointer management to avoid losing the next nodes in either half."
* **Pseudocode:**
    1. Use `slow` and `fast` pointers to find middle.
    2. Reverse the list starting from `slow.next`.
    3. Split the list by setting `slow.next = null`.
    4. Interleave nodes from the `firstHalf` and the `reversedSecondHalf`.
* **JavaScript Solution:**
```javascript
const reorderList = (headNode) => {
  if (!headNode || !headNode.next) return;

  // 1. Find Middle
  let slowPointer = headNode;
  let fastPointer = headNode.next;
  while (fastPointer && fastPointer.next) {
    slowPointer = slowPointer.next;
    fastPointer = fastPointer.next.next;
  }

  // 2. Reverse Second Half
  let secondHalfPointer = slowPointer.next;
  slowPointer.next = null;
  let previousNode = null;
  while (secondHalfPointer) {
    let nextNodeReference = secondHalfPointer.next;
    secondHalfPointer.next = previousNode;
    previousNode = secondHalfPointer;
    secondHalfPointer = nextNodeReference;
  }

  // 3. Interleave
  let firstHalfPointer = headNode;
  let secondHalfHead = previousNode;
  while (secondHalfHead) {
    let tempOne = firstHalfPointer.next;
    let tempTwo = secondHalfHead.next;

    firstHalfPointer.next = secondHalfHead;
    secondHalfHead.next = tempOne;

    firstHalfPointer = tempOne;
    secondHalfHead = tempTwo;
  }
};
```
* **Dry Run (input = [1, 2, 3]):**
    * Middle: 2. Second half: [3]. Reversed second half: [3].
    * Interleave: 1 points to 3. 3 points to 2. 2 points to null.
    * Result: 1 -> 3 -> 2.
* **Alternative Approach:** We could store all nodes in an array for $O(1)$ access, but that would use $O(n)$ space.

---

## 4. Remove Nth Node From End of List
[https://leetcode.com/problems/remove-nth-node-from-end-of-list/](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Maintain a gap of $n$ nodes between two pointers; when the lead pointer hits the end, the trailing pointer is at the node before the target.
* **The 'Talk Track':**
    * "I'll use a `dummyNode` as the start to handle the edge case where the head itself needs to be removed."
    * "By advancing the `rightPointer` $n$ steps ahead first, I create a fixed window that identifies the correct position relative to the end."
    * "This allows us to complete the operation in exactly one pass."
* **Pseudocode:**
    1. Create `dummyNode` pointing to `head`.
    2. Set `leftPointer` and `rightPointer` at `dummyNode`.
    3. Move `rightPointer` forward `n` times.
    4. Move both pointers until `rightPointer.next` is null.
    5. Set `leftPointer.next = leftPointer.next.next`.
    6. Return `dummyNode.next`.
* **JavaScript Solution:**
```javascript
const removeNthFromEnd = (headNode, n) => {
  const dummyHead = new ListNode(0, headNode);
  let leftPointer = dummyHead;
  let rightPointer = headNode;

  // Create the n-sized gap
  while (n > 0 && rightPointer) {
    rightPointer = rightPointer.next;
    n--;
  }

  while (rightPointer) {
    leftPointer = leftPointer.next;
    rightPointer = rightPointer.next;
  }

  // Delete node
  leftPointer.next = leftPointer.next.next;
  return dummyHead.next;
};
```
* **Dry Run (input = [1, 2], n = 2):**
    * `dummy`->1->2. `right` moves to null.
    * `left` stays at `dummy`.
    * `left.next` = 1.next (which is 2).
    * Result: [2].
* **Alternative Approach:** We could do two passes—one to count total length and another to remove the $(L-n+1)$th node.

---

## 5. Copy List with Random Pointer
[https://leetcode.com/problems/copy-list-with-random-pointer/](https://leetcode.com/problems/copy-list-with-random-pointer/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$ (for the Map)
* **Key Intuition:** Use a Hash Map to map every original node to its new copy, then perform a second pass to wire the `next` and `random` pointers.
* **The 'Talk Track':**
    * "A two-pass approach is cleanest: first create the nodes, then link them."
    * "The Hash Map is essential here because a random pointer might point to a node that appears later in the list."
    * "I'll treat `null` as a valid key/value in my map to simplify the assignment logic."
* **Pseudocode:**
    1. Create a `nodeMap` (Original Node -> New Node).
    2. Pass 1: Iterate list, create a new node for each original, and store in `nodeMap`.
    3. Pass 2: Iterate list again, set `newNodes.next` and `newNodes.random` using the `nodeMap`.
    4. Return `nodeMap.get(head)`.
* **JavaScript Solution:**
```javascript
const copyRandomList = (headNode) => {
  if (!headNode) return null;

  const oldToNewMap = new Map();
  oldToNewMap.set(null, null);

  let currentNode = headNode;
  while (currentNode) {
    oldToNewMap.set(currentNode, new Node(currentNode.val));
    currentNode = currentNode.next;
  }

  currentNode = headNode;
  while (currentNode) {
    const newNode = oldToNewMap.get(currentNode);
    newNode.next = oldToNewMap.get(currentNode.next);
    newNode.random = oldToNewMap.get(currentNode.random);
    currentNode = currentNode.next;
  }

  return oldToNewMap.get(headNode);
};
```
* **Dry Run (input = [[1, null]]):**
    * Pass 1: `nodeMap` has {1: CopyOf1}.
    * Pass 2: CopyOf1.next = Map(null) = null. CopyOf1.random = Map(null) = null.
    * Return CopyOf1.
* **Alternative Approach:** We could interleave the copies within the original list (Original -> Copy -> Original -> Copy) to achieve $O(1)$ extra space.

---

## 6. Add Two Numbers
[https://leetcode.com/problems/add-two-numbers/](https://leetcode.com/problems/add-two-numbers/)

* **Complexity:** Time: $O(max(n, m))$ | Space: $O(1)$ (ignoring output)
* **Key Intuition:** Iterate through both lists simultaneously, maintaining a `carry` variable; this is essentially manual long addition.
* **The 'Talk Track':**
    * "I'll iterate as long as either list has nodes OR there is a non-zero carry."
    * "Using the modulo operator (`% 10`) gets the digit, and integer division or `Math.floor` gets the new carry."
    * "I'll use a `dummyNode` again to simplify building the result list."
* **Pseudocode:**
    1. `dummyHead = new ListNode(0)`, `carry = 0`.
    2. While `listOne` OR `listTwo` OR `carry > 0`:
       a. `val1 = listOne ? listOne.val : 0`.
       b. `val2 = listTwo ? listTwo.val : 0`.
       c. `sum = val1 + val2 + carry`.
       d. `carry = Math.floor(sum / 10)`.
       e. Add `sum % 10` to result list.
    3. Return `dummyHead.next`.
* **JavaScript Solution:**
```javascript
const addTwoNumbers = (listOne, listTwo) => {
  const dummyHead = new ListNode(0);
  let currentTail = dummyHead;
  let carryValue = 0;

  while (listOne !== null || listTwo !== null || carryValue > 0) {
    const valueOne = listOne ? listOne.val : 0;
    const valueTwo = listTwo ? listTwo.val : 0;

    const currentSum = valueOne + valueTwo + carryValue;
    carryValue = Math.floor(currentSum / 10);
    
    currentTail.next = new ListNode(currentSum % 10);
    currentTail = currentTail.next;

    if (listOne) listOne = listOne.next;
    if (listTwo) listTwo = listTwo.next;
  }

  return dummyHead.next;
};
```
* **Dry Run (L1 = [2], L2 = [9]):**
    * 2 + 9 + 0 = 11. `carry` = 1. New node val = 1.
    * Loop 2: 0 + 0 + 1 = 1. `carry` = 0. New node val = 1.
    * Result: [1, 1].
* **Alternative Approach:** We could convert lists to numbers, add, and convert back, but this fails for lists longer than the maximum safe integer.

---

## 7. Linked List Cycle
[https://leetcode.com/problems/linked-list-cycle/](https://leetcode.com/problems/linked-list-cycle/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Floyd’s Cycle-Finding Algorithm (Tortoise and Hare); if a cycle exists, the fast pointer will eventually lap the slow pointer.
* **The 'Talk Track':**
    * "This is the classic pointer-based cycle detection algorithm."
    * "The fast pointer moves at 2x speed; if there's a loop, the distance between them decreases by 1 each step until they meet."
    * "If the fast pointer ever hits null, we've definitively proven there is no cycle."
* **Pseudocode:**
    1. `slow = head`, `fast = head`.
    2. While `fast` and `fast.next` are not null:
       a. `slow = slow.next`.
       b. `fast = fast.next.next`.
       c. If `slow === fast`, return true.
    3. Return false.
* **JavaScript Solution:**
```javascript
const hasCycle = (headNode) => {
  let slowPointer = headNode;
  let fastPointer = headNode;

  while (fastPointer !== null && fastPointer.next !== null) {
    slowPointer = slowPointer.next;
    fastPointer = fastPointer.next.next;

    if (slowPointer === fastPointer) {
      return true;
    }
  }

  return false;
};
```
* **Dry Run (input = [1, 2], cycle at pos 0):**
    * Step 1: `slow`=2, `fast`=1 (fast went 2->1).
    * Step 2: `slow`=1, `fast`=1. Match!
    * Return true.
* **Alternative Approach:** Use a `Set` to store visited nodes, which takes $O(n)$ space.

---

## 8. Find the Duplicate Number
[https://leetcode.com/problems/find-the-duplicate-number/](https://leetcode.com/problems/find-the-duplicate-number/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** Treat the array as a linked list where `index` points to `value`; this becomes a Cycle Detection problem (specifically finding the entrance of the cycle).
* **The 'Talk Track':**
    * "Since the numbers are between 1 and $n$, they can be viewed as pointers to other indices in the array."
    * "The duplicate number is the entry point of the cycle because two different indices point to the same value."
    * "I'll use Floyd's algorithm: phase one to find the intersection, and phase two to find the entrance."
* **Pseudocode:**
    1. Phase 1: `slow = nums[0]`, `fast = nums[nums[0]]`. Move until they meet.
    2. Phase 2: Create `slowTwo = 0`. Move `slow` and `slowTwo` one step at a time until they meet.
    3. Return `slow`.
* **JavaScript Solution:**
```javascript
const findDuplicate = (nums) => {
  let slowPointer = nums[0];
  let fastPointer = nums[nums[0]];

  // Phase 1: Finding intersection
  while (slowPointer !== fastPointer) {
    slowPointer = nums[slowPointer];
    fastPointer = nums[nums[fastPointer]];
  }

  // Phase 2: Finding cycle entrance
  let secondSlowPointer = 0;
  while (secondSlowPointer !== slowPointer) {
    secondSlowPointer = nums[secondSlowPointer];
    slowPointer = nums[slowPointer];
  }

  return slowPointer;
};
```
* **Dry Run (input = [1, 3, 4, 2, 2]):**
    * Phase 1 intersection at index 4 (value 2).
    * Phase 2: Start 0 and 2. Move 0->1, 2->3. Move 1->3, 3->2. ... They meet at value 2.
    * Return 2.
* **Alternative Approach:** Sorting the array ($O(n \log n)$) or using a Set ($O(n)$ space), but both violate the problem's constraints.

---

## 9. LRU Cache
[https://leetcode.com/problems/lru-cache/](https://leetcode.com/problems/lru-cache/)

* **Complexity:** Time: $O(1)$ for both `get` and `put` | Space: $O(capacity)$
* **Key Intuition:** Combine a Hash Map (for $O(1)$ access) with a Doubly Linked List (for $O(1)$ reordering and removal).
* **The 'Talk Track':**
    * "The Map provides the key-to-node lookup, while the Doubly Linked List maintains the temporal order of usage."
    * "Every time a key is accessed or added, I'll move that node to the 'head' (most recent) of the list."
    * "When capacity is exceeded, I'll remove the node from the 'tail' (least recent) and delete it from the Map."
* **Pseudocode:**
    1. Define `ListNode` with `prev`, `next`, `key`, `val`.
    2. `get(key)`: If exists, move node to head and return value.
    3. `put(key, value)`: If exists, update and move to head. If not, create and add to head. If over capacity, remove tail.
* **JavaScript Solution:**
```javascript
class DoubleNode {
  constructor(key, value) {
    this.key = key;
    this.value = value;
    this.previous = null;
    this.next = null;
  }
}

class LRUCache {
  constructor(capacity) {
    this.capacity = capacity;
    this.cacheMap = new Map();
    this.head = new DoubleNode(0, 0);
    this.tail = new DoubleNode(0, 0);
    this.head.next = this.tail;
    this.tail.previous = this.head;
  }

  _remove(node) {
    node.previous.next = node.next;
    node.next.previous = node.previous;
  }

  _insertAtHead(node) {
    const nextNode = this.head.next;
    this.head.next = node;
    node.previous = this.head;
    node.next = nextNode;
    nextNode.previous = node;
  }

  get(key) {
    if (this.cacheMap.has(key)) {
      const node = this.cacheMap.get(key);
      this._remove(node);
      this._insertAtHead(node);
      return node.value;
    }
    return -1;
  }

  put(key, value) {
    if (this.cacheMap.has(key)) {
      this._remove(this.cacheMap.get(key));
    }
    const newNode = new DoubleNode(key, value);
    this._insertAtHead(newNode);
    this.cacheMap.set(key, newNode);

    if (this.cacheMap.size > this.capacity) {
      const lruNode = this.tail.previous;
      this._remove(lruNode);
      this.cacheMap.delete(lruNode.key);
    }
  }
}
```
* **Dry Run (Cap=1, Put 1, Put 2):**
    * Put 1: Map={1:N1}. List: H<->N1<->T.
    * Put 2: Add N2 to head. Cap exceeded. Remove tail (N1). Map={2:N2}.
* **Alternative Approach:** In JavaScript, `Map` objects remember insertion order, so you *could* technically use just a `Map`, but the Doubly Linked List is the expected "Senior" answer.

---

## 10. [Latest 2026] Double a Number Represented as a Linked List
[https://leetcode.com/problems/double-a-number-represented-as-a-linked-list/](https://leetcode.com/problems/double-a-number-represented-as-a-linked-list/)

* **Complexity:** Time: $O(n)$ | Space: $O(1)$
* **Key Intuition:** A single pass can handle the doubling and the carry by checking if the *next* node's value will cause a carry (>4).
* **The 'Talk Track':**
    * "Doubling a number is simply $val * 2 \pmod{10}$ plus a carry from the right."
    * "Instead of reversing the list, I'll look ahead: if the next node is $> 4$, the current node needs to add 1 to its doubled value."
    * "I'll handle the potential new head (if the original head is $> 4$) by prepending a node if necessary."
* **Pseudocode:**
    1. If `head.val > 4`, create a new node with value 0 and make it the new head.
    2. Iterate through list:
       a. `currentNode.val = (currentNode.val * 2) % 10`.
       b. If `currentNode.next` exists and `currentNode.next.val > 4`, `currentNode.val += 1`.
    3. Return `head`.
* **JavaScript Solution:**
```javascript
const doubleIt = (headNode) => {
  let currentPointer = headNode;
  
  // Handle overflow at the root
  if (headNode.val > 4) {
    headNode = new ListNode(0, headNode);
    currentPointer = headNode;
  }

  while (currentPointer) {
    currentPointer.val = (currentPointer.val * 2) % 10;
    
    if (currentPointer.next && currentPointer.next.val > 4) {
      currentPointer.val += 1;
    }
    currentPointer = currentPointer.next;
  }

  return headNode;
};
```
* **Dry Run (input = [5]):**
    * 5 > 4: head becomes 0->5. `curr` = 0.
    * Node 0: val = (0*2)%10 = 0. Next (5) > 4, so 0+1=1. `curr` = 5.
    * Node 5: val = (5*2)%10 = 0.
    * Result: [1, 0].
* **Alternative Approach:** Reverse the list, multiply with carry, and reverse back.

---

## 11. [Latest 2026] Insert Greatest Common Divisors in Linked List
[https://leetcode.com/problems/insert-greatest-common-divisors-in-linked-list/](https://leetcode.com/problems/insert-greatest-common-divisors-in-linked-list/)

* **Complexity:** Time: $O(n \cdot \log(min(val)))$ | Space: $O(1)$
* **Key Intuition:** Iterate through the list and for every pair of adjacent nodes, calculate their GCD and insert a new node between them.
* **The 'Talk Track':**
    * "I'll implement the Euclidean algorithm for GCD calculation, which is $O(\log(min(a,b)))$."
    * "Since I'm inserting nodes while traversing, I need to make sure I move the pointer to the 'original' next node to avoid an infinite loop."
    * "This is a straightforward pointer insertion problem combined with a mathematical sub-problem."
* **Pseudocode:**
    1. Define `getGCD(a, b)`: Euclidean algorithm.
    2. `currentNode = head`.
    3. While `currentNode.next`:
       a. `gcdVal = getGCD(currentNode.val, currentNode.next.val)`.
       b. Insert `new ListNode(gcdVal)` between `currentNode` and `currentNode.next`.
       c. Advance `currentNode` two steps.
* **JavaScript Solution:**
```javascript
const insertGreatestCommonDivisors = (headNode) => {
  const calculateGCD = (valueOne, valueTwo) => {
    while (valueTwo !== 0) {
      let temporaryValue = valueTwo;
      valueTwo = valueOne % valueTwo;
      valueOne = temporaryValue;
    }
    return valueOne;
  };

  let currentNode = headNode;

  while (currentNode && currentNode.next) {
    const commonDivisor = calculateGCD(currentNode.val, currentNode.next.val);
    const newNode = new ListNode(commonDivisor);

    // Insertion
    newNode.next = currentNode.next;
    currentNode.next = newNode;

    // Skip the newly inserted node
    currentNode = newNode.next;
  }

  return headNode;
};
```
* **Dry Run (input = [18, 6]):**
    * GCD(18, 6) = 6.
    * List becomes 18 -> 6 -> 6.
    * Return head.
* **Alternative Approach:** We could build a completely new list, but that would use $O(n)$ space unnecessarily.

---
