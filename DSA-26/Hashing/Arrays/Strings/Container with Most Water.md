# Container With Most Water (LeetCode 11)

> **Difficulty:** Medium  
> **Tags:** Array, Two Pointers, Greedy

---

# Problem Statement

You are given an integer array `height` of length `n`.

There are `n` vertical lines drawn such that the two endpoints of the `i-th` line are:

```text
(i, 0) and (i, height[i])
```

Choose any two lines that together with the x-axis form a container.

Return the **maximum amount of water** the container can store.

> **Note:** You may not slant the container.

---

# Example 1

```text
Input

height = [1,8,6,2,5,4,8,3,7]
```

Output

```text
49
```

### Explanation

Choose

```text
Height = 8 and 7

Width = 8 - 1 = 7

Area = 7 × 7 = 49
```

---

# Example 2

```text
Input

height = [1,1]

Output

1
```

---

# Constraints

```text
2 <= height.length <= 10^5

0 <= height[i] <= 10^4
```

---

# Understanding the Problem

The water stored depends on **two things**:

1. Width between the two lines.
2. Smaller height of the two lines.

Formula

```text
Area = Width × Minimum Height
```

where

```text
Width = right - left

Height = min(height[left], height[right])
```

---

# Observation

Suppose we choose

```text
Left Height = 8

Right Height = 5
```

Water stored

```text
Width × 5
```

Even if the left height is 100,

the water level **cannot exceed 5** because water spills from the shorter side.

Therefore,

> **The shorter line always limits the water level.**

This is the key observation that leads to the optimal solution.

---

# Approach 1 — Brute Force

## Idea

Try every possible pair of lines.

For each pair:

- Calculate width.
- Find the smaller height.
- Calculate area.
- Update the maximum.

---

## Algorithm

```text
for i

    for j = i+1

        width = j - i

        height = min(height[i], height[j])

        area = width × height

        update answer
```

---

## Dry Run

```text
height = [1,8,6,2]
```

Pairs checked

```text
(1,8)

(1,6)

(1,2)

(8,6)

(8,2)

(6,2)
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

    public int maxArea(int[] height) {

        int maxArea = 0;

        for (int i = 0; i < height.length; i++) {

            for (int j = i + 1; j < height.length; j++) {

                int width = j - i;

                int currentHeight = Math.min(height[i], height[j]);

                int area = width * currentHeight;

                maxArea = Math.max(maxArea, area);
            }
        }

        return maxArea;
    }
}
```

---

# Why Brute Force is Bad?

If

```text
n = 100000
```

Number of comparisons

```text
100000²

= 10¹⁰
```

Impossible within time limits.

We need something better.

---

# Approach 2 — Two Pointer (Optimal) ⭐⭐⭐

## Intuition

Initially,

take the **maximum possible width**.

```text
Left = 0

Right = n-1
```

Calculate the area.

Now the question is:

> Which pointer should we move?

---

# Most Important Observation

Suppose

```text
Left Height = 4

Right Height = 9
```

Area

```text
Width × 4
```

Water is limited by **4**.

If we move the taller line (9),

```text
Width decreases

Height remains limited by 4
```

Area can **never increase**.

So moving the taller line is useless.

Instead,

move the **shorter** line,

hoping to find a taller line.

---

# Why Move the Smaller Height?

Suppose

```text
Left = 2

Right = 8
```

Heights

```text
2 and 8
```

Current Area

```text
Width × 2
```

Move Right

New Width

```text
Smaller
```

Height still

```text
≤2
```

Area definitely decreases.

But if we move Left,

we may find

```text
5

7

10
```

Now

Minimum height increases.

Area may increase.

This is why we always move the smaller height.

---

# Algorithm

1. Place one pointer at the beginning.
2. Place another pointer at the end.
3. Compute current area.
4. Update maximum area.
5. Move the pointer having the smaller height.
6. Continue until both pointers meet.

---

# Dry Run

Input

```text
height = [1,8,6,2,5,4,8,3,7]
```

Initially

```text
i = 0

j = 8
```

---

| Left | Right | Heights | Width | Min Height | Area | Max | Move |
|------|-------|---------|------|-----------|------|------|------|
|0|8|1,7|8|1|8|8|Left++|
|1|8|8,7|7|7|49|49|Right--|
|1|7|8,3|6|3|18|49|Right--|
|1|6|8,8|5|8|40|49|Right--|
|1|5|8,4|4|4|16|49|Right--|
|1|4|8,5|3|5|15|49|Right--|
|1|3|8,2|2|2|4|49|Right--|
|1|2|8,6|1|6|6|49|Right--|

