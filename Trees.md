# Senior Engineer's Study Guide: Binary Trees

The **Binary Tree** algorithmic category tests your understanding of recursion, depth-first search (DFS), and breadth-first search (BFS). Success here depends on identifying whether to process nodes top-down (passing information down via arguments) or bottom-up (returning values up the call stack).



---

## 1. Invert Binary Tree
[https://leetcode.com/problems/invert-binary-tree/](https://leetcode.com/problems/invert-binary-tree/)

* **Complexity:** Time: $O(n)$ | Space: $O(h)$ where $h$ is tree height.
* **Key Intuition:** At every node, we simply swap its left and right children and recursively repeat the process for both.
* **The 'Talk Track':**
    * "I'll use a recursive DFS approach here because the problem has a clear self-similar structure."
    * "The base case is a null node, at which point we just return null to the caller."
    * "I'm performing the swap before the recursive calls, but the order wouldn't strictly matter as long as all nodes are visited."
* **Pseudocode:**
    1. If `root` is null, return null.
    2. Swap `root.left` and `root.right`.
    3. Recurse on the new `root.left`.
    4. Recurse on the new `root.right`.
    5. Return `root`.
* **JavaScript Solution:**
```javascript
const invertTree = (root) => {
  if (!root) return null;

  const temporaryLeft = root.left;
  root.left = root.right;
  root.right = temporaryLeft;

  invertTree(root.left);
  invertTree(root.right);

  return root;
};
```
* **Dry Run (root = [1, 2]):**
    * Node 1: Swap left (2) and right (null). Left is now null, Right is 2.
    * Recurse null: returns. Recurse 2: swaps its nulls and returns.
    * Result: [1, null, 2].
* **Alternative Approach:** We could use an iterative BFS with a queue to swap children level by level.

---

## 2. Diameter of Binary Tree
[https://leetcode.com/problems/diameter-of-binary-tree/](https://leetcode.com/problems/diameter-of-binary-tree/)

* **Complexity:** Time: $O(n)$ | Space: $O(h)$
* **Key Intuition:** The longest path through a node is the sum of the heights of its left and right subtrees.
* **The 'Talk Track':**
    * "I'll calculate the height of each node recursively, but update a global maximum diameter as a side effect."
    * "By returning the height ($max(left, right) + 1$), I allow parent nodes to calculate their own diameters efficiently."
    * "This is a bottom-up DFS pattern where information flows from leaves to the root."
* **Pseudocode:**
    1. Initialize `maxDiameter = 0`.
    2. Define `getHeight(node)`:
       a. If null, return 0.
       b. `leftHeight = getHeight(node.left)`.
       c. `rightHeight = getHeight(node.right)`.
       d. Update `maxDiameter` with `leftHeight + rightHeight`.
       e. Return `max(leftHeight, rightHeight) + 1`.
    3. Call `getHeight(root)` and return `maxDiameter`.
* **JavaScript Solution:**
```javascript
const diameterOfBinaryTree = (root) => {
  let maximumDiameterSoFar = 0;

  const calculateHeight = (currentNode) => {
    if (!currentNode) return 0;

    const leftSubtreeHeight = calculateHeight(currentNode.left);
    const rightSubtreeHeight = calculateHeight(currentNode.right);

    maximumDiameterSoFar = Math.max(maximumDiameterSoFar, leftSubtreeHeight + rightSubtreeHeight);

    return Math.max(leftSubtreeHeight, rightSubtreeHeight) + 1;
  };

  calculateHeight(root);
  return maximumDiameterSoFar;
};
```
* **Dry Run (root = [1, 2]):**
    * Node 2: returns height 1, `maxDiameter` = 0.
    * Node 1: `leftHeight`=1, `rightHeight`=0. `maxDiameter` = 1.
    * Return 1.
* **Alternative Approach:** We could calculate height separately for every node, but that would be $O(n^2)$.

---

## 3. Balanced Binary Tree
[https://leetcode.com/problems/balanced-binary-tree/](https://leetcode.com/problems/balanced-binary-tree/)

* **Complexity:** Time: $O(n)$ | Space: $O(h)$
* **Key Intuition:** A tree is balanced if every node's subtrees differ in height by no more than 1 and both subtrees are also balanced.
* **The 'Talk Track':**
    * "I'll use a modified height function that returns -1 if any subtree is unbalanced."
    * "This 'early exit' allows us to stop processing as soon as a violation is found deep in the tree."
    * "This ensures we only visit each node once, maintaining linear time complexity."
* **Pseudocode:**
    1. Define `checkHeight(node)`:
       a. If null, return 0.
       b. `left = checkHeight(node.left)`.
       c. `right = checkHeight(node.right)`.
       d. If `left === -1` or `right === -1` or `abs(left - right) > 1`, return -1.
       e. Return `max(left, right) + 1`.
    2. Return `checkHeight(root) !== -1`.
* **JavaScript Solution:**
```javascript
const isBalanced = (root) => {
  const getBalancedHeight = (currentNode) => {
    if (!currentNode) return 0;

    const leftHeight = getBalancedHeight(currentNode.left);
    if (leftHeight === -1) return -1;

    const rightHeight = getBalancedHeight(currentNode.right);
    if (rightHeight === -1) return -1;

    if (Math.abs(leftHeight - rightHeight) > 1) return -1;

    return Math.max(leftHeight, rightHeight) + 1;
  };

  return getBalancedHeight(root) !== -1;
};
```
* **Dry Run (root = [1, 2, 3]):**
    * Node 2: height 1. Node 3: height 1.
    * Node 1: |1 - 1| = 0. Balanced. Return true.
* **Alternative Approach:** A top-down approach would call `getHeight` repeatedly, resulting in $O(n \log n)$ for a balanced tree.

---

## 4. Same Tree
[https://leetcode.com/problems/same-tree/](https://leetcode.com/problems/same-tree/)

* **Complexity:** Time: $O(n)$ | Space: $O(h)$
* **Key Intuition:** Two trees are identical if their root values are the same and their left and right subtrees are identical.
* **The 'Talk Track':**
    * "I'll handle the base cases first: both null (true), or one null/mismatched values (false)."
    * "Then I'll recursively check the left children and the right children."
    * "This is a simple pre-order traversal comparison."
* **Pseudocode:**
    1. If both `p` and `q` are null, return true.
    2. If one is null or `p.val !== q.val`, return false.
    3. Return `isSameTree(p.left, q.left) && isSameTree(p.right, q.right)`.
* **JavaScript Solution:**
```javascript
const isSameTree = (treeA, treeB) => {
  if (!treeA && !treeB) return true;
  if (!treeA || !treeB || treeA.val !== treeB.val) return false;

  return (
    isSameTree(treeA.left, treeB.left) && 
    isSameTree(treeA.right, treeB.right)
  );
};
```
* **Dry Run (p=[1], q=[1, 2]):**
    * Roots match (1). Recurse left: `p.left` is null, `q.left` is 2. Mismatch! Return false.
* **Alternative Approach:** We could use BFS with two queues and compare nodes level by level.

---

## 5. Subtree of Another Tree
[https://leetcode.com/problems/subtree-of-another-tree/](https://leetcode.com/problems/subtree-of-another-tree/)

* **Complexity:** Time: $O(n \cdot m)$ | Space: $O(h)$
* **Key Intuition:** For every node in the main tree, we check if the tree starting there is identical to the subRoot.
* **The 'Talk Track':**
    * "I'll leverage the `isSameTree` helper function I previously wrote."
    * "The logic is: either this current node is the root of the subtree, or the subtree exists in my left or right branches."
    * "I'll handle the base case where the main tree becomes null but the subRoot doesn't."
* **Pseudocode:**
    1. If `subRoot` is null, return true (null is a subtree of everything).
    2. If `root` is null, return false.
    3. If `isSameTree(root, subRoot)`, return true.
    4. Return `isSubtree(root.left, subRoot) || isSubtree(root.right, subRoot)`.
* **JavaScript Solution:**
```javascript
const isSubtree = (root, subRoot) => {
  if (!subRoot) return true;
  if (!root) return false;

  const isSame = (treeA, treeB) => {
    if (!treeA && !treeB) return true;
    if (!treeA || !treeB || treeA.val !== treeB.val) return false;
    return isSame(treeA.left, treeB.left) && isSame(treeA.right, treeB.right);
  };

  if (isSame(root, subRoot)) return true;

  return isSubtree(root.left, subRoot) || isSubtree(root.right, subRoot);
};
```
* **Dry Run (root=[1, 2], subRoot=[2]):**
    * `isSame(1, 2)`: false.
    * `isSubtree(1.left, 2)`: `isSame(2, 2)` is true. Return true.
* **Alternative Approach:** We could serialize both trees into strings with special delimiters and use a string matching algorithm like KMP for $O(n + m)$.

---

## 6. Lowest Common Ancestor of a Binary Search Tree
[https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

* **Complexity:** Time: $O(h)$ | Space: $O(1)$ (iterative) or $O(h)$ (recursive)
* **Key Intuition:** In a BST, the LCA is the first node where the two target values "split" (one is smaller, one is larger).
* **The 'Talk Track':**
    * "I'll utilize the BST property: left is smaller, right is larger."
    * "If both targets are smaller than current, the LCA must be in the left subtree; if both are larger, the right."
    * "If they aren't both on the same side, the current node is the 'split' point and thus the LCA."
* **Pseudocode:**
    1. While `currentNode` exists:
       a. If `p` and `q` are both smaller than `currentNode.val`, move `currentNode` to `left`.
       b. If `p` and `q` are both larger than `currentNode.val`, move `currentNode` to `right`.
       c. Else, return `currentNode`.
* **JavaScript Solution:**
```javascript
const lowestCommonAncestor = (root, p, q) => {
  let currentNode = root;

  while (currentNode) {
    if (p.val < currentNode.val && q.val < currentNode.val) {
      currentNode = currentNode.left;
    } else if (p.val > currentNode.val && q.val > currentNode.val) {
      currentNode = currentNode.right;
    } else {
      return currentNode;
    }
  }
};
```
* **Dry Run (root=[2, 1, 3], p=1, q=3):**
    * Root 2: 1 < 2 and 3 > 2. This is the split. Return 2.
* **Alternative Approach:** We could use the general Binary Tree LCA approach, but it wouldn't take advantage of the $O(\log n)$ BST property.

---

## 7. Convert Sorted Array to Binary Search Tree
[https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

* **Complexity:** Time: $O(n)$ | Space: $O(\log n)$
* **Key Intuition:** To keep the BST height-balanced, the middle element of the sorted array must be the root.
* **The 'Talk Track':**
    * "I'll use a binary search-like approach, picking the middle as the root and recursing on the left and right halves."
    * "I'll use indices (`left`, `right`) instead of slicing the array to keep the space complexity optimal."
    * "The base case is when `left > right`, meaning there are no elements to process."
* **Pseudocode:**
    1. Helper `build(left, right)`:
       a. If `left > right`, return null.
       b. `mid = Math.floor((left + right) / 2)`.
       c. `node = new TreeNode(nums[mid])`.
       d. `node.left = build(left, mid - 1)`.
       e. `node.right = build(mid + 1, right)`.
       f. Return `node`.
* **JavaScript Solution:**
```javascript
const sortedArrayToBST = (nums) => {
  const buildSubtree = (leftIndex, rightIndex) => {
    if (leftIndex > rightIndex) return null;

    const middleIndex = Math.floor((leftIndex + rightIndex) / 2);
    const currentNode = new TreeNode(nums[middleIndex]);

    currentNode.left = buildSubtree(leftIndex, middleIndex - 1);
    currentNode.right = buildSubtree(middleIndex + 1, rightIndex);

    return currentNode;
  };

  return buildSubtree(0, nums.length - 1);
};
```
* **Dry Run (nums = [1, 2]):**
    * `mid` = 0. Node(1). `left` = null. `right` = build(1, 1).
    * `mid` = 1. Node(2). `left` = null. `right` = null.
    * Result: [1, null, 2].
* **Alternative Approach:** We could pick the middle-right element as root, resulting in a slightly different but still balanced BST.

---

## 8. Binary Tree Level Order Traversal
[https://leetcode.com/problems/binary-tree-level-order-traversal/](https://leetcode.com/problems/binary-tree-level-order-traversal/)



* **Complexity:** Time: $O(n)$ | Space: $O(n)$ (width of the tree)
* **Key Intuition:** Use a queue to process nodes level-by-level, keeping track of the queue's length to distinguish between levels.
* **The 'Talk Track':**
    * "BFS is the standard for level-order problems; I'll use a queue to store nodes for processing."
    * "The key is capturing the `queue.length` at the start of each level's iteration so I only process nodes belonging to that specific level."
    * "I'll push the values into a sub-array and add that sub-array to my final result."
* **Pseudocode:**
    1. If `root` null, return [].
    2. `queue = [root]`, `result = []`.
    3. While `queue` not empty:
       a. `levelSize = queue.length`.
       b. `currentLevel = []`.
       c. Loop `levelSize` times:
          i. `node = queue.shift()`.
          ii. Push `node.val` to `currentLevel`.
          iii. Push children to `queue`.
       d. Push `currentLevel` to `result`.
* **JavaScript Solution:**
```javascript
const levelOrder = (root) => {
  if (!root) return [];
  
  const traversalResult = [];
  const nodeQueue = [root];

  while (nodeQueue.length > 0) {
    const levelSize = nodeQueue.length;
    const currentLevelValues = [];

    for (let i = 0; i < levelSize; i++) {
      const currentNode = nodeQueue.shift();
      currentLevelValues.push(currentNode.val);

      if (currentNode.left) nodeQueue.push(currentNode.left);
      if (currentNode.right) nodeQueue.push(currentNode.right);
    }
    traversalResult.push(currentLevelValues);
  }

  return traversalResult;
};
```
* **Dry Run (root = [1, 2]):**
    * Level 1: `size`=1. Pop 1. `queue`=[2]. `res`=[[1]].
    * Level 2: `size`=1. Pop 2. `queue`=[]. `res`=[[1], [2]].
* **Alternative Approach:** We could use DFS by passing the `level` depth as an argument and appending values to the corresponding index in the result array.

---

## 9. Binary Tree Right Side View
[https://leetcode.com/problems/binary-tree-right-side-view/](https://leetcode.com/problems/binary-tree-right-side-view/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** The right side view is simply the last element of every level in a level-order traversal.
* **The 'Talk Track':**
    * "I'll use BFS here as it's the most intuitive way to isolate levels."
    * "Instead of storing all values, I'll only grab the very last node processed in each level loop."
    * "Alternatively, a DFS (Root-Right-Left) would also work, picking the first node seen at each new depth."
* **Pseudocode:**
    1. Perform level order traversal (BFS).
    2. In each level loop, if `i === levelSize - 1`, push `node.val` to results.
* **JavaScript Solution:**
```javascript
const rightSideView = (root) => {
  if (!root) return [];
  const visibleNodes = [];
  const queue = [root];

  while (queue.length > 0) {
    const levelSize = queue.length;
    for (let i = 0; i < levelSize; i++) {
      const currentNode = queue.shift();
      if (i === levelSize - 1) {
        visibleNodes.push(currentNode.val);
      }
      if (currentNode.left) queue.push(currentNode.left);
      if (currentNode.right) queue.push(currentNode.right);
    }
  }
  return visibleNodes;
};
```
* **Dry Run (root = [1, 2]):**
    * Level 1: `i`=0. Last node. Push 1.
    * Level 2: `i`=0. Last node. Push 2.
    * Result: [1, 2].
* **Alternative Approach:** Using DFS (Root -> Right -> Left) and checking if `currentDepth === result.length` is often more concise.

---

## 10. Count Good Nodes in Binary Tree
[https://leetcode.com/problems/count-good-nodes-in-binary-tree/](https://leetcode.com/problems/count-good-nodes-in-binary-tree/)

* **Complexity:** Time: $O(n)$ | Space: $O(h)$
* **Key Intuition:** A node is "good" if its value is greater than or equal to the maximum value seen along the path from the root.
* **The 'Talk Track':**
    * "I'll use a recursive DFS and pass the `currentMax` as an argument down the tree."
    * "If the current node is $\ge$ `currentMax`, it's a 'good' node and I'll update the `currentMax` for its children."
    * "This is a top-down DFS approach."
* **Pseudocode:**
    1. Define `dfs(node, currentMax)`:
       a. If null, return 0.
       b. `count = 0`.
       c. If `node.val >= currentMax`, `count = 1`, `currentMax = node.val`.
       d. Return `count + dfs(node.left, currentMax) + dfs(node.right, currentMax)`.
* **JavaScript Solution:**
```javascript
const goodNodes = (root) => {
  const countGood = (currentNode, maximumOnPath) => {
    if (!currentNode) return 0;

    let isGood = 0;
    if (currentNode.val >= maximumOnPath) {
      isGood = 1;
      maximumOnPath = currentNode.val;
    }

    return (
      isGood + 
      countGood(currentNode.left, maximumOnPath) + 
      countGood(currentNode.right, maximumOnPath)
    );
  };

  return countGood(root, -Infinity);
};
```
* **Dry Run (root = [3, 1]):**
    * Root 3: 3 $\ge$ -Inf. `isGood`=1. `max`=3.
    * Node 1: 1 < 3. `isGood`=0.
    * Return 1.
* **Alternative Approach:** We could use an iterative BFS and store the `maxOnPath` inside the queue along with each node.

---

## 11. Validate Binary Search Tree
[https://leetcode.com/problems/validate-binary-search-tree/](https://leetcode.com/problems/validate-binary-search-tree/)

* **Complexity:** Time: $O(n)$ | Space: $O(h)$
* **Key Intuition:** Every node must be within a valid range $(min, max)$, where the range narrows as we move down the tree.
* **The 'Talk Track':**
    * "It's a common mistake to only check a node against its immediate parent; we must check against the global range constraints."
    * "When we go left, the `max` allowed value becomes the parent's value; when we go right, the `min` becomes the parent's value."
    * "I'll use `null` or `Infinity` as initial boundaries to handle any possible integer values in the nodes."
* **Pseudocode:**
    1. Define `validate(node, min, max)`:
       a. If null, return true.
       b. If `node.val <= min` or `node.val >= max`, return false.
       c. Return `validate(node.left, min, node.val) && validate(node.right, node.val, max)`.
* **JavaScript Solution:**
```javascript
const isValidBST = (root) => {
  const validate = (currentNode, lowerBound, upperBound) => {
    if (!currentNode) return true;

    if (
      (lowerBound !== null && currentNode.val <= lowerBound) ||
      (upperBound !== null && currentNode.val >= upperBound)
    ) {
      return false;
    }

    return (
      validate(currentNode.left, lowerBound, currentNode.val) &&
      validate(currentNode.right, currentNode.val, upperBound)
    );
  };

  return validate(root, null, null);
};
```
* **Dry Run (root = [2, 1, 3]):**
    * Root 2: (-Inf, +Inf). Valid.
    * Node 1: (-Inf, 2). Valid.
    * Node 3: (2, +Inf). Valid. Return true.
* **Alternative Approach:** An In-order traversal of a valid BST should produce a strictly increasing sequence of numbers.

---

## 12. Kth Smallest Element in a BST
[https://leetcode.com/problems/kth-smallest-element-in-a-bst/](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

* **Complexity:** Time: $O(h + k)$ | Space: $O(h)$
* **Key Intuition:** An in-order traversal (Left-Root-Right) of a BST visits nodes in ascending order.
* **The 'Talk Track':**
    * "I'll use an iterative in-order traversal with a stack to avoid processing the whole tree if $k$ is small."
    * "I'll traverse to the leftmost node, then start popping and decrementing $k$."
    * "The $k^{th}$ node I pop is my answer."
* **Pseudocode:**
    1. `stack = []`, `currentNode = root`.
    2. While true:
       a. While `currentNode`: push and move left.
       b. `currentNode = stack.pop()`.
       c. `k--`. If `k === 0`, return `currentNode.val`.
       d. `currentNode = currentNode.right`.
* **JavaScript Solution:**
```javascript
const kthSmallest = (root, k) => {
  const nodeStack = [];
  let currentNode = root;

  while (true) {
    while (currentNode) {
      nodeStack.push(currentNode);
      currentNode = currentNode.left;
    }

    currentNode = nodeStack.pop();
    k -= 1;
    if (k === 0) return currentNode.val;

    currentNode = currentNode.right;
  }
};
```
* **Dry Run (root = [2, 1], k = 1):**
    * Stack: [2, 1].
    * Pop 1. `k` becomes 0. Return 1.
* **Alternative Approach:** We could do a full recursive in-order traversal and return the $k-1$ index of the result array.

---

## 13. Construct Binary Tree from Preorder and Inorder Traversal
[https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** The first element in `preorder` is always the root; finding this root in `inorder` tells us the size of the left and right subtrees.
* **The 'Talk Track':**
    * "I'll use a Hash Map to store `inorder` indices for $O(1)$ lookups."
    * "The `preorder` array gives us the nodes in the order they should be created, while the `inorder` array tells us the boundaries of their subtrees."
    * "This is a recursive reconstruction problem."
* **Pseudocode:**
    1. Create `map` of `inorder` values -> indices.
    2. `preIndex = 0`.
    3. `build(left, right)`:
       a. If `left > right`, return null.
       b. `val = preorder[preIndex++]`.
       c. `root = new TreeNode(val)`.
       d. `mid = map.get(val)`.
       e. `root.left = build(left, mid - 1)`.
       f. `root.right = build(mid + 1, right)`.
       g. Return `root`.
* **JavaScript Solution:**
```javascript
const buildTree = (preorder, inorder) => {
  const inorderIndexMap = new Map();
  inorder.forEach((val, index) => inorderIndexMap.set(val, index));

  let preorderPointer = 0;

  const construct = (leftBoundary, rightBoundary) => {
    if (leftBoundary > rightBoundary) return null;

    const rootValue = preorder[preorderPointer];
    const rootNode = new TreeNode(rootValue);
    preorderPointer += 1;

    const splitIndex = inorderIndexMap.get(rootValue);

    rootNode.left = construct(leftBoundary, splitIndex - 1);
    rootNode.right = construct(splitIndex + 1, rightBoundary);

    return rootNode;
  };

  return construct(0, inorder.length - 1);
};
```
* **Dry Run (pre=[1, 2], in=[2, 1]):**
    * `rootValue` = 1. `splitIndex` = 1.
    * `left` = construct(0, 0) -> `rootValue` = 2.
    * `right` = construct(2, 1) -> null.
    * Result: [1, 2].
* **Alternative Approach:** Without a Hash Map, searching for the split index would make the algorithm $O(n^2)$.

---

## 14. [Latest 2026] Smallest String Starting From Leaf
[https://leetcode.com/problems/smallest-string-starting-from-leaf/](https://leetcode.com/problems/smallest-string-starting-from-leaf/)

* **Complexity:** Time: $O(n \cdot h)$ | Space: $O(h)$
* **Key Intuition:** We build strings from root to leaf, reverse them at the leaf, and keep track of the lexicographically smallest result.
* **The 'Talk Track':**
    * "I'll perform a DFS and maintain a string of characters seen along the path."
    * "When I reach a leaf, I'll reverse the path string to represent the leaf-to-root path and compare it with my current global minimum."
    * "The string comparison takes $O(h)$, which is why the time complexity is $O(n \cdot h)$."
* **Pseudocode:**
    1. `smallest = null`.
    2. `dfs(node, path)`:
       a. If null, return.
       b. `path = char(node.val) + path`.
       c. If leaf: update `smallest`.
       d. Recurse left and right.
* **JavaScript Solution:**
```javascript
const smallestFromLeaf = (root) => {
  let smallestString = "";

  const dfs = (currentNode, currentPath) => {
    if (!currentNode) return;

    // Convert 0-25 to 'a'-'z'
    const char = String.fromCharCode(currentNode.val + 97);
    const newPath = char + currentPath;

    if (!currentNode.left && !currentNode.right) {
      if (smallestString === "" || newPath < smallestString) {
        smallestString = newPath;
      }
      return;
    }

    dfs(currentNode.left, newPath);
    dfs(currentNode.right, newPath);
  };

  dfs(root, "");
  return smallestString;
};
```
* **Dry Run (root = [0, 1]):**
    * Node 0: `path` = "a".
    * Node 1: `path` = "ba". Leaf. `smallest` = "ba".
    * Return "ba".
* **Alternative Approach:** You could use BFS, but it would require more memory to store the paths for every node in the queue.

---

## 15. [Latest 2026] Find Distance in a Binary Tree
[https://leetcode.com/problems/find-distance-in-a-binary-tree/](https://leetcode.com/problems/find-distance-in-a-binary-tree/)

* **Complexity:** Time: $O(n)$ | Space: $O(h)$
* **Key Intuition:** The distance between two nodes $p$ and $q$ is $dist(root, p) + dist(root, q) - 2 \cdot dist(root, LCA)$.
* **The 'Talk Track':**
    * "First, I'll find the Lowest Common Ancestor (LCA) using the standard recursive approach."
    * "Then, I'll write a helper function to find the depth of a node relative to a given starting node."
    * "The distance is simply the sum of the depths of $p$ and $q$ from the LCA."
* **Pseudocode:**
    1. Find `lca` of `p` and `q`.
    2. Define `getDist(node, target, depth)`: returns `depth` if `node === target`.
    3. Return `getDist(lca, p, 0) + getDist(lca, q, 0)`.
* **JavaScript Solution:**
```javascript
const findDistance = (root, p, q) => {
  if (p === q) return 0;

  const findLCA = (node, valP, valQ) => {
    if (!node || node.val === valP || node.val === valQ) return node;
    const left = findLCA(node.left, valP, valQ);
    const right = findLCA(node.right, valP, valQ);
    if (left && right) return node;
    return left || right;
  };

  const getDepth = (node, targetValue, currentDepth) => {
    if (!node) return -1;
    if (node.val === targetValue) return currentDepth;
    
    const leftDist = getDepth(node.left, targetValue, currentDepth + 1);
    if (leftDist !== -1) return leftDist;
    
    return getDepth(node.right, targetValue, currentDepth + 1);
  };

  const lcaNode = findLCA(root, p, q);
  return getDepth(lcaNode, p, 0) + getDepth(lcaNode, q, 0);
};
```
* **Dry Run (root=[1, 2, 3], p=2, q=3):**
    * LCA is 1.
    * `dist(1, 2)` = 1. `dist(1, 3)` = 1.
    * Total = 2.
* **Alternative Approach:** We could convert the tree to an adjacency list (Graph) and use BFS to find the shortest path between the two nodes.

---
