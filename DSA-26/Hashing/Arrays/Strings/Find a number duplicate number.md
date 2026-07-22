# Find the Duplicate Number (LeetCode 287)

> **Difficulty:** Medium  
> **Tags:** Array, Binary Search, Two Pointers, Floyd's Cycle Detection

---

# Problem Statement

Given an integer array `nums` containing **n + 1** integers where:

- Every integer is in the range

```text
[1, n]
```

- Only **one number is duplicated**.
- The duplicate number may appear more than twice.

Return the duplicate number.

### Important Constraints

- ❌ Do NOT modify the array.
- ❌ Use only constant extra space.

---

# Example 1

### Input

```text
nums = [1,3,4,2,2]
```

### Output

```text
2
```

---

# Example 2

### Input

```text
nums = [3,1,3,4,2]
```

### Output

```text
3
```

---

# Example 3

### Input

```text
nums = [3,3,3,3,3]
```

### Output

```text
3
```

---

# Constraints

```text
1 <= n <= 10^5

nums.length = n + 1

1 <= nums[i] <= n

Exactly one duplicate exists.
```

---

# Understanding the Problem

Suppose

```text
nums = [1,3,4,2,2]
```

Length

```text
5
```

Values

```text
1...4
```

There are

```text
5 numbers

Only 4 unique values possible
```

According to the **Pigeonhole Principle**,

one value **must repeat**.

---

# Why Can't We Sort?

Sorting gives an easy solution.

```
[1,2,2,3,4]
```

Duplicate

```
2
```

But sorting modifies the array.

The problem explicitly forbids modifying it.

---

# Approach 1 — Brute Force

## Idea

For every element,

count how many times it appears.

If count becomes greater than 1,

return it.

---

## Algorithm

```text
For every number

    Count frequency

    If frequency > 1

        Return number
```

---

## Complexity

### Time

```text
O(n²)
```

### Space

```text
O(1)
```

---

## Java Code

```java
class Solution {

    public int findDuplicate(int[] nums) {

        for (int i = 0; i < nums.length; i++) {

            int count = 0;

            for (int j = 0; j < nums.length; j++) {

                if (nums[i] == nums[j])
                    count++;
            }

            if (count > 1)
                return nums[i];
        }

        return -1;
    }
}
```

---

# Approach 2 — HashSet

## Idea

Store visited numbers.

If a number already exists,

it is the duplicate.

---

## Dry Run

```text
nums = [1,3,4,2,2]
```

Visited

```text
{}

{1}

{1,3}

{1,3,4}

{1,3,4,2}

2 already exists

Answer = 2
```

---

## Complexity

### Time

```text
O(n)
```

### Space

```text
O(n)
```

---

## Java Code

```java
class Solution {

    public int findDuplicate(int[] nums) {

        Set<Integer> set = new HashSet<>();

        for (int num : nums) {

            if (!set.add(num))
                return num;
        }

        return -1;
    }
}
```

---

# Approach 3 — Sorting

## Idea

Sort the array.

Adjacent equal numbers indicate the duplicate.

---

## Complexity

Time

```text
O(n log n)
```

Space

Depends on sorting.

Also violates the constraint of modifying the array.

---

# Approach 4 — Binary Search on Answer

## Intuition

Notice that numbers are between

```text
1...n
```

Binary search on the value range.

For each midpoint,

count how many numbers are less than or equal to it.

If

```text
count > mid
```

duplicate lies on the left.

Else,

it lies on the right.

---

## Complexity

Time

```text
O(n log n)
```

Space

```text
O(1)
```

---

# Approach 5 — Floyd's Cycle Detection (Optimal) ⭐⭐⭐

Also called

```text
Tortoise and Hare Algorithm
```

This is the expected interview solution.

---

# Most Important Observation

Treat every value as the **next index**.

Instead of thinking

```text
Array
```

Think

```text
Linked List
```

Example

```text
nums = [1,3,4,2,2]
```

Index

```text
0 1 2 3 4
```

Value

```text
1 3 4 2 2
```

Represent it as

```text
0 → 1
1 → 3
3 → 2
2 → 4
4 → 2
```

Visualization

```text
0
|
v
1
|
v
3
|
v
2 -----
|      |
v      |
4 <-----
```

Notice

```text
2

↓

4

↓

2
```

A cycle is formed.

The duplicate number is exactly where the cycle starts.

---

# Why Does a Cycle Exist?

There are

```text
n + 1 indices

Only n possible values
```

Eventually,

two indices point to the same value.

That creates a loop.

Exactly like a linked list with a cycle.

---

# Floyd's Algorithm

It has **two phases**.

---

# Phase 1 — Detect Cycle

Use

```text
Slow Pointer

Fast Pointer
```

Slow moves

```text
1 step
```

Fast moves

```text
2 steps
```

Eventually,

they meet inside the cycle.

---

# Phase 2 — Find Cycle Entrance

Reset slow to the beginning.

Move both pointers

```text
1 step
```

Where they meet again

is the duplicate number.

---

# Why Does This Work?

Let

```text
L = distance from start to cycle

C = cycle length

X = meeting point
```

Mathematical proof shows

after resetting one pointer,

both pointers reach the cycle entrance together.

