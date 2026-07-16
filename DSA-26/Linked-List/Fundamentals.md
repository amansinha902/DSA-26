# Linked List in Java

## What is a Linked List?

A **Linked List** is a collection of elements (called **nodes**) where each node contains:

- The actual data
- A reference (link) to the next node

### Example

```text
+---------+      +---------+      +---------+
| Data:10 | ---> | Data:20 | ---> | Data:30 | ---> null
+---------+      +---------+      +---------+
```

### Explanation

- The first node stores `10`.
- It knows where the second node is.
- The second node knows where the third node is.
- The last node points to `null`, meaning there are no more nodes.

---

# Basic Structure of a Node

```java
class Node {
    String data;
    Node next;

    Node(String data) {
        this.data = data;
        this.next = null;
    }
}
```

### Explanation

- `data` stores the value.
- `next` stores the reference to the next node.
- Initially, `next` is set to `null`.

---

# Adding an Element at the Beginning

```java
public void addFirst(String data) {
    Node newNode = new Node(data);

    if (head == null) {
        head = newNode;
        return;
    }

    newNode.next = head;
    head = newNode;
}
```

### How it Works

Suppose the list is:

```text
Head
 |
 v
10 -> 20 -> 30 -> null
```

Add `5` at the beginning.

Create a new node:

```text
5
```

Point it to the current head:

```text
5 ----> 10 -> 20 -> 30
```

Move the head to the new node:

```text
Head
 |
 v
5 -> 10 -> 20 -> 30 -> null
```

**Time Complexity:** `O(1)`

---

# Adding an Element at the End

```java
public void addLast(String data) {
    Node newNode = new Node(data);

    if (head == null) {
        head = newNode;
        return;
    }

    Node currNode = head;

    while (currNode.next != null) {
        currNode = currNode.next;
    }

    currNode.next = newNode;
}
```

### How it Works

Current list:

```text
10 -> 20 -> 30 -> null
```

Traverse until the last node:

```text
currNode = 30
```

Attach the new node:

```text
10 -> 20 -> 30 -> 40 -> null
```

**Time Complexity:** `O(n)`

---

# Printing the Linked List

```java
public void printLL() {

    if (head == null) {
        System.out.println("List is empty");
        return;
    }

    Node currNode = head;

    while (currNode != null) {
        System.out.print(currNode.data + " -> ");
        currNode = currNode.next;
    }

    System.out.println("NULL");
}
```

### Output

```text
10 -> 20 -> 30 -> NULL
```

### How it Works

Start from `head` and keep moving to the next node until reaching `null`.

**Time Complexity:** `O(n)`

---

# Delete a Node at Any Position

```java
public void deleteAtPosition(int pos) {

    if (head == null) {
        return;
    }

    // Delete the first node
    if (pos == 0) {
        head = head.next;
        return;
    }

    Node currNode = head;

    // Move to the previous node
    for (int i = 0; i < pos - 1; i++) {
        currNode = currNode.next;
    }

    // Skip the node to be deleted
    currNode.next = currNode.next.next;
}
```

### Example

Initial list:

```text
Position

0      1      2      3

10 -> 20 -> 30 -> 40 -> null
```

Delete node at position `2`.

Move to the previous node (`20`):

```text
10 -> 20 -> 30 -> 40
      ^
  currNode
```

Update the link:

```java
currNode.next = currNode.next.next;
```

Result:

```text
10 -> 20 -> 40 -> null
```

The node containing `30` is no longer part of the list.

**Time Complexity:** `O(n)`

---

# Time Complexities

| Operation | Time Complexity |
|-----------|-----------------|
| Add at Beginning | `O(1)` |
| Add at End | `O(n)` |
| Print List | `O(n)` |
| Delete at Position | `O(n)` |

---

# Key Takeaways

- A Linked List is made up of **nodes**.
- Every node stores:
  - Data
  - Reference to the next node
- `head` always points to the first node.
- The last node always points to `null`.
- Adding at the beginning is very fast (`O(1)`).
- Adding at the end requires traversal (`O(n)`).
- Deleting a node requires updating the previous node's `next` reference to skip the node being removed.