---
title: "Longest common subsequence problem and poj 1159 Palindrome"
date: 2013-04-24
tags: [java, python, algorithm]
excerpt: "java, python, algorithm"
---

The longest common subsequence problem is a very basic problem in dynamic programming. If you dive in,there is many other methods to compute the longest common subsequence. Here is just the very basic methods,it also has some relationship with palindrome problem. To make a string become palindrome,it can compute the long common subsquence first.

Java code:
```java
package datastructure.test;

public class LCS {
	private String X;
	private String Y;
	private int M;	// the length of X
	private int N;	// the length of Y
	private int[][] opt;	// two dimensional array to store the maximum length of X and Y

	/*
	 * constructor
	 */
	public LCS(String X, String Y) {
		this.X = X;
		this.Y = Y;
		this.M = X.length();
		this.N = Y.length();

		// opt[i][j] = length of LCS of x[i..M] and y[j..N]
		this.opt = new int[M + 1][N + 1];
	}

	/*
	 * compute the LCS and all subproblem via dynamic programming
	 * the longest length be set in opt[0][0]
	 */
	public void dyncomputeLCS() {
		for (int i = M - 1; i >= 0; i--) {
			for (int j = N - 1; j >= 0; j--) {
				if (this.X.charAt(i) == this.Y.charAt(j)) {
					this.opt[i][j] = this.opt[i + 1][j + 1] + 1;
				}
				else {
					this.opt[i][j] = Math.max(this.opt[i + 1][j], this.opt[i][j + 1]);
				}
			}
		}
	}

	/*
	 * compute the LCS of X and Y with the recursive way
	 */
	public int reclcs (String X, String Y) {
		if (X.length() == 0 || Y.length() == 0) {
			return 0;
		}
		else if (X.charAt(X.length() - 1) == Y.charAt(Y.length() - 1)) {
			return reclcs(X.substring(0, X.length() - 1), Y.substring(0, Y.length() - 1)) + 1;
		}
		else {
			return Math.max(reclcs(X.substring(0, X.length() - 1), Y), reclcs(X, Y.substring(0, Y.length() - 1)));
		}
	}

	/*
	 * compute the LCS of X and Y with the dynamic way
	 */
	public int dynlcs (String X, String Y) {
		int i, j;
		int lenx = X.length();
		int leny = Y.length();
		int[][] table = new int[lenx][leny];

		/*
		 * below init segment can be ignored, because java will do it by default
		for (i = 1; i < lenx; i++) {
			table[i][0] = 0;
		}

		for (j = 1; j < leny; j++) {
			table[0][j] = 0;
		}
		*/

		for (i = 1; i < lenx; i++) {
			for (j =1; j < leny; j++) {
				if (X.charAt(i) == Y.charAt(j)) {
					table[i][j] = table[i - 1][j - 1] + 1;
				}
				else {
					table[i][j] = Math.max(table[i - 1][j], table[i][j -1]);
				}
			}
		}

		return table[lenx - 1][leny - 1];
	}

	/*
	 * with dynamic way to compute the shortest length of insert characters to
	 * make X to be palindrome
	 */
	public int palindromedyn (String X) {
		int lenx = X.length();
		int[][] dp = new int[lenx][lenx];

		for (int i = lenx - 1; i >= 0; i--) {
			for (int j = i + 1; j <= lenx - 1; j++) {
				if (X.charAt(i) == X.charAt(j)) {
					dp[i][j] = dp[i + 1][j - 1];
				}
				else {
					dp[i][j] = Math.min(dp[i + 1][j], dp[i][j - 1]) + 1;
				}
			}
		}

		return dp[0][X.length() - 1];
	}

	/*
	 * use dynamic way to compute the shortest length of insert characters to make X's substring
	 * to be palindrome, the substring's index is from start to end
	 *
	 */
	public int palindromedyn (String X, int start, int end) {
		int lenx = X.length();

		assert(start >= 0 && start < lenx && end >= 0 && end < lenx && start < end);

		int[][] dp = new int[lenx][lenx];

		for (int i = end - 1; i >= start; i--) {
			for (int j = i + 1; j <= end - 1; j++) {
				if (X.charAt(i) == X.charAt(j)) {
					dp[i][j] = dp[i + 1][j - 1];
				}
				else {
					dp[i][j] = Math.min(dp[i + 1][j], dp[i][j - 1]) + 1;
				}
			}
		}

		return dp[start][end - 1];
	}

	/*
	 * with recursive way to compute the shortest length of insert characters to
	 * make X to be palindrome
	 */
	public int palindromerec (String X) {
		if (X.length() == 0 || X.length() == 1) {
			return 0;
		}
		else if (X.charAt(0) == X.charAt(X.length() - 1)){
			return palindromerec(X.substring(1, X.length() - 1));
		}
		else
		{
			int a = palindromerec(X.substring(0, X.length() - 1));
			int b = palindromerec(X.substring(1, X.length()));
			return Math.min(a, b) + 1;
		}
	}

	/*
	 * use recursive way to compute the shortest length of insert characters to make X's substring
	 * to be palindrome, the substring's index is from start to end
	 *
	 */
	public int palindromerec (String X, int start, int end) {
		assert(start >= 0 && start < X.length() && end >= 0 && end < X.length());

		if (X.length() == 0 || X.length() == 1 || start >= end) {
			return 0;
		}
		else if (X.charAt(start) == X.charAt(end - 1)){
			return palindromerec(X.substring(start + 1, end - 1));
		}
		else {
			int a = palindromerec(X.substring(start, end - 1));
			int b = palindromerec(X.substring(start + 1, end));
			return Math.min(a, b) + 1;
		}
	}

	/*
	 * compute the shortest length of make X to a palindrome with longest common subsequence
	 */
	public int palindromelcs (String X) {
		String Y = stringreverse(X);
		//int lcslen = reclcs(X, Y);
		int lcslen = dynlcs(X, Y);
		return X.length() - lcslen;
	}

	/*
	 * compute the shortest length of make X's substring to a palindrome with longest common subsequence
	 * the index is from start to end
	 */
	public int palindromelcs (String X, int start, int end) {
		String Y = stringreverse(X.substring(start, end));
		int lcslen = reclcs(X.substring(start, end), Y);
		//int lcslen = dynlcs(X.substring(start, end), Y);
		return end - start - lcslen;
	}

	/*
	 * get the string s's reversed string
	 */
	private String stringreverse (String s) {
		String t = "";

		for (int i = s.length() - 1; i >= 0; i--) {
			t = t + s.charAt(i);
		}
		return t;
	}

	/*
	 * get the longest common substring length of X and Y
	 */
	public int maxLength () {
		return this.opt[0][0];
	}

	private int maxtriple (int a, int b, int c) {
		int max;

		if (a > b)
			max = a;
		else
			max = b;

		if (max < c)
			max = c;

		return max;
	}

	private int mintriple (int a, int b, int c) {
		return Math.min(a, Math.min(b, c));
	}

	/*
	 * print the longest common substring of X and Y
	 */
	public void printLCS() {
		int i = 0, j = 0;

		while (i < this.M && j < this.N) {
			if (this.X.charAt(i) == this.Y.charAt(j)) {
				System.out.print(this.X.charAt(i));
				i++;
				j++;
			}
			else if (this.opt[i + 1][j] >= this.opt[i][j + 1]) {
				i++;
			}
			else {
				j++;
			}
		}
	}



	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String X = "afddddd";
		String Y = "eindddd";

		LCS lcs = new LCS(X, Y);
		//lcs.dyncomputeLCS();
		//System.out.println("the max length of X and Y is : " + lcs.maxLength());
		//System.out.println("the max length of X and Y is : " + lcs.dynlcs(X, Y));
		//System.out.println("the recursive max length of X and Y is : " + lcs.reclcs(X, Y));

		//System.out.println("The reversed string is : " + lcs.stringreverse("abcd"));

		//lcs.printLCS();

		// compute poj 1159 Palindrome
		//System.out.println("The palindrome length is : " + lcs.palindromedyn("Ab3bd"));
		System.out.println("The palindrome dynamic length is : " + lcs.palindromedyn("Ab3bd", 0, 5));
		//System.out.println("The palindrome recursive length is : " + lcs.palindromerec("Ab3bd", 0, 5));
		//System.out.println("The palindrome lcs length is : " + lcs.palindromelcs("Ab3bd", 0, 5));

	}

}

```

