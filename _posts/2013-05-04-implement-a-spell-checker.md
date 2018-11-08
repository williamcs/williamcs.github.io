---
title: "Implement a spell checker with java"
date: 2013-05-04
tags: [java, dynamic, algorithm]
excerpt: "java, algorithm"
---

When you typing a word, many systems will suggest many words for you to select
the word which you intend to type. This is spell checker.

It will calculate the edit distance between a dictionary. Every time when typing
 a word, the cost is too expensive if the word compare to the whole dictionary.
If we hash the dictionary with the word length as the key, and the words list
as the value, it will be fast. There are also some other method to improve the
performance, here I just use hash.

For comparison, the dictionary file can be downloaded from internet or use the
linux's default file in /usr/share/dict/ directory. Here Levenshtein distance
is used to compute the edit distance between two words.

Below is my java code implementation.

```java
package datastructure.test;


import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Hashtable;
import java.util.List;

public class SpellChecker {
	private static final int DISTANCE_LEN = 2;	// the distance between two words, this can be changed for the request
	private static Hashtable<Integer, List<String>> ht;	// dictionary hash table

	/**
	 * constructor
	 */
	public SpellChecker() {

	}

	/**
	 * constructor
	 * @param dictFile
	 * @throws IOException
	 */
	public SpellChecker(String dictFile) throws IOException {
		this.ht = hashDictionary(dictFile);
	}

	public static Hashtable<Integer, List<String>> getHashtable(String dictFile) throws IOException {
		if(SpellChecker.ht != null) return SpellChecker.ht;

		SpellChecker.ht = hashDictionary(dictFile);
		return SpellChecker.ht;
	}

	/**
	 * hash the dictionary file with word length as key, the same length of word's list as value
	 * @param dictFile
	 * @return ht
	 * @throws IOException
	 */
	public static Hashtable<Integer, List<String>> hashDictionary(String dictFile) throws IOException {
		Hashtable<Integer, List<String>> ht = new Hashtable<Integer, List<String>>();
		BufferedReader br = new BufferedReader(new FileReader(dictFile));

		String line = null;
		int wordlen = 0;

		while((line = br.readLine()) != null) {
			line = line.trim();
			wordlen = line.length();

			if(wordlen == 0) continue;	// skip the empty line

			if(!ht.containsKey(wordlen)) {
				List<String> lst1 = new ArrayList<String>();
				lst1.add(line);
				ht.put(wordlen, lst1);
			}
			else {
				List<String> lst2 = ht.get(wordlen);
				lst2.add(line);
				ht.put(wordlen, lst2);
 			}
		}

		return ht;
	}

	/**
	 * get a list of words with word length and length + 1 from dictionary file
	 * @param dictFile
	 * @param wordlen
	 * @return lst
	 * @throws IOException
	 */
	public List<String> getDictList(int wordlen, String dictFile) throws IOException {
		List<String> lst = new ArrayList<String>();
		Hashtable<Integer, List<String>> ht = new Hashtable<Integer, List<String>>();
		ht = getHashtable(dictFile);	// cache the dictionary hash table
		//ht = hashDictionary(dictFile);

		// to get probably word list with the word to compare's length and length + 1
		List<String> lst1 = ht.get(wordlen);
		List<String> lst2 = ht.get(wordlen + 1);

		if(lst1 != null) {
			lst = lst1;
		}

		// concatenate two lists together
		if(lst2 != null) {
			for(int i = 0; i < lst2.size(); i++) {
				lst.add(lst2.get(i));
			}
		}


		return lst;
	}

	/**
	 * do the compare between word and dictList
	 * if the Levenshtein Distance is less than DISTANCE_LEN
	 * add the probably words to the result list
	 * @param word
	 * @param dictList
	 * @return result
	 */
	public List<String> spellChecker(String word, List<String> dictList) {
		List<String> result = new ArrayList<String>();
		// get a instance of LevenshteinDistance class to compute the words' distance
		LevenshteinDistance ld = new LevenshteinDistance();
		int distance = 0;
		String dictWord = "";

		for(int i = 0; i < dictList.size(); i++) {
			dictWord = (String)dictList.get(i);
			distance = ld.dynLevenDistance(word, dictWord);

			if(distance < DISTANCE_LEN) {
				result.add(dictWord);
			}
		}

		return result;
	}

	/**
	 * read the dict list from dict file to compare with the word
	 * @param word
	 * @param dictFile
	 * @return result
	 * @throws IOException
	 */
	public List<String> spellChecker(String word, String dictFile) throws IOException {
		List<String> result = new ArrayList<String>();
		List<String> wordList = getDictList(word.length(), dictFile);
		result = spellChecker(word, wordList);
		return result;
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		//String dictFile = "/usr/share/dict/words.pre-dictionaries-common";
		String dictFile = "/root/myworld/devsapce/wkspace/workspace/JavaTestProject/src/datastructure/test/dictionary.txt";
		try {
			SpellChecker spellchecker = new SpellChecker(dictFile);
			String word = "football";
			List<String> lst = spellchecker.spellChecker(word, dictFile);

			System.out.println("The probably words maybe : ");
			for(int i = 0; i < lst.size(); i++) {
				System.out.print(lst.get(i) + " ");
			}

		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

}

```

