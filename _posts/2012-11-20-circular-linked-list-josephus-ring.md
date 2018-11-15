---
title: "Circular linked list and Josephus ring"
date: 2012-11-20
tags: [python, algorithm]
excerpt: "python, algorithm"
---

```python
class ListNode:
    def __init__(self, data):
        self.data = data
        self.next = None

class CircleList:
    def __init__(self):
        self.head = None

    # add a node to the head
    def add(self, item):
        newNode = ListNode(item)
        if self.head is None:
            newNode.next = newNode
            self.head = newNode
        else:
            newNode.next = self.head.next
            self.head.next = newNode

    def next(self):
        self.head = self.head.next

    # delete the node at the head node
    def remove(self):
        curNode = self.head.next
        if curNode == self.head:
            self.head = None
        else:
            self.head.next = curNode.next
        return curNode

    def remove_item(self, item):  
        preNode = self.head
        curNode = self.head.next
        while curNode is not self.head and curNode.data != item:
            preNode = curNode
            curNode = curNode.next

        if curNode is self.head:
            self.head = self.head.next

        preNode.next = curNode.next

        return curNode.data

    def print_node(self):
        curNode = self.head
        while curNode.next != self.head:
            print curNode.next.data
            curNode = curNode.next
        print curNode.next.data # print the head node

# n people count m
def josephus(n, m):
    # create n people list
    l = CircleList()
    for i in range(n, 0, -1):
        l.add(i)

    for i in range(n - 1):  
        for j in range(m - 1):
            l.next()
        print 'The ',i, ' round deleted person is: ', l.remove().data

    print 'The winner is ', l.head.data
    #l.print_node()


def main():
    josephus(10, 4)


if __name__ == '__main__':
    main()

```
