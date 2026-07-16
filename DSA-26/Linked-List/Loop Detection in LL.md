# Detect a Loop (Cycle) in a Linked List

## Problem

Given the head of a linked list, determine whether the linked list contains a **cycle (loop)**.

### Example 1 (No Loop)

```text
Head
 |
 v
10 -> 20 -> 30 -> 40 -> NULL
```

Output:

```text
false
```

---

### Example 2 (Loop Exists)

```text
         +----------------+
         |                |
         v                |
10 -> 20 -> 30 -> 40 -----+
```

Output:

```text
true
```

---

# What is a Loop?

Normally, the last node points to `NULL`.

```text
10 -> 20 -> 30 -> NULL
```

A loop occurs when the last node points back to an earlier node.

Example:

```text
10 -> 20 -> 30 -> 40
      ^             |
      |_____________|
```

Now, if you keep traversing, you'll never reach `NULL`.

```
10 → 20 → 30 → 40
      ↑         ↓
      ←←←←←←←←←
```

This creates an infinite loop.

---

# Brute Force Approach

Store every visited node in a `HashSet`.

While traversing:

- If the node is already present in the HashSet → Loop found.
- Otherwise, add it to the HashSet.

### Code

```java
public boolean hasCycle(ListNode head) {

    HashSet<ListNode> visited = new HashSet<>();

    while (head != null) {

        if (visited.contains(head))
            return true;

        visited.add(head);

        head = head.next;
    }

    return false;
}
```

### Complexity

- **Time:** `O(n)`
- **Space:** `O(n)`

---

# Optimal Approach (Floyd's Cycle Detection)

Also called:

- Fast & Slow Pointer
- Tortoise and Hare Algorithm

---

# Intuition

Imagine two people running on a circular track.

- One walks.
- One runs twice as fast.

If there is a circle, eventually the faster person catches the slower one.

Exactly the same idea works for Linked Lists.

---

# Two Pointers

```java
ListNode slow = head;
ListNode fast = head;
```

Move:

```java
slow = slow.next;
```

One step.

Move:

```java
fast = fast.next.next;
```

Two steps.

Repeat until:

- `fast == null`
- `fast.next == null`
- OR `slow == fast`

---

# Dry Run (No Loop)

```text
10 -> 20 -> 30 -> 40 -> NULL
```

Initially

```text
S,F

10 -> 20 -> 30 -> 40
```

---

Iteration 1

Slow moves one step.

Fast moves two steps.

```text
10 -> 20 -> 30 -> 40

      S

           F
```

---

Iteration 2

```text
10 -> 20 -> 30 -> 40

           S

Fast becomes NULL
```

No loop.

---

# Dry Run (Loop Exists)

```text
         +-----------+
         |           |
         v           |
10 -> 20 -> 30 -> 40
```

Initially

```text
S,F

10
```

---

Iteration 1

```text
Slow = 20

Fast = 30
```

---

Iteration 2

```text
Slow = 30

Fast = 20
```

---

Iteration 3

```text
Slow = 40

Fast = 40
```

Both pointers meet.

Loop detected.

---

# Why Does This Work?

Suppose

```
Slow speed = 1 step

Fast speed = 2 steps
```

Every iteration,

Fast gains **one extra step** over Slow.

If there is a loop,

Fast keeps gaining until both meet.

Exactly like runners on a circular track.

---

# Code

```java
public boolean hasCycle(ListNode head) {

    if (head == null)
        return false;

    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {

        slow = slow.next;

        fast = fast.next.next;

        if (slow == fast)
            return true;
    }

    return false;
}
```

---

# Understanding Every Line

## Initialize

```java
ListNode slow = head;
ListNode fast = head;
```

Both pointers start from the first node.

---

## Loop Condition

```java
while (fast != null && fast.next != null)
```

Why?

Because Fast moves **2 steps**.

We must ensure both nodes exist.

Otherwise,

```java
fast.next.next
```

will throw a `NullPointerException`.

---

## Move Slow

```java
slow = slow.next;
```

Moves one node.

---

## Move Fast

```java
fast = fast.next.next;
```

Moves two nodes.

---

## Check Meeting

```java
if (slow == fast)
```

If both pointers refer to the **same node**,

there is definitely a cycle.

---

# Time Complexity

Every node is visited at most once.

**Time:** `O(n)`

---

# Space Complexity

Only two pointers are used.

**Space:** `O(1)`

---

# Visual Memory Trick

Imagine a circular running track.

```
        Fast →
      __________
     /          \
Slow             |
     \__________/
```

- Slow runs one step.
- Fast runs two steps.

If there is a circle,

Fast eventually catches Slow.

If there is no circle,

Fast reaches the finish line (`NULL`).

---

# Interview Tip

There are two common ways to detect a loop:

| Approach | Time | Space |
|----------|------|-------|
| HashSet | O(n) | O(n) |
| Floyd's Cycle Detection (Fast & Slow Pointer) | O(n) | O(1) ✅ |

**Preferred Answer in Interviews:** Floyd's Cycle Detection because it uses constant extra space.