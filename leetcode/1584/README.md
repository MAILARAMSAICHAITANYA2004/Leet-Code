# [1584. Min Cost to Connect All Points (Medium)](https://leetcode.com/problems/min-cost-to-connect-all-points)

<p>You are given an array <code>points</code> representing integer coordinates of some points on a 2D-plane, where <code>points[i] = [x<sub>i</sub>, y<sub>i</sub>]</code>.</p>

<p>The cost of connecting two points <code>[x<sub>i</sub>, y<sub>i</sub>]</code> and <code>[x<sub>j</sub>, y<sub>j</sub>]</code> is the <strong>manhattan distance</strong> between them: <code>|x<sub>i</sub> - x<sub>j</sub>| + |y<sub>i</sub> - y<sub>j</sub>|</code>, where <code>|val|</code> denotes the absolute value of <code>val</code>.</p>

<p>Return <em>the minimum cost to make all points connected.</em> All points are connected if there is <strong>exactly one</strong> simple path between any two points.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/08/26/d.png" style="width: 214px; height: 268px;" />
<pre>
<strong>Input:</strong> points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
<strong>Output:</strong> 20
<strong>Explanation:</strong> 
<img alt="" src="https://assets.leetcode.com/uploads/2020/08/26/c.png" style="width: 214px; height: 268px;" />
We can connect the points as shown above to get the minimum cost of 20.
Notice that there is a unique path between every pair of points.
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> points = [[3,12],[-2,5],[-4,1]]
<strong>Output:</strong> 18
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= points.length &lt;= 1000</code></li>
	<li><code>-10<sup>6</sup> &lt;= x<sub>i</sub>, y<sub>i</sub> &lt;= 10<sup>6</sup></code></li>
	<li>All pairs <code>(x<sub>i</sub>, y<sub>i</sub>)</code> are distinct.</li>
</ul>


