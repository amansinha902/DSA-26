# Intersection Point of Two Linked Lists

## Problem

Given the heads of two singly linked lists, determine the node at which the two lists intersect.

If they do not intersect, return `null`.

---

## Example

```text
List A

1 -> 2 -> 3
          \
           8 -> 10 -> NULL
          /
List B

5 -> 6
```

The intersection node is:

```
8
```

> **Note:** Two linked lists intersect when they share the **same node (memory reference)**, **not** when they contain the same value.

---

# Approach 1: Using HashMap / HashSet

## Intuition

Traverse the first linked list and store every node in a `HashSet`.

Then traverse the second list.

If a node is already present in the `HashSet`, that node is the intersection.

---

## Dry Run

```text
List A

1 -> 2 -> 3 -> 8 -> 10

List B

5 -> 6 -> 8 -> 10
```

### Step 1

Store all nodes of List A.

```
HashSet

{1,2,3,8,10}
```

*(Actually stores node references, not values.)*

---

### Step 2

Traverse List B

```
5
```

Not found.

```
6
```

Not found.

```
8
```

Found!

Return node `8`.

---

## Code

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {

    HashSet<ListNode> set = new HashSet<>();

    while (headA != null) {
        set.add(headA);
        headA = headA.next;
    }

    while (headB != null) {

        if (set.contains(headB))
            return headB;

        headB = headB.next;
    }

    return null;
}
```

---

## Complexity

- **Time:** `O(n + m)`
- **Space:** `O(n)`

---

# Approach 2: Length Difference Method

## Intuition

If one list is longer, skip the extra nodes first.

Then both pointers will have the same number of nodes remaining.

Move together until they meet.

---

## Example

```text
List A

1 -> 2 -> 3 -> 4 -> 8 -> 10

Length = 6

List B

5 -> 6 -> 8 -> 10

Length = 4
```

Difference

```
6 - 4 = 2
```

Move List A two steps.

```text
3 -> 4 -> 8 -> 10

5 -> 6 -> 8 -> 10
```

Now both lists have four nodes left.

Move together.

```
3 and 5

↓

4 and 6

↓

8 and 8
```

Intersection found.

---

## Algorithm

### Step 1

Find lengths of both lists.

### Step 2

Move the longer list ahead by

```
abs(lengthA - lengthB)
```

nodes.

### Step 3

Move both pointers together.

When

```java
headA == headB
```

return that node.

---

## Code

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {

    int lenA = getLength(headA);
    int lenB = getLength(headB);

    while (lenA > lenB) {
        headA = headA.next;
        lenA--;
    }

    while (lenB > lenA) {
        headB = headB.next;
        lenB--;
    }

    while (headA != null && headB != null) {

        if (headA == headB)
            return headA;

        headA = headA.next;
        headB = headB.next;
    }

    return null;
}

private int getLength(ListNode head) {

    int count = 0;

    while (head != null) {
        count++;
        head = head.next;
    }

    return count;
}
```

---

## Complexity

- **Time:** `O(n + m)`
- **Space:** `O(1)`

---

# Approach 3: Two Pointer Switching (Best)

## Intuition

This is the most elegant approach.

Instead of calculating lengths,

when a pointer reaches the end of one list,

make it start from the other list.

Eventually,

both pointers travel the **same total distance**.

If an intersection exists,

they will meet there.

---

## Example

```text
List A

1 -> 2 -> 3
          \
           8 -> 10
          /
List B

5 -> 6
```

Pointer A travels

```
A → B
```

Pointer B travels

```
B → A
```

Both travel

```
LengthA + LengthB
```

Hence they become aligned automatically.

---

# Dry Run

```
A

1 -> 2 -> 3 -> 8 ->10

B

5 -> 6 -> 8 ->10
```

### Round 1

```
Pointer A

1 →2→3→8→10→NULL
```

```
Pointer B

5→6→8→10→NULL
```

---

### Switch

When A becomes NULL

```
A starts from headB
```

When B becomes NULL

```
B starts from headA
```

Now

Both have travelled the same total distance.

Eventually,

```
A == B == Node(8)
```

Return `8`.

---

## Code

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {

   ListNode t1 = headA;
        ListNode t2 = headB;
        while(t1 != t2){
            t1 = t1.next;
            t2 = t2.next;

            if(t1 == t2) return t1;
            if(t1 == null) t1 = headB;
            if(t2 == null) t2 = headA;
        }
        return t1; // if both list are same
}
```

---

# Why Does This Work?

Suppose

```
LengthA = 6

LengthB = 4
```

Initially

```
A travels

6 nodes

B travels

4 nodes
```

After switching,

```
A travels

+4 nodes

Total =10
```

```
B travels

+6 nodes

Total =10
```

Both travel exactly

```
LengthA + LengthB
```

Therefore,

their extra distance is canceled out.

If an intersection exists,

they must meet there.

If not,

both become `NULL` at the same time.

---

# Complexity

- **Time:** `O(n + m)`
- **Space:** `O(1)`

---

# Comparison

| Approach | Time | Space | Interview Preference |
|----------|------|--------|----------------------|
| HashSet | O(n + m) | O(n) | Easy |
| Length Difference | O(n + m) | O(1) | Good |
| Two Pointer Switching | O(n + m) | O(1) | ⭐ Best |

---

# Interview Tips

### HashSet

- Store all nodes of the first list.
- Traverse the second list.
- First common node is the answer.

---

### Length Difference

- Calculate lengths.
- Skip extra nodes from the longer list.
- Move both together.

---

### Two Pointer Switching (Most Elegant)

- Traverse both lists.
- When a pointer reaches `NULL`, switch it to the other list.
- They either:
  - Meet at the intersection node.
  - Or both become `NULL`.

No length calculation is required.

---

# Memory Trick

Imagine two runners.

Runner A runs:

```
Track A

↓

Track B
```

Runner B runs:

```
Track B

↓

Track A
```

Both run the same total distance.

If the tracks merge, they'll meet at the merge point.

If they don't merge, they'll both finish together (`NULL`).