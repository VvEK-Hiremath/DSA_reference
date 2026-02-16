# Sorting Algorithms — Overview and Pseudocode

This document summarizes commonly used sorting algorithms, their characteristics, and concise pseudocode you can use for documentation or interviews.

## Table of Contents
- Overview
- Simple Comparison Sorts
	- Bubble Sort
	- Selection Sort
	- Insertion Sort
	- Shell Sort
- Divide and Conquer Sorts
	- Merge Sort
	- Quick Sort
	- Heap Sort
- Non-comparison Sorts
	- Counting Sort
	- Radix Sort
	- Bucket Sort
- Complexity, Stability, and Use-cases Summary

## Overview

Sorting arranges elements into an order (typically ascending or descending). Most general-purpose sorts are comparison-based and have a lower bound of $O(n \log n)$ time. Non-comparison sorts exploit input properties to achieve linear time in certain cases.

---

## Simple Comparison Sorts

### Bubble Sort
- Description: Repeatedly steps through the list, compares adjacent items and swaps them if out of order. Good as an educational example; rarely used in production.
- Time: Best $O(n)$ (already sorted), Average/Worst $O(n^2)$
- Space: $O(1)$
- Stable: Yes
- In-place: Yes

Pseudocode:
```
procedure BubbleSort(A)
	n := length(A)
	for i from 1 to n-1 do
		swapped := false
		for j from 0 to n-2 do
			if A[j] > A[j+1] then
				swap A[j] and A[j+1]
				swapped := true
		if not swapped then
			break
```

### Selection Sort
- Description: Repeatedly selects the minimum (or maximum) element and moves it to the sorted portion.
- Time: Best/Average/Worst $O(n^2)$
- Space: $O(1)$
- Stable: No (can be made stable with extra work)
- In-place: Yes

Pseudocode:
```
procedure SelectionSort(A)
	n := length(A)
	for i from 0 to n-2 do
		minIndex := i
		for j from i+1 to n-1 do
			if A[j] < A[minIndex] then
				minIndex := j
		if minIndex != i then
			swap A[i] and A[minIndex]
```

### Insertion Sort
- Description: Builds the sorted array one item at a time by inserting each new element into its correct position.
- Time: Best $O(n)$ (nearly sorted), Average/Worst $O(n^2)$
- Space: $O(1)$
- Stable: Yes
- In-place: Yes

Pseudocode:
```
procedure InsertionSort(A)
	for i from 1 to length(A)-1 do
		key := A[i]
		j := i-1
		while j >= 0 and A[j] > key do
			A[j+1] := A[j]
			j := j-1
		A[j+1] := key
```

### Shell Sort
- Description: Generalization of insertion sort that allows exchanges of far-apart elements using a gap sequence; practical and faster than simple quadratic sorts.
- Time: Depends on gap sequence; typical empirical performance between $O(n^{1.25})$ and $O(n^{1.5})$; worst-case varies by sequence.
- Space: $O(1)$
- Stable: No
- In-place: Yes

Pseudocode (simple gap-halving):
```
procedure ShellSort(A)
	n := length(A)
	gap := floor(n/2)
	while gap > 0 do
		for i from gap to n-1 do
			temp := A[i]
			j := i
			while j >= gap and A[j-gap] > temp do
				A[j] := A[j-gap]
				j := j - gap
			A[j] := temp
		gap := floor(gap/2)
```

---

## Divide and Conquer Sorts

### Merge Sort
- Description: Recursively splits the array and merges sorted halves. Stable and predictable time complexity.
- Time: Best/Average/Worst $O(n \log n)$
- Space: $O(n)$ (for merge arrays)
- Stable: Yes
- In-place: No (standard implementation)

Pseudocode:
```
procedure MergeSort(A)
	if length(A) <= 1 then
		return A
	mid := floor(length(A)/2)
	left := MergeSort(A[0:mid])
	right := MergeSort(A[mid:])
	return Merge(left, right)

procedure Merge(L, R)
	result := empty array
	while L not empty and R not empty do
		if first(L) <= first(R) then
			append first(L) to result; remove first(L)
		else
			append first(R) to result; remove first(R)
	append remaining elements of L and R to result
	return result
```

### Quick Sort
- Description: Picks a pivot, partitions array into less/greater, recursively sorts partitions. Very fast in practice but worst-case $O(n^2)$ (can be avoided with good pivoting).
- Time: Best/Average $O(n \log n)$, Worst $O(n^2)$
- Space: $O(\log n)$ average recursion stack (worst $O(n)$)
- Stable: No (standard)
- In-place: Typically yes (Lomuto/Hoare partition schemes)

Pseudocode (Lomuto partition):
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
		if A[j] <= pivot then
			swap A[i], A[j]
			i := i+1
	swap A[i], A[high]
	return i
