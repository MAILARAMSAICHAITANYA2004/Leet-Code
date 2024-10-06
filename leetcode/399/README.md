# [399. Evaluate Division (Medium)](https://leetcode.com/problems/evaluate-division/)

<p>You are given <code>equations</code>&nbsp;in the format <code>A / B = k</code>, where <code>A</code> and <code>B</code> are variables represented as strings, and <code>k</code> is a real number (floating-point number). Given some <code>queries</code>, return <em>the answers</em>. If the answer does not exist, return <code>-1.0</code>.</p>

<p>The input is always valid. You may assume that evaluating the queries will result in no division by zero and there is no contradiction.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<pre><strong>Input:</strong> equations = [["a","b"],["b","c"]], values = [2.0,3.0], queries = [["a","c"],["b","a"],["a","e"],["a","a"],["x","x"]]
<strong>Output:</strong> [6.00000,0.50000,-1.00000,1.00000,-1.00000]
<strong>Explanation:</strong> 
Given: <em>a / b = 2.0</em>, <em>b / c = 3.0</em>
queries are: <em>a / c = ?</em>, <em>b / a = ?</em>, <em>a / e = ?</em>, <em>a / a = ?</em>, <em>x / x = ?</em>
return: [6.0, 0.5, -1.0, 1.0, -1.0 ]
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> equations = [["a","b"],["b","c"],["bc","cd"]], values = [1.5,2.5,5.0], queries = [["a","c"],["c","b"],["bc","cd"],["cd","bc"]]
<strong>Output:</strong> [3.75000,0.40000,5.00000,0.20000]
</pre>

<p><strong>Example 3:</strong></p>

<pre><strong>Input:</strong> equations = [["a","b"]], values = [0.5], queries = [["a","b"],["b","a"],["a","c"],["x","y"]]
<strong>Output:</strong> [0.50000,2.00000,-1.00000,-1.00000]
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= equations.length &lt;= 20</code></li>
	<li><code>equations[i].length == 2</code></li>
	<li><code>1 &lt;= equations[i][0], equations[i][1] &lt;= 5</code></li>
	<li><code>values.length ==&nbsp;equations.length</code></li>
	<li><code>0.0 &lt;&nbsp;values[i] &lt;= 20.0</code></li>
	<li><code>1 &lt;= queries.length &lt;= 20</code></li>
	<li><code>queries[i].length == 2</code></li>
	<li><code>1 &lt;= queries[i][0], queries[i][1] &lt;= 5</code></li>
	<li><code>equations[i][0], equations[i][1],&nbsp;queries[i][0], queries[i][1]</code>&nbsp;consist of lower case English letters and digits.</li>
</ul>


