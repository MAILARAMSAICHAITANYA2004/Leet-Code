# [1219. Path with Maximum Gold (Medium)](https://leetcode.com/problems/path-with-maximum-gold/)

<p>In a gold mine <code>grid</code>&nbsp;of size <code>m * n</code>,&nbsp;each cell in this mine has an integer representing the amount of gold&nbsp;in that cell,&nbsp;<code>0</code> if it is empty.</p>

<p>Return the maximum amount of gold you&nbsp;can collect under the conditions:</p>

<ul>
	<li>Every time you are located in a cell you will collect all the gold in that cell.</li>
	<li>From your position you can walk one step to the left, right, up or down.</li>
	<li>You can't visit the same cell more than once.</li>
	<li>Never visit a cell with&nbsp;<code>0</code> gold.</li>
	<li>You can start and stop collecting gold from&nbsp;<strong>any </strong>position in the grid that has some gold.</li>
</ul>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<pre><strong>Input:</strong> grid = [[0,6,0],[5,8,7],[0,9,0]]
<strong>Output:</strong> 24
<strong>Explanation:</strong>
[[0,6,0],
 [5,8,7],
 [0,9,0]]
Path to get the maximum gold, 9 -&gt; 8 -&gt; 7.
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> grid = [[1,0,7],[2,0,6],[3,4,5],[0,3,0],[9,0,20]]
<strong>Output:</strong> 28
<strong>Explanation:</strong>
[[1,0,7],
 [2,0,6],
 [3,4,5],
 [0,3,0],
 [9,0,20]]
Path to get the maximum gold, 1 -&gt; 2 -&gt; 3 -&gt; 4 -&gt; 5 -&gt; 6 -&gt; 7.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= grid.length,&nbsp;grid[i].length &lt;= 15</code></li>
	<li><code>0 &lt;= grid[i][j] &lt;= 100</code></li>
	<li>There are at most <strong>25&nbsp;</strong>cells containing gold.</li>
</ul>


**Related Topics**:  
[Backtracking](https://leetcode.com/tag/backtracking/)

## Solution 1. DFS

The solution uses Depth-First Search (DFS) to explore a grid representing a gold mine. It recursively visits each cell, accumulating gold 
while marking visited cells as zero. After exploring, it backtracks to restore the cell's value. The main function iterates through the grid, 
starting DFS from gold-containing cells and returns the maximum gold collected.

```cpp
// OJ: https://leetcode.com/problems/path-with-maximum-gold/
// Author: github.com/lzl124631x
// Time: O(MN*4^(MN))
// Space: O(MN) because in the worst case the DFS will visit all cells.
class Solution {
    int M, N, ans = 0; 
    int dirs[4][2] = {{0,1},{0,-1},{1,0},{-1,0}}; // Direction vectors for DFS

    // Depth-first search to explore gold collection
    void dfs(vector<vector<int>> &G, int i, int j, int cnt) {
        int g = G[i][j]; // Current gold amount
        G[i][j] = 0; // Mark as visited
        cnt += g; // Accumulate gold
        ans = max(ans, cnt); // Update maximum gold

        // Explore all four directions
        for (auto &[dx, dy] : dirs) {
            int x = i + dx, y = j + dy; // New coordinates
            if (x < 0 || y < 0 || x >= M || y >= N || G[x][y] == 0) continue; // Out of bounds or visited
            dfs(G, x, y, cnt); // Recur for next cell
        }
        
        G[i][j] = g; // Backtrack: unmark cell
    }

public:
    // Function to get maximum gold
    int getMaximumGold(vector<vector<int>>& G) {
        M = G.size(), N = G[0].size(); // Get dimensions
        for (int i = 0; i < M; ++i) {
            for (int j = 0; j < N; ++j) {
                if (G[i][j] == 0) continue; // Skip cells with no gold
                dfs(G, i, j, 0); // Start DFS from current cell
            }
        }
        return ans; // Return maximum gold collected
    }
};

```
