# Dynamic Programming — Concepts, Patterns, and Pseudocode

This document explains the dynamic programming (DP) paradigm, common patterns, proof/derivation techniques, and concise pseudocode for important example problems.

## Table of Contents
- DP intuition and when to use it
- Techniques: Memoization vs Tabulation
- Common patterns
- Classic problems and pseudocode
	- Fibonacci (intro)
	- 0/1 Knapsack
	- Unbounded Knapsack / Rod Cutting
	- Longest Common Subsequence (LCS)
	- Longest Increasing Subsequence (LIS) — DP and patience sorting note
	- Edit Distance (Levenshtein)
	- Coin Change (min coins)
	- Matrix Chain Multiplication
- Complexity, tips, and debugging DP

## DP intuition and when to use it

Dynamic programming solves problems that have overlapping subproblems and optimal substructure. If a naive recursion repeats work, DP can store (memoize) results and reuse them, turning exponential-time recurrences into polynomial-time algorithms.

Use DP when:
- The problem can be expressed by a recurrence.
- Subproblems overlap significantly.
- Optimal solution can be composed from optimal solutions of subproblems.

## Techniques: Memoization vs Tabulation
- Memoization (top-down): Write recursion, cache results. Easy to implement; recursion depth may matter.
- Tabulation (bottom-up): Fill table iteratively from small subproblems to larger. Often more space/time predictable and avoids recursion overhead.

## Common patterns
- 1D DP over array indices (e.g., coin change, LIS)
- 2D DP over two sequences (e.g., LCS, edit distance)
- Interval DP over ranges (e.g., matrix chain multiplication)
- DP with bitmasks for subsets (small n up to ~20)
- DP on trees (post-order traversal, compute state per node)

---

## Classic problems and pseudocode

### Fibonacci (intro)
- Recurrence: F(n) = F(n-1) + F(n-2), base F(0)=0, F(1)=1
- Naive recursion is exponential; DP yields linear time.

Top-down (memoization):
```
procedure FibMemo(n, memo)
	if n in memo then return memo[n]
	if n <= 1 then return n
	memo[n] := FibMemo(n-1, memo) + FibMemo(n-2, memo)
	return memo[n]
```

Bottom-up (tabulation):
```
procedure FibTab(n)
	if n <= 1 then return n
	dp[0] := 0; dp[1] := 1
	for i from 2 to n do
		dp[i] := dp[i-1] + dp[i-2]
	return dp[n]
```

### 0/1 Knapsack
- Problem: Given n items with weights w[i] and values v[i], and capacity W, pick subset to maximize total value; each item 0 or 1 times.
- Recurrence: dp[i][c] = max(dp[i-1][c], v[i] + dp[i-1][c-w[i]] if c>=w[i])
- Complexity: $O(nW)$ time and $O(nW)$ space (can optimize to $O(W)$ space using reversed loop).

Pseudocode (tabulation):
```
procedure Knapsack01(items, W)
	n := length(items)
	dp := array (n+1) x (W+1) initialized to 0
	for i from 1 to n do
		for c from 0 to W do
			dp[i][c] := dp[i-1][c]
			if c >= items[i].weight then
				dp[i][c] := max(dp[i][c], items[i].value + dp[i-1][c - items[i].weight])
	return dp[n][W]
```

Space-optimized 1D variant (iterate capacity descending):
```
procedure Knapsack01Optimized(items, W)
	dp := array of length W+1 initialized to 0
	for each item in items do
		for c from W down to item.weight do
			dp[c] := max(dp[c], item.value + dp[c - item.weight])
	return dp[W]
```

### Unbounded Knapsack / Rod Cutting
- Unbounded: items can be taken unlimited times. Recurrence: dp[c] = max over items (dp[c - w[i]] + v[i])
- Rod Cutting: equivalent to unbounded where cuts are item types.

Pseudocode (unbounded):
```
procedure UnboundedKnapsack(items, W)
	dp := array length W+1 initialize -infinity; dp[0] := 0
	for c from 1 to W do
		for item in items do
			if item.weight <= c then
				dp[c] := max(dp[c], item.value + dp[c - item.weight])
	return dp[W]
```

### Longest Common Subsequence (LCS)
- Problem: Given sequences X[1..m], Y[1..n], find length (or sequence) of longest subsequence present in both.
- Recurrence: if X[i]==Y[j] then dp[i][j]=dp[i-1][j-1]+1 else dp[i][j]=max(dp[i-1][j], dp[i][j-1])
- Complexity: $O(mn)$ time and space (space can be reduced to $O(min(m,n))$ for length only).