```

Notes: Use randomized pivot or median-of-three to reduce worst-case likelihood.

### Heap Sort
- Description: Builds a binary heap from the array, repeatedly extracts the maximum (or minimum) to produce a sorted array. In-place and has guaranteed $O(n \log n)$ time.
- Time: Best/Average/Worst $O(n \log n)$
- Space: $O(1)$
- Stable: No
- In-place: Yes

Pseudocode:
```
procedure HeapSort(A)
	n := length(A)
	BuildMaxHeap(A)
	for i from n-1 down to 1 do
		swap A[0], A[i]
		heapSize := heapSize - 1
		MaxHeapify(A, 0, heapSize)

procedure BuildMaxHeap(A)
	heapSize := length(A)
	for i from floor(heapSize/2)-1 down to 0 do
		MaxHeapify(A, i, heapSize)

procedure MaxHeapify(A, i, heapSize)
	left := 2*i+1; right := 2*i+2
	largest := i
	if left < heapSize and A[left] > A[largest] then largest := left
	if right < heapSize and A[right] > A[largest] then largest := right
	if largest != i then
		swap A[i], A[largest]
		MaxHeapify(A, largest, heapSize)
```

---

## Non-comparison Sorts

### Counting Sort
- Description: Counts occurrences of each key and uses positions to place elements. Works when keys are small integers.
- Time: $O(n + k)$ where $k$ is range of keys
- Space: $O(n + k)$
- Stable: Yes (if implemented appropriately)
- In-place: No

Pseudocode:
```
procedure CountingSort(A, k)
	// A elements in range 0..k
	C := array of zeros length k+1
	for each x in A do
		C[x] := C[x] + 1
	for i from 1 to k do
		C[i] := C[i] + C[i-1]  // prefix sums
	B := array length A
	for i from length(A)-1 down to 0 do
		B[C[A[i]]-1] := A[i]
		C[A[i]] := C[A[i]] - 1
	return B
```

### Radix Sort
- Description: Sorts numbers digit-by-digit using a stable sort (usually counting sort) from least significant digit (LSD) or most significant digit (MSD).
- Time: $O(d \times (n + b))$ where $d$ digits and base $b$
- Space: $O(n + b)$
- Stable: Yes (depends on stable subroutine)
- In-place: No (usually)

Pseudocode (LSD):
```
procedure RadixSort(A, d, b)
	for digit from 0 to d-1 do
		A := StableSortByDigit(A, digit, base=b)
	return A
```

### Bucket Sort
- Description: Distributes elements into buckets, sorts each bucket (often with insertion sort), then concatenates buckets. Works well for uniformly distributed input.
- Time: Average $O(n + k)$, Worst depends on bucket sort used (can be $O(n^2)$)
- Space: $O(n + k)$
- Stable: Yes (if bucket sort and sub-sorts are stable)
- In-place: No (usually)

Pseudocode:
```
procedure BucketSort(A, k)
	create k empty buckets
	for each x in A do
		index := floor(k * x)  // for x in [0,1)
		append x to buckets[index]
	for each bucket do
		sort bucket (e.g., InsertionSort)
	return concatenation of buckets in order
```

---

## Complexity, Stability, and Use-cases Summary

- Comparison lower bound: Any comparison-based sort requires $\Omega(n \log n)$ comparisons in the worst case.
- Use small, simple sorts (Insertion/Selection) for tiny arrays or nearly-sorted data.
- Use Merge Sort for stable, predictable performance and external sorting (streams, files).
- Use Quick Sort for in-memory general-purpose sorting where average-case speed matters; use randomized pivot or introsort (switch to heap sort on deep recursion) to avoid worst-case.
- Use Heap Sort when you need $O(n \log n)$ worst-case and in-place behavior (but not stable).
- Use Counting/Radix/Bucket when keys are integers or have limited range/distribution to achieve near-linear times.

### Quick Reference Table

| Algorithm | Best | Average | Worst | Space | Stable | In-place |
|---|---:|---:|---:|---:|---:|---:|
| Bubble | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Yes | Yes |
| Selection | $O(n^2)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | No | Yes |
| Insertion | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Yes | Yes |
| Shell | $O(n\log n)$? | depends | depends | $O(1)$ | No | Yes |
| Merge | $O(n\log n)$ | $O(n\log n)$ | $O(n\log n)$ | $O(n)$ | Yes | No |
| Quick | $O(n\log n)$ | $O(n\log n)$ | $O(n^2)$ | $O(\log n)$ | No | Yes |
| Heap | $O(n\log n)$ | $O(n\log n)$ | $O(n\log n)$ | $O(1)$ | No | Yes |
| Counting | $O(n+k)$ | $O(n+k)$ | $O(n+k)$ | $O(n+k)$ | Yes | No |
| Radix | $O(d(n+b))$ | $O(d(n+b))$ | $O(d(n+b))$ | $O(n+b)$ | Yes | No |
| Bucket | $O(n+k)$ avg | $O(n+k)$ avg | $O(n^2)$ worst | $O(n+k)$ | Yes | No |

---

If you'd like, I can:
- add diagrams or step-by-step traces for a chosen algorithm,
- convert pseudocode into runnable Python implementations,
- or generate printable cheat-sheets for interviews.

