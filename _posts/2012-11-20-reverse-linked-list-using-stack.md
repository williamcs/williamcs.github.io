---
title: "Reverse linked list using stack and stack's implementation"
date: 2012-11-20
tags: [python, algorithm]
excerpt: "python, algorithm"
---

```python
class ListStack:
    # create an empty stack
    def __init__(self):
        self.elems = list()

    def isEmpty(self):
        return len(self) == 0

    # return the number in the stack
    def __len__(self):
        return len(self.elems)

    # return the top item of the stack
    def peek(self):
        assert not self.isEmpty(), "Can't peek at an empty stack"
        return self.elems[-1]

    def pop(self):
        assert not self.isEmpty(), "Can't pop from an empty stack"
        return self.elems.pop()

    def push(self, item):
        self.elems.append(item)

class LinkStack:
    # create an empty stack
    def __init__(self):
        self.top = None
        self.size = 0

    # return is the stack is empty
    def isEmpty(self):
        return self.top is None

    def peek(self):
        assert not self.isEmpty(), "Can't peek at an empty stack"
        return self.top.data

    def pop(self):
        assert not self.isEmpty(), "Can't peek at an empty stack"
        node = self.top
        self.top = self.top.next
        self.size -= 1
        return node

    def push(self, item):
        node = Node(item)
        node.next = self.top
        self.top = node

    def push_node(self, node):
        node.next = self.top
        self.top = node

#    def push(self, item):
#        self.top = StackNode(item, self.top)
#        self.size += 1
#
## stack node class
#class StackNode:
#    def __init__(self, item, link):
#        self.data = item
#        self.next = link

class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    def isEmpty(self):
        return self.head is None

    # add item to the head
    def add(self, item):
        newNode = Node(item)
        newNode.next = self.head
        self.head = newNode

    # add a node to the head
    def add_node(self, node):
        node.next = self.head
        self.head = node

    # remove head node at the head
    def remove_head(self):
        if self.head is not None:
            node = self.head
            self.head = self.head.next
            return node

    # remove node at the end    
    def remove_end(self):
        if self.head is not None:
            preNode = None
            curNode = self.head
            while curNode.next is not None:
                preNode = curNode
                curNode = curNode.next

            if curNode is self.head:
                self.head = None
            else:
                preNode.next = None
            return curNode


    # remove item from the list
    def remove(self, item):
        preNode = None
        curNode = self.head
        while curNode is not None and curNode.data != item:
            preNode = curNode
            curNode = curNode.next

        if curNode is self.head:
            self.head = curNode.next
        else:
            preNode.next = curNode.next
        return curNode.data

    # print node
    def print_node(self):
        curNode = self.head
        while curNode is not None:
            print curNode.data, ' '
            curNode = curNode.next

# reverse a linked list use stack
def reverse(L):
    s = LinkStack()

    while not L.isEmpty():
        s.push_node(L.remove_end())

    while not s.isEmpty():
        L.add_node(s.pop())

    return L


def main():
    l =  LinkedList()
    for i in range(10, 0, -1):
        l.add(i)   
    l.print_node()

    l = reverse(l)
    l.print_node()

if __name__ == '__main__':
    main()


```
