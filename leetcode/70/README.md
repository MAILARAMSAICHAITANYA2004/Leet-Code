# [70. Climbing Stairs (Easy)](https://leetcode.com/problems/climbing-stairs/)

<p>You are climbing a staircase. It takes <code>n</code> steps to reach the top.</p>

<p>Each time you can either climb <code>1</code> or <code>2</code> steps. In how many distinct ways can you climb to the top?</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<pre><strong>Input:</strong> n = 2
<strong>Output:</strong> 2
<strong>Explanation:</strong> There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> n = 3
<strong>Output:</strong> 3
<strong>Explanation:</strong> There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= n &lt;= 45</code></li>
</ul>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Google](https://leetcode.com/company/google), [Microsoft](https://leetcode.com/company/microsoft), [Bloomberg](https://leetcode.com/company/bloomberg), [Expedia](https://leetcode.com/company/expedia), [Adobe](https://leetcode.com/company/adobe), [Uber](https://leetcode.com/company/uber), [Apple](https://leetcode.com/company/apple), [LinkedIn](https://leetcode.com/company/linkedin), [Facebook](https://leetcode.com/company/facebook), [Walmart Labs](https://leetcode.com/company/walmart-labs), [Morgan Stanley](https://leetcode.com/company/morgan-stanley), [Twilio](https://leetcode.com/company/twilio)

**Related Topics**:  
[Math](https://leetcode.com/tag/math/), [Dynamic Programming](https://leetcode.com/tag/dynamic-programming/), [Memoization](https://leetcode.com/tag/memoization/)

**Similar Questions**:
* [Min Cost Climbing Stairs (Easy)](https://leetcode.com/problems/min-cost-climbing-stairs/)
* [Fibonacci Number (Easy)](https://leetcode.com/problems/fibonacci-number/)
* [N-th Tribonacci Number (Easy)](https://leetcode.com/problems/n-th-tribonacci-number/)

## Solution 1. Recursion

The climbStairsUtil function recursively computes the number of ways to climb n stairs. It uses base cases (n == 0 or n == 1) where the answer is 1.
The dp array stores previously computed results to avoid redundant calculations. If dp[n] is already computed (not -1), it returns the stored value.
The recursion sums the results of climbStairsUtil(n-1) and climbStairsUtil(n-2) to get the number of ways to reach step n.
The climbStairs function initializes the dp array and calls the helper function to compute the final result.

```cpp

// Time: O(N)
// Space: O(N)

class Solution {
public:
    int climbStairsUtil(int n, vector<int>& dp) {
        // Base cases
        if (n == 0 || n == 1) return 1;

        // If result is already computed, return it from dp array
        if (dp[n] != -1) return dp[n];

        // Recursively compute the result and store it in dp array
        dp[n] = climbStairsUtil(n - 1, dp) + climbStairsUtil(n - 2, dp);
        return dp[n];
    }

    int climbStairs(int n) {
        // Create a dp array initialized with -1
        vector<int> dp(n + 1, -1);
        
        // Call the recursive helper function with memoization
        return climbStairsUtil(n, dp);
    }
};
```

## Solution 2. Tabulation

A dp vector is initialized to store the number of ways to reach each step, with dp[0] and dp[1] set to 1 (base cases).
For each step from 2 to n, the number of ways to reach that step is the sum of the ways to reach the previous two steps (dp[i] = dp[i-1] + dp[i-2]).
Finally, the result (number of ways to climb n stairs) is stored in dp[n] and returned.

```cpp

// Time: O(N)
// Space: O(N)
class Solution {
public:
    int climbStairs(int n) {
        // Create a dp vector of size n+1 and initialize with -1
        vector<int> dp(n + 1, -1);
        
        // Base cases
        dp[0] = 1;  // 1 way to stay at the 0th step
        dp[1] = 1;  // 1 way to reach the 1st step
        
        // Fill the dp array for all steps from 2 to n
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];  // The current step can be reached from the previous or the step before that
        }
        
        // The result is stored in dp[n]
        return dp[n];
    }
};
```

## Solution 3. Space optimzation

The code optimizes space by replacing the dp array with two variables, prev1 and prev2, to store the results of the last two steps. This reduces the space complexity from O(n) to O(1), as only the last two values are needed at any point to compute the next step.

```cpp

// Time: O(N)
// Space: O(1)
class Solution {
public:
    int climbStairs(int n) {
        if (n == 0 || n == 1) return 1;

        // Variables to store the last two results
        int prev2 = 1;  // This corresponds to dp[0]
        int prev1 = 1;  // This corresponds to dp[1]
        
        // Iterate from 2 to n and calculate the result
        for (int i = 2; i <= n; i++) {
            int curr = prev1 + prev2;  // Current result is sum of last two
            prev2 = prev1;  // Update prev2 to the previous step
            prev1 = curr;   // Update prev1 to the current step
        }
        
        // The result is stored in prev1
        return prev1;
    }
};

```
