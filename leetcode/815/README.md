# [815. Bus Routes (Hard)](https://leetcode.com/problems/bus-routes)

<p>You are given an array <code>routes</code> representing bus routes where <code>routes[i]</code> is a bus route that the <code>i<sup>th</sup></code> bus repeats forever.</p>

<ul>
	<li>For example, if <code>routes[0] = [1, 5, 7]</code>, this means that the <code>0<sup>th</sup></code> bus travels in the sequence <code>1 -&gt; 5 -&gt; 7 -&gt; 1 -&gt; 5 -&gt; 7 -&gt; 1 -&gt; ...</code> forever.</li>
</ul>

<p>You will start at the bus stop <code>source</code> (You are not on any bus initially), and you want to go to the bus stop <code>target</code>. You can travel between bus stops by buses only.</p>

<p>Return <em>the least number of buses you must take to travel from </em><code>source</code><em> to </em><code>target</code>. Return <code>-1</code> if it is not possible.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> routes = [[1,2,7],[3,6,7]], source = 1, target = 6
<strong>Output:</strong> 2
<strong>Explanation:</strong> The best strategy is take the first bus to the bus stop 7, then take the second bus to the bus stop 6.
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> routes = [[7,12],[4,5,15],[6],[15,19],[9,12,13]], source = 15, target = 12
<strong>Output:</strong> -1
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= routes.length &lt;= 500</code>.</li>
	<li><code>1 &lt;= routes[i].length &lt;= 10<sup>5</sup></code></li>
	<li>All the values of <code>routes[i]</code> are <strong>unique</strong>.</li>
	<li><code>sum(routes[i].length) &lt;= 10<sup>5</sup></code></li>
	<li><code>0 &lt;= routes[i][j] &lt; 10<sup>6</sup></code></li>
	<li><code>0 &lt;= source, target &lt; 10<sup>6</sup></code></li>
</ul>


**Companies**:
[Uber](https://leetcode.com/company/uber), [TikTok](https://leetcode.com/company/tiktok), [PhonePe](https://leetcode.com/company/phonepe), [Amazon](https://leetcode.com/company/amazon), [Google](https://leetcode.com/company/google), [Microsoft](https://leetcode.com/company/microsoft)

**Related Topics**:  
[Array](https://leetcode.com/tag/array), [Hash Table](https://leetcode.com/tag/hash-table), [Breadth-First Search](https://leetcode.com/tag/breadth-first-search)

**Similar Questions**:
* [Minimum Costs Using the Train Line (Hard)](https://leetcode.com/problems/minimum-costs-using-the-train-line)

## Solution 1. BFS

approach :

	Data Structures: It utilizes a map (stops) to link each bus stop to the buses that serve it. It also maintains a set (seen) to track visited stops and a set (busTaken) to avoid revisiting buses.
	BFS Traversal: Starting from the source stop, it explores all reachable stops by traversing through available buses. For each stop, it checks if it matches the target.
	Bus and Stop Management: It enqueues new stops while marking buses and stops as seen. Each complete level of BFS corresponds to taking an additional bus, and the process continues until the target is found or all possibilities are exhausted.
	Output: If the target stop is reached, it returns the number of buses taken; otherwise, it returns -1 if there is no possible route.

```cpp

// OJ: https://leetcode.com/problems/bus-routes/
// Author: github.com/lzl124631x
// Time: O(N) where N is the sum of lengths of elements in `A`
// Space: O(N)

class Solution {
public:
    int numBusesToDestination(vector<vector<int>>& A, int source, int target) {
        int N = A.size(), step = 0;
        unordered_map<int, vector<int>> stops; // Map stops to buses
        for (int i = 0; i < N; ++i) {
            for (int n : A[i]) stops[n].push_back(i);
        }
        
        unordered_set<int> seen{source}, busTaken; // Track seen stops and buses
        queue<int> q{{source}}; // Queue for BFS
        
        while (q.size()) {
            int cnt = q.size(); // Number of stops to process
            while (cnt--) {
                int u = q.front();
                q.pop();
                if (u == target) return step; // Found target
                
                // Process all buses from current stop
                for (int bus : stops[u]) {
                    if (busTaken.count(bus)) continue; // Skip already taken buses
                    busTaken.insert(bus);
                    for (int v : A[bus]) {
                        if (seen.count(v)) continue; // Skip already seen stops
                        q.push(v); // Add new stop to queue
                        seen.insert(v);
                    }
                }
            }
            ++step; // Increment bus count
        }
        return -1; // No route found
    }
};


```

## Solution 2. BFS

approach : 

	Input: It takes a list of bus routes A, the source stop, and the target stop.
	Initial Check: If the source is the same as the target, it returns 0 since no buses are needed.
	Mapping Creation: It creates a mapping of each stop to the buses that serve it and initializes a queue with the buses that can be reached from the source.
	Breadth-First Search (BFS): It performs a BFS, exploring each bus and its stops:
    	For each bus, it checks if it reaches the target. If so, it returns the number of buses taken (steps).
    	If a stop hasn't been visited, it adds it to the set of seen stops and pushes any new buses connected to that stop into the queue.

Output: If the target cannot be reached, it returns -1.

```cpp

// OJ: https://leetcode.com/problems/bus-routes
// Author: github.com/lzl124631x
// Time: O(N) where N is the sum of lengths of elements in `A`
// Space: O(N)

class Solution {
public:
    int numBusesToDestination(vector<vector<int>>& A, int source, int target) {
        if (source == target) return 0; // Check if source is the same as target
        int N = A.size(), step = 1;
        unordered_map<int, vector<int>> m; // Map stops to bus indices
        queue<int> q; // Queue for buses
        unordered_set<int> seenBus, seenStop; // Track seen buses and stops

        // Populate the mapping and queue for buses from source stops
        for (int i = 0; i < N; ++i) {
            for (int n : A[i]) {
                m[n].push_back(i);
                if (n == source) {
                    q.push(i);
                    seenBus.insert(i);
                }
            }
        }

        // BFS to find the shortest path
        while (q.size()) {
            int cnt = q.size();
            while (cnt--) {
                int fromBus = q.front();
                q.pop();
                for (int fromStop : A[fromBus]) {
                    if (seenStop.count(fromStop)) continue; // Skip seen stops
                    if (fromStop == target) return step; // Found target
                    seenStop.insert(fromStop);
                    for (int toBus : m[fromStop]) {
                        if (seenBus.count(toBus)) continue; // Skip seen buses
                        seenBus.insert(toBus);
                        q.push(toBus);
                    }
                }
            }
            ++step; // Increment bus count
        }
        return -1; // Target not reachable
    }
};

```
