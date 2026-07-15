# Merge Two Sorted Linked Lists Using Recursion

## Problem

Given two sorted linked lists:

```text
List 1

1 -> 3 -> 5 -> NULL

List 2

2 -> 4 -> 6 -> NULL
```

Merge them into one sorted list:

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6 -> NULL
```

---

# Intuition

Imagine you have two queues of numbers.

```
List 1: 1 3 5
List 2: 2 4 6
```

Ask yourself:

> Which number should come first?

Obviously,

```
1
```

After choosing `1`, the problem becomes:

```
Merge

3 -> 5

with

2 -> 4 -> 6
```

Notice?

It's the **same problem**, just with smaller lists.

That's why recursion fits perfectly.

---

# Base Cases

## Case 1

If List 1 becomes empty

```text
NULL

2 -> 4 -> 6
```

Return List 2.

```java
if(list1 == null)
    return list2;
```

---

## Case 2

If List 2 becomes empty

```text
1 -> 3 -> 5

NULL
```

Return List 1.

```java
if(list2 == null)
    return list1;
```

---

# Recursive Logic

Compare the first nodes.

```
1 and 2
```

Since

```
1 < 2
```

choose `1`.

Now merge the remaining lists.

```
3 -> 5

2 -> 4 -> 6
```

---

# Dry Run

Initial

```text
List1

1 -> 3 -> 5

List2

2 -> 4 -> 6
```

---

### Compare 1 and 2

Smaller = **1**

Result starts with

```text
1
```

Now solve

```text
3 -> 5

2 -> 4 -> 6
```

---

### Compare 3 and 2

Smaller = **2**

Now

```text
1 -> 2
```

Solve

```text
3 -> 5

4 -> 6
```

---

### Compare 3 and 4

Smaller = **3**

Now

```text
1 -> 2 -> 3
```

Solve

```text
5

4 -> 6
```

---

### Compare 5 and 4

Choose **4**

```text
1 -> 2 -> 3 -> 4
```

Solve

```text
5

6
```

---

### Compare 5 and 6

Choose **5**

```text
1 -> 2 -> 3 -> 4 -> 5
```

Solve

```text
NULL

6
```

Base case:

Return `6`.

Final answer

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6
```

---

# Code

```java
public ListNode mergeTwoLists(ListNode list1, ListNode list2) {

    if (list1 == null)
        return list2;

    if (list2 == null)
        return list1;

    if (list1.val < list2.val) {

        list1.next = mergeTwoLists(list1.next, list2);

        return list1;

    } else {

        list2.next = mergeTwoLists(list1, list2.next);

        return list2;
    }
}
```

---

# Understanding Every Line

## Base Cases

```java
if(list1 == null)
    return list2;
```

If List 1 finishes first,

just return the remaining List 2.

---

```java
if(list2 == null)
    return list1;
```

Same logic.

---

## Compare Values

```java
if(list1.val < list2.val)
```

Choose the smaller node.

---

## Recursive Call

```java
list1.next = mergeTwoLists(list1.next, list2);
```

Meaning:

```
I have already selected list1.

Now merge everything after it.
```

Suppose

```
1 -> 3 -> 5

2 -> 4 -> 6
```

After choosing `1`, recursion merges

```
3 -> 5

2 -> 4 -> 6
```

Then attach the result after `1`.

---

## Return

```java
return list1;
```

The head of the merged list is `1`.

---

# Call Stack Visualization

```
merge(1,2)

↓

choose 1

↓

merge(3,2)

↓

choose 2

↓

merge(3,4)

↓

choose 3

↓

merge(5,4)

↓

choose 4

↓

merge(5,6)

↓

choose 5

↓

merge(NULL,6)

↓

return 6
```

Now recursion starts returning

```
5 -> 6

↓

4 -> 5 -> 6

↓

3 -> 4 -> 5 -> 6

↓

2 -> 3 -> 4 -> 5 -> 6

↓

1 -> 2 -> 3 -> 4 -> 5 -> 6
```

---

# Time Complexity

Every node is visited exactly once.

**Time:** `O(m + n)`

where

- `m` = length of first list
- `n` = length of second list

---

# Space Complexity

Because of recursion,

**Space:** `O(m + n)`

(call stack)

---

# Memory Trick

Imagine two sorted queues.

```
Queue 1

1 3 5

Queue 2

2 4 6
```

Always pick the smaller front element.

After picking it,

ask recursion to merge the remaining queues.

Keep doing this until one queue becomes empty.

Then simply attach the remaining queue.

That's exactly how the recursive solution works.