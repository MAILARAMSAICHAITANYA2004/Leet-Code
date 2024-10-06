# [1631. Path With Minimum Effort (Medium)](https://leetcode.com/problems/path-with-minimum-effort/)

<p>You are a hiker preparing for an upcoming hike. You are given <code>heights</code>, a 2D array of size <code>rows x columns</code>, where <code>heights[row][col]</code> represents the height of cell <code>(row, col)</code>. You are situated in the top-left cell, <code>(0, 0)</code>, and you hope to travel to the bottom-right cell, <code>(rows-1, columns-1)</code> (i.e.,&nbsp;<strong>0-indexed</strong>). You can move <strong>up</strong>, <strong>down</strong>, <strong>left</strong>, or <strong>right</strong>, and you wish to find a route that requires the minimum <strong>effort</strong>.</p>

<p>A route's <strong>effort</strong> is the <strong>maximum absolute difference</strong><strong> </strong>in heights between two consecutive cells of the route.</p>

<p>Return <em>the minimum <strong>effort</strong> required to travel from the top-left cell to the bottom-right cell.</em></p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<p><img alt="" src="https://assets.leetcode.com/uploads/2020/10/04/ex1.png" style="width: 300px; height: 300px;"></p>

<pre><strong>Input:</strong> heights = [[1,2,2],[3,8,2],[5,3,5]]
<strong>Output:</strong> 2
<strong>Explanation:</strong> The route of [1,3,5,3,5] has a maximum absolute difference of 2 in consecutive cells.
This is better than the route of [1,2,2,2,5], where the maximum absolute difference is 3.
</pre>

<p><strong>Example 2:</strong></p>

<p><img alt="" src="https://assets.leetcode.com/uploads/2020/10/04/ex2.png" style="width: 300px; height: 300px;"></p>

<pre><strong>Input:</strong> heights = [[1,2,3],[3,8,4],[5,3,5]]
<strong>Output:</strong> 1
<strong>Explanation:</strong> The route of [1,2,3,4,5] has a maximum absolute difference of 1 in consecutive cells, which is better than route [1,3,5,3,5].
</pre>

<p><strong>Example 3:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/10/04/ex3.png" style="width: 300px; height: 300px;">
<pre><strong>Input:</strong> heights = [[1,2,1,1,1],[1,2,1,2,1],[1,2,1,2,1],[1,2,1,2,1],[1,1,1,2,1]]
<strong>Output:</strong> 0
<strong>Explanation:</strong> This route does not require any effort.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>rows == heights.length</code></li>
	<li><code>columns == heights[i].length</code></li>
	<li><code>1 &lt;= rows, columns &lt;= 100</code></li>
	<li><code>1 &lt;= heights[i][j] &lt;= 10<sup>6</sup></code></li>
</ul>

**Companies**:  
[Google](https://leetcode.com/company/google)

**Related Topics**:  
[Array](https://leetcode.com/tag/array/), [Binary Search](https://leetcode.com/tag/binary-search/), [Depth-First Search](https://leetcode.com/tag/depth-first-search/), [Breadth-First Search](https://leetcode.com/tag/breadth-first-search/), [Union Find](https://leetcode.com/tag/union-find/), [Heap (Priority Queue)](https://leetcode.com/tag/heap-priority-queue/), [Matrix](https://leetcode.com/tag/matrix/)

**Similar Questions**:
* [Swim in Rising Water (Hard)](https://leetcode.com/problems/swim-in-rising-water/)
* [Path With Maximum Minimum Value (Medium)](https://leetcode.com/problems/path-with-maximum-minimum-value/)

## Solution 1. Dijkstra

The code implements Dijkstra's algorithm to find the minimum effort path in a grid. It initializes a distance matrix and 
a priority queue, starting from the top-left corner with zero effort. It explores each cell, calculating the effort needed 
to move to adjacent cells, updating distances, and prioritizing paths with lower efforts. The process continues until the 
bottom-right corner is reached, returning the minimum effort, or until all paths are explored, returning -1 if no path exists.

```cpp
// OJ: https://leetcode.com/problems/path-with-minimum-effort/
// Author: github.com/lzl124631x
// Time: O(MNlog(MN))
// Space: O(MN)

class Solution {
public:
    int minimumEffortPath(vector<vector<int>>& A) {
        int M = A.size(), N = A[0].size(), dirs[4][2] = {{0,1},{0,-1},{1,0},{-1,0}}; // Directions for moving up, down, left, right
        vector<vector<int>> dist(M, vector<int>(N, INT_MAX)); // Stores minimum effort to reach each cell
        priority_queue<array<int, 3>, vector<array<int, 3>>, greater<>> pq; // Min-heap to prioritize paths with less effort
        dist[0][0] = 0;
        pq.push({ 0, 0, 0 }); // Start from the top-left corner with 0 effort
        
        while (pq.size()) {
            auto [e, x, y] = pq.top(); // Current minimum effort, and the position (x, y)
            pq.pop();
            if (e > dist[x][y]) continue; // If this effort is not the smallest for (x, y), skip it
            if (x == M - 1 && y == N - 1) return dist[x][y]; // If we reached the bottom-right corner, return the effort
            
            for (auto &[dx, dy] : dirs) { // Explore all 4 possible directions
                int a = x + dx, b = y + dy;
                if (a < 0 || b < 0 || a >= M || b >= N) continue; // Skip if out of bounds
                int effort = max(e, abs(A[x][y] - A[a][b])); // Calculate the effort to move to the next cell
                if (effort >= dist[a][b]) continue; // If the new effort is not better, skip this path
                dist[a][b] = effort; // Update with the smaller effort
                pq.push({ effort, a, b }); // Push the new state to the heap
            }
        }
        return -1;
    }
};
```
