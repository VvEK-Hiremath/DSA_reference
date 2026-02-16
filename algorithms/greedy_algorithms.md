# Greedy Algorithms — Concepts, Examples, and Pseudocode

This document explains the greedy algorithm design paradigm, the conditions under which greedy works, important proof techniques, and several classic examples with concise pseudocode and complexity notes.

## Table of Contents
- Greedy paradigm (intuition)
- Key properties and proof techniques
- Core examples
	- Activity Selection (Interval Scheduling)
	- Fractional Knapsack
	- Huffman Coding
	- Coin Change (canonical systems)
	- Job Sequencing with Deadlines (max profit)
- Graph algorithms using greedy strategy
	- Kruskal's MST
	- Prim's MST
	- Dijkstra's shortest paths
- Complexity & When To Use Greedy

## Greedy paradigm (intuition)

Greedy algorithms build a solution iteratively by making a locally optimal choice at each step, hoping these local choices lead to a global optimum. Greedy methods are usually simple and efficient but do not always produce optimal solutions.

Greedy works when two key properties hold:
- Greedy-choice property: A global optimal solution can be reached by choosing a locally optimal choice first.
- Optimal substructure: An optimal solution to the problem contains within it optimal solutions to subproblems.

Proof techniques typically used:
- Exchange argument: Show any optimal solution can be transformed step-by-step into the greedy solution without worsening the objective.
- Induction on problem size: Prove optimality assuming it holds for smaller instances.

---

## Core examples

### Activity Selection (Interval Scheduling)
- Problem: Given activities with start and finish times, select the maximum number of mutually non-overlapping activities.
- Greedy idea: Always pick the activity with the earliest finish time that is compatible with previously picked ones.
- Correctness: Exchange argument — if an optimal solution didn't pick the earliest-finishing activity, swapping in the earliest-finisher cannot reduce the number of activities and only expands available time.
- Time: $O(n\log n)$ if sorting by finish time; selection itself is $O(n)$.

Pseudocode:
```
procedure ActivitySelection(activities)
	sort activities by finish time
	selected := empty list
	lastFinish := -infinity
	for a in activities do
		if a.start >= lastFinish then
			append a to selected
			lastFinish := a.finish
	return selected
```

### Fractional Knapsack
- Problem: Given items with value and weight, and a capacity W, maximize value; items can be fractionally taken.
- Greedy idea: Take items in decreasing order of value/weight ratio until capacity is filled.
- Correctness: Exchange argument — any deviation where a lower-ratio fraction replaces a higher-ratio fraction can be improved by swapping.
- Time: $O(n\log n)$ for sorting; linear if using selection on ratios for special cases.

Pseudocode:
```
procedure FractionalKnapsack(items, W)
	sort items by value/weight descending
	remaining := W
	totalValue := 0
	for item in items do
		if item.weight <= remaining then
			take whole item
			remaining -= item.weight
			totalValue += item.value
		else
			take fraction = remaining / item.weight
			totalValue += item.value * fraction
			break
	return totalValue
```

### Huffman Coding (Optimal prefix codes)
- Problem: Given symbol frequencies, build a prefix-free binary code minimizing total encoded length.
- Greedy idea: Repeatedly merge the two least frequent symbols/nodes into a combined node; use a min-heap.
- Correctness: Proven optimal by an exchange/induction argument — the two least frequent symbols must be siblings in some optimal tree.
- Time: $O(n\log n)$ using a binary heap.

Pseudocode:
```
procedure Huffman(frequencies)
	create min-heap H of nodes with given frequencies
	while H.size > 1 do
		x := extractMin(H)
		y := extractMin(H)
		z := new node with freq = x.freq + y.freq
		z.left := x; z.right := y
		insert H, z
	return extractMin(H)  // root of Huffman tree
```

### Coin Change (Greedy for canonical systems)
- Problem: Make change for amount using minimum number of coins from a set of denominations.
- Greedy idea: Repeatedly take the largest denomination not exceeding the remaining amount.
- Correctness: Works for canonical systems (like many modern currencies) but fails for arbitrary coin sets; correctness typically shown by exchange argument when the coin system is canonical.
- Time: $O(k)$ after sorting denominations, where $k$ is number of coin types.

