---
title: "Lowest common ancestor in binary search tree"
date: 2014-09-01
tags: [c++, algorithm]
excerpt: "c++, algorithm"
---

This is a very common question, I  write this just for review. Because it is binary search tree. So we can use binary search tree's property to solve the problem. 

  Solution:  Assume the two nodes are n1 and n2 and n1 < n2, n is the lca to be found. if we n1 < n < n2 , then n is the lca. Otherwise recursively traverse the BST to find when n1 < n < n2. If n is greater than both n1 and n2, then lca lies on left subtree of n. If  n is smaller than both n1 and n2, then lca lies on right subtree of n. Assuming both n1 and n2 are in the BST. 

Consider the below BST for an example:
<img src="{{ site.url }}{{ site.baseurl }}/images/old/bst.gif" alt="linearly separable data">

Below is cpp code to get the lca of this bst:
```cpp
#include <iostream>

using namespace std;

template <class T>  class BST;

template <class T> class BSTNode	// binary search tree node definition
{
	friend class BST<T>;

private:
	BSTNode<T> *leftChild;	// left child
	BSTNode<T> *rightChild;	// right child
	T data;	// data domain

public:
	BSTNode() : leftChild(NULL), rightChild(NULL) {}	// constructor
	BSTNode(const T item) : data(item), leftChild(NULL), rightChild(NULL) {}	// constructor
	BSTNode(const T item, BSTNode<T> *left, BSTNode<T> *right)
		: data(item), leftChild(left), rightChild(right) {}

	T getData() const { return data; }	// get data

	BSTNode<T>* getLeft() const { return leftChild; }	// get left child
	BSTNode<T>* getRight() const { return rightChild; }	// get right child
	~BSTNode() {}
};

template <class T> class BST	// binary search tree class definition
{
private:
	BSTNode<T> *root;	// root node
	void insert(const T &x, BSTNode<T> * &ptr); // insert node to the tree
	void insert_iter(const T &x, BSTNode<T> * &ptr);

	T lca(BSTNode<T> *ptr, T a, T b);	// return the lowest common ancester of node data between a and b
	T lca_iter(BSTNode<T> *ptr, T a, T b);	// iterative way to find lca
	bool find(T x, BSTNode<T> *ptr);	// find node in the binary search tree
	bool find_iter(T x, BSTNode<T> *ptr);	// find node in bst iterative way
public:
	BST<T>() : root(NULL) {}	// constructor
	void insert(const T &x) { insert_iter(x, root); }
	void insert(BSTNode<T> *ptr);
	void traverse(BSTNode<T> *current);
	void traverse() { traverse(root); }

	T lca(T a, T b) { return lca_iter(root, a, b); }
	bool find(T x) { return find_iter(x, root); }
};

template <class T> void BST<T>::insert(const T &x, BSTNode<T> * &ptr)
{
	if (ptr == NULL)
	{
		ptr = new BSTNode<T>(x);
		if (ptr == NULL) { cerr << "Out of space" << endl; exit(1); }
	}
	else if (x < ptr->data) insert(x, ptr->leftChild);
	else if (x > ptr->data) insert(x, ptr->rightChild);
}

template <class T> void BST<T>::insert_iter(const T &x, BSTNode<T> * &ptr)
{
	if (ptr == NULL)
	{
		ptr = new BSTNode<T>(x);
		return;
	}
	else
	{
		BSTNode<T> *temp = ptr;
		while (true)
		{
			if (temp->data == x)
			{
				return;
			}
			else if (x < temp->data)
			{
				if (temp->leftChild == NULL)
				{
					temp->leftChild = new BSTNode<T>(x);
					return;
				}
				else
				{
					temp = temp->leftChild;
				}

			}
			else if (x > temp->data)
			{
				if (temp->rightChild == NULL)
				{
					temp->rightChild = new BSTNode<T>(x);
					return;
				}
				else
				{
					temp = temp->rightChild;
				}
			}
		}
	}
}

template <class T> void BST<T>::insert(BSTNode<T> *ptr)
{
	BSTNode<T> *p = NULL;

	if (NULL == root)
	{
		root = ptr;
		return;
	}
	else
	{
		p = root;
	}

	while (true)
	{
		if (ptr->getData() == p->getData()) return;
		else if (ptr->getData() < p->getData())
		{
			if (p->getLeft() == NULL)
			{
				p->leftChild = ptr;
				return;
			}
			else
			{
				p = p->getLeft();
			}
		}
		else
		{
			if (p->getRight() == NULL)
			{
				p->rightChild = ptr;
				return;
			}
			else
			{
				p = p->getRight();
			}
		}
	}
}

template <class T> void BST<T>::traverse(BSTNode<T> *current)
{
	if (current != NULL)
	{
		cout << current->data << " ";
		traverse(current->leftChild);
		traverse(current->rightChild);
	}
}

template <class T> T BST<T>::lca(BSTNode<T> *ptr, T a, T b)
{
	//if (ptr == NULL) return T(NULL);

	//if (ptr->data > a && ptr->data > b)
	//{
	//	return lca(ptr->leftChild, a, b);
	//}

	//if (ptr->data < a && ptr->data < b)
	//{
	//	return lca(ptr->rightChild, a, b);
	//}

	//return ptr->data;

	if (!find(a) || !find(b)) return T(NULL);	// if a or b not in the bst, return default value.

	if (ptr == NULL) return T(NULL);	// if a and b are not in the tree or a == b then return default value of type T.

	if (a > b) { T temp = a; a = b; b = temp; }	// if a > b then swap them first.

	if (ptr->data > a && ptr->data < b) return ptr->getData();
	else if (ptr->data > a && ptr->data > b) return lca(ptr->leftChild, a, b);
	else return lca(ptr->rightChild, a, b);

}

template <class T> T BST<T>::lca_iter(BSTNode<T> *ptr, T a, T b)
{
	if (!find(a) || !find(b)) return T(NULL);	// if a or b not in the bst, return default value.

	if (ptr == NULL) return T(NULL);	// if a and b are not in the tree or a == b then return default value of type T.

	if (a > b) { T temp = a; a = b; b = temp; }	// if a > b then swap them first.

	BSTNode<T> *temp = ptr;

	while (temp != NULL)
	{
		if (temp->data > a && temp->data > b)
		{
			temp = temp->leftChild;
		}
		else if (temp->data < a && temp->data < b)
		{
			temp = temp->rightChild;
		}
		else
		{
			break;
		}
	}

	return temp->getData();
}

template <class T> bool BST<T>::find(T x, BSTNode<T> *ptr)
{
	if (ptr == NULL) return false;
	else if (x < ptr->data) return find(x, ptr->leftChild);
	else if (x > ptr->data) return find(x, ptr->rightChild);
	else return true;
}

template <class T> bool BST<T>::find_iter(T x, BSTNode<T> *ptr)
{
	if (ptr != NULL)
	{
		BSTNode<T> *temp = ptr;

		while (temp != NULL)
		{
			if (temp->data == x) return true;
			else if (temp->data > x) temp = temp->leftChild;
			else temp = temp->rightChild;
		}
	}

	return false;
}

int main()
{
	cout << "Hello world!" << endl;

	BST<int> *b = new BST<int>();
	b->insert(20);
	b->insert(8);
	b->insert(22);
	b->insert(4);
	b->insert(12);
	b->insert(10);
	b->insert(14);

	b->traverse();

	cout << endl;

	int anc = b->lca(14, 4);

	cout << anc << endl;


	/*BSTNode<int> *node = new BSTNode<int>(1);
	b->insert(node);*/

	getchar();
	return 0;
}

```

