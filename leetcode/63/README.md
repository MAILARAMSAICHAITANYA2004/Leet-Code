# [63. Unique Paths II (Medium)](https://leetcode.com/problems/unique-paths-ii)

<p>You are given an <code>m x n</code> integer array <code>grid</code>. There is a robot initially located at the <b>top-left corner</b> (i.e., <code>grid[0][0]</code>). The robot tries to move to the <strong>bottom-right corner</strong> (i.e., <code>grid[m - 1][n - 1]</code>). The robot can only move either down or right at any point in time.</p>
<p>An obstacle and space are marked as <code>1</code> or <code>0</code> respectively in <code>grid</code>. A path that the robot takes cannot include <strong>any</strong> square that is an obstacle.</p>
<p>Return <em>the number of possible unique paths that the robot can take to reach the bottom-right corner</em>.</p>
<p>The testcases are generated so that the answer will be less than or equal to <code>2 * 10<sup>9</sup></code>.</p>
<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/11/04/robot1.jpg" style="width: 242px; height: 242px;">
<pre><strong>Input:</strong> obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
<strong>Output:</strong> 2
<strong>Explanation:</strong> There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -&gt; Right -&gt; Down -&gt; Down
2. Down -&gt; Down -&gt; Right -&gt; Right
</pre>
<p><strong class="example">Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/11/04/robot2.jpg" style="width: 162px; height: 162px;">
<pre><strong>Input:</strong> obstacleGrid = [[0,1],[0,0]]
<strong>Output:</strong> 1
</pre>
<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>
<ul>
	<li><code>m == obstacleGrid.length</code></li>
	<li><code>n == obstacleGrid[i].length</code></li>
	<li><code>1 &lt;= m, n &lt;= 100</code></li>
	<li><code>obstacleGrid[i][j]</code> is <code>0</code> or <code>1</code>.</li>
</ul>

