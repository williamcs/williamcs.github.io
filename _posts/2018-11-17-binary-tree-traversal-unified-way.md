---
title: "Binary tree traversal in an unified way"
date: 2018-11-17
tags: [java, scala, algorithm]
excerpt: "java, scala, algorithm"
---

This is a very very common question, but there has an unified way to do the
binary tree traversal.

Java implementation:
```java
package interview.others;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int x) {
        this.val = x;
    }
}

public class BinaryTreeTraversal {

    private class Guide {
        // 0: visit node; 1: print node;
        int type;
        TreeNode node;

        public Guide(int type, TreeNode node) {
            this.type = type;
            this.node = node;
        }
    }

    public List preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        Stack<Guide> stack = new Stack();

        stack.push(new Guide(0, root));

        while (!stack.isEmpty()) {
            Guide current = stack.pop();

            if (current.node == null) continue;

            if (current.type == 1) {
                result.add(current.node.val);
            } else {
                stack.push(new Guide(0, current.node.right));
                stack.push(new Guide(0, current.node.left));
                stack.push(new Guide(1, current.node));
            }
        }

        return result;
    }

    public List inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        Stack<Guide> stack = new Stack<>();

        stack.push(new Guide(0, root));

        while (!stack.isEmpty()) {
            Guide current = stack.pop();

            if (current.node == null) continue;

            if (current.type == 1) {
                result.add(current.node.val);
            } else {
                stack.push(new Guide(0, current.node.right));
                stack.push(new Guide(1, current.node));
                stack.push(new Guide(0, current.node.left));
            }
        }

        return result;
    }

    public List postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        Stack<Guide> stack = new Stack<>();

        stack.push(new Guide(0, root));

        while (!stack.isEmpty()) {
            Guide current = stack.pop();

            if (current.node == null) continue;

            if (current.type == 1) {
                result.add(current.node.val);
            } else {
                stack.push(new Guide(1, current.node));
                stack.push(new Guide(0, current.node.right));
                stack.push(new Guide(0, current.node.left));
            }
        }

        return result;
    }

    public void printResult(List<Integer> result) {
        for (Integer ele: result) {
            System.out.print(" " + ele);
        }
        System.out.println();
    }

    public static void main(String[] args) {
        BinaryTreeTraversal solution = new BinaryTreeTraversal();

        TreeNode node0 = new TreeNode(2);
        TreeNode node1 = new TreeNode(1);
        TreeNode node2 = new TreeNode(3);

        node0.left = node1;
        node0.right = node2;

        List<Integer> preResult = solution.preorderTraversal(node0);
        solution.printResult(preResult);

        List<Integer> inResult = solution.inorderTraversal(node0);
        solution.printResult(inResult);

        List<Integer> posResult = solution.postorderTraversal(node0);
        solution.printResult(posResult);
    }
}

```

Scala implementation:
```scala
package fp.algorithm.scalgos.binarytree

import scala.collection.mutable.ListBuffer

class TreeNode(var _value: Int) {
  var value: Int = _value
  var left: TreeNode = null
  var right: TreeNode = null
}

object BinaryTreeTraversal {
  // guideType 0: visit node; guideType 1: print node;
  case class Guide(guideType: Int, node: TreeNode)

  def preorderTraversal(root: TreeNode): List[Int] = {
    val result: ListBuffer[Int] = new ListBuffer[Int]()
    val stack = scala.collection.mutable.Stack[Guide]()

    stack.push(new Guide(0, root))

    while (stack.nonEmpty) {
      val current = stack.pop()

      if (current.node != null) {
        if (current.guideType == 1) {
          result.append(current.node.value)
        } else {
          stack.push(new Guide(0, current.node.right))
          stack.push(new Guide(0, current.node.left))
          stack.push(new Guide(1, current.node))
        }
      }
    }

    result.toList
  }

  def inorderTraversal(root: TreeNode): List[Int] = {
    val result = new ListBuffer[Int]()
    val stack = scala.collection.mutable.Stack[Guide]()

    stack.push(new Guide(0, root))

    while (stack.nonEmpty) {
      val current = stack.pop()

      if (current.node != null) {
        if (current.guideType == 1) {
          result.append(current.node.value)
        } else {
          stack.push(new Guide(0, current.node.right))
          stack.push(new Guide(1, current.node))
          stack.push(new Guide(0, current.node.left))
        }
      }
    }

    result.toList
  }

  def postorderTraversal(root: TreeNode): List[Int] = {
    val result = ListBuffer[Int]()
    val stack = scala.collection.mutable.Stack[Guide]()

    stack.push(new Guide(0, root))

    while (stack.nonEmpty) {
      val current = stack.pop()

      if (current.node != null) {
        if (current.guideType == 1) {
          result.append(current.node.value)
        } else {
          stack.push(new Guide(1, current.node))
          stack.push(new Guide(0, current.node.right))
          stack.push(new Guide(0, current.node.left))
        }
      }
    }

    result.toList
  }

  def printResult(list: List[Int]): Unit = {
    println(list.mkString(" "))
  }

  def main(args: Array[String]): Unit = {
    val node0 = new TreeNode(2)
    val node1 = new TreeNode(1)
    val node2 = new TreeNode(3)

    node0.left = node1
    node0.right = node2

    val preResult = preorderTraversal(node0)
    printResult(preResult)

    val inResult = inorderTraversal(node0)
    printResult(inResult)

    val posResult = postorderTraversal(node0)
    printResult(posResult)
  }
}

```