Below is c code for reference:
```c
#include <stdio.h>
#include <stdlib.h>

struct node
{
	int data;
	struct node *left, *right;
};

struct node* newNode(int data)
{
	struct node* node = (struct node*)malloc(sizeof(struct node));
	node->data = data;
	node->left = node->right = NULL;
	return node;
}

/*
function to find LCA of n1 and n2. The function assumes that both n1
and n2 are present
*/
struct node* lca(struct node* root, int n1, int n2)
{
	if (root == NULL) return NULL;

	// if both n1 and n2 are smaller that root, then LCA lies in root's left subtree
	if (root->data > n1 && root->data > n2)
	{
		return lca(root->left, n1, n2);
	}

	// if both n1 and n2 are greater than root, then LCA lies in root's right subtree
	if (root->data < n1 && root->data < n2)
	{
		return lca(root->right, n1, n2);
	}

	return root;
}

struct node* lca_iter(struct node* root, int n1, int n2)
{
	if (root == NULL) return NULL;

	while (root != NULL)
	{
		if (root->data > n1 && root->data > n2)
		{
			root = root->left;
		}
		else if (root->data < n1 && root->data < n2)
		{
			root = root->right;
		}
		else
		{
			break;
		}
	}

	return root;
}


int main()
{
	struct node *root = newNode(20);
	root->left = newNode(8);
	root->right = newNode(22);
	root->left->left = newNode(4);
	root->left->right = newNode(12);
	root->left->right->left = newNode(10);
	root->left->right->right = newNode(14);

	int n1 = 10, n2 = 14;
	struct node *t = lca_iter(root, n1, n2);
	printf("LCA of %d and %d is %d \n", n1, n2, t->data);

	n1 = 14, n2 = 8;
	t = lca_iter(root, n1, n2);
	printf("LCA of %d and %d is %d \n", n1, n2, t->data);

	n1 = 10, n2 = 22;
	t = lca_iter(root, n1, n2);
	printf("LCA of %d and %d is %d \n", n1, n2, t->data);

	getchar();

	//system("pause");
	return 0;
}

```
reference: [Lowest Common Ancestor in a Binary Search Tree.](https://www.geeksforgeeks.org/lowest-common-ancestor-in-a-binary-search-tree/)
