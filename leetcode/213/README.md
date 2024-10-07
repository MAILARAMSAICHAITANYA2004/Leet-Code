# [213. House Robber II (Medium)](https://leetcode.com/problems/house-robber-ii/)

<p>You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are <strong>arranged in a circle.</strong> That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have security system connected and&nbsp;<b>it will automatically contact the police if two adjacent houses were broken into on the same night</b>.</p>

<p>Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight <strong>without alerting the police</strong>.</p>

<p><strong>Example 1:</strong></p>

<pre><strong>Input:</strong> [2,3,2]
<strong>Output:</strong> 3
<strong>Explanation:</strong> You cannot rob house 1 (money = 2) and then rob house 3 (money = 2),
&nbsp;            because they are adjacent houses.
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> [1,2,3,1]
<strong>Output:</strong> 4
<strong>Explanation:</strong> Rob house 1 (money = 1) and then rob house 3 (money = 3).
&nbsp;            Total amount you can rob = 1 + 3 = 4.</pre>


**Related Topics**:  
[Dynamic Programming](https://leetcode.com/tag/dynamic-programming/)

**Similar Questions**:
* [House Robber (Easy)](https://leetcode.com/problems/house-robber/)
* [Paint House (Easy)](https://leetcode.com/problems/paint-house/)
* [Paint Fence (Easy)](https://leetcode.com/problems/paint-fence/)
* [House Robber III (Medium)](https://leetcode.com/problems/house-robber-iii/)
* [Non-negative Integers without Consecutive Ones (Hard)](https://leetcode.com/problems/non-negative-integers-without-consecutive-ones/)
* [Coin Path (Hard)](https://leetcode.com/problems/coin-path/)

## Solution 1. DP 

In this problem we can't directly use the solution of [198. House Robber (Easy)](https://leetcode.com/problems/house-robber/) becase the choice of robbing the first house could determine whether we can rob the last house.

Breaking the Circle: Since the first and last houses are adjacent, the circle is broken into two linear cases: one excluding the first house and another excluding the last house.
Max Profit Calculation: The solve function computes the maximum sum for a linear arrangement using dynamic programming, tracking the maximum sum including or excluding each house.
Final Result: The function robStreet calculates the maximum profit for both linear cases and returns the larger of the two.

```cpp
// OJ: https://leetcode.com/problems/house-robber-ii/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(N)
class Solution {
public:
    long long int solve(vector<int>& arr) {
        int n = arr.size();
        long long int prev = arr[0]; // Max sum up to the previous house
        long long int prev2 = 0; // Max sum up to two houses before
        
        for (int i = 1; i < n; i++) {
            long long int pick = arr[i]; // Include current house
            if (i > 1)
                pick += prev2; // Add max sum from two houses back
            long long int nonPick = 0 + prev; // Exclude current house
            
            // Store the max of picking or not picking the current house
            long long int cur_i = max(pick, nonPick);
            prev2 = prev;
            prev = cur_i;
        }
        return prev; // Max sum possible up to the last house
    }
    
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 1)
            return nums[0]; // Special case: only one house
        
        vector<int> arr1, arr2;
        
        // Create two arrays excluding the first and last houses respectively
        for (int i = 0; i < n; i++) {
            if (i != 0) arr1.push_back(nums[i]); // Exclude first house
            if (i != n - 1) arr2.push_back(nums[i]); // Exclude last house
        }

        // Max sum without first and without last houses
        long long int ans1 = solve(arr1);
        long long int ans2 = solve(arr2);
        
        // Return the maximum of the two possibilities
        return max(ans1, ans2);
    }
};


```


## Solution 2. DP Space Optimization

The optimization in this solution revolves around reducing space complexity by using only two variables (prev and prev2) instead of an additional DP array:

    Space Optimization: Instead of using a DP array to store results for each house, we only keep track of the last two results (prev and prev2). This saves space from O(n) to O(1).

    Circular Condition Handling: Since the houses are arranged in a circle, we split the problem into two cases:
        One excludes the first house (index 1 to n-1).
        The other excludes the last house (index 0 to n-2).

    Result: By solving both subproblems and returning the maximum result, the circular constraint is respected while maintaining an optimized space usage.

```cpp

// Time: O(N)
// Space: O(1)

class Solution {
public:
    long long int solve(int start, int end, vector<int>& nums) {
        long long int prev = nums[start]; // Max sum up to the previous house
        long long int prev2 = 0; // Max sum up to two houses before
        
        for (int i = start + 1; i <= end; i++) {
            long long int pick = nums[i]; // Include current house
            if (i > start + 1)
                pick += prev2; // Add max sum from two houses back
            long long int nonPick = 0 + prev; // Exclude current house
            
            // Store the max of picking or not picking the current house
            long long int cur_i = max(pick, nonPick);
            prev2 = prev;
            prev = cur_i;
        }
        return prev; // Max sum possible up to the last house in range
    }
    
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 1)
            return nums[0]; // Special case: only one house
        
        // Calculate max sum excluding the first house (index 1 to n-1)
        long long int ans1 = solve(1, n - 1, nums);
        
        // Calculate max sum excluding the last house (index 0 to n-2)
        long long int ans2 = solve(0, n - 2, nums);
        
        return max(ans1, ans2); // Return the maximum of the two
    }
};


```
