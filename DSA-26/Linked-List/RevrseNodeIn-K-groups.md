# Reverse Nodes in K Group

## Problem

Given a linked list, reverse the nodes of the list **k at a time**.

If the remaining nodes are fewer than `k`, leave them as they are.

---

## Example 1

### Input

```text
Head

1 -> 2 -> 3 -> 4 -> 5
```

```
k = 2
```

### Output

```text
2 -> 1 -> 4 -> 3 -> 5
```

---

## Example 2

### Input

```text
1 -> 2 -> 3 -> 4 -> 5
```

```
k = 3
```

### Output

```text
3 -> 2 -> 1 -> 4 -> 5
```

Only the first three nodes are reversed.

The last two nodes remain unchanged because there are fewer than `k` nodes.

---

# Intuition

The idea is:

1. Check whether there are at least `k` nodes.
2. Reverse exactly `k` nodes.
3. Recursively solve the remaining linked list.
4. Attach the reversed group with the remaining result.

Think of the list as small chunks.

Example

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6
```

For

```
k = 2
```

Break into groups

```text
(1 2) (3 4) (5 6)
```

Reverse each group

```text
(2 1) (4 3) (6 5)
```

Join them.

---

# Recursive Approach

## Step 1 : Check if K Nodes Exist

Suppose

```text
1 -> 2 -> 3 -> 4 -> 5
```

```
k = 3
```

Count three nodes.

```
1

↓

2

↓

3
```

Three nodes exist.

Proceed.

---

Suppose

```text
4 -> 5
```

Need

```
k = 3
```

Only two nodes remain.

Do not reverse.

Return the list as it is.

---

# Step 2 : Reverse First K Nodes

Original

```text
1 -> 2 -> 3
```

Reverse

```text
3 -> 2 -> 1
```

Exactly like normal linked list reversal.

---

# Step 3 : Solve Remaining List

Remaining

```text
4 -> 5 -> 6
```

Again call

```java
reverseKGroup(4, k)
```

Recursion repeats the same process.

---

# Step 4 : Connect Both Parts

After reversing

```text
3 -> 2 -> 1
```

Remaining recursion returns

```text
6 -> 5 -> 4
```

Connect

```text
3 -> 2 -> 1 -> 6 -> 5 -> 4
```

Done.

---

# Dry Run

Input

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6
```

```
k = 2
```

---

### Group 1

Reverse

```text
1 -> 2

↓

2 -> 1
```

Recursively solve

```text
3 -> 4 -> 5 -> 6
```

---

### Group 2

Reverse

```text
3 -> 4

↓

4 -> 3
```

Recursively solve

```text
5 -> 6
```

---

### Group 3

Reverse

```text
5 -> 6

↓

6 -> 5
```

Return.

---

Now recursion returns

```text
6 -> 5
```

Attach

```text
4 -> 3 -> 6 -> 5
```

Attach again

```text
2 -> 1 -> 4 -> 3 -> 6 -> 5
```

Done.

---

# Code

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode temp = head;
        int count =0;
        //check if k nodes availble
        while(count < k){
            if(temp == null) return head;
            temp = temp.next;
            count++;
        }

        ListNode prevNode = reverseKGroup(temp,k);

        //reverse current group 
        temp = head;
        count =0;
        while(count < k){
            ListNode next = temp.next;
            temp.next = prevNode;
            prevNode = temp;
            temp = next;
            count++;
        }
        return prevNode;
    }
}
```

---

# Understanding Every Line

## Count K Nodes

```java
ListNode curr = head;

int count = 0;

while(curr != null && count < k){
    curr = curr.next;
    count++;
}
```

Purpose:

Check whether enough nodes exist.

---

## Reverse Only When Possible

```java
if(count == k)
```

If fewer than `k` nodes remain,

leave them unchanged.

---

## Solve Remaining List

```java
curr = reverseKGroup(curr, k);
```

Recursively reverse the remaining linked list.

---

## Reverse Current Group

```java
while(count-- > 0){
```

Reverse exactly `k` nodes.

---

### Save Next Node

```java
ListNode temp = head.next;
```

Store the next node before changing links.

---

### Reverse Pointer

```java
head.next = curr;
```

Current node now points to the already processed part.

---

### Move Curr

```java
curr = head;
```

The current node becomes the new head of the reversed group.

---

### Move Head

```java
head = temp;
```

Proceed to the next node.

---

### Update Head

```java
head = curr;
```

The new head of this reversed group.

---

# Visual Example of Reversal

Suppose

```text
1 -> 2 -> 3
```

Initially

```
curr = NULL
```

Iteration 1

```
1 -> NULL
```

Iteration 2

```
2 -> 1
```

Iteration 3

```
3 -> 2 -> 1
```

Exactly the same reversal logic.

---

# Time Complexity

Each node is visited once.

```
Time = O(n)
```

---

# Space Complexity

Because of recursion

```
Space = O(n / k)
```

(call stack)

---

# Iterative Approach (Interview Preferred)

Many interviewers prefer the iterative solution.

### Idea

1. Use a **dummy node**.
2. Find the `k`th node.
3. Reverse the group.
4. Connect the previous group with the reversed group.
5. Move to the next group.

Time

```
O(n)
```

Space

```
O(1)
```

---

# Interview Tips

### Recursive

- Count `k` nodes.
- If fewer than `k`, return.
- Reverse current group.
- Recursively solve the remaining list.
- Connect both parts.

---

### Iterative

- Use a dummy node.
- Reverse one group at a time.
- Maintain previous group's tail.
- Constant extra space.

---

# Memory Trick

Imagine a train.

```
1 -> 2 -> 3 -> 4 -> 5 -> 6
```

Break the train into wagons of size `k`.

```
(1 2)

(3 4)

(5 6)
```

Reverse each wagon independently.

```
(2 1)

(4 3)

(6 5)
```

Finally, reconnect all the wagons.