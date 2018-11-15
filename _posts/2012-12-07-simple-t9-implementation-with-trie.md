---
title: "Simple T9 implementation with Trie"
date: 2012-12-07
tags: [java, algorithm]
excerpt: "java, algorithm"
---

```java
package hello.test;

import java.io.*;
import java.util.LinkedList;
import java.util.Queue;

public class Trie {

	private final int R = 26;  // the trie branches
	private Node root = new Node(); // the root node

	// the t9 mapped array which maps number to string on the typing board
	private String[] t9 = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};

	// trie node definition
	private class Node {
		private boolean isWord;
		private Node[] next;

		public Node() {
			this(false);
		}

		public Node(boolean isWord) {
			this.isWord = isWord;
			this.next = new Node[R];
		}
	}

	// insert a word to the trie
	public void insert(String s) {
		Node current = root;

		for(int i = 0; i < s.length(); i++) {
			if(current.next[s.charAt(i) - 'a'] == null) {
				Node n = new Node();
				current.next[s.charAt(i) - 'a'] = n;
			}

			current = current.next[s.charAt(i) - 'a'];
		}

		current.isWord = true;
	}

	// insert a character to some node
	public void insert(Node current, char c) {
		if(current.next[c - 'a'] == null) {
			Node node = new Node();
			current.next[c - 'a'] = node;
		}
		current = current.next[c - 'a'];
	}

	// search a word in the trie
	public boolean search(String s) {
		Node current = root;

		for(int i = 0; i < s.length(); i++) {
			if(current.next[s.charAt(i) - 'a'] == null) {
				return false;
			}
			current = current.next[s.charAt(i) - 'a'];
		}

		return current.isWord == true;
	}

	// breadth first search for a number string use queue
	public void bfs_search(String strNum) {
		Queue<String> q = new LinkedList<String>();

		q.add("");

		for(int i = 0; i < strNum.length(); i++) {
			String keyStr = t9[strNum.charAt(i) - '0'];
			int len = q.size();

			while(len -- > 0) {
				String preStr = q.remove();
				for(int j = 0; j < keyStr.length(); j++) {
					String tmpStr = preStr + keyStr.charAt(j);
					//q.add(tmpStr);
					if(search(tmpStr) && tmpStr.length() == strNum.length()) {
						System.out.println(tmpStr);
					} else {
						q.add(tmpStr);
					}
				}
			}
		}
	}

	// delete a node
	public void delete(Node node) {
		for(int i = 0; i < R; i++) {
			if(node.next != null) {
				delete(node.next[i]);
			}
		}
		node = null;
	}

	// print words
	public void print(Node node) {
		if(node == null) return;
		for(int i = 0; i < R; i++) {
			if(node.next[i] != null) {
				System.out.print((char) (97 + i));
				if(node.next[i].isWord == true) {
					System.out.println();
				}
				print(node.next[i]);
			}

		}
	}

	// print words from root
	public void print() {
		print(root);
	}

	// convert number string to String array
	private String[] numToString(String strNum) {
		String[] strArray = new String[strNum.length()];
		for(int i = 0; i < strNum.length(); i++) {
			strArray[i] = t9[strNum.charAt(i) - '0'];
		}
		return strArray;
	}

	/**
	 * @param args
	 * @throws IOException
	 * @throws Exceptions
	 */
	public static void main(String[] args) throws IOException  {
		// TODO Auto-generated method stub
		Trie t = new Trie();
		BufferedReader bufreader = new BufferedReader(new FileReader("/home/will/myworld/workspace/HelloProject/src/hello/test/testdict.txt"));
		String line = null;

		while((line = bufreader.readLine()) != null) {
			t.insert(line);
		}

		bufreader.close();

		//t.print();

		t.bfs_search("4355");
		t.bfs_search("4332");
		t.bfs_search("43556");
	}

}



```

T9 has many complex features, I just write a basic function. The dictionary can be downloaded from internet. Here I just input a few words in the testdict.txt for test.  The items are:

hell

hello

idea

next

supper
