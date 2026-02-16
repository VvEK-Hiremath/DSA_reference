# Backtracking — Concepts, Patterns, and Pseudocode

This document explains the backtracking paradigm, typical pruning strategies, and presents important examples with concise pseudocode suitable for study and interview preparation.

## Table of Contents
- Overview and when to use backtracking
- Generic backtracking template
- Pruning and heuristics
- Classic examples
	- Permutations
	- Combinations / Subsets
	- Subset Sum / Combination Sum
	- N-Queens
	- Sudoku solver
	- Knight's Tour
	- Word Search (grid)
	- Hamiltonian Path (graph)
- Complexity, tips, and debugging

## Overview and when to use backtracking

Backtracking is a depth-first search over a solution space where you build candidates incrementally and abandon (backtrack from) any candidate that cannot possibly lead to a valid solution. Use backtracking when:
- The search space is exponential but constraints/pruning can dramatically reduce it.
- You need all solutions or one valid solution for combinatorial problems (scheduling, packing, puzzles).

Backtracking differs from brute force by adding early rejection (pruning) of partial candidates.

---

## Generic backtracking template

The common structure:
```
procedure Backtrack(state)
	if state is a complete solution then
		process solution (record / return)
		return
	for choice in possibleChoices(state) do
		if choice is valid for state then
			make choice (modify state)
			Backtrack(state)
			undo choice (restore state)
```

Key points:
- Represent state minimally and support efficient undo.
- Validate partial state quickly to prune large branches.
- Optionally order choices to reach solutions earlier (heuristics).

---

## Pruning and heuristics
- Constraint checking: verify partial solution respects constraints.
- Forward checking: remove impossible choices for future steps early.
- Ordering heuristics: choose most constrained variable first (MRV), or choose choices likely to succeed first.
- Memoization: cache dead sub-states (careful with large state space).
- Symmetry breaking: avoid equivalent branches (e.g., fix first queen column in N-Queens variants).

---

## Classic examples

### Permutations
- Problem: Generate all permutations of an array `A`.

Pseudocode (swap-based):
```
procedure Permute(A, l, r)
	if l == r then
		output A
		return
	for i from l to r do
		swap A[l], A[i]
		Permute(A, l+1, r)
		swap A[l], A[i]  // backtrack
```

Alternatively use `used[]` boolean and build result array incrementally.

### Combinations / Subsets
- Problem: All k-combinations or all subsets of a set.

Pseudocode (choose / not choose):
```
procedure Subsets(A, idx, current)
	if idx == length(A) then
		output current
		return
	// exclude
	Subsets(A, idx+1, current)
	// include
	append A[idx] to current
	Subsets(A, idx+1, current)
	remove last from current
```

For k-combinations, add a constraint to only include when remaining positions suffice.

### Subset Sum / Combination Sum
- Problem: Find subset(s) summing to target. For Combination Sum variations, items may be reused.

Pseudocode (no reuse):
```
procedure SubsetSum(nums, idx, target, current)
	if target == 0 then output current; return
	if target < 0 or idx == length(nums) then return
	// include
	append nums[idx] to current
	SubsetSum(nums, idx+1, target - nums[idx], current)
	remove last from current
	// exclude
	SubsetSum(nums, idx+1, target, current)
```

Pseudocode (allow reuse of same element): iterate idx .. end and call with same i for reuse; sort input and skip duplicates to prevent repeated combos.

### N-Queens
- Problem: Place N queens on an N×N board so no two attack each other.
- Constraints: no two queens share row, column, or diagonal.
- Use row-by-row placement; keep sets/arrays for columns and diagonals to check validity in O(1).

Pseudocode:
```
procedure SolveNQueens(row)
	if row == N then
		output board
		return
	for col from 0 to N-1 do
		if col not in cols and (row-col) not in diag1 and (row+col) not in diag2 then
			place queen at (row,col)
			add col to cols; add row-col to diag1; add row+col to diag2
			SolveNQueens(row+1)
			remove queen and undo cols/diag1/diag2
```

Time: exponential; good pruning yields solutions quickly for typical N (N ≤ 14 practical with bitmasks).

### Sudoku solver
- Problem: Fill 9×9 grid so each row, column, and 3×3 box has digits 1..9.
- Use backtracking with constraint sets for rows, columns, and boxes; choose empty cell with fewest candidates (MRV) to prune.

Pseudocode:
```
procedure SolveSudoku()
	if no empty cells then output board; return true
	pick empty cell with fewest candidates
	for digit in candidates(cell) do
		place digit
		if SolveSudoku() then return true
		remove digit
	return false
```

### Knight's Tour
- Problem: Find a sequence of knight moves visiting each square exactly once.
- Heuristic: Warnsdorff's rule — always move to the square with the fewest onward moves.

Pseudocode (DFS with ordering):
```
procedure KnightTour(pos, moveCount)
	if moveCount == N*N then output path; return true
	for next in orderedMoves(pos) do
		if next not visited then
			mark visited; append next
			if KnightTour(next, moveCount+1) then return true
			unmark visited; pop next
	return false
```

### Word Search (grid)
- Problem: Given grid of letters and a word, check if word exists via adjacent cells (4- or 8-directional) without reuse of a cell per word.

Pseudocode:
```
procedure Exist(board, word)
	for each cell (i,j) do
		if board[i][j] == word[0] and dfs(i,j,0) then return true
	return false

procedure dfs(i,j,idx)
	if idx == length(word) then return true
	if out of bounds or board[i][j] != word[idx] then return false
	mark board[i][j] as visited
	for each neighbor (ni,nj) do
		if dfs(ni,nj, idx+1) then unmark and return true
	unmark board[i][j]
	return false
```

### Hamiltonian Path (graph)
- Problem: Path visiting each vertex exactly once (decision problem NP-complete). Backtracking is typical for small graphs.

Pseudocode:
```
procedure Hamiltonian(v, visitedCount)
	if visitedCount == V then output path; return true
	for u in neighbors(v) do
		if u not visited then
			mark u visited; append u
			if Hamiltonian(u, visitedCount+1) then return true
			unmark u; pop u
	return false
```

---

## Complexity, tips, and debugging
- Complexity: worst-case exponential (often O(b^d) where b = branching factor, d = depth). Effective pruning and heuristics can make many practical instances solvable.
- Tips:
	- Use fast O(1) validity checks (hash/bitsets) to prune quickly.
	- Choose variables with minimum remaining values (MRV) to reduce branching.
	- Apply symmetry breaking to remove equivalent branches.
	- Use iterative deepening or limit depth if you need bounded solutions.
	- For repeated subproblems, consider memoization of canonical partial states.
- Debugging:
	- Start with small instances and print partial states.
	- Validate base cases and undo operations carefully to avoid state corruption.

If you want, I can:
- convert several pseudocode examples into runnable Python files with tests,
- add visual step-by-step traces for N-Queens or Sudoku,
- or provide bitmask-optimized versions for N-Queens and subset problems.

