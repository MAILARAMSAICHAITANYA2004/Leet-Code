# [403. Frog Jump (Hard)](https://leetcode.com/problems/frog-jump)

<p>A frog is crossing a river. The river is divided into some number of units, and at each unit, there may or may not exist a stone. The frog can jump on a stone, but it must not jump into the water.</p>
<p>Given a list of <code>stones</code>' positions (in units) in sorted <strong>ascending order</strong>, determine if the frog can cross the river by landing on the last stone. Initially, the frog is on the first stone and assumes the first jump must be <code>1</code> unit.</p>
<p>If the frog's last jump was <code>k</code> units, its next jump must be either <code>k - 1</code>, <code>k</code>, or <code>k + 1</code> units. The frog can only jump in the forward direction.</p>
<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>
<pre><strong>Input:</strong> stones = [0,1,3,5,6,8,12,17]
<strong>Output:</strong> true
<strong>Explanation:</strong> The frog can jump to the last stone by jumping 1 unit to the 2nd stone, then 2 units to the 3rd stone, then 2 units to the 4th stone, then 3 units to the 6th stone, 4 units to the 7th stone, and 5 units to the 8th stone.
</pre>
<p><strong class="example">Example 2:</strong></p>
<pre><strong>Input:</strong> stones = [0,1,2,3,4,8,9,11]
<strong>Output:</strong> false
<strong>Explanation:</strong> There is no way to jump to the last stone as the gap between the 5th and 6th stone is too large.
</pre>
<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>
<ul>
	<li><code>2 &lt;= stones.length &lt;= 2000</code></li>
	<li><code>0 &lt;= stones[i] &lt;= 2<sup>31</sup> - 1</code></li>
	<li><code>stones[0] == 0</code></li>
	<li><code>stones</code>&nbsp;is sorted in a strictly increasing order.</li>
</ul>

**Companies**:
[PhonePe](https://leetcode.com/company/phonepe), [Adobe](https://leetcode.com/company/adobe), [Amazon](https://leetcode.com/company/amazon)

**Related Topics**:  
[Array](https://leetcode.com/tag/array/), [Dynamic Programming](https://leetcode.com/tag/dynamic-programming/)

**Similar Questions**:
* [Minimum Sideway Jumps (Medium)](https://leetcode.com/problems/minimum-sideway-jumps/)
* [Solving Questions With Brainpower (Medium)](https://leetcode.com/problems/solving-questions-with-brainpower/)
* [Maximum Number of Jumps to Reach the Last Index (Medium)](https://leetcode.com/problems/maximum-number-of-jumps-to-reach-the-last-index/)

## Solution 1. DP Top-down

Base Case: If ind is 0, it returns 0 since there's no cost to stay at the first step.
Memoization: If the cost to reach ind has already been computed (stored in dp), it returns that value to avoid redundant calculations.
Jump Calculation: It computes the cost of jumping from the previous step (jumpOne) and from two steps back (jumpTwo), considering the height differences.
Return Value: It stores and returns the minimum cost of the two possible jumps in the dp array.

```cpp

// Time: O(N)
// Space: O(N)

int solve(int ind, vector<int>& height, vector<int>& dp) {
    // Base case: no cost to reach the first step
    if (ind == 0) return 0;  
    
    // Return the stored result if already computed
    if (dp[ind] != -1) return dp[ind];  
    
    int jumpTwo = INT_MAX; // Initialize jumpTwo to maximum value for comparison

    // Calculate the cost of jumping from the previous step
    int jumpOne = solve(ind - 1, height, dp) + abs(height[ind] - height[ind - 1]);
    
    // Calculate the cost of jumping from two steps back if possible
    if (ind > 1)
        jumpTwo = solve(ind - 2, height, dp) + abs(height[ind] - height[ind - 2]);
    
    // Store the minimum cost to reach the current step in dp array
    return dp[ind] = min(jumpOne, jumpTwo);  
}

```


## Solution 2. DP Bottom-Up

Initialization: It initializes a dp array to store the minimum cost to reach each step, with dp[0] set to 0 (no cost for the first step).
Dynamic Programming Loop: For each step from 1 to n-1, it computes the cost of jumping from the previous step (jumpOne) and, if applicable, from two steps back (jumpTwo).
Cost Calculation: It updates the dp array with the minimum of the two calculated costs for each step.
Output: Finally, it prints the minimum cost to reach the last step, which is stored in dp[n-1].

```cpp

// Time: O(N)
// Space: O(N)

int main() {
    vector<int> height{30, 10, 60, 10, 60, 50}; // Heights of the stairs
    int n = height.size(); // Number of steps
    vector<int> dp(n, -1); // DP array initialized to -1
    dp[0] = 0; // No cost to stay at the first step

    for (int ind = 1; ind < n; ind++) {
        int jumpTwo = INT_MAX; // Initialize jumpTwo to maximum value
        // Calculate cost to jump from the previous step
        int jumpOne = dp[ind - 1] + abs(height[ind] - height[ind - 1]);
        
        // Calculate cost to jump from two steps back if applicable
        if (ind > 1)
            jumpTwo = dp[ind - 2] + abs(height[ind] - height[ind - 2]);

        // Store the minimum cost in dp array
        dp[ind] = min(jumpOne, jumpTwo);
    }
    
    cout << dp[n - 1]; // Output the minimum cost to reach the last step
}

```

## Solution 3. DP Space Optimization

The code optimizes space complexity by using only two variables, prev and prev2, instead of a full dp array. This allows it to store the minimum costs to reach the 
last two steps, reducing the space usage from O(n) to O(1) while still maintaining the same O(n) time complexity for calculating the minimum cost to reach the last step.

```cpp

// Time: O(N)
// Space: O(1)

int main() {
    vector<int> height{30, 10, 60, 10, 60, 50}; // Heights of the stairs
    int n = height.size(); // Number of steps
    int prev = 0; // Minimum cost to reach the previous step
    int prev2 = 0; // Minimum cost to reach the step before previous

    for (int i = 1; i < n; i++) {
        int jumpTwo = INT_MAX; // Initialize jumpTwo to maximum value
        // Calculate cost to jump from the previous step
        int jumpOne = prev + abs(height[i] - height[i - 1]);
        
        // Calculate cost to jump from two steps back if applicable
        if (i > 1)
            jumpTwo = prev2 + abs(height[i] - height[i - 2]);
        
        int cur_i = min(jumpOne, jumpTwo); // Current minimum cost
        prev2 = prev; // Update prev2 to the previous step's cost
        prev = cur_i; // Update prev to the current cost
    }
    
    cout << prev; // Output the minimum cost to reach the last step
}



```
