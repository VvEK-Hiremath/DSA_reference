## Arrays: Concepts, Operations, and Sample Problems

### What is an Array?
An array is a data structure that stores a fixed-size sequence of elements of the same type. Elements are stored in contiguous memory locations and can be accessed using an index.

#### Key Properties:
- Fixed size (in most languages)
- Elements are of the same data type
- Random access using indices

### Basic Operations
1. **Traversal**: Accessing each element of the array.
2. **Insertion**: Adding an element at a specific position (may require shifting elements).
3. **Deletion**: Removing an element from a specific position (may require shifting elements).
4. **Searching**: Finding the index of a given element (linear or binary search).
5. **Updating**: Changing the value of an element at a given index.

---

## Sample Problems with Pseudocode

### 1. Find the Maximum Element in an Array
**Problem:** Given an array of integers, find the maximum element.

**Pseudocode:**
```
function findMax(arr):
	max = arr[0]
	for i from 1 to length(arr)-1:
		if arr[i] > max:
			max = arr[i]
	return max
```

---

### 2. Reverse an Array
**Problem:** Reverse the elements of an array in place.

**Pseudocode:**
```
function reverseArray(arr):
	left = 0
	right = length(arr) - 1
	while left < right:
		swap arr[left] and arr[right]
		left = left + 1
		right = right - 1
```

---

### 3. Linear Search
**Problem:** Find the index of a target value in an array. Return -1 if not found.

**Pseudocode:**
```
function linearSearch(arr, target):
	for i from 0 to length(arr)-1:
		if arr[i] == target:
			return i
	return -1
```

---

### 4. Remove Duplicates from a Sorted Array
**Problem:** Given a sorted array, remove duplicates in place and return the new length.

**Pseudocode:**
```
function removeDuplicates(arr):
	if length(arr) == 0:
		return 0
	j = 0
	for i from 1 to length(arr)-1:
		if arr[i] != arr[j]:
			j = j + 1
			arr[j] = arr[i]
	return j + 1
```

---

### 5. Rotate Array by k Steps
**Problem:** Rotate the array to the right by k steps.

**Pseudocode:**
```
function rotateArray(arr, k):
	n = length(arr)
	k = k mod n
	reverse(arr, 0, n-1)
	reverse(arr, 0, k-1)
	reverse(arr, k, n-1)
```
*Assume reverse(arr, start, end) reverses the subarray from start to end.*

---

## Tips
- Arrays are best for random access and fixed-size collections.
- For dynamic resizing, consider using lists (Python), ArrayList (Java), or vectors (C++).

---

*Practice these problems to master array fundamentals!*
