# Flatten a Multilevel Doubly Linked List (Recursive Approach)

## Solution

```java
class Solution {

    public Node flatten(Node head) {

        if (head == null)
            return head;

        Node curr = head;

        while (curr != null) {

            if (curr.child != null) {

                // Save the next node
                Node next = curr.next;

                // Flatten the child list recursively
                curr.next = flatten(curr.child);

                // Fix the previous pointer
                curr.next.prev = curr;

                // Remove the child pointer
                curr.child = null;

                // Move to the tail of the flattened child list
                while (curr.next != null) {
                    curr = curr.next;
                }

                // Attach the saved next node
                if (next != null) {
                    curr.next = next;
                    next.prev = curr;
                }
            }

            curr = curr.next;
        }

        return head;
    }
}
```

---

# Problem

Each node contains four pointers:

```java
class Node {
    int val;
    Node prev;
    Node next;
    Node child;
}
```

Unlike a normal doubly linked list, every node may have a **child** pointer.

The goal is to flatten the multilevel list into a single doubly linked list.

---

# Example

## Original List

```text
1 <-> 2 <-> 3 <-> 4

          |

          7 <-> 8
```

## Expected Output

```text
1 <-> 2 <-> 3 <-> 7 <-> 8 <-> 4
```

Notice that:

- The child list is inserted immediately after its parent.
- Every `child` pointer becomes `null`.

---

# Intuition

Whenever we encounter a node having a child:

1. Save the current node's next pointer.
2. Recursively flatten the child list.
3. Attach the flattened child list after the current node.
4. Move to the end of the child list.
5. Connect the saved next node.
6. Continue traversing.

Think of it like **Depth First Search (DFS)**.

```text
Parent
   |
 Child
   |
 Child's Child
   |
Back to Parent's Next
```

---

# Step-by-Step Dry Run

Initial List

```text
1 <-> 2 <-> 3 <-> 4

          |

          7 <-> 8
```

---

## Step 1

Current Node

```text
curr = 1
```

No child.

Move ahead.

---

## Step 2

```text
curr = 2
```

No child.

Move ahead.

---

## Step 3

```text
curr = 3
```

Child exists.

Enter

```java
if (curr.child != null)
```

---

## Step 4 : Save Next Node

```java
Node next = curr.next;
```

Current list

```text
3 <-> 4
```

Save

```text
next = 4
```

Why?

Because we are about to replace

```text
3.next
```

with the child list.

Without saving `4`, we'd lose the remaining list.

---

## Step 5 : Flatten Child List

```java
curr.next = flatten(curr.child);
```

Recursive call

```text
flatten(7)
```

returns

```text
7 <-> 8
```

Now

```text
3 -> 7 -> 8
```

---

## Step 6 : Fix Previous Pointer

```java
curr.next.prev = curr;
```

Since

```text
3.next = 7
```

We also need

```text
7.prev = 3
```

Now

```text
3 <-> 7
```

---

## Step 7 : Remove Child Pointer

```java
curr.child = null;
```

Now

```text
3.child -> NULL
```

because the child list has become part of the main list.

---

## Step 8 : Move to the Tail

```java
while (curr.next != null) {
    curr = curr.next;
}
```

Current list

```text
3 -> 7 -> 8
```

Move until

```text
curr = 8
```

Why?

Because we need to attach the saved node (`4`) after the child list.

---

## Step 9 : Connect Saved Next

```java
curr.next = next;
```

Currently

```text
curr = 8

next = 4
```

Result

```text
8 -> 4
```

Now fix the previous pointer.

```java
next.prev = curr;
```

Result

```text
8 <-> 4
```

Entire list becomes

```text
1 <-> 2 <-> 3 <-> 7 <-> 8 <-> 4
```

---

## Step 10 : Continue Traversal

```java
curr = curr.next;
```

Now

```text
curr = 4
```

Continue until the list ends.

---

# Why Do We Traverse Until the Tail?

This loop is very important.

```java
while (curr.next != null) {
    curr = curr.next;
}
```

Why?

Because

```java
flatten(curr.child)
```

returns the **head** of the flattened child list.

Example

```text
Child List

7 -> 8 -> 9
```

After flattening, we only know

```text
7
```

But to reconnect the remaining list

```text
4
```

we need the **tail**

```text
9
```

So we traverse until the last node.

---

# Nested Child Example

```text
1 <-> 2

      |

      3 <-> 4

           |

           5
```

Recursion works like this:

```
flatten(2)

↓

flatten(3)

↓

flatten(5)

↓

Return

↓

Attach 5

↓

Attach 4

↓

Attach 2
```

The deepest child is always processed first.

This is exactly how **Depth First Search (DFS)** works.

---

# Time Complexity

### Worst Case

```
O(n²)
```

Why?

Every time we flatten a child list, we again traverse to its tail.

```java
while (curr.next != null)
```

If there are many nested child lists, some nodes are visited multiple times.

---

# Space Complexity

```
O(d)
```

where

- `d` = maximum depth of child lists

because of recursion.

---

# Drawback of This Approach

The algorithm is correct and accepted on LeetCode.

However, finding the tail every time causes repeated traversal.

Example

```
Flatten child

↓

Traverse to tail

↓

Flatten another child

↓

Traverse to tail again
```

This repeated traversal leads to the worst-case complexity of **O(n²)**.

---

# Better Approach

An optimized recursive DFS returns the **tail** of every flattened list.

Instead of

```java
while (curr.next != null)
```

we directly get

```java
Node childTail = dfs(childHead);
```

Now we can connect

```java
childTail.next = next;
```

without traversing again.

Complexity becomes:

- **Time:** `O(n)`
- **Space:** `O(d)`

---

# Interview Summary

### Current Solution

- ✅ Easy to understand
- ✅ Accepted on LeetCode
- ✅ Uses recursion
- ❌ Worst-case `O(n²)` because of repeated tail traversal

### Optimized DFS

- ✅ Returns the tail of every flattened list
- ✅ Avoids repeated traversal
- ✅ Runs in `O(n)` time
- ✅ Preferred in interviews