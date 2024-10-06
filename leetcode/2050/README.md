# [2050. Parallel Courses III (Hard)](https://leetcode.com/problems/parallel-courses-iii)

<p>You are given an integer <code>n</code>, which indicates that there are <code>n</code> courses labeled from <code>1</code> to <code>n</code>. You are also given a 2D integer array <code>relations</code> where <code>relations[j] = [prevCourse<sub>j</sub>, nextCourse<sub>j</sub>]</code> denotes that course <code>prevCourse<sub>j</sub></code> has to be completed <strong>before</strong> course <code>nextCourse<sub>j</sub></code> (prerequisite relationship). Furthermore, you are given a <strong>0-indexed</strong> integer array <code>time</code> where <code>time[i]</code> denotes how many <strong>months</strong> it takes to complete the <code>(i+1)<sup>th</sup></code> course.</p>

<p>You must find the <strong>minimum</strong> number of months needed to complete all the courses following these rules:</p>

<ul>
	<li>You may start taking a course at <strong>any time</strong> if the prerequisites are met.</li>
	<li><strong>Any number of courses</strong> can be taken at the <strong>same time</strong>.</li>
</ul>

<p>Return <em>the <strong>minimum</strong> number of months needed to complete all the courses</em>.</p>

<p><strong>Note:</strong> The test cases are generated such that it is possible to complete every course (i.e., the graph is a directed acyclic graph).</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>
<strong><img alt="" src="https://assets.leetcode.com/uploads/2021/10/07/ex1.png" style="width: 392px; height: 232px;" /></strong>

<pre>
<strong>Input:</strong> n = 3, relations = [[1,3],[2,3]], time = [3,2,5]
<strong>Output:</strong> 8
<strong>Explanation:</strong> The figure above represents the given graph and the time required to complete each course. 
We start course 1 and course 2 simultaneously at month 0.
Course 1 takes 3 months and course 2 takes 2 months to complete respectively.
Thus, the earliest time we can start course 3 is at month 3, and the total time required is 3 + 5 = 8 months.
</pre>

<p><strong class="example">Example 2:</strong></p>
<strong><img alt="" src="https://assets.leetcode.com/uploads/2021/10/07/ex2.png" style="width: 500px; height: 365px;" /></strong>

<pre>
<strong>Input:</strong> n = 5, relations = [[1,5],[2,5],[3,5],[3,4],[4,5]], time = [1,2,3,4,5]
<strong>Output:</strong> 12
<strong>Explanation:</strong> The figure above represents the given graph and the time required to complete each course.
You can start courses 1, 2, and 3 at month 0.
You can complete them after 1, 2, and 3 months respectively.
Course 4 can be taken only after course 3 is completed, i.e., after 3 months. It is completed after 3 + 4 = 7 months.
Course 5 can be taken only after courses 1, 2, 3, and 4 have been completed, i.e., after max(1,2,3,7) = 7 months.
Thus, the minimum time needed to complete all the courses is 7 + 5 = 12 months.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= n &lt;= 5 * 10<sup>4</sup></code></li>
	<li><code>0 &lt;= relations.length &lt;= min(n * (n - 1) / 2, 5 * 10<sup>4</sup>)</code></li>
	<li><code>relations[j].length == 2</code></li>
	<li><code>1 &lt;= prevCourse<sub>j</sub>, nextCourse<sub>j</sub> &lt;= n</code></li>
	<li><code>prevCourse<sub>j</sub> != nextCourse<sub>j</sub></code></li>
	<li>All the pairs <code>[prevCourse<sub>j</sub>, nextCourse<sub>j</sub>]</code> are <strong>unique</strong>.</li>
	<li><code>time.length == n</code></li>
	<li><code>1 &lt;= time[i] &lt;= 10<sup>4</sup></code></li>
	<li>The given graph is a directed acyclic graph.</li>
</ul>


