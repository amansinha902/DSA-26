# Reverse a Linked List Using Iteration

## Problem

Given:

```text
Head
 |
 v
10 -> 20 -> 30 -> 40 -> NULL
```

Reverse it to:

```text
Head
 |
 v
40 -> 30 -> 20 -> 10 -> NULL
```

---

# Intuition

Every node points **forward**.

```text
10 ----> 20 ----> 30 ----> 40
```

We need to make every node point **backward**.

```text
10 <---- 20 <---- 30 <---- 40
```

The challenge is:

Suppose you're at node `20`.

If you immediately change:

```text
20 -> 10
```

you lose the connection to `30`.

So before changing the pointer, we must save the next node.

That's why we use **three pointers**.

---

# Three Pointers

```java
Node prev = null;
Node curr = head;
Node next = null;
```

### What each pointer does

- **prev** → Previous node
- **curr** → Current node
- **next** → Saves the next node before changing links

Initially

```text
prev      curr
 |         |
null      10 -> 20 -> 30 -> 40
```

---

# Algorithm

Repeat until `curr` becomes `null`.

### Step 1: Save the next node

```java
next = curr.next;
```

Before changing anything, remember where to go next.

```
curr = 10

next = 20
```

---

### Step 2: Reverse the pointer

```java
curr.next = prev;
```

Instead of pointing to `20`,

`10` now points to `null`.

```text
10 -> NULL

20 -> 30 -> 40
```

---

### Step 3: Move `prev` forward

```java
prev = curr;
```

Now

```text
prev
 |
10 -> NULL

curr
 |
10
```

---

### Step 4: Move `curr` forward

```java
curr = next;
```

Now

```text
prev
 |
10 -> NULL

curr
 |
20 -> 30 -> 40
```

Repeat the same process.

---

# Complete Dry Run

## Initial State

```text
prev = NULL

curr
 |
10 -> 20 -> 30 -> 40 -> NULL
```

---

## Iteration 1

Save next

```text
next = 20
```

Reverse

```text
10 -> NULL
```

Move pointers

```text
prev = 10
curr = 20
```

Current state

```text
NULL <- 10

20 -> 30 -> 40
```

---

## Iteration 2

Save next

```text
next = 30
```

Reverse

```text
20 -> 10
```

Move pointers

```text
prev = 20
curr = 30
```

State

```text
NULL <- 10 <- 20

30 -> 40
```

---

## Iteration 3

Save next

```text
next = 40
```

Reverse

```text
30 -> 20
```

Move pointers

```text
prev = 30
curr = 40
```

State

```text
NULL <- 10 <- 20 <- 30

40
```

---

## Iteration 4

Save next

```text
next = NULL
```

Reverse

```text
40 -> 30
```

Move pointers

```text
prev = 40
curr = NULL
```

State

```text
NULL <- 10 <- 20 <- 30 <- 40
```

Since `curr == NULL`, stop.

The new head is `prev`.

---

# Code

```java
public Node reverse(Node head) {

    Node prev = null;
    Node curr = head;
    Node next = null;

    while (curr != null) {

        // Save next node
        next = curr.next;

        // Reverse current pointer
        curr.next = prev;

        // Move pointers
        prev = curr;
        curr = next;
    }

    return prev;
}
```

---

# Understanding Every Line

```java
next = curr.next;
```

Save the next node.

Without this, you'll lose the rest of the list.

---

```java
curr.next = prev;
```

Reverse the direction.

Example:

Before

```text
10 -> 20
```

After

```text
10 -> NULL
```

---

```java
prev = curr;
```

Move `prev` to the current node.

---

```java
curr = next;
```

Continue to the next node.

---

# Why Do We Need `next`?

Suppose we have

```text
10 -> 20 -> 30
```

If we immediately do

```java
curr.next = prev;
```

it becomes

```text
10 -> NULL
```

Now you've lost the only reference to `20`.

The remaining list is gone.

That's why we first save it:

```java
next = curr.next;
```

---

# Time Complexity

- **Time:** `O(n)`
- **Space:** `O(1)`

Unlike recursion, iteration uses **constant extra space** because it doesn't use the call stack.

---

# Memory Trick

Imagine you're walking through a line of people:

```text
10 -> 20 -> 30 -> 40
```

For every person:

1. Remember who is standing ahead (`next`).
2. Turn them around to face the previous person (`curr.next = prev`).
3. Move one step forward.

Keep repeating until you reach the end.

Eventually, everyone is facing backward:

```text
40 -> 30 -> 20 -> 10 -> NULL
```