That entrance is exactly the duplicate value.

---

# Dry Run

Input

```text
nums = [1,3,4,2,2]
```

---

### Initial

```text
slow = nums[0] = 1

fast = nums[0] = 1
```

---

### Move Once

```text
slow = nums[1] = 3

fast = nums[ nums[1] ]

= nums[3]

= 2
```

---

### Continue

| Slow | Fast |
|------|------|
|3|2|
|2|2|

Pointers meet.

Cycle found.

---

Reset slow

```text
slow = nums[0] = 1
```

Move one step each

| Slow | Fast |
|------|------|
|3|4|
|2|2|

Meet at

```text
2
```

Answer

```text
2
```

---

# Your Solution (Optimal)

```java
class Solution {

    public int findDuplicate(int[] nums) {

        int s = nums[0];
        int f = nums[0];

        s = nums[s];
        f = nums[nums[f]];

        while (s != f) {

            s = nums[s];
            f = nums[nums[f]];
        }

        s = nums[0];

        while (s != f) {

            s = nums[s];
            f = nums[f];
        }

        return s;
    }
}
```

---

# Line-by-Line Explanation

### Step 1

```java
int s = nums[0];
int f = nums[0];
```

Initialize both pointers.

---

### Step 2

```java
s = nums[s];
```

Move slow one step.

---

### Step 3

```java
f = nums[nums[f]];
```

Move fast two steps.

---

### Step 4

```java
while(s != f)
```

Continue until both meet.

Meeting confirms the presence of a cycle.

---

### Step 5

```java
s = nums[0];
```

Reset slow to the beginning.

---

### Step 6

Move both

```java
s = nums[s];

f = nums[f];
```

One step each.

---

### Step 7

Where they meet

```text
Duplicate Number
```

Return it.

---

# Alternative Initialization

A more common implementation:

```java
class Solution {

    public int findDuplicate(int[] nums) {

        int slow = nums[0];
        int fast = nums[0];

        do {

            slow = nums[slow];
            fast = nums[nums[fast]];

        } while (slow != fast);

        slow = nums[0];

        while (slow != fast) {

            slow = nums[slow];
            fast = nums[fast];
        }

        return slow;
    }
}
```

Both implementations are correct.

---

# Complexity

### Time

```text
O(n)
```

---

### Space

```text
O(1)
```

Exactly what the problem requires.

---

# Comparison

| Approach | Time | Space | Modifies Array | Interview |
|----------|------|--------|----------------|-----------|
|Brute Force|O(n²)|O(1)|No|❌|
|HashSet|O(n)|O(n)|No|❌|
|Sorting|O(n log n)|Depends|Yes|❌|
|Binary Search|O(n log n)|O(1)|No|✅|
|Floyd's Cycle Detection|O(n)|O(1)|No|⭐⭐⭐|

---

# Common Mistakes

## Mistake 1

Thinking this is a linked list problem.

It is **not**.

We simply **treat the array like a linked list** by using values as next pointers.

---

## Mistake 2

Resetting the fast pointer.

Wrong.

Only reset the **slow pointer**.

---

## Mistake 3

Moving fast only one step.

Wrong.

Fast must move

```text
2 steps
```

---

## Mistake 4

Trying to sort the array.

Violates the problem constraint.

---

# Edge Cases

```text
[1,1]

[2,2,2,2]

Duplicate appears many times

Duplicate at beginning

Duplicate at end
```

---

# Interview Questions

## Q1. Why does this problem form a cycle?

Because there are `n + 1` indices but only `n` possible values. By the Pigeonhole Principle, at least two indices point to the same value, creating a cycle.

---

## Q2. Why does the duplicate correspond to the start of the cycle?

The duplicate value is the first node that has multiple incoming edges. This creates the entrance to the cycle in the linked-list representation.

---

## Q3. Why reset only the slow pointer?

After the first meeting, mathematical properties of Floyd's algorithm guarantee that moving both pointers one step at a time will make them meet at the cycle entrance.

---

## Q4. Why not use a HashSet?

A HashSet requires `O(n)` extra space, but the problem explicitly requires **constant extra space**.

---

# Pattern Recognition

Whenever you see:

```text
Values represent indices

Find duplicate

No modification allowed

Constant space
```

Think immediately:

```text
Floyd's Cycle Detection
```

---

# Similar Problems

### Easy

- Linked List Cycle
- Happy Number

### Medium

- Linked List Cycle II
- Find All Duplicates in an Array

### Hard

- First Missing Positive

---

# 30-Second Revision

- Treat the array as a linked list:
  ```
  index → nums[index]
  ```
- A duplicate creates a cycle.
- Phase 1:
  - Slow = 1 step
  - Fast = 2 steps
  - Detect the cycle.
- Phase 2:
  - Reset slow to the start.
  - Move both one step.
  - Their meeting point is the duplicate.
- Time Complexity: **O(n)**
- Space Complexity: **O(1)**

> **Interview takeaway:** The trick is recognizing that the array can be viewed as a linked list where each value points to the next index. The duplicate value becomes the entrance of a cycle, allowing Floyd's Cycle Detection Algorithm to find it in `O(n)` time and `O(1)` space.