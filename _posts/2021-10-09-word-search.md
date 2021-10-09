---
layout: post
title: "Leetcode - Word Search I and Word Search II"
categories:
  - python
  - leetcode 
tags:
  - leetcode
  - daily challenge
---

Leetcode solution and explanation for today's daily challenge. These are problems 79 and 212 on leetcode.

## Word Search I

#### Problem Statement
> Given an m x n grid of characters board and a string word, return true if word exists in the grid.

![](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)

In this grid, we can say the word "ABCCED" is valid and return True. We can't say the same for "DEEC", since no valid word is found we will return False.

A general approach is to iterate through the grid and find the starting letter of our word. If it is found we will perform a Depth-First search on the matrix to find any letters that match with the remaining characters on the string.

```python
def exist(self, board: List[List[str]], word: str) -> bool:
    # iterate through the board
    for i in range(len(board)):
        for j in range(len(board[i])):
            # if we find a matching start letter, lets perform a DFS on the board.
            if board[i][j] == word[0] and self.dfs(board, i, j, 0, word):
                return True
    return False
```

Why use a DFS? A DFS can be easier to implement and understand, also because we only want one matching string we can ignore non-matching characters in our DFS function.

```python
def dfs(self, board, i, j, len_word, word):
    # if we reach the end of the word we will return True
    if len_word == len(word):
        return True
    # if we reach out of bounds or no-matching letters is found return false
    if i < 0 or i >= len(board) or j >= len(board[i]) or j < 0 or board[i][j] != word[len_word]:
        return False
    # empty current spot, so we don't recursively return to that spot and accidentally count it
    x = board[i][j]
    board[i][j] = ''
    # make recursive calls 
    ret_bool = self.dfs(board, i+1, j, len_word+1, word) or \
                self.dfs(board, i-1, j, len_word+1, word) or \
                self.dfs(board, i, j+1, len_word+1, word) or \
                self.dfs(board, i, j-1, len_word+1, word)
    # reset once all the calls are made
    board[i][j] = x
    return ret_bool
```

#### Runtime and Space Complexity
The runtime complexity of the DFS solution is O(m * n 4^s) where m is the number of rows, n is the number of columns, and s is the length of the word.

Where does the 4 come from?

The 4 is the number of choices or neighbors present during our DFS, we have to call four neighboring cells in our DFS and each neighbor the next four cells until the word is found or not found.

The space complexity is O(s) if you modify the matrix in-place, s being the number of calls on the call stack

## Word Search II

Word Search II is similar to Word Search I in many ways except for one key difference. Instead of looking for a single word, instead we are looking for multiple this brings an optimization that previously we ignored.

#### Naive Attempt

Using our code from Word Search I we can simply add another for loop that iterates over the word list and if we find that the word exists in the matrix we append it to a new list.
While this works, it is very slow, the old runtime was O(m * n 4^s), now we are adding another linear time to this notation. Let's see if we can improve this.

#### Introducing Tries

A trie is a type of tree that contains keys or characters as leaves within it. It is often used as a search structure for finding leaves quickly. It is often traversed depth first, making it perfect for our use case.

#### Trie implementation

```python
class TrieNode:
    def __init__(self):
        self.check_word = False
        self.children = {}

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        cur = self.root
        for letter in word:
            if letter not in cur.children:
                cur.children[letter] = TrieNode()
            cur = cur.children[letter]
        cur.check_word = True
```

#### Using Tries and the benefit

Using a trie is quite simple, to add a new word to the trie you call `insert(word)` on the data structure, this iterates over the word, and adds the subsequent letters to its children.

The benefits of using a trie over hashing or a BST is you get linear searching and insert and makes prefix searching doable. 

The disadvantage is primarily memory constraints, memory explodes after adding many strings to the trie.

In word search we primarily want the benefit of linear search times and prefix searching so we can disregard words.

#### DFS with Tries

Some changes need to be made in order to use a trie effectively. The base case for our DFS is still the same, but we can use the `check_word` param in each node to check if it is a whole word we added to the trie. If it is we know we have reached the end of that subtree.

```python
def dfs(self, board, node, i, j, x, ret_words):
    if node.check_word:
        ret_words.append(x)
        node.check_word = False
    if i < 0 or i >= len(board) or j >= len(board[i]) or j < 0:
        return None
    y = board[i][j]
    if y in node.children:
        node = node.children[y]
    else:
        return None
    board[i][j] = ''
    self.dfs(board, node, i+1, j, x+y, ret_words)
    self.dfs(board, node, i-1, j, x+y, ret_words) 
    self.dfs(board, node, i, j+1, x+y, ret_words)
    self.dfs(board, node, i, j-1, x+y, ret_words) 
    board[i][j] = y
```

#### Full implementation

```python
class TrieNode:
    def __init__(self):
        self.check_word = False
        self.children = {}
class Trie:

    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        cur = self.root
        for letter in word:
            if letter not in cur.children:
                cur.children[letter] = TrieNode()
            cur = cur.children[letter]
        cur.check_word = True
        
class Solution:
    def findWords(self, board: List[List[str]], words: List[str]) -> List[str]:
        t = Trie()
        ret_words = []
        start_node = t.root
        for word in words:
            t.insert(word)
        for i in range(len(board)):
            for j in range(len(board[i])):
                self.dfs(board, start_node, i, j, "", ret_words)
        return ret_words
    
    def dfs(self, board, node, i, j, x, ret_words):
        if node.check_word:
            ret_words.append(x)
            node.check_word = False
        if i < 0 or i >= len(board) or j >= len(board[i]) or j < 0:
            return None
        y = board[i][j]
        if y in node.children:
            node = node.children[y]
        else:
            return None
        board[i][j] = ''
        self.dfs(board, node, i+1, j, x+y, ret_words)
        self.dfs(board, node, i-1, j, x+y, ret_words) 
        self.dfs(board, node, i, j+1, x+y, ret_words)
        self.dfs(board, node, i, j-1, x+y, ret_words) 
        board[i][j] = y
```