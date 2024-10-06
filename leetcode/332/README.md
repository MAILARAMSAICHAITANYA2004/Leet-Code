# [332. Reconstruct Itinerary (Hard)](https://leetcode.com/problems/reconstruct-itinerary/)

<p>You are given a list of airline <code>tickets</code> where <code>tickets[i] = [from<sub>i</sub>, to<sub>i</sub>]</code> represent the departure and the arrival airports of one flight. Reconstruct the itinerary in order and return it.</p>

<p>All of the tickets belong to a man who departs from <code>"JFK"</code>, thus, the itinerary must begin with <code>"JFK"</code>. If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string.</p>

<ul>
	<li>For example, the itinerary <code>["JFK", "LGA"]</code> has a smaller lexical order than <code>["JFK", "LGB"]</code>.</li>
</ul>

<p>You may assume all tickets form at least one valid itinerary. You must use all the tickets once and only once.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/03/14/itinerary1-graph.jpg" style="width: 382px; height: 222px;">
<pre><strong>Input:</strong> tickets = [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
<strong>Output:</strong> ["JFK","MUC","LHR","SFO","SJC"]
</pre>

<p><strong>Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/03/14/itinerary2-graph.jpg" style="width: 222px; height: 230px;">
<pre><strong>Input:</strong> tickets = [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
<strong>Output:</strong> ["JFK","ATL","JFK","SFO","ATL","SFO"]
<strong>Explanation:</strong> Another possible reconstruction is ["JFK","SFO","ATL","JFK","ATL","SFO"] but it is larger in lexical order.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= tickets.length &lt;= 300</code></li>
	<li><code>tickets[i].length == 2</code></li>
	<li><code>from<sub>i</sub>.length == 3</code></li>
	<li><code>to<sub>i</sub>.length == 3</code></li>
	<li><code>from<sub>i</sub></code> and <code>to<sub>i</sub></code> consist of uppercase English letters.</li>
	<li><code>from<sub>i</sub> != to<sub>i</sub></code></li>
</ul>


**Companies**:  
[Media.net](https://leetcode.com/company/medianet), [Facebook](https://leetcode.com/company/facebook), [Uber](https://leetcode.com/company/uber), [Directi](https://leetcode.com/company/directi), [Amazon](https://leetcode.com/company/amazon), [Google](https://leetcode.com/company/google), [Twilio](https://leetcode.com/company/twilio), [DoorDash](https://leetcode.com/company/doordash), [Walmart Labs](https://leetcode.com/company/walmart-labs)

**Related Topics**:  
[Depth-First Search](https://leetcode.com/tag/depth-first-search/), [Graph](https://leetcode.com/tag/graph/), [Eulerian Circuit](https://leetcode.com/tag/eulerian-circuit/)

**Similar Questions**:
* [Longest Common Subpath (Hard)](https://leetcode.com/problems/longest-common-subpath/)
* [Valid Arrangement of Pairs (Hard)](https://leetcode.com/problems/valid-arrangement-of-pairs/)

## Solution 1. DFS

For each node, try each neighbor node in ascending lexical order. The first path that reaches `N + 1` nodes is the answer.

The code finds an itinerary that visits all given flights exactly once, starting from "JFK." It constructs a 
graph using an adjacency list to represent flights and sorts the destinations lexicographically. The solution 
employs Depth-First Search (DFS) to explore possible itineraries. If all flights are used, it returns the valid 
itinerary. If a path doesn't work, it backtracks by removing the last destination and marking the flight as 
unused. The process continues until a valid itinerary is found or all options are exhausted.

```cpp
// OJ: https://leetcode.com/problems/reconstruct-itinerary/
// Author: github.com/lzl124631x
// Time: O(E^2)
// Space: O(E)

// For each node, try each neighbor node in ascending lexical order. The first path that reaches N + 1 nodes is the answer.
// It is given that we may assume all tickets form at least one valid itinerary. You must use all the tickets once and only once.
// we are keeping idx for making sure that we won't choose same path again, not the dest

class Solution {
public:
    vector<string> findItinerary(vector<vector<string>>& flights) {
        unordered_map<string, vector<pair<string, int>>> adjList;  // Adjacency list for graph representation
        int flightCount = flights.size();
        vector<bool> used(flightCount);  // Tracks whether a flight has been used in the itinerary
        vector<string> itinerary = {"JFK"};  // Start the itinerary from "JFK"
        
        // Build the adjacency list
        for (int i = 0; i < flightCount; ++i) 
            adjList[flights[i][0]].emplace_back(flights[i][1], i);
        
        // Sort destinations in lexicographical order for each source
        for (auto &[src, dests] : adjList) 
            sort(begin(dests), end(dests));
        
        // Perform DFS to find valid itinerary
        dfs(itinerary, adjList, used, flightCount);
        return itinerary;
    }
    
private:
    bool dfs(vector<string> &itinerary, unordered_map<string, vector<pair<string, int>>> &adjList, vector<bool> &used, int flightCount) {
        // Base case: If the itinerary includes all flights
        if (itinerary.size() == flightCount + 1) return true;
        
        // Explore each possible destination from the current airport
        for (auto &[nextDest, idx] : adjList[itinerary.back()]) {
            if (used[idx]) continue;  // Skip used flights
            used[idx] = true;  // Mark flight as used
            itinerary.push_back(nextDest);  // Add destination to itinerary
            if (dfs(itinerary, adjList, used, flightCount)) return true;  // Recursive DFS call
            itinerary.pop_back();  // Backtrack if this path doesn't work
            used[idx] = false;  // Unmark flight
        }
        return false;
    }
};
```

## Solution 2. Eulerian Path

The code constructs an itinerary that visits all flights exactly once, starting from "JFK." It uses a graph representation
with an adjacency list, where each airport maps to a multiset of its lexicographically sorted destinations. 
The solution employs Depth-First Search (DFS) to explore all possible paths from the current airport. It recursively 
visits the smallest destination, removes it from the multiset, and adds the airport to the itinerary once all its 
destinations are visited. Finally, the itinerary is reversed to reflect the correct order of travel before being returned.

```cpp
// OJ: https://leetcode.com/problems/reconstruct-itinerary/
// Author: github.com/lzl124631x
// Time: O(E^2)
// Space: O(E)
// Ref: https://leetcode.com/problems/reconstruct-itinerary/discuss/78768/Short-Ruby-Python-Java-C%2B%2B
class Solution {
public:
    vector<string> findItinerary(vector<vector<string>>& flights) {
        unordered_map<string, multiset<string>> adjList;  // Graph with multiple destinations sorted lexicographically
        
        // Build the graph by adding each flight's destination to the source's multiset
        for (auto &flight : flights) 
            adjList[flight[0]].insert(flight[1]);
        
        vector<string> itinerary;
        dfs("JFK", adjList, itinerary);  // Start DFS from "JFK"
        
        // Reverse the itinerary to get the correct order
        return vector<string>(rbegin(itinerary), rend(itinerary));
    }
    
private:
    // Depth-First Search to construct the itinerary
    void dfs(string airport, unordered_map<string, multiset<string>> &adjList, vector<string> &itinerary) {
        while (!adjList[airport].empty()) {  // Explore all destinations from the current airport
            auto next = *adjList[airport].begin();  // Get the smallest lexicographical destination
            adjList[airport].erase(adjList[airport].begin());  // Remove it from the set
            dfs(next, adjList, itinerary);  // Recursive DFS call to visit the next destination
        }
        itinerary.push_back(airport);  // Add the airport to the itinerary when all destinations are visited
    }
};

```