**Companies**:
[Atlassian](https://leetcode.com/company/atlassian), [Cruise Automation](https://leetcode.com/company/cruise-automation), [athenahealth](https://leetcode.com/company/athenahealth)

**Related Topics**:  
[Array](https://leetcode.com/tag/array/), [Dynamic Programming](https://leetcode.com/tag/dynamic-programming/), [Matrix](https://leetcode.com/tag/matrix/)

**Similar Questions**:
* [Unique Paths (Medium)](https://leetcode.com/problems/unique-paths/)
* [Unique Paths III (Hard)](https://leetcode.com/problems/unique-paths-iii/)
* [Minimum Path Cost in a Grid (Medium)](https://leetcode.com/problems/minimum-path-cost-in-a-grid/)
* [Paths in Matrix Whose Sum Is Divisible by K (Hard)](https://leetcode.com/problems/paths-in-matrix-whose-sum-is-divisible-by-k/)

## Solution 1. DP Top-down

Recursive Function: The mazeObstaclesUtil function recursively counts the number of paths from the bottom-right corner (n-1, m-1) to the top-left corner (0, 0) while navigating around obstacles.
Base Cases: It checks for obstacles, ensures it does not go out of bounds, and returns 1 when it reaches the destination.
Memoization: A dp table stores results of already computed cells to avoid redundant calculations, improving efficiency.
Path Calculation: The function explores two possible moves—up and left—accumulating valid paths from these directions.
Execution: The mazeObstacles function initializes the dp table, calls the utility function, and returns the total number of valid paths.

```cpp

// Time Complexity: O(N*M)
// Reason: At max, there will be N*M calls of recursion.
// Space Complexity: O((M-1)+(N-1)) + O(N*M)
// Reason: We are using a recursion stack space:O((M-1)+(N-1)), here (M-1)+(N-1) is the path length and an external DP Array of size ‘N*M’.

#include <bits/stdc++.h>
using namespace std;

// Helper function to count the number of paths from (i, j) to (0, 0)
int mazeObstaclesUtil(int i, int j, vector<vector<int>> &maze, vector<vector<int>> &dp) {
    // Base cases
    if (i > 0 && j > 0 && maze[i][j] == -1) return 0; // If there's an obstacle at (i, j), return 0
    if (i == 0 && j == 0) return 1; // If we reach the destination (0, 0), return 1
    if (i < 0 || j < 0) return 0; // If we go out of bounds, return 0
    if (dp[i][j] != -1) return dp[i][j]; // If the result is already computed, return it

    // Recursive calls to explore paths from (i, j) to (0, 0)
    int up = mazeObstaclesUtil(i - 1, j, maze, dp);
    int left = mazeObstaclesUtil(i, j - 1, maze, dp);

    // Store the result in the DP table and return it
    return dp[i][j] = up + left;
}

// Main function to count paths with obstacles in a maze
int mazeObstacles(int n, int m, vector<vector<int>> &maze) {
    vector<vector<int>> dp(n, vector<int>(m, -1)); // DP table to memoize results
    return mazeObstaclesUtil(n - 1, m - 1, maze, dp); // Start from the bottom-right corner
}

```
## Solution 2. DP Bottom-Up

Recursive Pathfinding: The mazeObstaclesUtil function recursively calculates paths from the bottom-right cell (n-1, m-1) to the top-left cell (0, 0) while avoiding obstacles marked by -1 in the maze.
Base Conditions: It handles three base cases: returning 0 for obstacles, returning 1 when reaching the destination, and returning 0 for out-of-bound indices.
Memoization: A dynamic programming table (dp) is used to store already computed paths for each cell to optimize performance and avoid recalculating values.
Exploring Moves: The function explores two directions—up and left—summing the valid paths from both directions to compute the total paths to the current cell.
Overall Execution: The mazeObstacles function initializes the DP table and triggers the recursive counting process, ultimately returning the total number of valid paths.

```cpp

// Time Complexity: O(N*M)
// Space Complexity: O(N*M)

#include <bits/stdc++.h>
using namespace std;

// Helper function to count the number of paths from (0, 0) to (n-1, m-1) in the maze
int mazeObstaclesUtil(int n, int m, vector<vector<int>> &maze, vector<vector<int>> &dp) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            // Base conditions
            if (i > 0 && j > 0 && maze[i][j] == -1) {
                dp[i][j] = 0; // If there's an obstacle at (i, j), no paths can pass through it
                continue;
            }
            if (i == 0 && j == 0) {
                dp[i][j] = 1; // If we are at the starting point, there is one path to it
                continue;
            }

            int up = 0;
            int left = 0;

            // Check if we can move up and left (if not at the edge of the maze)
            if (i > 0)
                up = dp[i - 1][j]; // Number of paths from above
            if (j > 0)
                left = dp[i][j - 1]; // Number of paths from the left

            // Total number of paths to reach (i, j) is the sum of paths from above and left
            dp[i][j] = up + left;
        }
    }

    // The final result is stored in dp[n-1][m-1], which represents the destination
    return dp[n - 1][m - 1];
}

// Main function to count paths with obstacles in a maze
int mazeObstacles(int n, int m, vector<vector<int>> &maze) {
    vector<vector<int>> dp(n, vector<int>(m, -1)); // DP table to memoize results
    return mazeObstaclesUtil(n, m, maze, dp); // Start from the top-left corner (0, 0)
}

```
## Solution 3. DP Space Optimization

Space Reduction: It uses two 1D arrays (prev and temp) instead of a 2D array, reducing space complexity from O(n×m) to O(m)
Iterative Approach: The algorithm iterates through the grid rather than using recursion, which avoids overhead and stack overflow risks.
Direct Base Case Handling: Base cases are checked within the loop, preventing unnecessary calculations for obstacles and the starting point.
Efficient Path Calculation: It computes paths incrementally, using previously calculated values, ensuring only essential computations are performed.
Single Pass: The algorithm processes each cell in one pass, maintaining a time complexity of O(n×m)

```cpp

// Time: O(N*M)
// Space: O(N)


#include <bits/stdc++.h>
using namespace std;

// Function to count the number of paths from the top-left corner (0, 0) to the bottom-right corner (n-1, m-1)
int mazeObstacles(int n, int m, vector<vector<int>> &maze) {
    vector<int> prev(m, 0); // Initialize a vector to store the previous row's path counts

    for (int i = 0; i < n; i++) {
        vector<int> temp(m, 0); // Initialize a temporary vector for the current row
        for (int j = 0; j < m; j++) {
            // Base conditions
            if (i > 0 && j > 0 && maze[i][j] == -1) {
                temp[j] = 0; // If there's an obstacle at (i, j), no paths can pass through it
                continue;
            }
            if (i == 0 && j == 0) {
                temp[j] = 1; // If we are at the starting point, there is one path to it
                continue;
            }

            int up = 0;
            int left = 0;

            // Check if we can move up and left (if not at the edge of the maze)
            if (i > 0)
                up = prev[j]; // Number of paths from above (previous row)
            if (j > 0)
                left = temp[j - 1]; // Number of paths from the left (current row)

            // Total number of paths to reach (i, j) is the sum of paths from above and left
            temp[j] = up + left;
        }
        prev = temp; // Update the previous row with the current row
    }

    // The final result is stored in prev[m-1], which represents the destination in the last row
    return prev[m - 1];
}


```