Answer

```text
49
```

---

# Your Solution (Optimal)

```java
class Solution {

    public int maxArea(int[] height) {

        int n = height.length;

        int maxArea = 0;

        int i = 0;

        int j = n - 1;

        while (i < j) {

            int width = j - i;

            int currentHeight = Math.min(height[i], height[j]);

            int area = width * currentHeight;

            maxArea = Math.max(maxArea, area);

            if (height[i] >= height[j]) {

                j--;

            } else {

                i++;
            }
        }

        return maxArea;
    }
}
```

---

# Line-by-Line Explanation

### Step 1

```java
int i = 0;
int j = n - 1;
```

Start with the **maximum possible width**.

---

### Step 2

```java
int width = j - i;
```

Distance between the two selected lines.

---

### Step 3

```java
int currentHeight = Math.min(height[i], height[j]);
```

Water level is determined by the shorter line.

---

### Step 4

```java
int area = width * currentHeight;
```

Calculate current container area.

---

### Step 5

```java
maxArea = Math.max(area, maxArea);
```

Update the maximum area found so far.

---

### Step 6

```java
if(height[i] >= height[j])
```

If the right line is shorter (or equal),

move the right pointer.

Otherwise,

move the left pointer.

Equivalent logic:

```java
if(height[left] < height[right])
    left++;
else
    right--;
```

Both implementations are correct.

---

# Why Does This Work?

Suppose

```text
Left Height = 2

Right Height = 10
```

Current area

```text
Width × 2
```

Moving the taller line

```text
Width decreases

Minimum height still ≤ 2
```

Area cannot improve.

Only moving the shorter line gives a chance to increase the limiting height.

This greedy choice is always safe.

---

# Complexity

### Time

```text
O(n)
```

Each pointer moves at most `n` times.

---

### Space

```text
O(1)
```

No extra data structure is used.

---

# Comparison

| Approach | Time | Space | Interview |
|----------|------|--------|-----------|
|Brute Force|O(n²)|O(1)|❌|
|Two Pointer|O(n)|O(1)|⭐⭐⭐|

---

# Common Mistakes

## Mistake 1

Moving both pointers every time.

Wrong.

Move **only one pointer**.

---

## Mistake 2

Moving the taller pointer.

Wrong.

Always move the shorter one.

---

## Mistake 3

Using

```java
Math.max(height[i], height[j])
```

Wrong.

Water is limited by the **smaller** height.

Always use

```java
Math.min(...)
```

---

## Mistake 4

Using

```java
width = j - i + 1
```

Wrong.

Correct width is

```java
j - i
```

---

# Edge Cases

```text
[1,1]

[0,0]

[5,5]

Strictly increasing heights

Strictly decreasing heights

Large equal heights

Minimum input size
```

---

# Interview Questions

## Q1. Why do we move the smaller height?

Because the shorter line limits the water level. Moving the taller line only reduces the width without increasing the limiting height, so it cannot produce a better answer.

---

## Q2. Why start from both ends?

Starting from both ends gives the **maximum possible width**. We then gradually reduce the width while trying to increase the limiting height.

---

## Q3. Can this be solved using Dynamic Programming?

No.

The optimal solution relies on a greedy two-pointer strategy.

---

## Q4. Why isn't sorting allowed?

Sorting changes the original positions of the lines.

The width depends on the original indices, so sorting would produce incorrect answers.

---

# Pattern Recognition

Whenever you see

```text
Maximum area

Maximum distance

Two boundaries

Need optimal pair
```

Think

```text
Two Pointers
```

Especially when:

- Array is processed from both ends.
- Width decreases gradually.
- One side limits the answer.

---

# Similar Problems

### Easy

- Two Sum II

### Medium

- 3Sum
- Trapping Rain Water (Harder variation)
- Boats to Save People

### Hard

- Trapping Rain Water
- Largest Rectangle in Histogram

---

# 30-Second Revision

- Water stored depends on:
  ```
  width × min(height[left], height[right])
  ```
- Start with the widest container.
- Calculate the area.
- Move the pointer pointing to the **shorter** line.
- Repeat until both pointers meet.
- Time Complexity: **O(n)**
- Space Complexity: **O(1)**

> **Interview takeaway:** The shorter line always limits the water level. Therefore, moving the taller line can never improve the answer. The greedy choice of moving the shorter line makes the two-pointer solution optimal.