**Companies**:
[Pinterest](https://leetcode.com/company/pinterest), [Google](https://leetcode.com/company/google)

**Related Topics**:  
[Array](https://leetcode.com/tag/array), [Dynamic Programming](https://leetcode.com/tag/dynamic-programming), [Graph](https://leetcode.com/tag/graph), [Topological Sort](https://leetcode.com/tag/topological-sort)

**Similar Questions**:
* [Course Schedule III (Hard)](https://leetcode.com/problems/course-schedule-iii)
* [Parallel Courses (Medium)](https://leetcode.com/problems/parallel-courses)
* [Single-Threaded CPU (Medium)](https://leetcode.com/problems/single-threaded-cpu)
* [Process Tasks Using Servers (Medium)](https://leetcode.com/problems/process-tasks-using-servers)
* [Maximum Employees to Be Invited to a Meeting (Hard)](https://leetcode.com/problems/maximum-employees-to-be-invited-to-a-meeting)

**Hints**:
* What is the earliest time a course can be taken?
* How would you solve the problem if all courses take equal time?
* How would you generalize this approach?

## Solution 1. Topological Sort (BFS)

A node's distance is its time plus the maximum distance of all predecessor nodes. We can calculate the distances via a topological sort. The answer is the maximum distance.

The code finds the minimum time to finish all courses with prerequisites using topological sorting. It builds a graph where each node represents a course, 
and edges represent prerequisites. Courses with no prerequisites (indegree 0) are added to a queue. For each course processed, it updates the time 
required for dependent courses by taking the maximum of the current time and the new time (course time + prerequisite time). Once all courses are 
processed, the maximum time among all courses is returned as the answer.

```cpp
// OJ: https://leetcode.com/problems/parallel-courses-iii/
// Author: github.com/lzl124631x
// Time: O(N + E)
// Space: O(N + E)
class Solution {
public:
    int minimumTime(int n, vector<vector<int>>& E, vector<int>& T) {
        vector<vector<int>> G(n); // adjacency list
        vector<int> indegree(n), dist(n); // track indegrees and distances
        for (auto &e : E) { // build graph and count indegrees
            G[e[0] - 1].push_back(e[1] - 1);
            indegree[e[1] - 1]++;
        }
        queue<int> q;
        for (int i = 0; i < n; ++i) {
            if (indegree[i] == 0) { // enqueue nodes with 0 indegree
                q.push(i);
                dist[i] = T[i]; // initial distance is the time for each course
            } 
        }
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            for (int v : G[u]) {
                dist[v] = max(dist[u] + T[v], dist[v]); // update distance of successor
                if (--indegree[v] == 0) q.push(v); // enqueue node when indegree becomes 0
            }
        }
        return *max_element(begin(dist), end(dist)); // maximum distance is the result
    }
};

```

## Solution 2. Topological Sort (DFS)

DFS version topological sort is **Post-order Traversal + Memo**.

The code calculates the minimum time required to complete all courses with given prerequisites using Depth-First Search (DFS). It constructs a 
directed graph where each course points to its prerequisites. For each course, the DFS explores its dependencies, calculating the maximum 
time needed to complete all prerequisite courses before the current course. The total time for each course is the sum of its own time and 
the maximum time of its prerequisites. Finally, the maximum time across all courses is returned as the result.

```cpp
// OJ: https://leetcode.com/problems/parallel-courses-iii/
// Author: github.com/lzl124631x
// Time: O(N + E)
// Space: O(N + E)
class Solution {
public:
    int minimumTime(int n, vector<vector<int>>& E, vector<int>& T) {
        vector<vector<int>> G(n); // adjacency list
        vector<int> dist(n); // store the time to finish each course
        
        // Build the graph
        for (auto &e : E) G[e[1] - 1].push_back(e[0] - 1);
        
        // Run DFS for each course
        for (int i = 0; i < n; ++i) dfs(i, G, T, dist);
        
        return *max_element(begin(dist), end(dist)); // return the max time
    }

    // DFS function to calculate the time to finish course `u`
    int dfs(int u, vector<vector<int>>& G, vector<int>& T, vector<int>& dist) {
        if (dist[u]) return dist[u]; // if already computed, return the result
        int mx = 0;
        for (int v : G[u]) mx = max(mx, dfs(v, G, T, dist)); // find max time of prerequisites
        return dist[u] = mx + T[u]; // calculate time to finish course `u`
    }
};

```

## Discuss

https://leetcode.com/problems/parallel-courses-iii/discuss/1537501/C%2B%2B-Topological-Sort