Python code:
```python
D = {}

def lcs_recursive(X, Y):
    x , y = len(X), len(Y)
    global D

    if x == 0 or y == 0:
        return 0
    elif X[x - 1] == Y[y - 1]:
        if x - 1 not in D:
            D[x - 1] = X[x - 1]
        return lcs_recursive(X[: x - 1], Y[: y - 1]) + 1
    else:
        return max(lcs_recursive(X, Y[: y - 1]), lcs_recursive(X[: x - 1], Y))

def lcs_dynamic(X, Y):
    m , n = len(X), len(Y)
    c = [[0 for col in range(m + 1)] for row in range(n + 1)]

    for i in range(m):
        c[i][0] = 0
    for j in range(n):
        c[0][j] = 0

    for i in range(m):
        for j in range(n):
            if X[i] == Y[j]:
                c[i + 1][j + 1] = c[i][j] + 1
            elif c[i][j + 1] >= c[i + 1][j]:
                c[i + 1][j + 1] = c[i][j + 1]
            else:
                c[i + 1][j + 1] = c[i + 1][j]

    return c[m][n]

def lcs_dynamic1(X, Y):
    m , n = len(X), len(Y)
    tuple = (0, False)
    c = [[tuple for col in range(m + 1)] for row in range(n + 1)]

#    for i in range(m):
#        c[i][0] = 0
#    for j in range(n):
#        c[0][j] = 0

    for i in range(m):
        for j in range(n):
            if X[i] == Y[j]:
                c[i + 1][j + 1] = (c[i][j][0] + 1, True)
            elif int(c[i][j + 1][0]) >= int(c[i + 1][j][0]):
                c[i + 1][j + 1] = (c[i][j + 1][0], False)
            else:
                c[i + 1][j + 1] = (c[i + 1][j][0], False)

    return c

def print_lcs(C, m, n, X):
    for i in range(m):
        for j in range(n):
            if C[i][j][1] == True:
                print(X[i - 1])


def test():
    X = "abcddd"
    Y = "bcddde"
    lcs_len = lcs_recursive(X, Y)
    print(lcs_len)
    print(D.keys(), D.values())

def test1():
    X = "abcddd"
    Y = "bcddde"
    lcs= lcs_dynamic1(X, Y)
    print_lcs(lcs, len(X), len(Y), X)
    #print(lcs_len, len(lcs_len))

if __name__ == "__main__":
    test1()
#    tuple = (0, False)
#    multilist = [[tuple for col in range(5)] for row in range(3)]
#    multilist[0][4] = (5, True)
#    h = multilist[0][4][0]
#    print(int(h) > 4)
    #test()
#    X = "abcd"
#    x = len(X)
#    print(X[x - 1])


```
