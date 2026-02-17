## Additional Tree Type
- **Almost Complete Binary Tree**: A binary tree in which all levels are completely filled except possibly the last, and the last level has all its nodes as far left as possible. This is very similar to a complete binary tree, and the terms are often used interchangeably. However, in some contexts, 'almost complete' may refer to a tree that is close to being complete but may have a few missing nodes at the last level, still maintaining the left alignment.
# Tree Data Structure

A **tree** is a hierarchical data structure consisting of nodes, with a single node designated as the root. Each node may have zero or more child nodes, and there are no cycles (i.e., no node can be its own ancestor).

## Key Terminology
- **Root**: The topmost node in the tree.
- **Parent**: A node that has one or more child nodes.
- **Child**: A node that descends from another node (its parent).
- **Leaf**: A node with no children.
- **Edge**: The connection between two nodes.
- **Subtree**: A tree formed by a node and its descendants.
- **Height**: The length of the longest path from the root to a leaf.


## Types of Trees
- **Binary Tree**: Each node has at most two children (left and right).
- **Full Binary Tree**: Every node has either 0 or 2 children (no node has only one child).
- **Complete Binary Tree**: All levels are completely filled except possibly the last, which is filled from left to right.
- **Balanced Tree**: A tree where the height of the left and right subtrees of any node differ by at most a fixed amount (commonly 1). This ensures operations remain efficient (e.g., AVL, Red-Black Trees).
- **Binary Search Tree (BST)**: A binary tree where the left child is less than the parent, and the right child is greater.
- **AVL Tree**: A self-balancing BST where the heights of two child subtrees differ by at most one.
- **Red-Black Tree**: A self-balancing BST with additional color properties for balancing.
- **Heap**: A complete binary tree where each node is greater (max-heap) or less (min-heap) than its children.
- **Trie**: A tree used for efficient retrieval of strings.
- **B-Tree**: A self-balancing search tree for large blocks of data, commonly used in databases.

## Important Operations (with Pseudocode)

### 1. Traversal
#### Inorder Traversal (for Binary Trees)
```
function inorder(node):
	if node is not null:
		inorder(node.left)
		visit(node)
		inorder(node.right)
```

#### Preorder Traversal
```
function preorder(node):
	if node is not null:
		visit(node)
		preorder(node.left)
		preorder(node.right)
```

#### Postorder Traversal
```
function postorder(node):
	if node is not null:
		postorder(node.left)
		postorder(node.right)
		visit(node)
```

### 2. Insertion (Binary Search Tree)
```
function insert(node, value):
	if node is null:
		return new Node(value)
	if value < node.value:
		node.left = insert(node.left, value)
	else:
		node.right = insert(node.right, value)
	return node
```

### 3. Search (Binary Search Tree)
```
function search(node, value):
	if node is null or node.value == value:
		return node
	if value < node.value:
		return search(node.left, value)
	else:
		return search(node.right, value)
```

### 4. Deletion (Binary Search Tree)
```
function delete(node, value):
	if node is null:
		return null
	if value < node.value:
		node.left = delete(node.left, value)
	else if value > node.value:
		node.right = delete(node.right, value)
	else:
		if node.left is null:
			return node.right
		else if node.right is null:
			return node.left
		temp = findMin(node.right)
		node.value = temp.value
		node.right = delete(node.right, temp.value)
	return node

function findMin(node):
	while node.left is not null:
		node = node.left
	return node
```

---
These are the fundamental concepts, types, and operations for tree data structures. Each type of tree may have additional or specialized operations.
