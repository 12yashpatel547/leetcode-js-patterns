# Senior Engineer's Study Guide: Heaps & Priority Queues

The **Heaps / Priority Queues** pattern is essential for problems involving "Top K" elements, scheduling, or dynamic ordering. As a senior engineer, the focus is on understanding the efficiency of maintaining a partially sorted structure ($O(\log n)$ insertions/removals) versus full sorting ($O(n \log n)$). 

*Note: Since JavaScript does not have a built-in Priority Queue, in a real interview you would either implement a basic version or discuss the Min/Max Heap logic. For this guide, I will provide the semantic logic and clean implementations assuming a standard `MinHeap` or `MaxHeap` class exists where necessary.*

---

## 1. Kth Largest Element in a Stream
[https://leetcode.com/problems/kth-largest-element-in-a-stream/](https://leetcode.com/problems/kth-largest-element-in-a-stream/)

* **Complexity:** Time: $O(N \log K)$ for initialization, $O(\log K)$ per add | Space: $O(K)$
* **Key Intuition:** By maintaining a Min-Heap of size $K$, the root of the heap is always the $K^{th}$ largest element currently in the stream.
* **The 'Talk Track':**
    * "I'll use a Min-Heap because we only care about the $K$ largest values; any value smaller than the root of a $K$-sized Min-Heap can be safely discarded."
    * "By keeping the heap size constrained to $K$, the `add` operation remains efficient at logarithmic time."
    * "This is more performant than sorting the entire collection every time a new value arrives."
* **Pseudocode:**
    1. Initialize a Min-Heap and store `k`.
    2. For each number in the initial array, call the `add` method.
    3. `add(val)` logic: Push `val` to heap. If heap size > `k`, pop the smallest element.
    4. Return the root of the heap.
* **JavaScript Solution:**
```javascript
class KthLargest {
  constructor(kValue, initialNums) {
    this.targetK = kValue;
    this.minHeap = new MinPriorityQueue(); // Conceptual built-in
    
    for (const currentNumber of initialNums) {
      this.add(currentNumber);
    }
  }

  add(newValue) {
    this.minHeap.enqueue(newValue);
    
    if (this.minHeap.size() > this.targetK) {
      this.minHeap.dequeue();
    }
    
    return this.minHeap.front().element;
  }
}
```
* **Dry Run (k=3, nums=[4, 5], add=8):**
    * Init: Heap has [4, 5].
    * Add 8: Heap adds 8 -> [4, 5, 8]. Size is 3 (<= k).
    * Return front: 4.
* **Alternative Approach:** We could maintain a sorted array, but insertion would be $O(N)$ instead of $O(\log K)$.

---

## 2. Last Stone Weight
[https://leetcode.com/problems/last-stone-weight/](https://leetcode.com/problems/last-stone-weight/)

* **Complexity:** Time: $O(N \log N)$ | Space: $O(N)$
* **Key Intuition:** We always need the two largest stones. A Max-Heap allows us to retrieve and remove the two largest elements efficiently in a loop.
* **The 'Talk Track':**
    * "I'll simulate the process using a Max-Heap to ensure I'm always picking the two heaviest stones."
    * "If the two stones are unequal, I'll push the difference back into the heap; if they are equal, they both vanish."
    * "The loop continues until one or zero stones remain."
* **Pseudocode:**
    1. Build a Max-Heap from all stone weights.
    2. While heap size > 1:
       a. Pop `stoneOne` (largest) and `stoneTwo` (second largest).
       b. If `stoneOne !== stoneTwo`, push `stoneOne - stoneTwo` back to heap.
    3. Return the last stone if it exists, else 0.
* **JavaScript Solution:**
```javascript
const lastStoneWeight = (stonesArray) => {
  const maxHeap = new MaxPriorityQueue();
  
  for (const stoneWeight of stonesArray) {
    maxHeap.enqueue(stoneWeight);
  }

  while (maxHeap.size() > 1) {
    const heaviestStone = maxHeap.dequeue().element;
    const secondHeaviestStone = maxHeap.dequeue().element;

    if (heaviestStone !== secondHeaviestStone) {
      maxHeap.enqueue(heaviestStone - secondHeaviestStone);
    }
  }

  return maxHeap.size() === 0 ? 0 : maxHeap.front().element;
};
```
* **Dry Run (input = [2, 4, 1]):**
    * Heap: [4, 2, 1].
    * Pop 4 and 2. Difference 2. Push 2.
    * Heap: [2, 1].
    * Pop 2 and 1. Difference 1. Push 1.
    * Result: 1.
* **Alternative Approach:** Sorting every iteration would be $O(N^2 \log N)$, which is inefficient.

---

## 3. K Closest Points to Origin
[https://leetcode.com/problems/k-closest-points-to-origin/](https://leetcode.com/problems/k-closest-points-to-origin/)

* **Complexity:** Time: $O(N \log K)$ | Space: $O(K)$
* **Key Intuition:** We maintain a Max-Heap of size $K$. If we find a point closer than the current "furthest" point in our heap (the root), we swap them.
* **The 'Talk Track':**
    * "I'll calculate the Euclidean distance squared to avoid the computational cost of `Math.sqrt()` since we only need to compare relative sizes."
    * "By using a Max-Heap of size $K$, we keep the closest points inside and discard the furthest ones."
    * "This is a standard 'Top-K' pattern where a Max-Heap is used to find the minimum values."
* **Pseudocode:**
    1. Define distance function: $x^2 + y^2$.
    2. Initialize Max-Heap based on distance.
    3. Iterate through points:
       a. Add point to heap.
       b. If heap size > $K$, remove the point with the largest distance.
    4. Return the $K$ points remaining in the heap.
* **JavaScript Solution:**
```javascript
const kClosest = (pointsCollection, targetK) => {
  const calculateDistance = (pointCoordinates) => {
    return pointCoordinates[0] ** 2 + pointCoordinates[1] ** 2;
  };

  const maxHeap = new MaxPriorityQueue({
    priority: (point) => calculateDistance(point)
  });

  for (const currentPoint of pointsCollection) {
    maxHeap.enqueue(currentPoint);
    
    if (maxHeap.size() > targetK) {
      maxHeap.dequeue();
    }
  }

  return maxHeap.toArray().map(item => item.element);
};
```
* **Dry Run (points = [[1,3], [-2,2]], k = 1):**
    * Distances: [10, 8].
    * Add [1,3]. Heap size 1.
    * Add [-2,2]. Heap size 2. Pop furthest (10).
    * Result: [[-2,2]].
* **Alternative Approach:** We could use QuickSelect to achieve $O(N)$ average time complexity.

---

## 4. Task Scheduler
[https://leetcode.com/problems/task-scheduler/](https://leetcode.com/problems/task-scheduler/)

* **Complexity:** Time: $O(N)$ (where $N$ is tasks count) | Space: $O(1)$ (max 26 tasks)
* **Key Intuition:** We should always try to schedule the most frequent task first to minimize idle time, using a Max-Heap to track current frequencies and a queue for tasks in "cool-down."
* **The 'Talk Track':**
    * "I'll use a Max-Heap to prioritize tasks with the highest remaining frequency."
    * "I'll use a secondary 'cooldown' queue to hold tasks until they can be executed again based on the interval `n`."
    * "Each iteration represents one unit of time, whether we process a task or stay idle."
* **Pseudocode:**
    1. Count frequencies of tasks.
    2. Add all non-zero frequencies to a Max-Heap.
    3. Initialize `currentTime = 0`, `cooldownQueue = []`.
    4. While heap or queue is not empty:
       a. `currentTime++`.
       b. Pop most frequent from heap, decrement, and if > 0, add to queue with its "available time."
       c. If queue head is available at `currentTime`, move it back to heap.
* **JavaScript Solution:**
```javascript
const leastInterval = (tasksCollection, cooldownPeriod) => {
  const taskCounts = new Map();
  for (const taskLabel of tasksCollection) {
    taskCounts.set(taskLabel, (taskCounts.get(taskLabel) || 0) + 1);
  }

  const maxHeap = new MaxPriorityQueue();
  for (const count of taskCounts.values()) {
    maxHeap.enqueue(count);
  }

  let totalTimeUnits = 0;
  const waitingQueue = []; // format: [remainingCount, availableTime]

  while (maxHeap.size() > 0 || waitingQueue.length > 0) {
    totalTimeUnits++;

    if (maxHeap.size() > 0) {
      const remainingCount = maxHeap.dequeue().element - 1;
      if (remainingCount > 0) {
        waitingQueue.push([remainingCount, totalTimeUnits + cooldownPeriod]);
      }
    }

    if (waitingQueue.length > 0 && waitingQueue[0][1] === totalTimeUnits) {
      maxHeap.enqueue(waitingQueue.shift()[0]);
    }
  }

  return totalTimeUnits;
};
```
* **Dry Run (tasks = [A, A, B], n = 2):**
    * Time 1: Process A (1 left). Queue: [[1, 3]].
    * Time 2: Process B (0 left). Queue: [[1, 3]].
    * Time 3: Process A from queue. 
    * Result: 3.
* **Alternative Approach:** This can be solved with a purely mathematical formula: $(maxFreq - 1) * (n + 1) + numberOfTasksWithMaxFreq$.

---

## 5. Design Twitter
[https://leetcode.com/problems/design-twitter/](https://leetcode.com/problems/design-twitter/)

* **Complexity:** Time: $O(K \log F)$ for feed (where $F$ is following count) | Space: $O(U + T)$
* **Key Intuition:** This is a "Merge K Sorted Lists" problem where each user's tweet list is a sorted list (by timestamp). We use a Max-Heap to pull the most recent 10 tweets.
* **The 'Talk Track':**
    * "I'll store global timestamps to ensure tweets across different users can be compared correctly."
    * "The `getNewsFeed` method is the core complexity; I'll use a Max-Heap to merge the most recent tweets from all followed users."
    * "I'll use Sets for followers to ensure $O(1)$ follow/unfollow and prevent duplicates."
* **Pseudocode:**
    1. Store `tweets` in a Map (userId -> [[timestamp, tweetId]]).
    2. Store `following` in a Map (userId -> Set of followees).
    3. `getNewsFeed(userId)`:
       a. Gather all followees + self.
       b. Use Max-Heap to merge the last 10 tweets from all relevant lists.
* **JavaScript Solution:**
```javascript
class Twitter {
  constructor() {
    this.globalTimer = 0;
    this.userTweetsMap = new Map();
    this.followerMap = new Map();
  }

  postTweet(userId, tweetId) {
    if (!this.userTweetsMap.has(userId)) this.userTweetsMap.set(userId, []);
    this.userTweetsMap.get(userId).push([this.globalTimer++, tweetId]);
  }

  getNewsFeed(userId) {
    const maxHeap = new MaxPriorityQueue({ priority: (tweet) => tweet[0] });
    const followeeSet = this.followerMap.get(userId) || new Set();
    followeeSet.add(userId);

    for (const followeeId of followeeSet) {
      const tweets = this.userTweetsMap.get(followeeId) || [];
      // Optimization: Only take the last 10 from each user
      for (let i = Math.max(0, tweets.length - 10); i < tweets.length; i++) {
        maxHeap.enqueue(tweets[i]);
        if (maxHeap.size() > 10) maxHeap.dequeue(); // Keep 10 smallest in a MIN heap logic, or use Max properly
      }
    }
    // Logic note: To get latest 10, use a MinHeap of size 10 or MaxHeap everything and pop 10.
    // Assuming MinHeap logic for "Top 10 Latest":
    const result = [];
    while (maxHeap.size() > 0) result.unshift(maxHeap.dequeue().element[1]);
    return result;
  }

  follow(followerId, followeeId) {
    if (!this.followerMap.has(followerId)) this.followerMap.set(followerId, new Set());
    this.followerMap.get(followerId).add(followeeId);
  }

  unfollow(followerId, followeeId) {
    if (this.followerMap.has(followerId)) this.followerMap.get(followerId).delete(followeeId);
  }
}
```
* **Dry Run (Post T1, Follow, Get Feed):**
    * Feed merges self tweets and followee tweets into one heap.
    * Heap sorts by timestamp.
    * Return top 10 IDs.
* **Alternative Approach:** One could pre-compute feeds (Fan-out on write), but that increases storage and write latency.

---

## 6. [Latest 2026] Most Frequent IDs
[https://leetcode.com/problems/most-frequent-ids/](https://leetcode.com/problems/most-frequent-ids/)

* **Complexity:** Time: $O(N \log N)$ | Space: $O(N)$
* **Key Intuition:** We need the max frequency after every update. Since frequencies change, we use a Max-Heap to track `[frequency, id]` but perform "lazy removal" (ignore heap roots that don't match the current frequency in our map).
* **The 'Talk Track':**
    * "Since updating a value inside a heap is $O(N)$, I'll use a 'lazy' Max-Heap approach."
    * "I'll store the current source-of-truth frequencies in a Hash Map."
    * "If the root of the heap has a frequency that doesn't match my Map, I know it's stale and I'll pop it until I find a valid one."
* **Pseudocode:**
    1. `currentFrequencies` Map.
    2. `maxHeap` stores `[count, id]`.
    3. For each update `nums[i], freq[i]`:
       a. Update map.
       b. Push `[newCount, id]` to heap.
       c. While `heap.root.count !== map.get(id)`, pop.
       d. Current max is `heap.root.count`.
* **JavaScript Solution:**
```javascript
const mostFrequentIDs = (idArray, frequencyUpdateArray) => {
  const frequencyMap = new Map();
  const maxHeap = new MaxPriorityQueue({ priority: (entry) => entry[0] });
  const result = [];

  for (let currentIndex = 0; currentIndex < idArray.length; currentIndex++) {
    const currentId = idArray[currentIndex];
    const updateAmount = frequencyUpdateArray[currentIndex];
    
    const newCount = (frequencyMap.get(currentId) || 0) + updateAmount;
    frequencyMap.set(currentId, newCount);
    
    maxHeap.enqueue([newCount, currentId]);

    // Lazy removal of stale heap entries
    while (maxHeap.front().element[0] !== frequencyMap.get(maxHeap.front().element[1])) {
      maxHeap.dequeue();
    }

    result.push(maxHeap.front().element[0]);
  }

  return result;
};
```
* **Dry Run (IDs = [1], Freq = [5]):**
    * Map: {1: 5}. Heap: [[5, 1]].
    * Result: [5].
* **Alternative Approach:** A Balanced BST could work but is harder to implement in an interview.

---

## 7. [Latest 2026] Mark Elements on Array by Performing Queries
[https://leetcode.com/problems/mark-elements-on-array-by-performing-queries/](https://leetcode.com/problems/mark-elements-on-array-by-performing-queries/)

* **Complexity:** Time: $O(N \log N + Q \log N)$ | Space: $O(N)$
* **Key Intuition:** We need to find the smallest unmarked elements repeatedly. A Min-Heap storing `[value, index]` allows us to find these efficiently while we keep track of "marked" indices in a boolean array or Set.
* **The 'Talk Track':**
    * "I'll use a Min-Heap to keep all elements sorted by value, and then by index for tie-breaking."
    * "I'll maintain a total sum and subtract values as they get marked to provide $O(1)$ sum reporting for each query."
    * "I must check if an element from the heap is already marked before processing it."
* **Pseudocode:**
    1. Push all `[val, index]` to Min-Heap.
    2. Calculate `totalSum`.
    3. For each query `[idx, k]`:
       a. Mark `idx` if not marked, subtract `nums[idx]` from `totalSum`.
       b. While `k > 0` and heap is not empty:
          i. Pop `[val, heapIdx]`.
          ii. If `heapIdx` is not marked, mark it, subtract `val`, `k--`.
       c. Push `totalSum` to result.
* **JavaScript Solution:**
```javascript
const unmarkedSumQueries = (nums, queries) => {
  const collectionLength = nums.length;
  const isMarked = new Array(collectionLength).fill(false);
  let currentTotalSum = nums.reduce((a, b) => a + b, 0);
  
  const minHeap = new MinPriorityQueue({
    priority: (item) => item[0] * 1000000 + item[1] // Value primary, Index secondary
  });

  for (let i = 0; i < collectionLength; i++) {
    minHeap.enqueue([nums[i], i]);
  }

  const results = [];
  for (const [targetIndex, countK] of queries) {
    if (!isMarked[targetIndex]) {
      isMarked[targetIndex] = true;
      currentTotalSum -= nums[targetIndex];
    }

    let markedInQuery = 0;
    while (markedInQuery < countK && minHeap.size() > 0) {
      const [value, index] = minHeap.dequeue().element;
      if (!isMarked[index]) {
        isMarked[index] = true;
        currentTotalSum -= value;
        markedInQuery++;
      }
    }
    results.push(currentTotalSum);
  }

  return results;
};
```
* **Dry Run (nums = [1, 2, 2], query = [0, 1]):**
    * Sum = 5. Mark index 0. Sum = 4.
    * Mark 1 smallest unmarked (value 2 at index 1). Sum = 2.
    * Result: [2].
* **Alternative Approach:** Sorting the indices initially and using a pointer could work, but a heap is more dynamic if values were changing.

---