Pseudocode (canonical systems):
```
procedure GreedyCoinChange(denominations, amount)
	sort denominations descending
	result := empty map
	for coin in denominations do
		count := floor(amount / coin)
		result[coin] := count
		amount := amount - count * coin
	if amount == 0 then return result else return failure
```

### Job Sequencing with Deadlines (Max Profit)
- Problem: Each job has a deadline and profit; schedule at most one job per time slot to maximize profit.
- Greedy idea: Sort jobs by profit descending and place each job in the latest available slot before its deadline (use DSU or an array of slots).
- Time: $O(n\log n + n\alpha(n))$ if using union-find, or $O(n^2)$ naive.

Pseudocode (slot array simple):
```
procedure JobSequencing(jobs)
	sort jobs by profit descending
	maxDeadline := max(job.deadline)
	slots := array of size maxDeadline initialized to None
	for job in jobs do
		for t from min(maxDeadline, job.deadline) down to 1 do
			if slots[t] is None then
				slots[t] := job
				break
	return all non-empty slots
```

---

## Graph algorithms using greedy strategy

### Kruskal's Minimum Spanning Tree
- Idea: Sort edges by weight and add them if they don't form a cycle (use union-find).
- Greedy-choice: Always pick the next smallest edge that keeps forest acyclic.
- Time: $O(E\log E)$ for sorting + union-find costs.

Pseudocode:
```
procedure Kruskal(G)
	A := empty set
	sort edges E by weight ascending
	make-set for each vertex
	for (u,v) in E in sorted order do
		if find(u) != find(v) then
			add (u,v) to A
			union(u,v)
	return A
```

### Prim's Minimum Spanning Tree
- Idea: Grow a tree from a starting vertex by repeatedly adding the cheapest edge connecting the tree to a new vertex (use min-priority queue).
- Time: $O(E\log V)$ with binary heap, $O(E + V\log V)$ with Fibonacci heap.

Pseudocode:
```
procedure Prim(G, start)
	for v in V do
		key[v] := +infinity; parent[v] := null
	key[start] := 0
	Q := all vertices in min-priority queue keyed by key[]
	while Q not empty do
		u := extractMin(Q)
		for each v adjacent to u do
			if v in Q and weight(u,v) < key[v] then
				parent[v] := u
				key[v] := weight(u,v)
				decreaseKey(Q, v, key[v])
	return tree defined by parent[]
```

### Dijkstra's Shortest Paths
- Idea: Repeatedly select the vertex with the smallest tentative distance (greedy) and relax outgoing edges. Works for non-negative weights.
- Time: $O(E\log V)$ with binary heap.

Pseudocode:
```
procedure Dijkstra(G, source)
	for v in V do dist[v] := +infinity; dist[source] := 0
	Q := min-priority queue of (v, dist[v])
	while Q not empty do
		u := extractMin(Q)
		for each edge (u,v) with weight w do
			if dist[u] + w < dist[v] then
				dist[v] := dist[u] + w
				decreaseKey(Q, v, dist[v])
	return dist[]
```

---

## Complexity & When To Use Greedy

- Greedy algorithms are typically fast (sorting + linear pass, or priority-queue-driven) and simple to implement.
- Before applying greedy, verify greedy-choice property and optimal substructure; use exchange arguments or induction for correctness.
- Use greedy when you can prove that local optimal decisions are safe (e.g., activity selection, fractional knapsack, MST, Huffman, Dijkstra for non-negative weights).
- Avoid greedy when the locally optimal choice can preclude an optimal global solution (e.g., 0/1 knapsack general case, general set cover, some scheduling variants).

If you want, I can:
- add step-by-step traces for a chosen example (showing picks/heap updates),
- convert the pseudocode into runnable Python implementations (with tests),
- or create a printable one-page cheat-sheet.

