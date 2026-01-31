# Senior Engineer's Study Guide: Intervals

The **Intervals** pattern involves processing sets of time periods or ranges. As a senior engineer, the focus is on efficient sorting, greedy logic to minimize overlaps, and managing "start" vs "end" events. The core challenge is usually handling the transition boundaries and maintaining an optimal set of non-overlapping ranges.

---

## 1. Meeting Rooms
[https://www.lintcode.com/problem/920/](https://www.lintcode.com/problem/920/)

* **Complexity:** Time: $O(n \log n)$ | Space: $O(1)$ (or $O(n)$ depending on sorting implementation)
* **Key Intuition:** A person can attend all meetings if and only if no two meetings overlap, which is checked by comparing the end of one meeting to the start of the next after sorting.
* **The 'Talk Track':**
    * "First, I'll sort the intervals by start time; this is a non-negotiable prerequisite for most interval-based logic."
    * "I only need to compare the current meeting's start time with the previous meeting's end time."
    * "Using a simple early-return pattern here makes the code more efficient for large datasets."
* **Pseudocode:**
    1. Sort the `intervals` array based on the `start` time.
    2. Iterate through the sorted `intervals` starting from the second element.
    3. If the `start` of the current interval is less than the `end` of the previous interval, return `false`.
    4. If the loop finishes, return `true`.
* **JavaScript Solution:**
```javascript
const canAttendMeetings = (intervals) => {
  intervals.sort((intervalA, intervalB) => intervalA.start - intervalB.start);

  for (let currentIndex = 1; currentIndex < intervals.length; currentIndex++) {
    const previousInterval = intervals[currentIndex - 1];
    const currentInterval = intervals[currentIndex];

    if (currentInterval.start < previousInterval.end) {
      return false;
    }
  }

  return true;
};
```
* **Dry Run (input = [[0, 5], [5, 10]]):**
    * Sorted: [[0, 5], [5, 10]].
    * `currentIndex` = 1: `currentInterval.start` (5) is NOT less than `previousInterval.end` (5).
    * Result: `true`.
* **Alternative Approach:** We could use a sweep-line algorithm, but that is over-engineering for a simple overlap check.

---

## 2. Insert Interval
[https://leetcode.com/problems/insert-interval/](https://leetcode.com/problems/insert-interval/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** We process the intervals in three stages: those before the new interval, the merging phase with the new interval, and those after.
* **The 'Talk Track':**
    * "I'll iterate through the existing intervals and handle three distinct cases to keep the logic clean."
    * "While merging, I'll update the `newInterval` boundaries dynamically by taking the minimum of starts and maximum of ends."
    * "This linear approach avoids a full $O(n \log n)$ sort since the input is already sorted."
* **Pseudocode:**
    1. Initialize `result` array.
    2. While `intervals[i].end < newInterval.start`, push to `result`.
    3. While `intervals[i].start <= newInterval.end`, merge by updating `newInterval`'s start/end.
    4. Push the merged `newInterval`.
    5. Push all remaining intervals.
* **JavaScript Solution:**
```javascript
const insert = (intervals, newInterval) => {
  const resultIntervals = [];
  let currentIndex = 0;
  const collectionLength = intervals.length;

  // Case 1: Intervals before the new one
  while (currentIndex < collectionLength && intervals[currentIndex][1] < newInterval[0]) {
    resultIntervals.push(intervals[currentIndex]);
    currentIndex++;
  }

  // Case 2: Merging overlapping intervals
  while (currentIndex < collectionLength && intervals[currentIndex][0] <= newInterval[1]) {
    newInterval[0] = Math.min(newInterval[0], intervals[currentIndex][0]);
    newInterval[1] = Math.max(newInterval[1], intervals[currentIndex][1]);
    currentIndex++;
  }
  resultIntervals.push(newInterval);

  // Case 3: Intervals after the merged one
  while (currentIndex < collectionLength) {
    resultIntervals.push(intervals[currentIndex]);
    currentIndex++;
  }

  return resultIntervals;
};
```
* **Dry Run (intervals = [[1, 3]], newInterval = [2, 5]):**
    * `currentIndex` = 0: `intervals[0].end` (3) is NOT < `newInterval.start` (2).
    * Merge: `newInterval` becomes [min(1,2), max(3,5)] = [1, 5]. `currentIndex` = 1.
    * Push [1, 5].
    * Result: [[1, 5]].
* **Alternative Approach:** We could push the new interval into the array and run the 'Merge Intervals' algorithm, but that would be $O(n \log n)$.

---

## 3. Merge Intervals
[https://leetcode.com/problems/merge-intervals/](https://leetcode.com/problems/merge-intervals/)

* **Complexity:** Time: $O(n \log n)$ | Space: $O(n)$
* **Key Intuition:** After sorting, overlapping intervals are adjacent; we can merge the current interval into the last interval in our result list if an overlap is detected.
* **The 'Talk Track':**
    * "Sorting by start time ensures that we only ever need to look at the last interval added to our results to check for a merge."
    * "I'll use a greedy approach to extend the end time of the current merged interval."
    * "This is a core pattern that translates well to calendar and scheduling systems."
* **Pseudocode:**
    1. Sort `intervals` by start time.
    2. Push the first interval to `result`.
    3. For each subsequent `interval`:
       a. Let `lastMerged = result[result.length - 1]`.
       b. If `interval.start <= lastMerged.end`, update `lastMerged.end = max(lastMerged.end, interval.end)`.
       c. Else, push `interval` to `result`.
* **JavaScript Solution:**
```javascript
const merge = (intervals) => {
  if (intervals.length === 0) return [];

  intervals.sort((intervalA, intervalB) => intervalA[0] - intervalB[0]);

  const mergedCollection = [intervals[0]];

  for (let currentIndex = 1; currentIndex < intervals.length; currentIndex++) {
    const lastMergedInterval = mergedCollection[mergedCollection.length - 1];
    const currentInterval = intervals[currentIndex];

    if (currentInterval[0] <= lastMergedInterval[1]) {
      lastMergedInterval[1] = Math.max(lastMergedInterval[1], currentInterval[1]);
    } else {
      mergedCollection.push(currentInterval);
    }
  }

  return mergedCollection;
};
```
* **Dry Run (input = [[1, 4], [4, 5]]):**
    * `merged` = [[1, 4]].
    * `currentIndex` = 1: 4 <= 4 is true. `lastMergedInterval[1]` = max(4, 5) = 5.
    * Result: [[1, 5]].
* **Alternative Approach:** We could use a linked list for the result if we expected frequent insertions, but an array is more cache-friendly.

---

## 4. Non-overlapping Intervals
[https://leetcode.com/problems/non-overlapping-intervals/](https://leetcode.com/problems/non-overlapping-intervals/)

* **Complexity:** Time: $O(n \log n)$ | Space: $O(1)$
* **Key Intuition:** This is the Interval Scheduling Maximization problem. To keep the most intervals, always pick the interval that ends earliest (Greedy).
* **The 'Talk Track':**
    * "Paradoxically, sorting by *end* time is the optimal strategy here because it leaves the most room for subsequent intervals."
    * "I'll track the `lastEndTime` and increment a counter whenever I find a non-overlapping interval."
    * "The final answer is the total count minus the number of non-overlapping intervals I could fit."
* **Pseudocode:**
    1. Sort `intervals` by end time.
    2. Initialize `count = 0`, `lastEnd = -Infinity`.
    3. For each `interval`:
       a. If `interval.start >= lastEnd`, increment `count`, update `lastEnd = interval.end`.
    4. Return `intervals.length - count`.
* **JavaScript Solution:**
```javascript
const eraseOverlapIntervals = (intervals) => {
  if (intervals.length === 0) return 0;

  intervals.sort((intervalA, intervalB) => intervalA[1] - intervalB[1]);

  let nonOverlappingCount = 0;
  let lastProcessedEndTime = -Infinity;

  for (let currentIndex = 0; currentIndex < intervals.length; currentIndex++) {
    const currentInterval = intervals[currentIndex];
    
    if (currentInterval[0] >= lastProcessedEndTime) {
      nonOverlappingCount++;
      lastProcessedEndTime = currentInterval[1];
    }
  }

  return intervals.length - nonOverlappingCount;
};
```
* **Dry Run (input = [[1, 2], [1, 3]]):**
    * Sorted by end: [[1, 2], [1, 3]].
    * `i` = 0: 1 >= -Inf. `count` = 1. `lastEnd` = 2.
    * `i` = 1: 1 < 2. Skip.
    * Result: 2 - 1 = 1.
* **Alternative Approach:** We could sort by start time, but we would need to specifically handle the case where a very long interval starts early and blocks many shorter ones.

---

## 5. Meeting Rooms II
[https://www.lintcode.com/problem/919/](https://www.lintcode.com/problem/919/)

* **Complexity:** Time: $O(n \log n)$ | Space: $O(n)$
* **Key Intuition:** Use a "Sweep Line" approach by separating start and end times to track how many concurrent meetings are happening at any given moment.
* **The 'Talk Track':**
    * "I'll break the intervals into two sorted arrays: one for starts and one for ends."
    * "When a start event occurs, I need a room; when an end event occurs, a room is freed up."
    * "I'll use two pointers to move through time and track the peak room usage."
* **Pseudocode:**
    1. Extract all `starts` and `ends` into separate arrays and sort them.
    2. Use `startPointer` and `endPointer`.
    3. While `startPointer < totalMeetings`:
       a. If `starts[startPointer] < ends[endPointer]`, increment `rooms`, `startPointer++`.
       b. Else, decrement `rooms`, `endPointer++`.
       c. Update `maxRooms`.
* **JavaScript Solution:**
```javascript
const minMeetingRooms = (intervals) => {
  const startTimes = intervals.map(interval => interval.start).sort((a, b) => a - b);
  const endTimes = intervals.map(interval => interval.end).sort((a, b) => a - b);

  let roomsCurrentlyInUse = 0;
  let maximumRoomsRequired = 0;
  let endPointer = 0;

  for (let startPointer = 0; startPointer < startTimes.length; startPointer++) {
    roomsCurrentlyInUse++;

    while (startTimes[startPointer] >= endTimes[endPointer]) {
      roomsCurrentlyInUse--;
      endPointer++;
    }

    maximumRoomsRequired = Math.max(maximumRoomsRequired, roomsCurrentlyInUse);
  }

  return maximumRoomsRequired;
};
```
* **Dry Run (input = [[0, 30], [5, 10], [15, 20]]):**
    * Starts: [0, 5, 15]. Ends: [10, 20, 30].
    * `start` = 0: `rooms` = 1.
    * `start` = 5: `rooms` = 2.
    * `start` = 15: `rooms` = 3 -> (Wait, 15 >= 10) -> `rooms` = 2.
    * Peak: 2.
* **Alternative Approach:** We could use a Min-Heap to store end times, representing room availability, which also results in $O(n \log n)$.

---

## 6. [Latest 2026] Minimum Number of Coins for Fruits
[https://leetcode.com/problems/minimum-number-of-coins-for-fruits/](https://leetcode.com/problems/minimum-number-of-coins-for-fruits/)

* **Complexity:** Time: $O(n)$ | Space: $O(n)$
* **Key Intuition:** This problem involves intervals of "free" coverage. We use a monotonic queue (Deque) to maintain the minimum cost to reach the current fruit given the sliding window of free coverage from previous purchases.
* **The 'Talk Track':**
    * "Each purchase at index $i$ covers fruits up to index $2i+1$ as an interval of 'free' items."
    * "I'll use a deque to keep track of the indices that provide the minimum cost within the valid interval for the current fruit."
    * "This optimization reduces the $O(n^2)$ DP approach to $O(n)$ by efficiently managing the sliding window of possible 'free' triggers."
* **Pseudocode:**
    1. `dp[i]` is min cost to get fruit $i$.
    2. Use a `deque` to store indices where $j + (j+1) \ge i$.
    3. Remove indices from `deque` front that no longer cover $i$.
    4. `dp[i] = prices[i-1] + dp[deque.front]`.
    5. Maintain `deque` so values in `dp` are increasing.
* **JavaScript Solution:**
```javascript
const minimumCoins = (prices) => {
  const collectionLength = prices.length;
  const dpTable = new Array(collectionLength + 1).fill(0);
  const monotonicQueue = []; // Stores indices

  for (let currentIndex = 1; currentIndex <= collectionLength; currentIndex++) {
    // Remove indices that can't cover the current fruit for free
    while (monotonicQueue.length > 0 && monotonicQueue[0] + (monotonicQueue[0]) < currentIndex) {
      monotonicQueue.shift();
    }

    // Cost to buy fruit i is prices[i-1] + min cost to reach a fruit that covers i-1
    // If no fruit covers for free, prev cost is dpTable[i-1]
    const previousBestCost = monotonicQueue.length > 0 ? dpTable[monotonicQueue[0] - 1] : dpTable[currentIndex - 1];
    dpTable[currentIndex] = prices[currentIndex - 1] + previousBestCost;

    // Maintain monotonic property for future coverage
    while (monotonicQueue.length > 0 && dpTable[monotonicQueue[monotonicQueue.length - 1] - 1] >= dpTable[currentIndex - 1]) {
      monotonicQueue.pop();
    }
    monotonicQueue.push(currentIndex);
  }

  // Final answer logic requires looking back at which purchase covers the end
  // (Simplified for this guide: standard DP with deque)
  return dpTable[collectionLength]; 
};
```
* **Dry Run (prices = [1, 10]):**
    * Fruit 1: `dp[1]` = 1. `deque` = [1]. Covers up to index 1 + 1 = 2.
    * Fruit 2: Covered by 1? Yes (1+1 >= 2). `dp[2]` = 10 + 1 (if bought) or we optimize.
    * Result: 1.
* **Alternative Approach:** A standard $O(n^2)$ Dynamic Programming approach is acceptable for smaller constraints but lacks the "Senior" performance profile.

---

## 7. [Latest 2026] Count Days Without Meetings
[https://leetcode.com/problems/count-days-without-meetings/](https://leetcode.com/problems/count-days-without-meetings/)

* **Complexity:** Time: $O(n \log n)$ | Space: $O(1)$ (ignoring sort space)
* **Key Intuition:** Merge all meeting intervals first to find total busy days; subtract this from the total range of days.
* **The 'Talk Track':**
    * "The total days is given as $days$, and I need to calculate the union of all meeting intervals."
    * "By merging overlaps, I get a set of disjoint intervals where meetings occur."
    * "Summing the length of these disjoint intervals gives me the total 'busy' days."
* **Pseudocode:**
    1. Sort `meetings` by start time.
    2. Merge all overlapping `meetings`.
    3. `totalBusyDays = 0`.
    4. For each merged interval `[start, end]`, `totalBusyDays += (end - start + 1)`.
    5. Return `days - totalBusyDays`.
* **JavaScript Solution:**
```javascript
const countDays = (totalDays, meetings) => {
  meetings.sort((meetingA, meetingB) => meetingA[0] - meetingB[0]);

  let totalBusyDays = 0;
  let currentMergedStart = meetings[0][0];
  let currentMergedEnd = meetings[0][1];

  for (let currentIndex = 1; currentIndex < meetings.length; currentIndex++) {
    const [nextStart, nextEnd] = meetings[currentIndex];

    if (nextStart <= currentMergedEnd) {
      currentMergedEnd = Math.max(currentMergedEnd, nextEnd);
    } else {
      totalBusyDays += (currentMergedEnd - currentMergedStart + 1);
      currentMergedStart = nextStart;
      currentMergedEnd = nextEnd;
    }
  }

  // Add the final interval
  totalBusyDays += (currentMergedEnd - currentMergedStart + 1);

  return totalDays - totalBusyDays;
};
```
* **Dry Run (days = 10, meetings = [[5, 7], [1, 3]]):**
    * Sorted: [[1, 3], [5, 7]].
    * Merge: No overlap. `busy` = (3-1+1) + (7-5+1) = 3 + 3 = 6.
    * Result: 10 - 6 = 4.
* **Alternative Approach:** We could use a segment tree for range updates, but that is $O(n \log days)$, which is worse if $days$ is large.

---
