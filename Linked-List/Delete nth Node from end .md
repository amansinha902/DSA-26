# Delete Nth Node From the End of a Linked List

## Problem

Given a linked list:

```text
Head
 |
 v
1 -> 2 -> 3 -> 4 -> 5 -> NULL
```

Delete the **2nd node from the end**.

Output:

```text
1 -> 2 -> 3 -> 5 -> NULL
```

---

# Brute Force Approach

### Step 1

Traverse the linked list and count the total number of nodes.

Suppose:

```text
1 -> 2 -> 3 -> 4 -> 5
```

Count = **5**

---

### Step 2

Convert the problem into:

> Delete the **(length - n)**th node from the beginning.

Here,

```
length = 5
n = 2

Position = 5 - 2 = 3
```

Delete the node at index **3** (0-based indexing).

---

### Complexity

- **Time:** `O(2n)` (Two traversals)
- **Space:** `O(1)`

---

# Optimal Approach (Two Pointers)

Instead of counting nodes first, use **Fast** and **Slow** pointers.

---

# Intuition

Maintain a gap of **n nodes** between `fast` and `slow`.

When `fast` reaches the end, `slow` will automatically be just before the node to delete.

---

# Example

Delete **2nd node from the end**

```text
1 -> 2 -> 3 -> 4 -> 5 -> NULL
```

Initially

```text
S,F

1 -> 2 -> 3 -> 4 -> 5
```

---

## Step 1: Move Fast `n` Steps

Move `fast` two steps.

```text
1 -> 2 -> 3 -> 4 -> 5

S

        F
```

Now the gap between `slow` and `fast` is **2 nodes**.

---

## Step 2: Move Both Together

Move both one step until `fast` reaches the last node.

### Move 1

```text
1 -> 2 -> 3 -> 4 -> 5

    S

            F
```

---

### Move 2

```text
1 -> 2 -> 3 -> 4 -> 5

        S

                F
```

Now `fast` is at the last node.

`slow` is just before the node we want to delete.

---

## Step 3: Delete

Current list

```text
1 -> 2 -> 3 -> 4 -> 5
          ^
        slow
```

Delete:

```java
slow.next = slow.next.next;
```

Result

```text
1 -> 2 -> 3 -> 5
```

Done.

---

# What if We Need to Delete the Head?

Example

```text
1 -> 2 -> 3
```

Delete **3rd node from end**.

That means deleting `1`.

If we simply move `fast` three steps,

```text
fast = NULL
```

Now `slow` is still at the head.

How do we delete it?

To handle this cleanly, we introduce a **dummy node**.

---

# Dummy Node Trick

Create a node before the head.

```text
Dummy -> 1 -> 2 -> 3
```

Now even if the head must be deleted,

there is always a previous node (`Dummy`).

---

# Code

```java
public ListNode removeNthFromEnd(ListNode head, int n) {

    ListNode dummy = new ListNode(0);
    dummy.next = head;

    ListNode slow = dummy;
    ListNode fast = dummy;

    // Move fast n + 1 steps
    for (int i = 0; i <= n; i++) {
        fast = fast.next;
    }

    // Move both pointers
    while (fast != null) {
        slow = slow.next;
        fast = fast.next;
    }

    // Delete the target node
    slow.next = slow.next.next;

    return dummy.next;
}
```

---

# Why Move Fast `n + 1` Steps?

This is the most confusing part.

Suppose:

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
```

Delete the **2nd node from the end**.

Move `fast` **3 steps** (`n + 1`).

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
 S                  F
```

Now the gap is **3 nodes**.

As both pointers move together:

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
                S           F
```

When `fast` becomes `NULL`:

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
                S
```

`slow` is exactly **before** the node to delete (`4`).

So,

```java
slow.next = slow.next.next;
```

removes `4`.

---

# Dry Run

Input

```text
1 -> 2 -> 3 -> 4 -> 5
n = 2
```

### Initial

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
S,F
```

---

Move Fast 3 steps

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
S               F
```

---

Move together

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
    S               F
```

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
        S               F
```

```text
Dummy -> 1 -> 2 -> 3 -> 4 -> 5
            S               F(NULL)
```

Now delete

```java
slow.next = slow.next.next;
```

Final

```text
1 -> 2 -> 3 -> 5
```

---

# Time Complexity

- **Time:** `O(n)` (Single traversal)
- **Space:** `O(1)`

---

# Interview Tips

### Brute Force

- Count nodes.
- Delete `(length - n)`th node.

### Optimal

- Use **Fast** and **Slow** pointers.
- Keep a gap of `n + 1` nodes (using a dummy node).
- When `fast` reaches `NULL`, `slow` is just before the node to delete.
- Update:

```java
slow.next = slow.next.next;
```

This removes the target node in one traversal.