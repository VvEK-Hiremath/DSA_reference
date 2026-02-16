# Divide and Conquer — Concepts, Examples, and Pseudocode

This document explains the divide-and-conquer paradigm, analysis techniques (recurrences, Master Theorem), and includes concise pseudocode for key algorithms and examples.

## Table of Contents
- Overview and pattern
- Master Theorem and recurrence solving
- Generic divide-and-conquer template
- Core examples
	- Binary Search
	- Merge Sort
	- Quick Sort (divide with partition)
- Advanced examples
	- Count Inversions (modified merge)
	- Karatsuba Multiplication
	- Closest Pair of Points (plane)
	- Strassen Matrix Multiplication (sketch)
- Complexity, when to use D&C, and tips

## Overview and pattern

Divide and conquer splits a problem into smaller subproblems, solves each recursively, then combines the results. It is effective when subproblems are similar to the original and can be solved independently.

Typical cost model: if a problem of size n splits into a subproblems each of size n/b, and combining takes f(n) time, the recurrence is
$$T(n) = a\,T\left(\frac{n}{b}\right) + f(n).$$

## Master Theorem (summary)

For recurrences of the form above with polynomial f(n), Master Theorem gives asymptotic bounds:
- If f(n) = O(n^{c}) where c < log_b(a) then T(n) = Theta(n^{log_b(a)}).
- If f(n) = Theta(n^{log_b(a)} * log^k n) then T(n) = Theta(n^{log_b(a)} * log^{k+1} n).
- If f(n) = Omega(n^{c}) where c > log_b(a) and regularity holds, then T(n) = Theta(f(n)).

Use the theorem to quickly determine whether the cost is dominated by recursion or combination.

---

## Generic template

```
procedure DnC(problem)
	if base case then solve directly
	split problem into subproblems p1..pa
	for each subproblem pi do
		result_i := DnC(pi)
	return combine(result_1..result_a)
```

Key: keep subproblems independent and combination efficient.

---

## Core examples

### Binary Search
- Idea: Divide sorted array into halves; recurse into the half that may contain target.
- Time: $O(\log n)$.

Pseudocode (iterative):
```
procedure BinarySearch(A, target)
	low := 0; high := length(A)-1
	while low <= high do
		mid := floor((low + high) / 2)
		if A[mid] == target then return mid
		else if A[mid] < target then low := mid + 1
		else high := mid - 1
	return -1
```

### Merge Sort
- Idea: Split array in half, recursively sort halves, then merge sorted halves.
- Recurrence: T(n) = 2T(n/2) + O(n) -> T(n) = O(n log n).

Pseudocode:
```
procedure MergeSort(A)
	if length(A) <= 1 then return A
	mid := floor(length(A)/2)
	L := MergeSort(A[0:mid])
	R := MergeSort(A[mid:])
	return Merge(L, R)

procedure Merge(L, R)
	result := empty list
	while L and R not empty do
		if first(L) <= first(R) then append first(L), remove it from L
		else append first(R), remove it from R
	append remaining elements
	return result
```

### Quick Sort (partition-based)
- Idea: Partition array around a pivot into less/greater and recursively sort partitions.
- Average time: $O(n \log n)$; worst-case $O(n^2)$ without good pivoting.

Pseudocode (Lomuto):
```
procedure QuickSort(A, low, high)
	if low < high then
		p := Partition(A, low, high)
		QuickSort(A, low, p-1)
		QuickSort(A, p+1, high)

procedure Partition(A, low, high)
	pivot := A[high]
	i := low
	for j from low to high-1 do
		if A[j] <= pivot then swap A[i], A[j]; i := i+1
	swap A[i], A[high]
	return i
```

Use random pivot or median-of-three to avoid worst-case on sorted inputs.

---

## Advanced examples

### Count Inversions (modified merge)
- Problem: Count pairs (i,j) with i<j and A[i] > A[j].
- Idea: Use merge sort and count cross-inversions during merge; runs in O(n log n).

Pseudocode (sketch): during Merge(L,R) when taking element from R that is smaller than L's current, add number of remaining elements in L to inversion count.

### Karatsuba Multiplication (fast integer multiply)
- Idea: Multiply n-digit numbers by splitting halves and using three recursive multiplications instead of four; recurrence T(n)=3T(n/2)+O(n) -> ~O(n^{log_2 3}) ~ O(n^{1.585}).

Pseudocode (sketch):
```
procedure Karatsuba(x, y)
	if small then return x*y
	split x into a,b and y into c,d (high/low halves)
	p1 := Karatsuba(a, c)
	p2 := Karatsuba(b, d)
	p3 := Karatsuba(a+b, c+d)
	return p1 * base^{2m} + (p3 - p1 - p2) * base^{m} + p2
```

### Closest Pair of Points (plane)
- Problem: Given n points, find pair with minimal Euclidean distance.
- Idea: Sort points by x, split into halves, find closest in left/right recursively, then check points within strip near midline using y-sorted order; runs O(n log n).

High-level pseudocode:
```
procedure ClosestPair(P sorted by x)
	if small n then brute force
	mid := n/2; (PL, PR) := split P
	dl := ClosestPair(PL); dr := ClosestPair(PR)
	d := min(dl, dr)
	build strip of points within d of mid x; sort strip by y
	for i from 0 to length(strip)-1 do
		for j from i+1 while (strip[j].y - strip[i].y) < d and j < i+7 do
			d := min(d, dist(strip[i], strip[j]))
	return d
```

### Strassen Matrix Multiplication (sketch)
- Idea: Multiply 2x2 block matrices using 7 multiplications instead of 8; recurrence T(n)=7T(n/2)+O(n^2) -> O(n^{log_2 7}) ~ O(n^{2.807}).
- Useful for large matrices but with practical cross-over and numerical stability considerations.

---

## Complexity, when to use D&C, and tips

- Use divide-and-conquer when the problem naturally splits into independent subproblems that are significantly smaller.
- D&C often achieves optimal or near-optimal asymptotic bounds (merge sort, Karatsuba, Strassen).
- Analyze using recurrences and Master Theorem.
- Practical tips:
	- Minimize extra work in combine step (f(n)).
	- Try to avoid excessive copying — pass indices or work in-place when safe.
	- Use randomization for pivot selection to avoid pathological inputs.
	- For small subproblem sizes, switch to simple algorithms (insertion sort) to reduce recursion overhead.

If you want, I can:
- add worked numerical examples (trace arrays/merges),
- convert pseudocode into runnable Python for selected algorithms,
- or add exercises and interview-style questions.

