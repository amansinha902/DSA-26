# Reverse a Linked List Using Recursion

## Problem

Given a linked list:

```text
Head
 |
 v
10 -> 20 -> 30 -> 40 -> NULL
```

Reverse it so that it becomes:

```text
Head
 |
 v
40 -> 30 -> 20 -> 10 -> NULL
```

---

# Intuition

With recursion, we **don't reverse the list while going forward**.

Instead:

1. Go all the way to the last node.
2. While returning from recursion, reverse each link.

Think of recursion like climbing down a staircase and then climbing back up.

- Going down → Reach the last node.
- Coming back up → Reverse the links.

---

# Step-by-Step Example

Initial list:

```text
10 -> 20 -> 30 -> 40 -> NULL
```

### Call Stack

```
reverse(10)
    |
reverse(20)
    |
reverse(30)
    |
reverse(40)
```

Now we reach the last node (`40`).

Since it is already the last node, return it.

---

## Returning Back (Actual Reversal Happens Here)

### Step 1

Current Node = `30`

```text
30 -> 40 -> NULL
```

Make `40` point back to `30`.

```java
head.next.next = head;
```

Result:

```text
40 -> 30
```

Now disconnect `30`.

```java
head.next = null;
```

Result:

```text
40 -> 30 -> NULL
```

---

### Step 2

Current Node = `20`

Before:

```text
20 -> 30
```

Make:

```text
30 -> 20
```

Disconnect `20`:

```text
40 -> 30 -> 20 -> NULL
```

---

### Step 3

Current Node = `10`

Before:

```text
10 -> 20
```

Reverse:

```text
20 -> 10
```

Disconnect `10`:

```text
40 -> 30 -> 20 -> 10 -> NULL
```

Done!

---

# Recursive Code

```java
public Node reverse(Node head) {

    // Base Case
    if (head == null || head.next == null) {
        return head;
    }

    // Reverse remaining list
    Node newHead = reverse(head.next);

    // Reverse current link
    head.next.next = head;

    // Break old link
    head.next = null;

    return newHead;
}
```

---

# Understanding Every Line

## Base Case

```java
if (head == null || head.next == null)
    return head;
```

Stop recursion when:

- List is empty
- Only one node is left

Example:

```text
40 -> NULL
```

Already reversed.

---

## Recursive Call

```java
Node newHead = reverse(head.next);
```

Suppose current node is `20`.

Instead of reversing now, ask recursion to reverse:

```text
30 -> 40
```

When recursion returns:

```text
40 -> 30
```

Now attach `20`.

---

## Reverse the Link

```java
head.next.next = head;
```

Suppose:

```text
20 -> 30
```

Here:

```
head = 20
head.next = 30
```

So,

```java
head.next.next = head;
```

means:

```java
30.next = 20;
```

Now:

```text
30 -> 20
```

---

## Break the Old Link

Without this line:

```java
head.next = null;
```

you would have:

```text
20 <----> 30
```

forming a cycle.

So we disconnect the old forward link.

After:

```text
30 -> 20 -> NULL
```

---

# Dry Run

Initial:

```text
10 -> 20 -> 30 -> 40 -> NULL
```

### Recursion Goes Down

```
reverse(10)
reverse(20)
reverse(30)
reverse(40)
```

Returns `40`.

---

### Coming Back

Reverse:

```text
40 -> 30
```

Then:

```text
40 -> 30 -> 20
```

Then:

```text
40 -> 30 -> 20 -> 10
```

Return `40` as the new head.

---

# Time Complexity

- **Time:** `O(n)`
- **Space:** `O(n)` (because of the recursion call stack)

---

# Key Interview Takeaways

- Go to the last node first.
- Reverse the links while recursion unwinds.
- `head.next.next = head` reverses the current link.
- `head.next = null` prevents cycles.
- Return the last node as the new head.

---

# Memory Trick

Imagine four people holding hands:

```text
10 -> 20 -> 30 -> 40
```

Walk to the last person (`40`).

Now while walking back:

- Tell `40` to hold `30`
- Tell `30` to hold `20`
- Tell `20` to hold `10`

Finally, ask `10` to let go.

Result:

```text
40 -> 30 -> 20 -> 10 -> NULL
```