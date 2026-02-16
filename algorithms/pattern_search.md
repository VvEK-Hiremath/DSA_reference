# Pattern Searching — Algorithms, Pseudocode, and Notes

This document summarizes important pattern/search algorithms on strings and sequences, with intuitive explanations, pseudocode, complexity notes, and typical use-cases.

## Table of Contents
- Overview
- Simple methods
	- Naive search
	- Rabin–Karp (rolling hash)
- Linear-time algorithms
	- Knuth–Morris–Pratt (KMP)
	- Z-algorithm
- Heuristic / sublinear-on-average
	- Boyer–Moore (bad-character & good-suffix)
- Multiple-pattern and automata
	- Aho–Corasick
	- Finite automata / suffix structures (overview)
- Complexity & Use-cases

## Overview

Pattern searching finds occurrences of a pattern P of length m inside a text T of length n. Algorithms trade off preprocessing of P, average vs worst-case time, and memory.

---

## Simple methods

### Naive search
- Idea: Try aligning P at each position of T and compare characters.
- Time: Worst-case $O(nm)$, simple and useful for small inputs.

Pseudocode:
```
procedure NaiveSearch(T, P)
	n := length(T); m := length(P)
	for i from 0 to n-m do
		j := 0
		while j < m and T[i+j] == P[j] do j := j+1
		if j == m then report i
```

### Rabin–Karp (rolling hash)
- Idea: Compute hash of pattern and rolling hash of each m-length window in T; on hash match, verify to avoid false positives.
- Average: $O(n + m)$ with good hash; worst-case $O(nm)$ if many spurious collisions.

Pseudocode (base B, mod M):
```
procedure RabinKarp(T, P, B, M)
	n := length(T); m := length(P)
	pHash := hash(P)
	tHash := hash(T[0..m-1])
	for i from 0 to n-m do
		if tHash == pHash then if T[i..i+m-1] == P then report i
		if i < n-m then
			tHash := (tHash - T[i]*B^{m-1})*B + T[i+m] mod M
```

---

## Linear-time algorithms

### Knuth–Morris–Pratt (KMP)
- Idea: Precompute a longest proper prefix which is also suffix (lps / failure function) for P to know how far to shift when a mismatch occurs; guarantees $O(n+m)$ time.
- Preprocessing: build `lps[0..m-1]` in $O(m)$.

Compute LPS (prefix function):
```
procedure ComputeLPS(P)
	m := length(P)
	lps := array of length m; lps[0] := 0
	len := 0; i := 1
	while i < m do
		if P[i] == P[len] then len := len+1; lps[i] := len; i := i+1
		else if len != 0 then len := lps[len-1]
		else lps[i] := 0; i := i+1
	return lps
```

KMP search using `lps`:
```
procedure KMPSearch(T, P)
	n := length(T); m := length(P)
	lps := ComputeLPS(P)
	i := 0; j := 0
	while i < n do
		if T[i] == P[j] then i := i+1; j := j+1
			if j == m then report i-j; j := lps[j-1]
		else if j != 0 then j := lps[j-1]
		else i := i+1
```

### Z-algorithm
- Idea: Build Z-array where Z[i] is length of longest substring starting at i that matches prefix of the string. Concatenate `P + "$" + T` and compute Z to find matches in linear time.
- Time: $O(n+m)$.

Pseudocode (high-level):
```
procedure ZAlgo(S)  // returns Z array
	n := length(S); Z := array length n; l := 0; r := 0
	for i from 1 to n-1 do
		if i <= r then Z[i] := min(r-i+1, Z[i-l])
		while i+Z[i] < n and S[Z[i]] == S[i+Z[i]] do Z[i] := Z[i]+1
		if i+Z[i]-1 > r then l := i; r := i+Z[i]-1
	return Z

// For pattern matching: S := P + "$" + T; check positions where Z[pos] >= m
```

---

## Heuristic / sublinear-on-average

### Boyer–Moore
- Idea: Scan pattern right-to-left and use precomputed shifts from bad-character and good-suffix heuristics to skip alignments; sublinear average time on typical texts.
- Worst-case can be $O(nm)$ for naive variants; with Galil rule and proper preprocessing, worst-case can be made linear.

High-level steps:
- Precompute bad-character table: for each character, rightmost position in P.
- Precompute good-suffix shifts.
- Align pattern to text, compare from right end; on mismatch compute shift = max(badCharShift, goodSuffixShift).

Pseudocode (sketch):
```
procedure BoyerMoore(T, P)
	preprocess badChar[] and goodSuffix[]
	s := 0  // shift
	while s <= n-m do
		j := m-1
		while j >= 0 and P[j] == T[s+j] do j := j-1
		if j < 0 then report s; s += goodSuffix[0]
		else s += max(1, j - badChar[T[s+j]])
```

---

## Multiple-pattern and automata

### Aho–Corasick
- Idea: Build a trie of patterns with failure links (like KMP on multiple patterns). Then stream through T once, following trie edges and failure links to report all pattern matches in $O(n + total
totalPatternLength + output)$ time.
- Use-case: multi-keyword search (e.g., spam filters, intrusion detection).

Pseudocode (high-level):
```
procedure BuildAho(patterns)
	build trie from patterns; compute failure links using BFS

procedure AhoSearch(T)
	node := root
	for i from 0 to n-1 do
		while node and no edge for T[i] do node := node.fail
		if node has edge T[i] then node := node.edge[T[i]] else node := root
		if node.output not empty then report matches
```

### Finite automata & suffix structures (overview)
- Finite automaton for pattern: build DFA of size O(m|Σ|) to run in O(n) time.
- Suffix tree / suffix array: powerful for many pattern tasks (longest repeated substring, multiple patterns, indexing). Building suffix array/tree takes O(n) or O(n log n); queries often O(m + occ).

---

## Complexity & Use-cases

- Naive: $O(nm)$ — simple, good for short patterns or small texts.
- Rabin–Karp: $O(n+m)$ average, $O(nm)$ worst (useful when matching many patterns by combining hashes).
- KMP / Z: $O(n+m)$ worst-case — good general-purpose exact-match.
- Boyer–Moore: sublinear average, good for long patterns and large alphabets.
- Aho–Corasick: $O(n + totalPatternLength + output)$ — best choice for many patterns.
- Suffix structures: powerful for advanced queries and indexing; initial build cost but fast repeated queries.

When to use which:
- Use `KMP` or `Z` if worst-case linear time is desired and pattern preprocessing is cheap.
- Use `Boyer–Moore` for practical fast searching on natural language text.
- Use `Rabin–Karp` when you want rolling-hash conveniences (e.g., substring fingerprinting, plagiarism detection).
- Use `Aho–Corasick` for multi-pattern search.

---

If you want, I can:
- add worked examples and step-by-step traces (KMP or Boyer–Moore),
- convert key algorithms into runnable Python implementations with tests,
- or add a cheatsheet comparing preprocessing and memory trade-offs.