Levenshtein distance:

```java
package datastructure.test;

public class LevenshteinDistance {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String s = "kitten";
		String t = "sitting";

		LevenshteinDistance ld = new LevenshteinDistance();

		//int recurlen = ld.recurLevenDistance(s, t);
		int dynlen = ld.dynLevenDistance(s, t);
		//System.out.println("The length is : " + recurlen);
		System.out.println("The length is : " + dynlen);
		System.out.println(s.length());
	}

	/*public int minimum(int a, int b, int c){
		return Math.min(Math.min(a, b), c);
	}*/

	public int minimum(int a, int b, int c){
		if(a < b && a < c) return a;
		if(b < a && b < c) return b;
		return c;
	}

	// compute the distance use recursive way
	public int recurLevenDistance(String s, String t){
		int slen = s.length();
		int tlen = t.length();

		int ins = 0; // for recording insert length
		int del = 0; // for recording delete length
		int sub = 0; // for substitution length

		if(slen == 0 && tlen == 0) return 0;
		if(slen == 0) return tlen;
		if(tlen == 0) return slen;

		/*if(s.charAt(slen - 1) == t.charAt(tlen - 1)){
			sub = recurLevenDistance(s.substring(0, slen - 1), t.substring(0, tlen - 1));
		}
		else{
			sub = recurLevenDistance(s.substring(0, slen - 1), t.substring(0, tlen - 1)) + 1;
		}*/

		sub = recurLevenDistance(s.substring(0, slen - 1), t.substring(0, tlen - 1))
				+ ((s.charAt(slen - 1) == t.charAt(tlen - 1)) ? 0 : 1);

		ins = recurLevenDistance(s.substring(0, slen - 1), t) + 1;
		del = recurLevenDistance(s, t.substring(0, tlen - 1)) + 1;

		return minimum(ins, del, sub);
	}

	// compute the distance use dynamic way
	public int dynLevenDistance(String s, String t){
		int[][] distance = new int[s.length() + 1][t.length() + 1];

		int i, j;
		for(i = 0; i <= s.length(); i++)
			distance[i][0] = i;
		for(j = 0; j <= t.length(); j++)
			distance[0][j] = j;

		for(i = 1; i <= s.length(); i++){
			for(j = 1; j <= t.length(); j++){
				distance[i][j] = minimum(distance[i - 1][j] + 1,
						distance[i][j - 1] + 1,
						distance[i - 1][j - 1] + ((s.charAt(i - 1) == t.charAt(j - 1)) ? 0 : 1));
			}
		}

		return distance[s.length()][t.length()];
	}
}

```
