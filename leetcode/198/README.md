# [198. House Robber (Medium)](https://leetcode.com/problems/house-robber/)

<p>You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and <b>it will automatically contact the police if two adjacent houses were broken into on the same night</b>.</p>

<p>Given an integer array <code>nums</code> representing the amount of money of each house, return <em>the maximum amount of money you can rob tonight <b>without alerting the police</b></em>.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<pre><strong>Input:</strong> nums = [1,2,3,1]
<strong>Output:</strong> 4
<strong>Explanation:</strong> Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> nums = [2,7,9,3,1]
<strong>Output:</strong> 12
<strong>Explanation:</strong> Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
Total amount you can rob = 2 + 9 + 1 = 12.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= nums.length &lt;= 100</code></li>
	<li><code>0 &lt;= nums[i] &lt;= 400</code></li>
</ul>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Microsoft](https://leetcode.com/company/microsoft), [Google](https://leetcode.com/company/google), [Cisco](https://leetcode.com/company/cisco), [Apple](https://leetcode.com/company/apple), [Adobe](https://leetcode.com/company/adobe), [Qualtrics](https://leetcode.com/company/qualtrics), [Arcesium](https://leetcode.com/company/arcesium), [Bloomberg](https://leetcode.com/company/bloomberg), [Goldman Sachs](https://leetcode.com/company/goldman-sachs), [ByteDance](https://leetcode.com/company/bytedance), [eBay](https://leetcode.com/company/ebay), [Flipkart](https://leetcode.com/company/flipkart), [PayTM](https://leetcode.com/company/paytm)

**Related Topics**:  
[Array](https://leetcode.com/tag/array/), [Dynamic Programming](https://leetcode.com/tag/dynamic-programming/)

**Similar Questions**:
* [Maximum Product Subarray (Medium)](https://leetcode.com/problems/maximum-product-subarray/)
* [House Robber II (Medium)](https://leetcode.com/problems/house-robber-ii/)
* [Paint House (Medium)](https://leetcode.com/problems/paint-house/)
* [Paint Fence (Medium)](https://leetcode.com/problems/paint-fence/)
* [House Robber III (Medium)](https://leetcode.com/problems/house-robber-iii/)
* [Non-negative Integers without Consecutive Ones (Hard)](https://leetcode.com/problems/non-negative-integers-without-consecutive-ones/)
* [Coin Path (Hard)](https://leetcode.com/problems/coin-path/)
* [Delete and Earn (Medium)](https://leetcode.com/problems/delete-and-earn/)

## Solution 1. DP Top-down

solveUtil is the recursive function that calculates the maximum sum up to index ind. It either picks the current element (and skips the next one) or skips the current element, returning the maximum of these two choices.
The dp array is used to store previously computed results to avoid redundant calculations (memoization).
solve initializes the dp array and starts the recursion from the last element in the array.
The base cases handle when the index is 0 (only one element to pick) or less than 0 (invalid index).

```cpp

// Time: O(N)
// Space: O(N)


#include <bits/stdc++.h>
using namespace std;

// Function to solve the problem using dynamic programming
int solveUtil(int ind, vector<int>& arr, vector<int>& dp) {
    // If the result for this index is already computed, return it
    if (dp[ind] != -1)
        return dp[ind];

    // Base cases
    if (ind == 0) 
        return arr[ind];
    if (ind < 0)  
        return 0;

    // Choose the current element or skip it, and take the maximum
    int pick = arr[ind] + solveUtil(ind - 2, arr, dp); // Choosing the current element
    int nonPick = 0 + solveUtil(ind - 1, arr, dp);      // Skipping the current element

    // Store the result in the DP table and return it
    return dp[ind] = max(pick, nonPick);
}

// Function to initiate the solving process
int solve(int n, vector<int>& arr) {
    vector<int> dp(n, -1); // Initialize the DP table with -1
    return solveUtil(n - 1, arr, dp); // Start solving from the last element
}

int main() {
    vector<int> arr{2, 1, 4, 9};
    int n = arr.size();
    
    // Call the solve function and print the result
    cout << solve(n, arr);

    return 0;
}



```

## Solution 2. DP Bottom-Up

solveUtil is the function that fills the dp array, where dp[i] represents the maximum sum that can be obtained from the first i+1 elements.
At each index i, the code either picks the current element (and adds the value from dp[i-2]) or does not pick it (taking the value from dp[i-1]), and stores the maximum of these two choices.
The result is stored in dp[n-1], which contains the maximum sum possible without picking adjacent elements.

```cpp

// Time: O(N)
// Space: O(N)


#include <bits/stdc++.h>
using namespace std;

// Function to solve the problem using dynamic programming
int solveUtil(int n, vector<int>& arr, vector<int>& dp) {
    // Base case: If there are no elements in the array, return 0
    dp[0] = arr[0];
    
    // Iterate through the elements of the array
    for (int i = 1; i < n; i++) {
        // Calculate the maximum value by either picking the current element
        // or not picking it (i.e., taking the maximum of dp[i-2] + arr[i] and dp[i-1])
        int pick = arr[i];
        if (i > 1)
            pick += dp[i - 2];
        int nonPick = dp[i - 1];
        
        // Store the maximum value in the dp array
        dp[i] = max(pick, nonPick);
    }
    
    // The last element of the dp array will contain the maximum sum
    return dp[n - 1];
}

// Function to initiate the solving process
int solve(int n, vector<int>& arr) {
    vector<int> dp(n, 0); // Initialize dp array with 0
    return solveUtil(n, arr, dp);
}

int main() {
    vector<int> arr{2, 1, 4, 9};
    int n = arr.size();
    
    // Call the solve function and print the result
    cout << solve(n, arr);

    return 0;
}


```

## Solution 3. DP Space Optimization

Variable Usage: Instead of using a full dp array to store maximum sums up to each index, the code utilizes only two variables, prev and prev2. This allows the program to maintain the necessary state information without storing all previous results.
Iterative Calculation: The function iteratively calculates the maximum sum at each index by considering whether to pick the current element or not, updating prev and prev2 accordingly. This avoids the overhead of additional memory allocation and simplifies the logic.
Efficiency: This results in a more efficient solution both in terms of space and time while maintaining the same O(n) time complexity, as it processes each element of the array only once.

```cpp

// Time: O(N)
// Space: O(1)


#include <bits/stdc++.h>
using namespace std;

// Function to solve the problem using dynamic programming
int solve(int n, vector<int>& arr) {
    int prev = arr[0];   // Initialize the maximum sum ending at the previous element
    int prev2 = 0;       // Initialize the maximum sum ending two elements ago
    
    for (int i = 1; i < n; i++) {
        int pick = arr[i];  // Maximum sum if we pick the current element
        if (i > 1)
            pick += prev2;  // Add the maximum sum two elements ago
        
        int nonPick = 0 + prev;  // Maximum sum if we don't pick the current element
        
        int cur_i = max(pick, nonPick);  // Maximum sum ending at the current element
        prev2 = prev;   // Update the maximum sum two elements ago
        prev = cur_i;   // Update the maximum sum ending at the previous element
    }
    
    return prev;  // Return the maximum sum
}

int main() {
    vector<int> arr{2, 1, 4, 9};
    int n = arr.size();
    
    // Call the solve function and print the result
    cout << solve(n, arr);

    return 0;
}



```
