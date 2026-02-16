# Searching Algorithms — Overview and Pseudocode

This document collects important searching algorithms, concise descriptions, complexity notes, and clear pseudocode suitable for documentation or interview prep.

## Table of Contents
- Overview
- Linear and Binary Searches
	- Linear Search
	- Binary Search (iterative & recursive)
	- Ternary Search
- Interval & Probing Searches
	- Jump Search
	- Exponential Search
	- Interpolation Search
	- Fibonacci Search
- Data-structure Searches
	- Binary Search Tree (BST) Search
	- Hash Table Lookup
	- Trie (Prefix) Search
- Graph Searches
	- Breadth-First Search (BFS)
	- Depth-First Search (DFS)
- Complexity & Use-cases Summary

## Overview

Searching finds whether (and where) a target exists in a dataset. Algorithms differ by data layout (unsorted vs sorted), underlying data structure, and required guarantees (worst-case time, space, stability for updates).

---

## Linear and Binary Searches

### Linear Search
- Description: Scans array sequentially until the target is found. Works on unsorted arrays.
- Time: Best $O(1)$, Average/Worst $O(n)$
- Space: $O(1)$

Pseudocode:
```
procedure LinearSearch(A, target)
	for i from 0 to length(A)-1 do
		if A[i] == target then
			return i  // found
	return -1     // not found
```

### Binary Search
- Description: Repeatedly halves a sorted array to locate the target. One of the most important searching primitives.
- Time: $O(\log n)$
- Space: $O(1)$ iterative, $O(\log n)$ recursive stack

Iterative Pseudocode:
```
procedure BinarySearchIterative(A, target)
	low := 0; high := length(A)-1
	while low <= high do
		mid := floor((low + high) / 2)
		if A[mid] == target then return mid
		else if A[mid] < target then low := mid + 1
		else high := mid - 1
	return -1
```

Recursive Pseudocode:
```
procedure BinarySearchRecursive(A, target, low, high)
	if low > high then return -1
	mid := floor((low + high) / 2)
	if A[mid] == target then return mid
	else if A[mid] < target then return BinarySearchRecursive(A, target, mid+1, high)
	else return BinarySearchRecursive(A, target, low, mid-1)
```

### Ternary Search
- Description: Splits array (or unimodal function domain) into three parts using two mid points. Rarely faster than binary search for plain sorted arrays; used for unimodal function optimization.
- Time: $O(\log n)$ but with higher constant factors.

Pseudocode (array variant):
```
procedure TernarySearch(A, target, low, high)
	if low > high then return -1
	third := floor((high - low) / 3)
	m1 := low + third; m2 := high - third
	if A[m1] == target then return m1
	if A[m2] == target then return m2
	if target < A[m1] then return TernarySearch(A, target, low, m1-1)
	else if target > A[m2] then return TernarySearch(A, target, m2+1, high)
	else return TernarySearch(A, target, m1+1, m2-1)
```

---

## Interval & Probing Searches

### Jump Search
- Description: For sorted arrays, jumps by fixed block size (commonly sqrt(n)) then does linear scan in block.
- Time: $O(\sqrt{n})$

Pseudocode:
```
procedure JumpSearch(A, target)
	n := length(A)
	step := floor(sqrt(n))
	prev := 0
	while A[min(step, n)-1] < target do
		prev := step
		step := step + floor(sqrt(n))
		if prev >= n then return -1
	for i from prev to min(step, n)-1 do
		if A[i] == target then return i
	return -1
```

### Exponential Search
- Description: Find range by repeated doubling (useful for unbounded or infinite-length sorted lists), then binary search within found range.
- Time: $O(\log n)$

Pseudocode:
```
procedure ExponentialSearch(A, target)
	if A[0] == target then return 0
	i := 1
	while i < length(A) and A[i] <= target do
		i := i * 2
	return BinarySearchRecursive(A, target, i/2, min(i, length(A)-1))
```

### Interpolation Search
- Description: For uniformly distributed sorted arrays, estimates pivot position using target's value relative to bounds. Average $O(\log \log n)$ for uniform data; worst-case $O(n)$.
- Time: Average $O(\log \log n)$ (uniform), Worst $O(n)$

Pseudocode:
```
procedure InterpolationSearch(A, target)
	low := 0; high := length(A)-1
	while low <= high and target >= A[low] and target <= A[high] do
		pos := low + floor((target - A[low]) * (high - low) / (A[high] - A[low]))
		if A[pos] == target then return pos
		if A[pos] < target then low := pos + 1
		else high := pos - 1
	return -1
```

### Fibonacci Search
- Description: Uses Fibonacci numbers to divide array and narrow search range; similar to binary search but splits based on Fibonacci offsets.
- Time: $O(\log n)$