**Companies**:
[Microsoft](https://leetcode.com/company/microsoft), [Amazon](https://leetcode.com/company/amazon), [Directi](https://leetcode.com/company/directi)

**Related Topics**:  
[Array](https://leetcode.com/tag/array), [Union Find](https://leetcode.com/tag/union-find), [Graph](https://leetcode.com/tag/graph), [Minimum Spanning Tree](https://leetcode.com/tag/minimum-spanning-tree)

**Similar Questions**:
* [Minimum Number of Lines to Cover Points (Medium)](https://leetcode.com/problems/minimum-number-of-lines-to-cover-points)

## Solution 1. Kruskal

The run time is too restrict. If you sort the edges then use Kruskal you will get TLE. The time complexity is `O(N^2 * log(N^2))`.

We have to use min heap instead so that the time complexity is `O(K * log(N^2))` where `K` is the number of edges we need to scan to complete the tree. It's much smaller than `N^2` on average.

The solution uses Kruskal's algorithm with a Union-Find data structure to find the minimum cost to connect all points in a 2D plane. It first computes the Manhattan distances 
between all pairs of points and stores them as edges in a priority queue. Then, it iteratively selects the smallest edge, checks if the two endpoints are already connected, 
and if not, connects them using the Union-Find structure. This process continues until all points are connected, accumulating the total cost of the connections, which is then returned as the result.

```cpp
// OJ: https://leetcode.com/problems/min-cost-to-connect-all-points/
// Author: github.com/lzl124631x
// Time: O(K * log(N^2)) where K is the number of edged we need to scan to complete the tree
// Space: O(N^2)
// Ref: https://leetcode.com/problems/min-cost-to-connect-all-points/discuss/843940/C%2B%2B-Minimum-Spanning-Tree-(Kruskal)
class UnionFind {
    vector<int> id;
    int size;
public:
    UnionFind(int N) : id(N), size(N) {
        iota(begin(id), end(id), 0); // Initialize each node as its own parent
    }
    int find(int a) {
        return id[a] == a ? a : (id[a] = find(id[a])); // Path compression
    }
    void connect(int a, int b) {
        int p = find(a), q = find(b);
        if (p == q) return; // If already connected, do nothing
        id[p] = q; // Union
        --size;
    }
    bool connected(int a, int b) {
        return find(a) == find(b); // Check if two nodes are in the same set
    }
    int getSize() { return size; } // Get the number of disjoint sets
};

class Solution {
    typedef array<int, 3> Edge;
public:
    int minCostConnectPoints(vector<vector<int>>& A) {
        int N = A.size(), ans = 0;
        priority_queue<Edge, vector<Edge>, greater<Edge>> q;
        
        // Push all edges (Manhattan distances between points) into the priority queue
        for (int i = 0; i < N; ++i) {
            for (int j = i + 1; j < N; ++j) q.push({ abs(A[i][0] - A[j][0]) + abs(A[i][1] - A[j][1]), i, j });
        }
        
        UnionFind uf(N);
        while (uf.getSize() > 1) {
            auto [w, u, v] = q.top(); // Get the smallest edge
            q.pop();
            if (uf.connected(u, v)) continue; // Skip if already connected
            uf.connect(u, v); // Connect the nodes
            ans += w; // Add the weight to the result
        } 
        return ans;
    }
};

```

## Solution 2. Prim

1. Start from a random node (we use `0` here), add it to the minimum spanning tree (MST).
1. From all the edges connecting nodes in the MST and those outside of the MST, find the edge with the mimimal cost, and add the corresponding node to the MST.
1. Repeat Step 2 until all the nodes are added into the MST.

The solution employs a greedy approach to find the minimum cost to connect all points in a 2D space using Prim's algorithm. It initializes an array to track the
minimum distances to unconnected nodes and a seen array to mark connected nodes. Starting from an arbitrary point, it repeatedly updates the distances to all 
unconnected neighbors based on the Manhattan distance. It then selects the next closest unconnected point, adds its distance to the total cost, and marks 
it as connected. This process continues until all points are connected, and the total cost is returned.

```cpp
// OJ: https://leetcode.com/problems/min-cost-to-connect-all-points/
// Author: github.com/lzl124631x
// Time: O(N^2)
// Space: O(N)
// Ref: https://leetcode.com/problems/min-cost-to-connect-all-points/discuss/843921/PythonGolang-Just-add-points-greedily
class Solution {
public:
    int minCostConnectPoints(vector<vector<int>>& A) {
        int N = A.size(), ans = 0, cur = 0;
        vector<int> dist(N, INT_MAX), seen(N); // Initialize distance and seen arrays
        
        for (int i = 0; i < N - 1; ++i) {
            int x = A[cur][0], y = A[cur][1];
            seen[cur] = 1; // Mark the current node as connected
            
            for (int j = 0; j < N; ++j) {
                if (seen[j]) continue; // Skip already connected nodes
                // Relax the distance for unconnected nodes using the current node
                dist[j] = min(dist[j], abs(A[j][0] - x) + abs(A[j][1] - y));
            }
            // Pick the unconnected node with the minimum distance
            cur = min_element(begin(dist), end(dist)) - begin(dist);
            ans += dist[cur]; // Add the minimum distance to the result
            dist[cur] = INT_MAX; // Mark this distance as used
        }
        return ans; // Return the total minimum cost
    }
};

```

Or the heap version. Note that the heap version is not more performant because all the nodes are interconnected in this graph. If the nodes are sparsely connected, the heap version is better.

The solution uses Prim's algorithm to find the minimum cost to connect all points in a 2D plane. It initializes a priority queue (min-heap) to keep track of the minimum 
distances to unconnected points. Starting from the first point, it repeatedly selects the point with the smallest distance, marks it as connected, and updates the 
distances to its unconnected neighbors based on the Manhattan distance. The process continues until all points are connected, and the total cost is accumulated and returned as the result.

```cpp
// OJ: https://leetcode.com/problems/min-cost-to-connect-all-points
// Author: github.com/lzl124631x
// Time: O(N^2 * log(N^2))
// Space: O(N^2)
class Solution {
public:
    int minCostConnectPoints(vector<vector<int>>& A) {
        int N = A.size(), ans = 0;
        typedef pair<int, int> PII;
        priority_queue<PII, vector<PII>, greater<>> pq; // Min-heap for the minimum cost edges
        vector<int> dist(N, INT_MAX), seen(N); // Distance and seen arrays
        dist[0] = 0; // Start from the first point
        pq.emplace(0, 0); // Push the starting point into the priority queue

        while (pq.size()) {
            auto [cost, i] = pq.top(); // Get the node with the minimum cost
            pq.pop();
            if (seen[i]) continue; // Skip if already connected
            
            ans += dist[i]; // Add the cost to the result
            seen[i] = 1; // Mark the current node as connected
            
            for (int j = 0; j < N; ++j) {
                if (seen[j]) continue; // Skip already connected nodes
                // Calculate the Manhattan distance to unconnected nodes
                int d = abs(A[j][0] - A[i][0]) + abs(A[j][1] - A[i][1]);
                if (d < dist[j]) { // Update the distance if it's smaller
                    dist[j] = d;
                    pq.emplace(d, j); // Push the updated distance to the queue
                }
            }
        }
        return ans; // Return the total minimum cost
    }
};

```
