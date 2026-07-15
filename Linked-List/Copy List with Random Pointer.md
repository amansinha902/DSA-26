# Copy Linked List with Next and Random Pointer

## Problem

Each node contains:

- `val`
- `next`
- `random`

```java
class Node {
    int val;
    Node next;
    Node random;
}
```

The `random` pointer can point to:

- Any node in the list
- Or `null`

We need to create a **deep copy** of the entire list.

A deep copy means:

- Create brand new nodes.
- Copy both `next` and `random` pointers.
- None of the copied nodes should point to the original list.

---

# Example

Original List

```text
          Random
            |
            v
7 ------> 13 ------> 11 ------> 10 ------> 1
|          |            |          |        |
|          |            |          |        |
NULL       7            1         11        7
```

Copied List

```text
          Random
            |
            v
7' -----> 13' -----> 11' -----> 10' -----> 1'
|           |             |           |       |
|           |             |           |       |
NULL        7'           1'         11'      7'
```

Notice

Every pointer refers to the **new list**, not the old one.

---

# Approach 1 : HashMap (Easy to Understand)

## Intuition

We'll maintain a mapping:

```
Original Node

↓

Copied Node
```

Example

```
Original

7  →  Copy7

13 → Copy13

11 → Copy11
```

Once we know every copy,

connecting `next` and `random` becomes easy.

---

# Step 1

Create a copy of every node.

Store mapping.

```
Original

7

↓

Copy

7'
```

HashMap

```
7  -> 7'

13 ->13'

11 ->11'

10 ->10'

1  ->1'
```

At this point,

Only nodes exist.

Pointers are still empty.

---

# Step 2

Traverse again.

Now connect

```
next

and

random
```

using the HashMap.

Example

Original

```
13

next →11

random →7
```

Copied

```
13'

next → map.get(11)

random → map.get(7)
```

Done.

---

# Dry Run

Original

```
7 ->13 ->11
```

HashMap

```
7 →7'

13→13'

11→11'
```

Now

Original

```
13.random =7
```

Copied

```
13'.random =7'
```

Original

```
13.next =11
```

Copied

```
13'.next =11'
```

Finished.

---

# Code

```java
public Node copyRandomList(Node head) {

    if (head == null) {
        return null;
    }

    HashMap<Node, Node> mp = new HashMap<>();

    Node curr = head;
    Node prev = null;
    Node newHead = null;

    // First Pass: Create copied nodes
    while (curr != null) {

        Node temp = new Node(curr.val);

        mp.put(curr, temp);

        if (newHead == null) {
            newHead = temp;
            prev = temp;
        } else {
            prev.next = temp;
            prev = temp;
        }

        curr = curr.next;
    }

    // Second Pass: Assign random pointers
    curr = head;
    Node newCurr = newHead;

    while (curr != null) {

        if (curr.random == null) {
            newCurr.random = null;
        } else {
            newCurr.random = mp.get(curr.random);
        }

        curr = curr.next;
        newCurr = newCurr.next;
    }

    return newHead;
}
```

---

# Complexity

Time

```
O(n)
```

Space

```
O(n)
```

(HashMap)

---

# Approach 2 : Optimal (No Extra HashMap)

This is one of the most beautiful Linked List algorithms.

Uses **3 passes**.

Space

```
O(1)
```

(extra space)

---

# Intuition

Instead of storing mapping in HashMap,

store every copied node **next to its original node**.

---

## Step 1

Original

```
1 ->2 ->3
```

Insert copied nodes.

```
1 ->1' ->2 ->2' ->3 ->3'
```

Notice

Every copied node is immediately after its original.

---

# Step 2

Connect Random Pointer

Suppose

```
Original

1.random =3
```

Since

```
3'
```

is immediately after `3`,

```
3.next =3'
```

Therefore

```
1'.random

=

1.random.next
```

Very important formula

```java
copy.random = original.random.next;
```

---

Example

```
1.random =3
```

Then

```
1'.random

↓

3'
```

Done.

---

# Step 3

Separate Both Lists

Current

```
1 ->1' ->2 ->2' ->3 ->3'
```

Separate into

Original

```
1 ->2 ->3
```

Copied

```
1' ->2' ->3'
```

Done.

---

# Dry Run

Original

```
1 ->2 ->3
```

---

Pass 1

```
1 ->1' ->2 ->2' ->3 ->3'
```

---

Pass 2

Connect random.

---

Pass 3

Separate

Original

```
1 ->2 ->3
```

Copied

```
1' ->2' ->3'
```

---

# Code

```java
public Node copyRandomList(Node head) {

    if (head == null)
        return null;

    Node curr = head;

    // Pass 1: Insert copied nodes
    while (curr != null) {

        Node copy = new Node(curr.val);

        copy.next = curr.next;

        curr.next = copy;

        curr = copy.next;
    }

    // Pass 2: Copy random pointers
    curr = head;

    while (curr != null) {

        if (curr.random != null)
            curr.next.random = curr.random.next;

        curr = curr.next.next;
    }

    // Pass 3: Separate the lists
    curr = head;

    Node dummy = new Node(0);
    Node copyCurr = dummy;

    while (curr != null) {

        copyCurr.next = curr.next;

        curr.next = curr.next.next;

        copyCurr = copyCurr.next;

        curr = curr.next;
    }

    return dummy.next;
}
```

---

# Why Does

```java
curr.next.random = curr.random.next;
```

Work?

Suppose

```
Original

1.random →3
```

After Pass 1

```
1 ->1'

3 ->3'
```

Since

```
3'

=

3.next
```

we can simply write

```java
1'.random =3.next;
```

which is

```java
curr.next.random = curr.random.next;
```

No HashMap required!

---

# Complexity Comparison

| Approach | Time | Space | Interview Preference |
|----------|------|--------|----------------------|
| HashMap | O(n) | O(n) | Easy |
| Interweaving Nodes (3 Passes) | O(n) | O(1) | ⭐ Best |

---

# Interview Tips

### HashMap

- First pass: Create copies.
- Second pass: Connect `next` and `random`.
- Easy to understand.
- Uses extra memory.

---

### Optimal

Three passes:

1. Insert copied nodes after originals.
2. Set random pointers using:
   ```java
   copy.random = original.random.next;
   ```
3. Separate the original and copied lists.

This avoids using a `HashMap` while still running in **O(n)** time.

---

# Memory Trick

Think of each original node getting a **twin**.

Initially:

```
A -> B -> C
```

Create twins:

```
A -> A' -> B -> B' -> C -> C'
```

Because each twin sits right after its original, finding the twin of any node is easy:

```
Twin = original.next
```

After setting the random pointers, simply unzip the combined list into:

- Original list
- Copied list