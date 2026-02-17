## Standard Matrix Problems

### 1. Matrix Transpose
**Problem:** Given a matrix, return its transpose (swap rows and columns).

**Explanation:**
The transpose of a matrix is obtained by flipping it over its diagonal. For a matrix `A` of size `m x n`, the transpose is a matrix `A^T` of size `n x m` where `A^T[i][j] = A[j][i]`.

**Pseudocode:**
```
for i from 0 to n-1:
    for j from 0 to m-1:
        transpose[i][j] = A[j][i]
```

---

### 2. Rotate Matrix by 90 Degrees
**Problem:** Rotate a given `n x n` matrix by 90 degrees clockwise.

**Explanation:**
To rotate a matrix by 90 degrees, first transpose the matrix, then reverse each row.

**Pseudocode:**
```
// Transpose the matrix
for i from 0 to n-1:
    for j from i to n-1:
        swap A[i][j] with A[j][i]
// Reverse each row
for i from 0 to n-1:
    reverse A[i]
```

---

### 3. Search in a Sorted Matrix
**Problem:** Given a matrix where each row and column is sorted, search for a target value efficiently.

**Explanation:**
Start from the top-right or bottom-left corner. Move left if the current value is greater than the target, or move down if it is less.

**Pseudocode:**
```
row = 0
col = n - 1
while row < m and col >= 0:
    if A[row][col] == target:
        return true
    else if A[row][col] > target:
        col = col - 1
    else:
        row = row + 1
return false
```

---

### 4. Spiral Order Traversal
**Problem:** Print all elements of a matrix in spiral order.

**Explanation:**
Traverse the matrix layer by layer, moving right, down, left, and up, shrinking the boundaries after each direction.

**Pseudocode:**
```
top = 0, bottom = m-1, left = 0, right = n-1
while top <= bottom and left <= right:
    for i from left to right:
        print A[top][i]
    top = top + 1
    for i from top to bottom:
        print A[i][right]
    right = right - 1
    if top <= bottom:
        for i from right to left:
            print A[bottom][i]
        bottom = bottom - 1
    if left <= right:
        for i from bottom to top:
            print A[i][left]
        left = left + 1
```

---

### 5. Set Matrix Zeroes
**Problem:** If an element in a matrix is 0, set its entire row and column to 0.

**Explanation:**
First, identify all rows and columns that need to be zeroed. Then, update the matrix in a second pass.

**Pseudocode:**
```
rows = set()
cols = set()
for i from 0 to m-1:
    for j from 0 to n-1:
        if A[i][j] == 0:
            add i to rows
            add j to cols
for i from 0 to m-1:
    for j from 0 to n-1:
        if i in rows or j in cols:
            A[i][j] = 0
```

---

### 6. Find Path in Maze (Backtracking)
**Problem:** Given a binary matrix, find a path from the top-left to the bottom-right cell, moving only through cells with value 1.

**Explanation:**
Use backtracking to explore all possible paths. Mark visited cells to avoid cycles.

**Pseudocode:**
```
function solve_maze(A, x, y, path):
    if x or y out of bounds or A[x][y] == 0:
        return false
    if (x, y) is goal:
        add (x, y) to path
        return true
    mark A[x][y] as visited
    for each direction (dx, dy):
        if solve_maze(A, x+dx, y+dy, path):
            add (x, y) to path
            return true
    unmark A[x][y] as visited (if needed)
    return false
```

---

### 7. Connected Components (DFS/BFS)
**Problem:** Count the number of connected groups of 1s in a binary matrix.

**Explanation:**
Use Depth-First Search (DFS) or Breadth-First Search (BFS) to traverse each component, marking visited cells.

**Pseudocode:**
```
function dfs(A, x, y):
    if x or y out of bounds or A[x][y] != 1:
        return
    mark A[x][y] as visited
    for each direction (dx, dy):
        dfs(A, x+dx, y+dy)
count = 0
for i from 0 to m-1:
    for j from 0 to n-1:
        if A[i][j] == 1:
            dfs(A, i, j)
            count = count + 1
```

---

### 8. Matrix Multiplication
**Problem:** Multiply two matrices if their dimensions are compatible.

**Explanation:**
For matrices A (m x n) and B (n x p), the result is a matrix C (m x p) where C[i][j] = sum(A[i][k] * B[k][j]) for all k.

**Pseudocode:**
```
for i from 0 to m-1:
    for j from 0 to p-1:
        C[i][j] = 0
        for k from 0 to n-1:
            C[i][j] = C[i][j] + A[i][k] * B[k][j]
```

---

### 9. Diagonal Sum
**Problem:** Find the sum of the main and/or secondary diagonals of a square matrix.

**Explanation:**
Sum elements where row and column indices are equal (main diagonal) or sum elements where indices add up to n-1 (secondary diagonal).

**Pseudocode:**
```
main_diag = 0
sec_diag = 0
for i from 0 to n-1:
    main_diag = main_diag + A[i][i]
    sec_diag = sec_diag + A[i][n-1-i]
```

---

### 10. Search for a Word in a Grid
**Problem:** Given a 2D board and a word, check if the word exists in the grid (can move horizontally or vertically).

**Explanation:**
Use backtracking to search for the word starting from each cell. Mark cells as visited during the search to avoid revisiting.

**Pseudocode:**
```
function exist(board, word):
    for i from 0 to m-1:
        for j from 0 to n-1:
            if dfs(i, j, 0):
                return true
    return false
function dfs(x, y, idx):
    if idx == length of word:
        return true
    if x or y out of bounds or board[x][y] != word[idx]:
        return false
    mark board[x][y] as visited
    for each direction (dx, dy):
        if dfs(x+dx, y+dy, idx+1):
            return true
    unmark board[x][y] as visited
    return false
```

---
