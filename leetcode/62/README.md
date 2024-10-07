# [62. Unique Paths (Medium)](https://leetcode.com/problems/unique-paths)

<p>There is a robot on an <code>m x n</code> grid. The robot is initially located at the <strong>top-left corner</strong> (i.e., <code>grid[0][0]</code>). The robot tries to move to the <strong>bottom-right corner</strong> (i.e., <code>grid[m - 1][n - 1]</code>). The robot can only move either down or right at any point in time.</p>

<p>Given the two integers <code>m</code> and <code>n</code>, return <em>the number of possible unique paths that the robot can take to reach the bottom-right corner</em>.</p>

<p>The test cases are generated so that the answer will be less than or equal to <code>2 * 10<sup>9</sup></code>.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>
<img src="https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png" style="width: 400px; height: 183px;">
<pre><strong>Input:</strong> m = 3, n = 7
<strong>Output:</strong> 28
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre><strong>Input:</strong> m = 3, n = 2
<strong>Output:</strong> 3
<strong>Explanation:</strong> From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -&gt; Down -&gt; Down
2. Down -&gt; Down -&gt; Right
3. Down -&gt; Right -&gt; Down
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= m, n &lt;= 100</code></li>
</ul>


**Related Topics**:  
[Math](https://leetcode.com/tag/math/), [Dynamic Programming](https://leetcode.com/tag/dynamic-programming/), [Combinatorics](https://leetcode.com/tag/combinatorics/)

**Similar Questions**:
* [Unique Paths II (Medium)](https://leetcode.com/problems/unique-paths-ii/)
* [Minimum Path Sum (Medium)](https://leetcode.com/problems/minimum-path-sum/)
* [Dungeon Game (Medium)](https://leetcode.com/problems/dungeon-game/)
* [Minimum Path Cost in a Grid (Medium)](https://leetcode.com/problems/minimum-path-cost-in-a-grid/)
* [Minimum Cost Homecoming of a Robot in a Grid (Medium)](https://leetcode.com/problems/minimum-cost-homecoming-of-a-robot-in-a-grid/)
* [Number of Ways to Reach a Position After Exactly k Steps (Medium)](https://leetcode.com/problems/number-of-ways-to-reach-a-position-after-exactly-k-steps/)
* [Paths in Matrix Whose Sum Is Divisible by K (Medium)](https://leetcode.com/problems/paths-in-matrix-whose-sum-is-divisible-by-k/)

## Solution 1. DP Top-down

Purpose: Counts unique paths from the top-left to the bottom-right corner of an m x n grid.
Recursive Function: The countWaysUtil function calculates paths to cell (i, j) by moving up or left.
Base Case: Returns 1 when at the starting position (0, 0) and 0 for out-of-bounds indices.
Memoization: Uses a 2D vector dp to store computed results for each cell, optimizing performance by avoiding redundant calculations.
Counting Paths: Total paths to (i, j) are the sum of paths from the cell above and the cell to the left.
Initialization: The countWays function sets up the dp table and initiates the recursive count from the bottom-right corner.

```cpp

Time Complexity: O(M*N)
Space Complexity: O((N-1)+(M-1)) + O(M*N)
Reason: We are using a recursion stack space: O((N-1)+(M-1)), here (N-1)+(M-1) is the path length and an external DP Array of size ‘M*N’.

#include <bits/stdc++.h>
using namespace std;

// Recursive function to count the number of ways to reach (i, j) from (0, 0)
// in a grid of size m x n
int countWaysUtil(int i, int j, vector<vector<int>>& dp) {
    // Base case: If we reach the top-left corner (0, 0), there is one way.
    if (i == 0 && j == 0)
        return 1;

    // If we go out of bounds or reach a blocked cell, there are no ways.
    if (i < 0 || j < 0)
        return 0;

    // If we have already computed the number of ways for this cell, return it.
    if (dp[i][j] != -1)
        return dp[i][j];

    // Calculate the number of ways by moving up and left recursively.
    int up = countWaysUtil(i - 1, j, dp);
    int left = countWaysUtil(i, j - 1, dp);

    // Store the result in the dp table and return it.
    return dp[i][j] = up + left;
}

// Function to count the number of ways to reach the bottom-right cell (m-1, n-1)
// from the top-left cell (0, 0) in a grid of size m x n
int countWays(int m, int n) {
    // Create a memoization table (dp) to store the results of subproblems.
    vector<vector<int>> dp(m, vector<int>(n, -1));

    // Call the utility function with the bottom-right cell as the starting point.
    return countWaysUtil(m - 1, n - 1, dp);
}

```

## Solution 2. DP Bottom-Up

```cpp
Time Complexity: O(M*N)
Space Complexity: O(M*N)

#include <bits/stdc++.h>
using namespace std;

// Function to count the number of ways to reach the bottom-right cell (m-1, n-1)
// from the top-left cell (0, 0) in a grid of size m x n
int countWaysUtil(int m, int n, vector<vector<int>>& dp) {
    // Loop through the grid using two nested loops
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            // Base condition: If we are at the top-left cell (0, 0), there is one way.
            if (i == 0 && j == 0) {
                dp[i][j] = 1;
                continue; // Skip the rest of the loop and continue with the next iteration.
            }

            // Initialize variables to store the number of ways from the cell above (up) and left (left).
            int up = 0;
            int left = 0;

            // If we are not at the first row (i > 0), update 'up' with the value from the cell above.
            if (i > 0)
                up = dp[i - 1][j];

            // If we are not at the first column (j > 0), update 'left' with the value from the cell to the left.
            if (j > 0)
                left = dp[i][j - 1];

            // Calculate the number of ways to reach the current cell by adding 'up' and 'left'.
            dp[i][j] = up + left;
        }
    }

    // The result is stored in the bottom-right cell (m-1, n-1).
    return dp[m - 1][n - 1];
}

// Function to count the number of ways to reach the bottom-right cell (m-1, n-1)
// from the top-left cell (0, 0) in a grid of size m x n
int countWays(int m, int n) {
    // Create a memoization table (dp) to store the results of subproblems.
    vector<vector<int>> dp(m, vector<int>(n, -1));

    // Call the utility function with the grid size and the memoization table.
    return countWaysUtil(m, n, dp);
}

```

## Solution 3. DP Space Optimization

Space Optimization: It uses a single 1D vector (prev) instead of a 2D array, reducing space complexity from O(m×n) to O(n).
Iterative Approach: The algorithm iteratively calculates the number of ways to each cell, avoiding the overhead of recursion.
Temporary Storage: A temporary vector (temp) stores values for the current row, which are then used to update the previous row for future calculations.
Direct Access: It directly accesses values from the prev and temp vectors, minimizing redundant calculations.

```cpp

// Time: O(M*N)
// Space: O(N)


#include <bits/stdc++.h>
using namespace std;

// Function to count the number of ways to reach the bottom-right cell (m-1, n-1)
// from the top-left cell (0, 0) in a grid of size m x n
int countWays(int m, int n) {
    // Create a vector to represent the previous row of the grid.
    vector<int> prev(n, 0);

    // Iterate through the rows of the grid.
    for (int i = 0; i < m; i++) {
        // Create a temporary vector to represent the current row.
        vector<int> temp(n, 0);

        // Iterate through the columns of the grid.
        for (int j = 0; j < n; j++) {
            // Base case: If we are at the top-left cell (0, 0), there is one way.
            if (i == 0 && j == 0) {
                temp[j] = 1;
                continue;
            }

            // Initialize variables to store the number of ways from the cell above (up) and left (left).
            int up = 0;
            int left = 0;

            // If we are not at the first row (i > 0), update 'up' with the value from the previous row.
            if (i > 0)
                up = prev[j];

            // If we are not at the first column (j > 0), update 'left' with the value from the current row.
            if (j > 0)
                left = temp[j - 1];

            // Calculate the number of ways to reach the current cell by adding 'up' and 'left'.
            temp[j] = up + left;
        }

        // Update the previous row with the values calculated for the current row.
        prev = temp;
    }

    // The result is stored in the last cell of the previous row (n-1).
    return prev[n - 1];
}

int main() {
    int m = 3;
    int n = 2;

    // Call the countWays function and print the result.
    cout << "Number of ways to reach (" << m - 1 << ", " << n - 1 << "): " << countWays(m, n) << endl;

    return 0;
}



```


## Solution 4. Math (Combination)

We have `m - 1` right operations and `n - 1` down operations, `m + n - 2` operations in total. The result is the number of different combinations of these operations -- pick `m - 1` slots for right operations from `m + n - 2` slots.

$$
C(m + n - 2, m - 1) = \dfrac{(m+n-2)!}{(m-1)!\cdot (n-1)!} = \dfrac{m\cdot(m+1)\cdots(m+n-2)}{1\cdot2\cdots(n-1)}
$$

```cpp
// OJ: https://leetcode.com/problems/unique-paths
// Author: github.com/lzl124631x
// Time: O(min(M,N))
// Space: O(1)
class Solution {
public:
    int uniquePaths(int m, int n) {
        if (n > m) swap(n, m);
        long ans = 1;
        for (int i = 1; i <= n - 1; ++i) {
            ans = ans * (m - 1 + i) / i;
        }
        return ans;
    }
};
```

## Solution 5. Math (Combination)

We can use the `combination` function to compute $C_n^k$

```cpp
// OJ: https://leetcode.com/problems/unique-paths/
// Author: github.com/lzl124631x
// Time: O(min(M,N))
// Space: O(1)
class Solution {
    int combination(int n, int k) {
        k = min(k, n - k); // Since we loop in range [1, k], we make sure `k` is smaller than `n - k`
        long ans = 1;
        for (int i = 1; i <= k; ++i) ans = ans * (n - k + i) / i;
        return ans;
    }
public:
    int uniquePaths(int m, int n) {
        return combination(m + n - 2, m - 1);
    }
};
```
