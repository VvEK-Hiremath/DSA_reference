# Heap Data Structure

A **heap** is a specialized tree-based data structure that satisfies the heap property:
- In a **max-heap**, for any given node, the value of the node is greater than or equal to the values of its children.
- In a **min-heap**, for any given node, the value of the node is less than or equal to the values of its children.

Heaps are commonly implemented as complete binary trees, which means all levels are fully filled except possibly the last, which is filled from left to right.

## Types of Heaps
- **Max-Heap**: The largest element is at the root.
- **Min-Heap**: The smallest element is at the root.
- **Binary Heap**: A heap implemented as a binary tree.
- **Fibonacci Heap, Binomial Heap, etc.**: Advanced heap types used in specific algorithms.

## Heap Operations (with Pseudocode)

### 1. Insertion
```
function insert(heap, value):
    heap.append(value)
    index = heap.size - 1
    while index > 0 and heap[parent(index)] < heap[index]:
        swap(heap[parent(index)], heap[index])
        index = parent(index)

function parent(index):
    return (index - 1) // 2
```

### 2. Heapify (Max-Heap)
```
function heapify(heap, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    if left < n and heap[left] > heap[largest]:
        largest = left
    if right < n and heap[right] > heap[largest]:
        largest = right
    if largest != i:
        swap(heap[i], heap[largest])
        heapify(heap, n, largest)
```

### 3. Extract Max (Remove Root)
```
function extractMax(heap):
    if heap.size == 0:
        return null
    max = heap[0]
    heap[0] = heap[heap.size - 1]
    heap.pop()
    heapify(heap, heap.size, 0)
    return max
```

### 4. Build Heap
```
function buildHeap(array):
    n = array.size
    for i from n//2 - 1 down to 0:
        heapify(array, n, i)
```

---
Heaps are widely used in priority queues, heap sort, and algorithms like Dijkstra's shortest path.
