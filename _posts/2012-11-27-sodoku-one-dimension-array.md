---
title: "Sudoku backtracking with one dimension array"
date: 2012-11-27
tags: [java, backtracking, algorithm]
excerpt: "java, algorithm"
---

The below program is written in a few years ago, it costed me some effort to use
the backtracking search algorithm to solve the sodoku problem. to use one
dimensional array for storing the board data has some tricks. An usual way is
use two dimensional array to store the board data, the program is worth to remember,
and the days I struggled to solve this problem are good memory to recall.

Have fun and have that memory to cherish.

```java
package hello.test;

import java.io.*;
import java.util.*;

public class Sudoku {

	private static final int N = 9;  // the size of the board
	private int[] board;  // the one dimension array that stores board data

	// constructor
	public Sudoku() {
		try {
			init();
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		}
	}

	// init
	private void init() throws FileNotFoundException {
		board = new int[N * N];

		Scanner fin = new Scanner(new File("/home/will/myworld/workspace/HelloProject/src/sudokuin.txt"));
		int numCases = fin.nextInt();

		for (int c=1; c<=numCases; c++) {
			for(int i = 0; i < N * N; i++) {
				board[i] = fin.nextInt();
			}
		}

	}

	// solve the problem using backtracking method
	public void solve(int cell) {
		if (cell == N * N) { this.show(); return; }

		// skip over cell n since it has fixed value
		if (board[cell] != 0) { solve(cell + 1); return; }

		// try all 9 possibilities
		for (int n = 1; n <= N; n++) {
			if (isConsistent(cell, n)) {
				board[cell] = n;
				solve(cell + 1);
			}
		}
		board[cell] = 0; // clean up after itself
	}

	// if the cell puts number n, does it conflict in row, column and the small square
	private boolean isConsistent(int cell, int n) {
		//if (board[cell] != 0) return false;

		int R = cell / N;  // the row of the two dimensional array
		int C = cell % N;  // the column of the two dimensional array
		int r = R / 3;	   // the small 3 * 3 array's row
		int c = C / 3;     // the small 3 * 3 array's column

		// is the row consistent
		for(int i = 0; i < N; i++) {
			if(board[R * N + i] == n) return false;  // the row of the cell is equal to n
		}

		// is the column consistent
		for(int j = 0; j < N; j++) {
			if(board[j * N + C] == n) return false;  // the column of the cell is equal to n
		}

		// is the square consistent
		for(int i = 0; i < 3; i++) {
			for(int j = 0; j < 3; j++) {
				// (3 * r + i) * N + 3 * c + j is the small square's position in one dimensional array
				if(board[(3 * r + i) * N + 3 * c + j] == n) return false;  
			}
		}

		// otherwise, the cell is consistent in row, column and square, return true
		return true;
	}

	// show board
	public void show() {
		for(int i = 1; i <= N * N; i++) {
			System.out.print(board[i - 1] + " ");
			if(i % N == 0) {
				System.out.println();
			}
		}
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) throws Exception {
		Sudoku s = new Sudoku();
		s.solve(0);
	}

}
```

**Note:** `Below is one test case to run, you can copy it to a created sudokuin.txt
and run the program.`
{: .notice--info}

```
1
0 8 0 4 0 2 0 6 0
0 3 4 0 0 0 9 1 0
9 6 0 0 0 0 0 8 4
0 0 0 2 1 6 0 0 0
0 0 0 0 0 0 0 0 0
0 0 0 3 5 7 0 0 0
8 4 0 0 0 0 0 7 5
0 2 6 0 0 0 1 3 0
0 9 0 7 0 1 0 4 0
```

The origin date of the publish is: 2012-11-27