Pseudocode:
```
procedure LCS(X, Y)
	m := length(X); n := length(Y)
	dp := (m+1)x(n+1) array of zeros
	for i from 1 to m do
		for j from 1 to n do
			if X[i] == Y[j] then dp[i][j] := dp[i-1][j-1] + 1
			else dp[i][j] := max(dp[i-1][j], dp[i][j-1])
	return dp[m][n]
```

### Longest Increasing Subsequence (LIS)
- DP recurrence (classic): dp[i] = 1 + max(dp[j]) for all j < i with A[j] < A[i]. $O(n^2)$ time.
- Faster approach (patience sorting / tails with binary search) achieves $O(n\log n)$ for length only.

Pseudocode (O(n^2)):
```
procedure LIS_DP(A)
	n := length(A)
	dp := array length n initialized to 1
	for i from 0 to n-1 do
		for j from 0 to i-1 do
			if A[j] < A[i] then dp[i] := max(dp[i], dp[j] + 1)
	return max(dp)
```

Pseudocode (O(n log n) length):
```
procedure LIS_nlogn(A)
	tails := empty list
	for x in A do
		i := lower_bound(tails, x)  // first index with tails[i] >= x
		if i == length(tails) then append x to tails
		else tails[i] := x
	return length(tails)
```

### Edit Distance (Levenshtein)
- Problem: Minimum operations (insert/delete/replace) to convert string A to B.
- Recurrence: dp[i][j] = if A[i]==B[j] then dp[i-1][j-1] else 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
- Complexity: $O(mn)$ time and space.

Pseudocode:
```
procedure EditDistance(A, B)
	m := length(A); n := length(B)
	dp := (m+1)x(n+1)
	for i from 0 to m do dp[i][0] := i
	for j from 0 to n do dp[0][j] := j
	for i from 1 to m do
		for j from 1 to n do
			if A[i-1] == B[j-1] then dp[i][j] := dp[i-1][j-1]
			else dp[i][j] := 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
	return dp[m][n]
```

### Coin Change (minimum coins)
- Problem: Given coin denominations, minimum number of coins to form amount. Similar to unbounded knapsack minimizing count.
- Recurrence: dp[a] = 1 + min(dp[a - coin]) across coins.

Pseudocode:
```
procedure CoinChangeMin(coins, amount)
	dp := array length amount+1 initialized to +infinity; dp[0] := 0
	for a from 1 to amount do
		for coin in coins do
			if coin <= a then dp[a] := min(dp[a], 1 + dp[a-coin])
	if dp[amount] == +infinity then return -1 else return dp[amount]
```

### Matrix Chain Multiplication (interval DP)
- Problem: Given matrices A1..An, choose parenthesization to minimize scalar multiplications.
- Recurrence: dp[i][j] = min over k (dp[i][k] + dp[k+1][j] + cost of multiplying resulting matrices)
- Complexity: $O(n^3)$ time, $O(n^2)$ space.

Pseudocode:
```
procedure MatrixChain(p)  // p is array of dimensions length n+1
	n := length(p) - 1
	dp := n x n matrix initialize 0 for length 1 chains
	for L from 2 to n do  // chain length
		for i from 1 to n-L+1 do
			j := i + L - 1
			dp[i][j] := +infinity
			for k from i to j-1 do
				q := dp[i][k] + dp[k+1][j] + p[i-1]*p[k]*p[j]
				if q < dp[i][j] then dp[i][j] := q
	return dp[1][n]
```

---

## Complexity, tips, and debugging DP
- Time/space: Derive recurrence and estimate number of unique states and cost per state. Common complexities are $O(n)$, $O(nW)$, $O(n^2)$, $O(n^3)$, or $O(2^n \cdot n)$ for bitmask DP.
- Space optimization: Often reduce a 2D dp to 1D when transitions only depend on previous row (e.g., knapsack, LCS length).
- Reconstructing solution: Store choices/parents during DP to reconstruct the optimal set/path.
- Debugging tips:
	- Start with small inputs and compare DP table against brute force or recursion.
	- Print dp table and verify base cases and transitions.
	- For top-down, watch recursion depth and memo dictionary keys.
- Use careful indexing and consistent base-case initialization.

If you want, I can:
- convert selected pseudocode into runnable Python (with tests),
- add worked examples with tables filled step-by-step,
- or add bitmask DP and tree DP sections next.

