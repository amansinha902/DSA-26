# Reorder Linked List

## Problem

Given a singly linked list:

```text
L0 → L1 → L2 → ... → Ln
```

Reorder it into:

```text
L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → ...
```

You **cannot** modify the node values.

You must rearrange the nodes themselves.

---

## Example 1

### Input

```text
1 → 2 → 3 → 4
```

### Output

```text
1 → 4 → 2 → 3
```

---

## Example 2

### Input

```text
1 → 2 → 3 → 4 → 5
```

### Output

```text
1 → 5 → 2 → 4 → 3
```

---

# Intuition

The solution consists of **3 simple steps**.

1. Find the middle of the linked list.
2. Reverse the second half.
3. Merge both halves alternately.

Think of the list as:

```text
1 → 2 → 3 → 4 → 5
```

Split into

```text
First Half

1 → 2 → 3

Second Half

4 → 5
```

Reverse the second half

```text
5 → 4
```

Merge alternately

```text
1 → 5 → 2 → 4 → 3
```

Done.

---

# Step 1 : Find the Middle

Use the Fast & Slow Pointer approach.

```text
1 → 2 → 3 → 4 → 5

S,F
```

Move

- Slow → 1 step
- Fast → 2 steps

---

Iteration 1

```text
1 → 2 → 3 → 4 → 5

    S

        F
```

---

Iteration 2

```text
1 → 2 → 3 → 4 → 5

        S

                F
```

Fast reaches the end.

Middle is

```
3
```

---

# Step 2 : Reverse Second Half

Current

```text
1 → 2 → 3 → 4 → 5

          ↑

       Middle
```

Second half

```text
4 → 5
```

Reverse

```text
5 → 4
```

Now we have

First

```text
1 → 2 → 3
```

Second

```text
5 → 4
```

---

# Step 3 : Merge Alternately

Current

```text
First

1 → 2 → 3

Second

5 → 4
```

Merge

Take

```
1
```

Then

```
5
```

Then

```
2
```

Then

```
4
```

Then

```
3
```

Final

```text
1 → 5 → 2 → 4 → 3
```

---

# Dry Run

Input

```text
1 → 2 → 3 → 4 → 5
```

---

## Find Middle

```
Middle = 3
```

---

## Reverse

Before

```text
4 → 5
```

After

```text
5 → 4
```

---

## Merge

Initially

```text
First

1 → 2 → 3

Second

5 → 4
```

Iteration 1

```text
1 → 5

Remaining

2 → 3

4
```

Iteration 2

```text
1 → 5 → 2 → 4

Remaining

3
```

Attach last node

```text
1 → 5 → 2 → 4 → 3
```

Done.

---

# Code

```java
class Solution {

    public void reorderList(ListNode head) {

        if (head == null || head.next == null)
            return;

        // Step 1: Find middle
        ListNode slow = head;
        ListNode fast = head;

        while (fast.next != null && fast.next.next != null) {

            slow = slow.next;
            fast = fast.next.next;
        }

        // Step 2: Reverse second half
        ListNode second = reverse(slow.next);

        slow.next = null;

        // Step 3: Merge both halves
        ListNode first = head;

        while (second != null) {

            ListNode temp1 = first.next;
            ListNode temp2 = second.next;

            first.next = second;
            second.next = temp1;

            first = temp1;
            second = temp2;
        }
    }

    private ListNode reverse(ListNode head) {

        ListNode prev = null;

        while (head != null) {

            ListNode next = head.next;

            head.next = prev;

            prev = head;

            head = next;
        }

        return prev;
    }
}
```

---

# Understanding Every Line

## Find Middle

```java
while(fast.next != null && fast.next.next != null)
```

Move

```java
slow = slow.next;
```

1 step.

Move

```java
fast = fast.next.next;
```

2 steps.

Slow reaches the middle.

---

## Reverse

```java
ListNode second = reverse(slow.next);
```

Reverse only the second half.

Then disconnect

```java
slow.next = null;
```

Now both halves become independent.

---

## Merge

Store

```java
temp1 = first.next;
```

Store

```java
temp2 = second.next;
```

Attach

```java
first.next = second;
```

Then

```java
second.next = temp1;
```

Move both pointers.

Repeat.

---

# Why Do We Disconnect?

```java
slow.next = null;
```

Without this,

the original first half still points to the second half.

While merging,

this can create cycles.

Always separate both halves first.

---

# Time Complexity

Finding middle

```
O(n)
```

Reversing

```
O(n)
```

Merging

```
O(n)
```

Total

```
O(n)
```

---

# Space Complexity

```
O(1)
```

Only pointers are used.

---

# Interview Tips

This problem is a combination of **three classic linked list problems**:

1. Find the middle using Fast & Slow pointers.
2. Reverse the second half of the list.
3. Merge two linked lists alternately.

If you already know these three problems individually, this problem becomes straightforward.

---

# Memory Trick

Imagine a queue of people.

```
1 2 3 4 5
```

Split the queue.

```
1 2 3

4 5
```

Turn the second queue around.

```
5 4
```

Now interleave them.

```
1 5 2 4 3
```

That's exactly what the algorithm does.