Pseudocode (sketch):
```
procedure FibonacciSearch(A, target)
	// find smallest Fibonacci number F[k] >= n
	// use F[k-2] as probe offset and shrink the range using Fibonacci numbers
	// similar comparisons to binary search but with Fibonacci offsets
```

---

## Data-structure Searches

### Binary Search Tree (BST) Search
- Description: Search in binary search tree follows left/right child decisions; balanced BSTs provide $O(\log n)$ search.
- Time: Average/Best $O(\log n)$ (if balanced), Worst $O(n)$ (degenerate)

Pseudocode:
```
procedure BSTSearch(node, key)
	if node is null then return null
	if key == node.key then return node
	else if key < node.key then return BSTSearch(node.left, key)
	else return BSTSearch(node.right, key)
```

### Hash Table Lookup
- Description: Average-case $O(1)$ lookup using hashing. Worst-case $O(n)$ with poor hash function or severe collisions. Ideal for exact-match queries.
- Time: Average $O(1)$, Worst $O(n)$

Pseudocode (conceptual):
```
procedure HashLookup(H, key)
	idx := hash(key) mod H.capacity
	for each entry e in H.bucket[idx] do
		if e.key == key then return e.value
	return not_found
```

### Trie (Prefix) Search
- Description: Tree-like structure for strings; each edge represents a character. Excellent for prefix queries and dictionary operations.
- Time: $O(m)$ where $m$ is length of query string

Pseudocode (search):
```
procedure TrieSearch(root, s)
	node := root
	for ch in s do
		if node.children[ch] is null then return false
		node := node.children[ch]
	return node.isEndOfWord
```

---

## Graph Searches

### Breadth-First Search (BFS)
- Description: Explores graph level-by-level; finds shortest path in unweighted graphs.
- Time: $O(V + E)$

Pseudocode:
```
procedure BFS(start)
	create queue Q
	mark start visited
	enqueue start into Q
	while Q not empty do
		v := dequeue Q
		for each neighbor w of v do
			if w not visited then
				mark w visited
				enqueue w
```

### Depth-First Search (DFS)
- Description: Explores as deep as possible along each branch before backtracking; useful for topological sort, connectivity, cycles.
- Time: $O(V + E)$

Pseudocode (recursive):
```
procedure DFS(v)
	mark v visited
	for each neighbor w of v do
		if w not visited then
			DFS(w)
```

---

## Complexity & Use-cases Summary

- Use `Linear Search` for unsorted small arrays or when a single pass is acceptable.
- Use `Binary Search` for static, sorted arrays and when you can access by index.
- Use `Interpolation` or `Exponential` when data distribution or unbounded arrays allow faster probing.
- Use `Jump` or `Fibonacci` when memory locality or particular performance trade-offs are desired.
- Use `Hash Table` for average constant-time exact-match lookups.
- Use `Trie` for prefix searches and dictionary-like string operations.
- Use `BFS/DFS` for graph traversal; choose BFS when shortest unweighted paths are needed.

### Quick Reference Table

| Algorithm | Best | Average | Worst | Space | Notes |
|---|---:|---:|---:|---:|---|
| Linear | $O(1)$ | $O(n)$ | $O(n)$ | $O(1)$ | Works on unsorted arrays |
| Binary | $O(1)$ | $O(\log n)$ | $O(\log n)$ | $O(1)$ | Requires sorted random-access array |
| Jump | $O(1)$ | $O(\sqrt{n})$ | $O(n)$ | $O(1)$ | Good locality for sorted arrays |
| Exponential | $O(1)$ | $O(\log n)$ | $O(\log n)$ | $O(1)$ | Finds bounds then binary search |
| Interpolation | $O(\log\log n)$ | $O(\log\log n)$ | $O(n)$ | $O(1)$ | Best for uniform distributions |
| Fibonacci | $O(\log n)$ | $O(\log n)$ | $O(\log n)$ | $O(1)$ | Variant of binary search using Fibonacci numbers |
| BST | $O(\log n)$ | $O(\log n)$ | $O(n)$ | $O(h)$ | Balanced variants preferred |
| Hash | $O(1)$ | $O(1)$ | $O(n)$ | $O(n)$ | Excellent avg-case for exact match |
| Trie | $O(m)$ | $O(m)$ | $O(m)$ | $O(alphabet * m)$ | m = query length |
| BFS/DFS | $O(V+E)$ | $O(V+E)$ | $O(V+E)$ | $O(V)$ | Graph traversals |

---

If you want, I can:
- add visual traces for a chosen search (step-by-step index highlights),
- convert pseudocode into runnable Python implementations for several algorithms,
- or produce a printable one-page cheat-sheet.

