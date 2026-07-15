# Flattening a Linked List (Next & Bottom Pointers)

## Problem

Given a linked list where:

- `next` points to the next linked list.
- `bottom` points to a sorted linked list.

Flatten the entire structure into a **single sorted linked list** using only the `bottom` pointers.

---

## Node Structure

```java
class Node {
    int data;
    Node next;
    Node bottom;
}
```

---

## Example

### Input

```text
5 ----> 10 ----> 19 ----> 28
|        |         |         |
7        20        22        35
|                  |         |
8                  50        40
|                            |
30                           45
```

Each vertical (`bottom`) list is already sorted.

---

### Output

```text
5
|
7
|
8
|
10
|
19
|
20
|
22
|
28
|
30
|
35
|
40
|
45
|
50
```

---

# Intuition

The key observation is:

- Every **bottom list is already sorted**.
- We only need to merge sorted lists.

The problem can be broken down recursively.

Think of it as:

```
Flatten(Current List)

=

Current Vertical List

+

Flatten(Remaining Lists)

↓

Merge Both
```

This reduces the problem to the classic **Merge Two Sorted Linked Lists** problem.

---

# Step 1 : Flatten the Remaining Lists

Suppose we have

```text
5 ----> 10 ----> 19 ----> 28
```

We don't solve everything immediately.

Instead we trust recursion.

```
flatten(5)

↓

flatten(10)

↓

flatten(19)

↓

flatten(28)
```

Since

```
28.next == null
```

Return the last list.

---

# Step 2 : Merge While Returning

Recursion starts returning.

First merge

```text
19 List

19
|
22
|
50
```

with

```text
28 List

28
|
35
|
40
|
45
```

Result

```text
19
|
22
|
28
|
35
|
40
|
45
|
50
```

---

Next merge

```text
10 List
```

with

```text
Merged List
```

---

Finally merge

```text
5 List
```

with

```text
Merged List
```

Done.

---

# Merge Two Sorted Linked Lists

This is exactly the same recursive merge that we use in linked lists.

### Logic

Compare the first node of both lists.

- Smaller node becomes the answer.
- Recursively merge the remaining nodes.

---

## Merge Code

```java
Node mergeTwoSortedLL(Node root, Node head2) {

    if (root == null)
        return head2;

    if (head2 == null)
        return root;

    Node result;

    if (root.data < head2.data) {

        result = root;

        result.bottom = mergeTwoSortedLL(root.bottom, head2);

    } else {

        result = head2;

        result.bottom = mergeTwoSortedLL(root, head2.bottom);
    }

    // Optional but recommended
    result.next = null;

    return result;
}
```

---

# Flatten Function

```java
Node flatten(Node root) {

    if (root == null || root.next == null)
        return root;

    Node head2 = flatten(root.next);

    return mergeTwoSortedLL(root, head2);
}
```

---

# Complete Solution

```java
class Solution {

    Node mergeTwoSortedLL(Node root, Node head2) {

        if (root == null)
            return head2;

        if (head2 == null)
            return root;

        Node result;

        if (root.data < head2.data) {

            result = root;

            result.bottom = mergeTwoSortedLL(root.bottom, head2);

        } else {

            result = head2;

            result.bottom = mergeTwoSortedLL(root, head2.bottom);
        }

        result.next = null;

        return result;
    }

    Node flatten(Node root) {

        if (root == null || root.next == null)
            return root;

        Node head2 = flatten(root.next);

        return mergeTwoSortedLL(root, head2);
    }
}
```

---

# Dry Run

### Input

```text
5 ----> 10 ----> 19 ----> 28
```

---

### Recursive Calls

```
flatten(5)

↓

flatten(10)

↓

flatten(19)

↓

flatten(28)
```

Return `28`.

---

### Merge 19 and 28

```
19
|
22
|
28
|
35
|
40
|
45
|
50
```

---

### Merge 10

```
10
|
19
|
20
|
22
|
28
|
35
|
40
|
45
|
50
```

---

### Merge 5

```
5
|
7
|
8
|
10
|
19
|
20
|
22
|
28
|
30
|
35
|
40
|
45
|
50
```

Done.

---

# Why Does Recursion Work?

Instead of flattening the whole list at once,

we reduce the problem.

```
Flatten

5 ->10 ->19 ->28
```

becomes

```
Flatten

10 ->19 ->28
```

becomes

```
Flatten

19 ->28
```

becomes

```
Flatten

28
```

The smallest problem is solved first.

Then we merge while recursion returns.

This follows the divide-and-conquer principle.

---

# Time Complexity

Let:

- **N** = Total number of nodes
- **K** = Number of vertical linked lists

### Time Complexity

```
O(N × K)
```

Why?

Each merge processes nodes again.

The merged list grows larger after every recursive return.

---

# Space Complexity

```
O(K)
```

because of the recursion stack.

---

# Common Mistakes

## ❌ Mistake 1

Incorrect recursive call

```java
result.bottom = mergeTwoSortedLL(root.bottom);
```

**Correct**

```java
result.bottom = mergeTwoSortedLL(root.bottom, head2);
```

---

## ❌ Mistake 2

Calling the wrong function

```java
mergeTwoLists(...)
```

Should be

```java
mergeTwoSortedLL(...)
```

---

## ❌ Mistake 3

Missing base case

Always check

```java
if(root == null || root.next == null)
    return root;
```

Otherwise,

```java
flatten(root.next)
```

can throw a `NullPointerException`.

---

# Interview Tips

### Approach

1. Flatten the remaining linked lists recursively.
2. Merge the current list with the flattened list.
3. Use the standard recursive merge algorithm for two sorted linked lists.

---

# Memory Trick

Imagine you have several sorted vertical chains.

```
Chain 1

↓

Chain 2

↓

Chain 3

↓

Chain 4
```

Don't try to merge all at once.

Instead:

- Flatten everything to the right.
- Merge the current chain with the already flattened result.

Keep repeating until only one sorted chain remains.