**Related Topics**:  
[Union Find](https://leetcode.com/tag/union-find/), [Graph](https://leetcode.com/tag/graph/)



## Solution 1. DFS

Graph Construction: It builds a directed graph using an adjacency list, where each variable is a node. For each equation a/b = k, it creates two entries: graph[a][b]=k and graph[b][a]=1/k.
Query Processing: For each query a/b, it checks if both variables exist in the graph. If they do, it uses Depth-First Search (DFS) to find a path from a to b, multiplying the values along the path to compute the result.
Result Compilation: If a valid path is found, the computed value is added to the results; otherwise, -1 is returned if no valid path exists or if either variable is missing.

```cpp
// OJ: https://leetcode.com/problems/evaluate-division/
// Author: github.com/lzl124631x

class Solution {
    unordered_map<string, unordered_map<string, double>> graph; // Adjacency list to store equations and their values

    // Depth-first search to find the value of src / dst
    double dfs(const string &src, const string &dst, unordered_set<string> &visited) {
        if (graph[src].count(dst)) return graph[src][dst]; // Return precomputed value if exists

        for (const auto &[neighbor, value] : graph[src]) {
            if (visited.count(neighbor)) continue; // Skip visited neighbors
            visited.insert(neighbor); // Mark neighbor as visited
            double result = dfs(neighbor, dst, visited); // Recursive call

            // If a valid path is found, calculate the resulting value
            if (result != -1) return value * result;
        }
        return -1; // Return -1 if no valid path is found
    }

public:
    vector<double> calcEquation(vector<vector<string>> &equations, vector<double> &values, vector<vector<string>> &queries) {
        // Build the graph from equations and values
        for (int i = 0; i < equations.size(); ++i) {
            graph[equations[i][0]][equations[i][1]] = values[i]; // a / b = value
            graph[equations[i][1]][equations[i][0]] = 1.0 / values[i]; // b / a = 1 / value
        }
        
        vector<double> results; // Store results for each query
        for (const auto &q : queries) {
            // Check if both variables exist in the graph
            if (!graph.count(q[0]) || !graph.count(q[1])) {
                results.push_back(-1); // -1 if either variable is not found
            } else if (q[0] == q[1]) {
                results.push_back(1); // a / a = 1
            } else {
                unordered_set<string> visited{q[0]}; // Initialize visited set with the source
                results.push_back(dfs(q[0], q[1], visited)); // Compute the result using DFS
            }
        }
        return results; // Return computed results
    }
};

```



## Solution 2. DSU

Graph Representation: Each variable is assigned a unique ID, and a Disjoint Set is initialized to represent connections (equations) between variables.

Union Operation: For each equation a/b=k, the unionByValue method links the variables, updating their parent-child relationships and storing the ratio values to maintain the division relationships.

Query Processing: For each query a/b, it checks if both variables exist and if they belong to the same connected component. If they do, it computes the value of the division using the stored ratios; otherwise, it returns -1.0.

```cpp
class DisjointSet {
    vector<int> parent, size;
    vector<double> value;
public:
    DisjointSet(int n) {
        parent.resize(n);
        size.resize(n, 1);
        value.resize(n, 1.0);
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }

    int find(int x) {
        if (x != parent[x]) {
            int originalParent = parent[x];
            parent[x] = find(parent[x]);
            value[x] *= value[originalParent]; // update value during path compression
        }
        return parent[x];
    }

    void unionByValue(int x, int y, double ratio) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (size[rootX] < size[rootY]) {
                parent[rootX] = rootY;
                value[rootX] = value[y] * ratio / value[x];
                size[rootY] += size[rootX];
            } else {
                parent[rootY] = rootX;
                value[rootY] = value[x] / ratio / value[y];
                size[rootX] += size[rootY];
            }
        }
    }

    double getValue(int x, int y) {
        return value[x] / value[y];
    }
};

class Solution {
public:
    vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries) {
        unordered_map<string, int> id;
        int idCounter = 0;

        // Assign an ID to each variable
        for (const auto& eq : equations) {
            if (id.find(eq[0]) == id.end()) id[eq[0]] = idCounter++;
            if (id.find(eq[1]) == id.end()) id[eq[1]] = idCounter++;
        }

        DisjointSet ds(idCounter);

        // Union each equation
        for (int i = 0; i < equations.size(); i++) {
            const string& var1 = equations[i][0];
            const string& var2 = equations[i][1];
            ds.unionByValue(id[var1], id[var2], values[i]);
        }

        vector<double> res;
        // Process each query
        for (const auto& q : queries) {
            const string& var1 = q[0];
            const string& var2 = q[1];
            if (id.find(var1) == id.end() || id.find(var2) == id.end() || ds.find(id[var1]) != ds.find(id[var2])) {
                res.push_back(-1.0);
            } else {
                res.push_back(ds.getValue(id[var1], id[var2]));
            }
        }

        return res;
    }
};




```



## Solution 3. Floyd Warshall

Graph Representation: It initializes a distance matrix and maps each variable to a unique node. For each equation a/b=k, it updates the distances for both aa to bb and bb to aa (the inverse).

Floyd-Warshall Algorithm: This algorithm computes the shortest paths between all pairs of nodes, updating the distance matrix based on intermediary nodes to find possible division paths.

Query Processing: For each query a/b, it checks if both variables exist in the node map and if a valid path exists in the distance matrix. If valid, it retrieves the division result; otherwise, it returns -1.0.

```cpp

#define FLOAT_MAX DBL_MAX

class Solution {
public:
    vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries) {
        // Initialize distance matrix and node mapping
        vector<vector<double>> distances(2 * equations.size() + 1, vector<double>(2 * equations.size() + 1, FLOAT_MAX));
        unordered_map<string, int> nodes;
        int node = 1;

        // Construct the adjacency list for the graphs
        for (int i = 0; i < equations.size(); i++) {
            string s = equations[i][0], d = equations[i][1];
            double val = values[i];
            if (!nodes[s]) nodes[s] = node++;
            if (!nodes[d]) nodes[d] = node++;
            distances[nodes[s]][nodes[s]] = 1; // Self distance is 1
            distances[nodes[d]][nodes[d]] = 1; // Self distance is 1
            distances[nodes[s]][nodes[d]] = val; // Direct distance
            distances[nodes[d]][nodes[s]] = 1 / val; // Inverse distance
        }

        // Floyd-Warshall algorithm to compute all-pairs shortest paths
        for (int k = 1; k <= nodes.size(); k++) {
            for (int i = 1; i <= nodes.size(); i++) {
                for (int j = 1; j <= nodes.size(); j++) {
                    if (i == j || i == k || j == k) continue;
                    if (distances[i][k] != FLOAT_MAX && distances[k][j] != FLOAT_MAX)
                        distances[i][j] = min(distances[i][j], distances[i][k] * distances[k][j]);
                }
            }
        }

        vector<double> soln;
        // Process each query to find the results
        for (int i = 0; i < queries.size(); i++) {
            if (!nodes[queries[i][0]] || !nodes[queries[i][1]] || (distances[nodes[queries[i][0]]][nodes[queries[i][1]]] == FLOAT_MAX))
                soln.push_back(-1.0); // No valid path
            else
                soln.push_back(distances[nodes[queries[i][0]]][nodes[queries[i][1]]]); // Valid path
        }
        return soln; // Return results
    }
};



```

