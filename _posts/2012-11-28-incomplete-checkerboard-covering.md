---
title: "Incomplete checkerboard covering"
date: 2012-11-28
tags: [python, algorithm]
excerpt: "python, algorithm"
---

Cover a checkerboard that has one corner square missing. The size of the board is usuall 2**k, use recursive way the board will be

divided into four 2**(k-1) size small board. Cover the center board with a L shape tile, the one fourth board will the same as the original board.

(r, c) is the left top corner coordinate. s is size / 2.

<img src="{{ site.url }}{{ site.baseurl }}/images/old/checker_board.jpg" alt="linearly separable data">

```python
tile = 0 # global variable for the L shape tile counting


def cover(board, brow, bcol, mrow, mcol, size):
  '''
      brow, bcol is the board's up left corner coordinate
      mrow, mcol is the missing tile's coordinate
      size is the board size
      2**k size board will be divided to four 2**(k-1) size sub-board
      if the missing tile is one fourth board, the L shape will be put
      in the other three one fourth board's center corner
  '''
    if size is 1:
        return

    global tile
    tile += 1
    count = tile  # L shape tile number
    s = size // 2  # divide the board

    # cover the upper left board
    if mrow < brow + s and mcol < bcol + s:  # if the missing tile is in this square
        cover(board, brow, bcol, mrow, mcol, s)
    else:
        board[brow + s - 1][bcol + s - 1] = count  # cover the down right corner with L shape
        cover(board, brow, bcol, brow + s - 1, bcol + s - 1, s)

    # cover the upper right board
    if mrow < brow + s and mcol >= bcol + s:  # if the missing tile is in this square
        cover(board, brow, bcol + s, mrow, mcol, s)
    else:
        board[brow + s - 1][bcol + s] = count  # cover the left down corner with L shape
        cover(board, brow, bcol +s, brow + s - 1, bcol +s, s)

    # cover the down left board
    if mrow >= brow + s and mcol < bcol + s:  # if the missing tile is in this square
        cover(board, brow + s, bcol, mrow, mcol, s)
    else:
        board[brow + s][bcol + s - 1] = count  # cover the up right corner with L shape
        cover(board, brow + s, bcol, brow + s, bcol + s - 1, s)

    # cover the down right board
    if mrow >= brow + s and mcol >= bcol + s:  # if the missing tile is in this square
        cover(board, brow +s, bcol + s, mrow, mcol, s)
    else:
        board[brow + s][bcol + s] = count  # cover the up left corner with L shape
        cover(board, brow + s, bcol + s, brow + s, bcol + s, s)    

def main():
    board = [[0] * 8 for i in range(8)] # 8 * 8 board
    board[7][7] = -1 # the missing corner
    cover(board, 0, 0, 7, 7, len(board))

    for row in board:
        print ' %2i' * 8 % tuple(row)

if __name__ == '__main__':
    main()

```
