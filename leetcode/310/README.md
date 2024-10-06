# [310. Minimum Height Trees (Medium)](https://leetcode.com/problems/minimum-height-trees/)

<p>A tree is an undirected graph in which any two vertices are connected by&nbsp;<i>exactly</i>&nbsp;one path. In other words, any connected graph without simple cycles is a tree.</p>

<p>Given a tree of <code>n</code> nodes&nbsp;labelled from <code>0</code> to <code>n - 1</code>, and an array of&nbsp;<code>n - 1</code>&nbsp;<code>edges</code> where <code>edges[i] = [a<sub>i</sub>, b<sub>i</sub>]</code> indicates that there is an undirected edge between the two nodes&nbsp;<code>a<sub>i</sub></code> and&nbsp;<code>b<sub>i</sub></code> in the tree,&nbsp;you can choose any node of the tree as the root. When you select a node <code>x</code> as the root, the result tree has height <code>h</code>. Among all possible rooted trees, those with minimum height (i.e. <code>min(h)</code>)&nbsp; are called <strong>minimum height trees</strong> (MHTs).</p>

<p>Return <em>a list of all <strong>MHTs'</strong> root labels</em>.&nbsp;You can return the answer in <strong>any order</strong>.</p>

<p>The <strong>height</strong> of a rooted tree is the number of edges on the longest downward path between the root and a leaf.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/09/01/e1.jpg" style="width: 800px; height: 213px;">
<pre><strong>Input:</strong> n = 4, edges = [[1,0],[1,2],[1,3]]
<strong>Output:</strong> [1]
<strong>Explanation:</strong> As shown, the height of the tree is 1 when the root is the node with label 1 which is the only MHT.
</pre>

<p><strong>Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/09/01/e2.jpg" style="width: 800px; height: 321px;">
<pre><strong>Input:</strong> n = 6, edges = [[3,0],[3,1],[3,2],[3,4],[5,4]]
<strong>Output:</strong> [3,4]
</pre>

<p><strong>Example 3:</strong></p>

<pre><strong>Input:</strong> n = 1, edges = []
<strong>Output:</strong> [0]
</pre>

<p><strong>Example 4:</strong></p>

<pre><strong>Input:</strong> n = 2, edges = [[0,1]]
<strong>Output:</strong> [0,1]
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= n &lt;= 2 * 10<sup>4</sup></code></li>
	<li><code>edges.length == n - 1</code></li>
	<li><code>0 &lt;= a<sub>i</sub>, b<sub>i</sub> &lt; n</code></li>
	<li><code>a<sub>i</sub> != b<sub>i</sub></code></li>
	<li>All the pairs <code>(a<sub>i</sub>, b<sub>i</sub>)</code> are distinct.</li>
	<li>The given input is <strong>guaranteed</strong> to be a tree and there will be <strong>no repeated</strong> edges.</li>
</ul>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Facebook](https://leetcode.com/company/facebook), [Google](https://leetcode.com/company/google), [Microsoft](https://leetcode.com/company/microsoft)

**Related Topics**:  
[Depth-First Search](https://leetcode.com/tag/depth-first-search/), [Breadth-First Search](https://leetcode.com/tag/breadth-first-search/), [Graph](https://leetcode.com/tag/graph/), [Topological Sort](https://leetcode.com/tag/topological-sort/)

**Similar Questions**:
* [Course Schedule (Medium)](https://leetcode.com/problems/course-schedule/)
* [Course Schedule II (Medium)](https://leetcode.com/problems/course-schedule-ii/)


## Solution 1. BFS

Find the leaf nodes, trim them from the graph. Repeat this process until the graph only has 1 or 2 nodes left.

Graph Representation: It constructs an adjacency list from the edges and initializes an indegree array to count the number of connections for each node.
Leaf Identification: It enqueues all leaf nodes (nodes with an indegree of 1) into a queue.
Layered Removal: The algorithm iteratively removes leaf nodes while reducing the count of remaining nodes. This continues until only 1 or 2 nodes are left.
Result Compilation: The remaining nodes in the queue are the roots of the MHTs, which are collected and returned.

// Khan's algo - topo sort approach
// Peel of the outermost and then come layer by layer
// At most 2 nodes can be there in the graph at the end. So, we used while(n > 2)
// Changed indegree[it] == 0 to 1 here

```cpp
// OJ: https://leetcode.com/problems/minimum-height-trees
// Author: github.com/lzl124631x
// Time: O(V + E)
// Space: O(V + E)

class Solution {
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        if (n == 1) return { 0 }; // Handle the case for a single node

        vector<int> adj[n]; // Adjacency list
        vector<int> indegree(n, 0); // Degree of each node

        // Build the graph
        for(const auto& it : edges) {
            adj[it[0]].push_back(it[1]);
            adj[it[1]].push_back(it[0]);
            indegree[it[0]]++;
            indegree[it[1]]++;
        }
        
        queue<int> q;
        // Initialize queue with leaf nodes
        for (int i = 0; i < n; i++) {
            if (indegree[i] == 1) {
                q.push(i);
            }
        }
        
        vector<int> ans;

        // Remove leaves until 2 or fewer nodes remain
        while (n > 2) {
            int cnt = q.size();
            n -= cnt; // Reduce the number of nodes
            
            for (int i = 0; i < cnt; ++i) {
                int node = q.front(); // Get the leaf node
                q.pop();

                // Update indegree and add new leaves to the queue
                for (int it : adj[node]) {
                    indegree[it]--;
                    if (indegree[it] == 1) q.push(it);
                }
            }
        }

        // Remaining nodes are the roots of MHTs
        while (!q.empty()) {
            int t = q.front();
            ans.push_back(t);
            q.pop();
        }
        return ans;
    }
};


```
