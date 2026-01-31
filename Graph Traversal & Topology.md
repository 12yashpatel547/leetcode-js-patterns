# Senior Engineer's Study Guide: Graph Traversal & Topology

The **Graph** pattern involves exploring nodes and edges using Depth-First Search (DFS), Breadth-First Search (BFS), or Union-Find. As a senior engineer, the focus is on efficient state management (e.g., in-place modification vs. `Visited` sets), identifying cycle detection in directed vs. undirected graphs, and choosing between BFS for shortest paths and DFS for connectivity.

---

## 1. Number of Islands
[https://leetcode.com/problems/number-of-islands/](https://leetcode.com/problems/number-of-islands/)

* **Complexity:** Time: $O(Rows \times Cols)$ | Space: $O(Rows \times Cols)$
* **Key Intuition:** We treat the grid as an adjacency list where each '1' is a node; a single DFS/BFS traversal visits all connected '1's, effectively "sinking" the island.
* **The 'Talk Track':**
    * "I'll iterate through the grid, and every time I encounter an unvisited '1', I'll trigger a traversal to mark the entire island."
    * "To save space, I can sink the island by changing '1's to '0's in-place, assuming the interviewer allows mutation of the input."
    * "I'll use a helper function to handle the bounds checking and the recursive DFS calls to keep the main logic clean."
* **Pseudocode:**
    1. Iterate through every cell in the `grid`.
    2. If cell is '1', increment `islandCount` and call `dfsSink(row, col)`.
    3. `dfsSink` logic: If out of bounds or cell is '0', return.
    4. Set current cell to '0'.
    5. Recursively call `dfsSink` for Up, Down, Left, Right.
* **JavaScript Solution:**
```javascript
const numIslands = (grid) => {
  let islandCount = 0;
  const totalRows = grid.length;
  const totalCols = grid[0].length;

  const exploreAndSinkIsland = (currentRow, currentCol) => {
    const isOutOfBounds = currentRow < 0 || currentCol < 0 || 
                         currentRow >= totalRows || currentCol >= totalCols;
    
    if (isOutOfBounds || grid[currentRow][currentCol] === '0') {
      return;
    }

    grid[currentRow][currentCol] = '0'; // Sink the land

    exploreAndSinkIsland(currentRow + 1, currentCol);
    exploreAndSinkIsland(currentRow - 1, currentCol);
    exploreAndSinkIsland(currentRow, currentCol + 1);
    exploreAndSinkIsland(currentRow, currentCol - 1);
  };

  for (let rowIndex = 0; rowIndex < totalRows; rowIndex++) {
    for (let colIndex = 0; colIndex < totalCols; colIndex++) {
      if (grid[rowIndex][colIndex] === '1') {
        islandCount++;
        exploreAndSinkIsland(rowIndex, colIndex);
      }
    }
  }

  return islandCount;
};
```
* **Dry Run (input = [["1","1"],["0","0"]]):**
    * (0,0) is '1'. `count`=1. Sink (0,0) and its neighbor (0,1).
    * (0,1) is now '0', (1,0) and (1,1) are '0'.
    * Loop completes. Result: 1.
* **Alternative Approach:** We could use a Breadth-First Search with a queue to avoid the risks of stack overflow on extremely large grids.

---

## 2. Clone Graph
[https://leetcode.com/problems/clone-graph/](https://leetcode.com/problems/clone-graph/)

* **Complexity:** Time: $O(Nodes + Edges)$ | Space: $O(Nodes)$
* **Key Intuition:** We use a Hash Map to map original nodes to their corresponding clones to handle cycles and ensure each node is instantiated exactly once.
* **The 'Talk Track':**
    * "I'll use a recursive DFS approach combined with a `clonedNodesMap` to keep track of already visited nodes."
    * "Before exploring a neighbor, I check the map: if it exists, I link to the clone; if not, I create it."
    * "This ensures that cycles in the graph don't lead to infinite recursion."
* **Pseudocode:**
    1. If the `startNode` is null, return null.
    2. Create a `clonedNodesMap` (Original -> Clone).
    3. Define `dfsClone(node)`:
       a. If `node` is in map, return `map.get(node)`.
       b. Create `newNodeCopy` with `node.val`.
       c. Add to map.
       d. For each `neighbor` in `node.neighbors`, push `dfsClone(neighbor)` to `newNodeCopy.neighbors`.
    4. Return `dfsClone(startNode)`.
* **JavaScript Solution:**
```javascript
const cloneGraph = (startNode) => {
  const clonedNodesMap = new Map();

  const traverseAndClone = (originalNode) => {
    if (!originalNode) return null;
    if (clonedNodesMap.has(originalNode)) {
      return clonedNodesMap.get(originalNode);
    }

    const newNodeClone = new Node(originalNode.val);
    clonedNodesMap.set(originalNode, newNodeClone);

    for (const neighboringNode of originalNode.neighbors) {
      newNodeClone.neighbors.push(traverseAndClone(neighboringNode));
    }

    return newNodeClone;
  };

  return traverseAndClone(startNode);
};
```
* **Dry Run (input = Node1 <-> Node2):**
    * Call `dfs(N1)`. Map: {N1: C1}. Neighbors: [dfs(N2)].
    * Call `dfs(N2)`. Map: {N1: C1, N2: C2}. Neighbors: [dfs(N1)].
    * `dfs(N1)` returns C1 from map. C2 neighbors: [C1].
    * C1 neighbors: [C2]. Result: C1.
* **Alternative Approach:** We could use BFS with a queue, which would be better if the graph is very deep but not very wide.

---

## 3. Course Schedule
[https://leetcode.com/problems/course-schedule/](https://leetcode.com/problems/course-schedule/)

* **Complexity:** Time: $O(Nodes + Edges)$ | Space: $O(Nodes + Edges)$
* **Key Intuition:** This is a cycle detection problem in a directed graph; if a cycle exists, topological sorting is impossible.
* **The 'Talk Track':**
    * "I'll model the courses as a directed graph where an edge represents a prerequisite."
    * "I'll use Kahn's Algorithm (BFS) by tracking the 'in-degree' of each node; nodes with 0 in-degree can be taken immediately."
    * "If the number of nodes processed is less than the total courses, a cycle must exist."
* **Pseudocode:**
    1. Build an `adjacencyList` and an `inDegreeArray`.
    2. Add all nodes with `inDegree === 0` to a `processingQueue`.
    3. While `processingQueue` is not empty:
       a. `course = queue.shift()`, increment `completedCount`.
       b. For each `neighbor` of `course`, decrement `inDegreeArray[neighbor]`.
       c. If `inDegreeArray[neighbor] === 0`, push to queue.
    4. Return `completedCount === numCourses`.
* **JavaScript Solution:**
```javascript
const canFinish = (numCourses, prerequisites) => {
  const adjacencyList = Array.from({ length: numCourses }, () => []);
  const inDegreeArray = new Array(numCourses).fill(0);

  for (const [course, prerequisite] of prerequisites) {
    adjacencyList[prerequisite].push(course);
    inDegreeArray[course]++;
  }

  const processingQueue = [];
  for (let courseIndex = 0; courseIndex < numCourses; courseIndex++) {
    if (inDegreeArray[courseIndex] === 0) {
      processingQueue.push(courseIndex);
    }
  }

  let completedCoursesCount = 0;
  while (processingQueue.length > 0) {
    const currentCourse = processingQueue.shift();
    completedCoursesCount++;

    for (const dependentCourse of adjacencyList[currentCourse]) {
      inDegreeArray[dependentCourse]--;
      if (inDegreeArray[dependentCourse] === 0) {
        processingQueue.push(dependentCourse);
      }
    }
  }

  return completedCoursesCount === numCourses;
};
```
* **Dry Run (input = 2, [[1,0]]):**
    * `inDegree`: [0, 1]. `adj`: {0:[1], 1:[]}.
    * `queue`: [0].
    * Pop 0. `count`=1. Neighbor 1 `inDegree` becomes 0. `queue`: [1].
    * Pop 1. `count`=2.
    * Return 2 === 2 (true).
* **Alternative Approach:** We could use DFS with three states (unvisited, visiting, visited) to detect back-edges.

---

## 4. Redundant Connection
[https://leetcode.com/problems/redundant-connection/](https://leetcode.com/problems/redundant-connection/)

* **Complexity:** Time: $O(N \cdot \alpha(N))$ | Space: $O(N)$
* **Key Intuition:** We use the Disjoint Set Union (DSU) pattern to find the first edge that connects two nodes already belonging to the same component.
* **The 'Talk Track':**
    * "I'll use Union-Find with path compression and union by rank to keep the operations nearly constant time."
    * "For each edge, I'll attempt to 'union' the two nodes; if they already share the same root, this edge is redundant."
    * "Since the problem asks for the *last* such edge in the input, DSU is perfect because it processes edges in order."
* **Pseudocode:**
    1. Initialize `parent` array where `parent[i] = i`.
    2. Define `findRoot(node)` with path compression.
    3. For each `[nodeA, nodeB]` in `edges`:
       a. `rootA = findRoot(nodeA)`, `rootB = findRoot(nodeB)`.
       b. If `rootA === rootB`, return `[nodeA, nodeB]`.
       c. Else, `parent[rootA] = rootB`.
* **JavaScript Solution:**
```javascript
const findRedundantConnection = (edges) => {
  const collectionLength = edges.length;
  const parentArray = Array.from({ length: collectionLength + 1 }, (_, index) => index);

  const findRoot = (nodeIndex) => {
    if (parentArray[nodeIndex] === nodeIndex) {
      return nodeIndex;
    }
    // Path compression
    parentArray[nodeIndex] = findRoot(parentArray[nodeIndex]);
    return parentArray[nodeIndex];
  };

  const unionNodes = (nodeA, nodeB) => {
    const rootA = findRoot(nodeA);
    const rootB = findRoot(nodeB);

    if (rootA === rootB) return false;

    parentArray[rootA] = rootB;
    return true;
  };

  for (const [nodeA, nodeB] of edges) {
    if (!unionNodes(nodeA, nodeB)) {
      return [nodeA, nodeB];
    }
  }
};
```
* **Dry Run (input = [[1,2], [1,3], [2,3]]):**
    * Edge [1,2]: Union 1 and 2. `parent`: [0, 2, 2, 3].
    * Edge [1,3]: Union 2 and 3. `parent`: [0, 2, 3, 3].
    * Edge [2,3]: `find(2)` is 3, `find(3)` is 3. Same root!
    * Return [2,3].
* **Alternative Approach:** We could use DFS to check if `nodeB` is reachable from `nodeA` before adding the edge, but that would be $O(N^2)$.

# Senior Engineer's Study Guide: Graph Traversal & Topology (Part 2)

Continuing our analysis of the **Graph** pattern, this section covers connectivity, shortest paths in unweighted grids (BFS), and border-to-center traversal strategies.

---

## 6. Max Area of Island
[https://leetcode.com/problems/max-area-of-island/](https://leetcode.com/problems/max-area-of-island/)

* **Complexity:** Time: $O(Rows \times Cols)$ | Space: $O(Rows \times Cols)$
* **Key Intuition:** Similar to Number of Islands, but instead of counting occurrences, we return the sum of all recursive calls to calculate the total mass of each connected component.
* **The 'Talk Track':**
    * "I'll return the area of each island during the DFS traversal by summing 1 (for the current cell) with the results of the four neighboring recursive calls."
    * "To avoid double-counting or infinite loops, I'll sink the land by setting `grid[r][c] = 0` as soon as it's visited."
    * "I'll maintain a global `maxArea` variable that updates only if the area of the current island exceeds the previous maximum."
* **Pseudocode:**
    1. Initialize `maximumAreaFound = 0`.
    2. Iterate through every cell. If cell is 1:
       a. `currentArea = calculateArea(row, col)`.
       b. `maximumAreaFound = max(maximumAreaFound, currentArea)`.
    3. `calculateArea(r, c)`:
       a. If out of bounds or cell is 0, return 0.
       b. Set cell to 0.
       c. Return `1 + area(up) + area(down) + area(left) + area(right)`.
* **JavaScript Solution:**
```javascript
const maxAreaOfIsland = (grid) => {
  let maximumAreaFound = 0;
  const totalRows = grid.length;
  const totalCols = grid[0].length;

  const calculateIslandArea = (currentRow, currentCol) => {
    if (
      currentRow < 0 || currentCol < 0 || 
      currentRow >= totalRows || currentCol >= totalCols || 
      grid[currentRow][currentCol] === 0
    ) {
      return 0;
    }

    grid[currentRow][currentCol] = 0; // Mark as visited

    return (
      1 +
      calculateIslandArea(currentRow + 1, currentCol) +
      calculateIslandArea(currentRow - 1, currentCol) +
      calculateIslandArea(currentRow, currentCol + 1) +
      calculateIslandArea(currentRow, currentCol - 1)
    );
  };

  for (let rowIndex = 0; rowIndex < totalRows; rowIndex++) {
    for (let colIndex = 0; colIndex < totalCols; colIndex++) {
      if (grid[rowIndex][colIndex] === 1) {
        maximumAreaFound = Math.max(maximumAreaFound, calculateIslandArea(rowIndex, colIndex));
      }
    }
  }

  return maximumAreaFound;
};
```
* **Dry Run (input = [[1, 1], [1, 0]]):**
    * (0,0) is 1. Start DFS.
    * (0,0) + (0,1) + (1,0) = 3 units.
    * All these become 0. Loop continues but finds no more 1s.
    * Result: 3.
* **Alternative Approach:** We could use a Disjoint Set Union (DSU) and track the `size` of each set, which is useful if the grid is being updated dynamically.

---

## 7. Pacific Atlantic Water Flow
[https://leetcode.com/problems/pacific-atlantic-water-flow/](https://leetcode.com/problems/pacific-atlantic-water-flow/)

* **Complexity:** Time: $O(Rows \times Cols)$ | Space: $O(Rows \times Cols)$
* **Key Intuition:** Instead of checking every cell, we work backwards from the oceans. We perform DFS from the edges and find the intersection of cells that can reach both oceans.
* **The 'Talk Track':**
    * "Water flows from high to low, so if we start from the ocean (low) and move inland, we only move to cells of equal or greater height."
    * "I'll maintain two separate boolean grids: `canReachPacific` and `canReachAtlantic`."
    * "The result is the set of coordinates where both boolean grids are true."
* **Pseudocode:**
    1. Initialize `pacificVisited` and `atlanticVisited` boolean matrices.
    2. Run DFS from top and left edges for Pacific.
    3. Run DFS from bottom and right edges for Atlantic.
    4. DFS condition: `neighborHeight >= currentHeight`.
    5. Iterate through grid: if `pacificVisited[r][c] && atlanticVisited[r][c]`, add to result.
* **JavaScript Solution:**
```javascript
const pacificAtlantic = (heights) => {
  const totalRows = heights.length;
  const totalCols = heights[0].length;
  const reachesPacific = Array.from({ length: totalRows }, () => new Array(totalCols).fill(false));
  const reachesAtlantic = Array.from({ length: totalRows }, () => new Array(totalCols).fill(false));

  const traverseInland = (currentRow, currentCol, visitedMatrix, previousHeight) => {
    if (
      currentRow < 0 || currentCol < 0 || 
      currentRow >= totalRows || currentCol >= totalCols ||
      visitedMatrix[currentRow][currentCol] || 
      heights[currentRow][currentCol] < previousHeight
    ) {
      return;
    }

    visitedMatrix[currentRow][currentCol] = true;

    traverseInland(currentRow + 1, currentCol, visitedMatrix, heights[currentRow][currentCol]);
    traverseInland(currentRow - 1, currentCol, visitedMatrix, heights[currentRow][currentCol]);
    traverseInland(currentRow, currentCol + 1, visitedMatrix, heights[currentRow][currentCol]);
    traverseInland(currentRow, currentCol - 1, visitedMatrix, heights[currentRow][currentCol]);
  };

  for (let colIndex = 0; colIndex < totalCols; colIndex++) {
    traverseInland(0, colIndex, reachesPacific, heights[0][colIndex]);
    traverseInland(totalRows - 1, colIndex, reachesAtlantic, heights[totalRows - 1][colIndex]);
  }
  for (let rowIndex = 0; rowIndex < totalRows; rowIndex++) {
    traverseInland(rowIndex, 0, reachesPacific, heights[rowIndex][0]);
    traverseInland(rowIndex, totalCols - 1, reachesAtlantic, heights[rowIndex][totalCols - 1]);
  }

  const resultCoordinates = [];
  for (let rowIndex = 0; rowIndex < totalRows; rowIndex++) {
    for (let colIndex = 0; colIndex < totalCols; colIndex++) {
      if (reachesPacific[rowIndex][colIndex] && reachesAtlantic[rowIndex][colIndex]) {
        resultCoordinates.push([rowIndex, colIndex]);
      }
    }
  }

  return resultCoordinates;
};
```
* **Dry Run (input = [[1, 2], [2, 1]]):**
    * Pacific reaches (0,0), (0,1), (1,0).
    * Atlantic reaches (1,1), (0,1), (1,0).
    * Intersection: [[0,1], [1,0]].
* **Alternative Approach:** BFS could be used starting from all ocean-bordering cells simultaneously.

---

## 8. Surrounded Regions
[https://leetcode.com/problems/surrounded-regions/](https://leetcode.com/problems/surrounded-regions/)

* **Complexity:** Time: $O(Rows \times Cols)$ | Space: $O(Rows \times Cols)$
* **Key Intuition:** Any 'O' connected to the border cannot be captured. We find these "safe" regions first, then flip everything else.
* **The 'Talk Track':**
    * "The key insight is identifying which 'O's are *not* surrounded: specifically those on the border and their 'O' neighbors."
    * "I'll temporarily mark safe 'O's with a placeholder (like 'T') using DFS."
    * "In the final pass, 'T' becomes 'O' and any remaining 'O' becomes 'X'."
* **Pseudocode:**
    1. Iterate over border cells. If 'O', run `dfsMarkSafe(r, c)`.
    2. `dfsMarkSafe`: Change 'O' to 'T' and recurse on neighbors.
    3. Re-iterate through entire grid:
       a. If 'O', change to 'X'.
       b. If 'T', change back to 'O'.
* **JavaScript Solution:**
```javascript
const solve = (board) => {
  const totalRows = board.length;
  const totalCols = board[0].length;

  const markAsSafe = (currentRow, currentCol) => {
    if (
      currentRow < 0 || currentCol < 0 || 
      currentRow >= totalRows || currentCol >= totalCols || 
      board[currentRow][currentCol] !== 'O'
    ) {
      return;
    }

    board[currentRow][currentCol] = 'T'; // Temporary mark

    markAsSafe(currentRow + 1, currentCol);
    markAsSafe(currentRow - 1, currentCol);
    markAsSafe(currentRow, currentCol + 1);
    markAsSafe(currentRow, currentCol - 1);
  };

  // 1. Mark border-connected 'O's
  for (let rowIndex = 0; rowIndex < totalRows; rowIndex++) {
    markAsSafe(rowIndex, 0);
    markAsSafe(rowIndex, totalCols - 1);
  }
  for (let colIndex = 0; colIndex < totalCols; colIndex++) {
    markAsSafe(0, colIndex);
    markAsSafe(totalRows - 1, colIndex);
  }

  // 2. Flip cells
  for (let rowIndex = 0; rowIndex < totalRows; rowIndex++) {
    for (let colIndex = 0; colIndex < totalCols; colIndex++) {
      if (board[rowIndex][colIndex] === 'O') board[rowIndex][colIndex] = 'X';
      if (board[rowIndex][colIndex] === 'T') board[rowIndex][colIndex] = 'O';
    }
  }
};
```
* **Dry Run (input = [['X','O'],['X','X']]):**
    * Border (0,1) is 'O'. Mark safe: (0,1) = 'T'.
    * Pass 2: 'T' becomes 'O'.
    * Result: [['X','O'],['X','X']]. (No changes, correctly identifying it's not surrounded).
* **Alternative Approach:** We could use Union-Find, connecting all border 'O's to a dummy "Safe" node.

---

## 9. Rotting Oranges
[https://leetcode.com/problems/rotting-oranges/](https://leetcode.com/problems/rotting-oranges/)

* **Complexity:** Time: $O(Rows \times Cols)$ | Space: $O(Rows \times Cols)$
* **Key Intuition:** This is a Multi-Source BFS problem. Since rot spreads simultaneously, BFS naturally tracks the "minutes" as levels of the traversal.
* **The 'Talk Track':**
    * "I'll use a queue to store all initially rotten oranges and a counter for fresh ones."
    * "Each 'level' of the BFS represents one minute passing."
    * "If the fresh orange count isn't zero after the queue is empty, it's impossible to rot everything, so I'll return -1."
* **Pseudocode:**
    1. Count `freshOranges` and add all `[r, c]` of rotten oranges to `queue`.
    2. While `queue` is not empty and `freshOranges > 0`:
       a. For each orange in the current level:
          i. Check neighbors. If neighbor is fresh:
             - Make it rotten.
             - Decrement `freshOranges`.
             - Push to `queue`.
       b. Increment `minutesPassed`.
    3. Return `freshOranges === 0 ? minutesPassed : -1`.
* **JavaScript Solution:**
```javascript
const orangesRotting = (grid) => {
  const totalRows = grid.length;
  const totalCols = grid[0].length;
  const rottingQueue = [];
  let freshOrangeCount = 0;

  for (let rowIndex = 0; rowIndex < totalRows; rowIndex++) {
    for (let colIndex = 0; colIndex < totalCols; colIndex++) {
      if (grid[rowIndex][colIndex] === 2) rottingQueue.push([rowIndex, colIndex]);
      if (grid[rowIndex][colIndex] === 1) freshOrangeCount++;
    }
  }

  if (freshOrangeCount === 0) return 0;

  let minutesElapsed = 0;
  const directions = [[1, 0], [-1, 0], [0, 1], [0, -1]];

  while (rottingQueue.length > 0 && freshOrangeCount > 0) {
    const orangesAtCurrentMinute = rottingQueue.length;
    
    for (let i = 0; i < orangesAtCurrentMinute; i++) {
      const [currentRow, currentCol] = rottingQueue.shift();

      for (const [rowOffset, colOffset] of directions) {
        const neighborRow = currentRow + rowOffset;
        const neighborCol = currentCol + colOffset;

        if (
          neighborRow >= 0 && neighborRow < totalRows &&
          neighborCol >= 0 && neighborCol < totalCols &&
          grid[neighborRow][neighborCol] === 1
        ) {
          grid[neighborRow][neighborCol] = 2;
          freshOrangeCount--;
          rottingQueue.push([neighborRow, neighborCol]);
        }
      }
    }
    minutesElapsed++;
  }

  return freshOrangeCount === 0 ? minutesElapsed : -1;
};
```
* **Dry Run (input = [[2,1],[1,1]]):**
    * `queue`: [[0,0]], `fresh`: 3.
    * Min 1: Rot (0,1) and (1,0). `fresh`: 1. `queue`: [[0,1], [1,0]].
    * Min 2: Rot (1,1). `fresh`: 0.
    * Result: 2.
* **Alternative Approach:** We could use a "shadow grid" to calculate timestamps, but it's less memory-efficient than BFS.

---

## 10. [Latest 2026] All Ancestors of a Node in a Directed Acyclic Graph
[https://leetcode.com/problems/all-ancestors-of-a-node-in-a-directed-acyclic-graph/](https://leetcode.com/problems/all-ancestors-of-a-node-in-a-directed-acyclic-graph/)

* **Complexity:** Time: $O(N \cdot (N + E))$ | Space: $O(N^2)$
* **Key Intuition:** Instead of finding ancestors for each node, we perform a traversal from each node $i$ and mark $i$ as an ancestor for all reachable nodes.
* **The 'Talk Track':**
    * "To avoid redundant work, I'll reverse the perspective: I'll start a DFS from every node $u$ and visit all its descendants $v$."
    * "Each $v$ that I visit for the first time in $u$'s traversal gets $u$ added to its ancestor list."
    * "This naturally keeps the ancestor lists sorted since I'm processing nodes $u$ from 0 to $N-1$."
* **Pseudocode:**
    1. Build `adjacencyList`.
    2. `results` = list of empty arrays.
    3. For `ancestor = 0` to `n-1`:
       a. `dfs(currentNode)`:
          i. Mark `currentNode` as visited for this ancestor.
          ii. If `currentNode !== ancestor`, push `ancestor` to `results[currentNode]`.
          iii. For each `neighbor`, call `dfs`.
    4. Return `results`.
* **JavaScript Solution:**
```javascript
const getAncestors = (numNodes, edges) => {
  const adjacencyList = Array.from({ length: numNodes }, () => []);
  for (const [fromNode, toNode] of edges) {
    adjacencyList[fromNode].push(toNode);
  }

  const resultAncestors = Array.from({ length: numNodes }, () => []);

  const findDescendants = (ancestorId, currentNode, visitedSet) => {
    visitedSet.add(currentNode);

    for (const descendantNode of adjacencyList[currentNode]) {
      if (!visitedSet.has(descendantNode)) {
        resultAncestors[descendantNode].push(ancestorId);
        findDescendants(ancestorId, descendantNode, visitedSet);
      }
    }
  };

  for (let nodeIndex = 0; nodeIndex < numNodes; nodeIndex++) {
    findDescendants(nodeIndex, nodeIndex, new Set());
  }

  return resultAncestors;
};
```
* **Dry Run (input = 2, [[0,1]]):**
    * `i` = 0: `dfs` from 0. Neighbor 1 visited. `res[1]` = [0].
    * `i` = 1: `dfs` from 1. No neighbors.
    * Result: [[], [0]].
* **Alternative Approach:** Use topological sort and merge parent ancestor sets into children, but merging sets can be $O(N^2 \log N)$ or worse.

---

## 11. [Latest 2026] Minimum Cost to Reach Destination in Time
[https://leetcode.com/problems/minimum-cost-to-reach-destination-in-time/](https://leetcode.com/problems/minimum-cost-to-reach-destination-in-time/)

* **Complexity:** Time: $O(E \log E)$ | Space: $O(N \cdot T)$
* **Key Intuition:** This is a shortest path problem (Dijkstra) with a constraint (Time). We need a state defined by `[node, time]` to allow visiting a node multiple times if it offers a lower cost within different time limits.
* **The 'Talk Track':**
    * "Standard Dijkstra finds the cheapest path, but we might need a more expensive path that is faster to stay under the `maxTime`."
    * "I'll track the `minTime` seen so far for each node at a specific cost to prune the search space."
    * "The priority queue will prioritize the lowest `passingFee`."
* **Pseudocode:**
    1. `minTimeAtNode` array initialized to `Infinity`.
    2. `pq` stores `[fee, time, node]`, sorted by `fee`.
    3. While `pq` not empty:
       a. If `time > maxTime`, continue.
       b. If `node === n-1`, return `fee`.
       c. If `time >= minTimeAtNode[node]`, continue.
       d. `minTimeAtNode[node] = time`.
       e. Explore neighbors, adding `edgeTime` and `edgeFee`.
* **JavaScript Solution:**
```javascript
const minCost = (maxTime, edges, passingFees) => {
  const numNodes = passingFees.length;
  const adjacencyList = Array.from({ length: numNodes }, () => []);

  for (const [nodeU, nodeV, timeTaken] of edges) {
    adjacencyList[nodeU].push([nodeV, timeTaken]);
    adjacencyList[nodeV].push([nodeU, timeTaken]);
  }

  // Track the minimum time to reach each node; if we reach it later with a higher cost, we prune
  const minTimeReached = new Array(numNodes).fill(Infinity);
  
  // [totalFee, currentTime, currentNode]
  const priorityQueue = [[passingFees[0], 0, 0]];

  while (priorityQueue.length > 0) {
    priorityQueue.sort((a, b) => a[0] - b[0]); // Maximize efficiency with a real MinHeap
    const [currentFee, currentTime, currentNode] = priorityQueue.shift();

    if (currentTime > maxTime) continue;
    if (currentNode === numNodes - 1) return currentFee;

    // Pruning: If we've reached this node before in less time, this path isn't better
    if (currentTime >= minTimeReached[currentNode]) continue;
    minTimeReached[currentNode] = currentTime;

    for (const [neighborNode, travelTime] of adjacencyList[currentNode]) {
      const nextTime = currentTime + travelTime;
      if (nextTime <= maxTime) {
        priorityQueue.push([currentFee + passingFees[neighborNode], nextTime, neighborNode]);
      }
    }
  }

  return -1;
};
```
* **Dry Run (maxTime=30, edges=[[0,1,10]], fees=[5,10]):**
    * Start: `[5, 0, 0]`.
    * Neighbor 1: `[15, 10, 1]`.
    * Pop 1, match `n-1`. Return 15.
* **Alternative Approach:** Dynamic Programming where `dp[t][i]` is min cost to reach node `i` at time `t`.
