---
layout: post
title: "Mini Cactpot Solving: A Wonderfully Deep Interview Question"
categories:
  - python
  - jupyter
tags:
  - FFXIV
  - Mini-Cactpot
  - python
  - jupyter
  - interview
  - statistics
---

After watching Asmongold, a famous World of Warcraft streamer, get hooked on the rather unassuming game of Mini-Cactpot. I wanted to explore what it takes to solve Mini-Cactpot and how randomness affects the desired outcome of getting the max score. The actual solving of Mini-Cactpot is rather trivial, but the exploration of different methods to solve the puzzle actually makes for a great problem-solving computer science interview question much to the likes of [John Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life). All the code I wrote for this blog post is on my [Github](https://github.com/kinilune/cactpot-solver).

## What is Mini-Cactpot?

If you have never played the game Final Fantasy XIV you might not be familiar with the game of Mini-Cactpot. Mini-Cactpot is a simple game inside of Final Fantasy XIV that you play in a virtual casino called the Gold Saucer. The game takes place on a three-by-three grid with the numbers one through nine placed on random spots within the grid. As a player you are only revealed one number on the board, you are then tasked with picking three other hidden numbers. After selecting your numbers you pick a line, a line can be horizontal, vertical or a diagonal selection of three numbers hidden or not. Once selected the sum of the three numbers in the line is compared to a [board of payouts](#figure-2). 1-2-3 or 6 is the largest payout in the game of 10,000 Manderville Gold Saucer points or MGP for short. Players can buy cool things in game with a large amount of MGP.

[Figure 1](#figure-1) shows an example of a fully completed cactpot board.

<a name="figure-1"></a>![Mini-Cactpot Board](../assets/images/cactpotboard.png){: height="230px" width="230px"}
**Figure 1**: _A solved mini-cactpot board_

| Sum | Payout |
| --- | --- |
| 6 | 10000 |
| 7 | 36 |
| 8 | 720 |
| 9 | 360 |
| 10 | 80 |
| 11 | 252 |
| 12 | 108 |
| 13 | 72 |
| 14 | 54 |
| 15 | 180 |
| 16 | 72 |
| 17 | 180 |
| 18 | 119 |
| 19 | 36 |
| 20 | 306 |
| 21 | 1080 |
| 22 | 144 |
| 23 | 1800 |
| 24 | 3600 |

<a name="figure-2"></a>**Figure 2**: _Mini-Cactpot payout board_

## Solving an already solved mini-cactpot board

Take the board from [figure 1](#figure-1) and let's represent this board in array form and compute the MGP you would receive from any of the available lines. 
```python
board = [7, 6, 9, 4, 5, 3, 2, 1, 8]
print(compute_evs(board))
```