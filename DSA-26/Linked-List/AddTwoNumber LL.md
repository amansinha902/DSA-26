# Add Two Numbers Represented by Linked Lists

## Problem

You are given two **non-empty linked lists** representing two non-negative integers.

The digits are stored in **reverse order**, and each node contains a single digit.

Return the sum as a linked list.

---

## Example

```
List 1

2 -> 4 -> 3

List 2

5 -> 6 -> 4
```

These represent:

```
342

+

465

=

807
```

Since the digits are stored in reverse order, the answer should be:

```
7 -> 0 -> 8
```

---

# Intuition

Think about how we add numbers on paper.

```
  342
+ 465
-----
  807
```

We always:

1. Add the current digits.
2. Add any carry from the previous addition.
3. Store the last digit.
4. Carry the remaining value.

We do the exact same thing while traversing the linked lists.

---

# Step-by-Step Dry Run

Input

```
List1

2 -> 4 -> 3

List2

5 -> 6 -> 4
```

Initially

```
carry = 0

Dummy

0
```

---

## Iteration 1

Current digits

```
2 + 5 + carry(0)

= 7
```

```
Digit = 7

Carry = 0
```

Answer

```
Dummy -> 7
```

Move both pointers.

---

## Iteration 2

```
4 + 6 + 0

=10
```

```
Digit = 0

Carry = 1
```

Answer

```
Dummy -> 7 -> 0
```

Move pointers.

---

## Iteration 3

```
3 + 4 + carry(1)

=8
```

```
Digit = 8

Carry = 0
```

Answer

```
Dummy -> 7 -> 0 -> 8
```

Done.

---

# Another Example (Carry at the End)

```
9 -> 9 -> 9

1
```

Represents

```
999 + 1 = 1000
```

---

Iteration 1

```
9 + 1 =10

Digit =0

Carry =1
```

Answer

```
0
```

---

Iteration 2

```
9 + 0 +1 =10

Digit =0

Carry =1
```

Answer

```
0 -> 0
```

---

Iteration 3

```
9 +0 +1 =10

Digit =0

Carry =1
```

Answer

```
0 -> 0 -> 0
```

Both lists finish.

But

```
Carry =1
```

So create one more node.

Final Answer

```
0 -> 0 -> 0 -> 1
```

Represents

```
1000
```

---

# Algorithm

Repeat while

- List1 has nodes
- OR List2 has nodes
- OR Carry exists

For every iteration

1. Take value from List1 (or 0 if finished)
2. Take value from List2 (or 0 if finished)
3. Add carry
4. Create a node with `sum % 10`
5. Update carry using `sum / 10`
6. Move pointers

---

# Code

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

    ListNode dummy = new ListNode(0);
    ListNode current = dummy;

    int carry = 0;

    while (l1 != null || l2 != null || carry != 0) {

        int sum = carry;

        if (l1 != null) {
            sum += l1.val;
            l1 = l1.next;
        }

        if (l2 != null) {
            sum += l2.val;
            l2 = l2.next;
        }

        carry = sum / 10;

        current.next = new ListNode(sum % 10);

        current = current.next;
    }

    return dummy.next;
}
```

---

# Understanding Every Line

## Create Dummy Node

```java
ListNode dummy = new ListNode(0);
```

The dummy node helps us easily build the result list.

Initially

```
Dummy

0
```

---

## Current Pointer

```java
ListNode current = dummy;
```

Used to keep adding new nodes.

---

## Carry

```java
int carry = 0;
```

Initially there is no carry.

---

## Loop Condition

```java
while (l1 != null || l2 != null || carry != 0)
```

Continue if

- List1 still has nodes
- OR List2 still has nodes
- OR carry still exists

---

## Start Sum

```java
int sum = carry;
```

Always include carry from the previous addition.

---

## Add List1 Value

```java
if(l1 != null){
    sum += l1.val;
    l1 = l1.next;
}
```

If List1 has ended,

treat its value as **0**.

---

## Add List2 Value

```java
if(l2 != null){
    sum += l2.val;
    l2 = l2.next;
}
```

Same idea.

---

## Calculate Carry

```java
carry = sum / 10;
```

Example

```
sum =15

carry =1
```

---

## Store Current Digit

```java
sum % 10
```

Example

```
15 %10

=5
```

Create node

```java
current.next = new ListNode(sum % 10);
```

---

## Move Current

```java
current = current.next;
```

Move to the last node.

---

## Return Answer

```java
return dummy.next;
```

We skip the dummy node and return the actual head.

---

# Why Do We Use a Dummy Node?

Without dummy

```
Result = NULL
```

First insertion becomes complicated because you need special handling.

With dummy

```
Dummy -> 7 -> 0 -> 8
```

Finally

```
return dummy.next;
```

Answer

```
7 -> 0 -> 8
```

Much cleaner.

---

# Time Complexity

Every node is visited once.

**Time:** `O(max(n, m))`

where

- `n` = length of first list
- `m` = length of second list

---

# Space Complexity

Only the output list is created.

Extra space (excluding the output list): **`O(1)`**

---

# Interview Tips

- Use a **dummy node** to simplify result construction.
- Maintain a **carry** for sums greater than or equal to 10.
- Continue until **both lists are exhausted and carry becomes 0**.
- Store:
  - **Digit:** `sum % 10`
  - **Carry:** `sum / 10`

---

# Memory Trick

Imagine adding numbers on paper from **right to left**:

```
  342
+ 465
------
  807
```

Since the linked lists are stored in **reverse order**, the first nodes already represent the rightmost digits.

So simply:

1. Add current digits.
2. Add carry.
3. Store the last digit.
4. Carry the remaining value.
5. Move to the next nodes.

Repeat until everything is processed.