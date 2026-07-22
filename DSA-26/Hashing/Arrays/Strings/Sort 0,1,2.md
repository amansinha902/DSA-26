# Sort Colors (LeetCode 75)

> **Difficulty:** Medium  
> **Tags:** Array, Two Pointers, Dutch National Flag Algorithm

---

# Problem Statement

Given an array `nums` with `n` objects colored:

- `0` → Red
- `1` → White
- `2` → Blue

Sort them **in-place** so that objects of the same color are adjacent, in the order:

```text
0 → 1 → 2
```

You **must not** use the library sort function.

---

# Example 1

### Input

```text
nums = [2,0,2,1,1,0]
```

### Output

```text
[0,0,1,1,2,2]
```

---

# Example 2

### Input

```text
nums = [2,0,1]
```

### Output

```text
[0,1,2]
```

---

# Constraints

```text
1 <= nums.length <= 300

nums[i] is either 0, 1 or 2.
```

---

# Understanding the Problem

There are only **three distinct values**:

```text
0
1
2
```

We need to arrange them in order.

Important requirements:

- Sort **in-place**.
- No extra array.
- No built-in sorting.

---

# Observation

Since there are only three numbers,

we don't actually need a comparison-based sorting algorithm.

We can divide the array into **three regions**.

This is known as the **Dutch National Flag Algorithm**, invented by Edsger Dijkstra.

---

# Approach 1 — Brute Force (Sorting)

## Idea

Simply sort the array.

---

## Java Code

```java
Arrays.sort(nums);
```

---

## Complexity

Time

```text
O(n log n)
```

Space

```text
Depends on sorting implementation.
```

---

## Why Not?

The problem explicitly asks us **not** to use the library sort.

---

# Approach 2 — Counting Sort

## Idea

Count how many:

- 0s
- 1s
- 2s

Then overwrite the array.

---

## Algorithm

Step 1

Count

```text
count0

count1

count2
```

Step 2

Fill array

```
0

0

...

1

1

...

2

2
```

---

## Dry Run

Input

```text
[2,0,2,1,1,0]
```

Counts

```text
0 -> 2

1 -> 2

2 -> 2
```

Fill

```text
[0,0,1,1,2,2]
```

---

## Complexity

Time

```text
O(n)
```

Space

```text
O(1)
```

(Only three counters)

---

## Java Code

```java
class Solution {

    public void sortColors(int[] nums) {

        int zero = 0;
        int one = 0;
        int two = 0;

        for (int num : nums) {

            if (num == 0)
                zero++;
            else if (num == 1)
                one++;
            else
                two++;
        }

        int index = 0;

        while (zero-- > 0)
            nums[index++] = 0;

        while (one-- > 0)
            nums[index++] = 1;

        while (two-- > 0)
            nums[index++] = 2;
    }
}
```

---

# Approach 3 — Dutch National Flag Algorithm (Optimal) ⭐⭐⭐

This is the approach expected in interviews.

---

# Intuition

Instead of counting,

we place each element directly into its correct region.

Maintain three regions:

```text
0's Region

1's Region

Unknown Region

2's Region
```

Initially

```text
Unknown Region = Entire Array
```

As we scan,

the unknown region becomes smaller.

---

# Three Pointers

Your solution uses three pointers:

```text
i -> Next position where 0 should go

j -> Current element being processed

k -> Next position where 2 should go
```

Initially

```text
i = 0

j = 0

k = n-1
```

---

# Regions Maintained

At every point during execution,

```text
0 ........ i-1
```

All are

```text
0
```

---

```text
i ........ j-1
```

All are

```text
1
```

---

```text
j ........ k
```

Unknown elements.

Need processing.

---

```text
k+1 ........ n-1
```

All are

```text
2
```

This invariant is maintained throughout the algorithm.

---

# Algorithm

While

```text
j <= k
```

Case 1

Current element is

```text
0
```

Swap with

```text
i
```

Move

```text
i++

j++
```

---

Case 2

Current element is

```text
1
```

Already in correct region.

Simply

```text
j++
```

---

Case 3

Current element is

```text
2
```

Swap with

```text
k
```

Move

```text
k--
```

Do **NOT** increment `j`.

---

# Why Don't We Increment j After Swapping 2?

This is the most important interview question.

Suppose

```text
[2,1,0]
```

Initially

```text
j = 0

k = 2
```

Swap

```text
2

↓

0
```

Array becomes

```text
[0,1,2]
```

The new value at

```text
j
```

is

```text
0
```

It has never been processed.

If we increment `j`,

we skip it.

Therefore,

after swapping with `k`,

only decrease `k`.

Keep `j` at the same position.

---

# Dry Run

Input

```text
[2,0,2,1,1,0]
```

Initially

```text
i = 0

j = 0

k = 5
```

| i | j | k | Array | Action |
|---|---|---|-------|--------|
|0|0|5|2 0 2 1 1 0|Swap j,k|
|0|0|4|0 0 2 1 1 2|k--|
|0|0|4|0 0 2 1 1 2|Swap i,j|
|1|1|4|0 0 2 1 1 2|i++,j++|
|1|1|4|0 0 2 1 1 2|Swap i,j|
|2|2|4|0 0 2 1 1 2|i++,j++|
|2|2|4|0 0 2 1 1 2|Swap j,k|
|2|2|3|0 0 1 1 2 2|k--|
|2|2|3|0 0 1 1 2 2|1 → j++|
|2|3|3|0 0 1 1 2 2|1 → j++|

Now

```text
j > k
```

Done.

---

# Your Solution (Optimal)

```java
class Solution {

    public void sortColors(int[] nums) {

        int n = nums.length;

        int i = 0;
        int j = 0;
        int k = n - 1;

        while (j <= k) {

            if (nums[j] == 2) {

                int temp = nums[j];
                nums[j] = nums[k];
                nums[k] = temp;

                k--;

            } else if (nums[j] == 0) {

                int temp = nums[j];
                nums[j] = nums[i];
                nums[i] = temp;

                i++;
                j++;

            } else {

                j++;
            }
        }
    }
}
```

---

# Line-by-Line Explanation

### Step 1

```java
int i = 0;
```

Next position where a `0` should be placed.

---

### Step 2

```java
int j = 0;
```

Current element being processed.

---

### Step 3

```java
int k = n - 1;
```

Next position where a `2` should be placed.

---

### Case 1

```java
nums[j] == 0
```

Swap with

```text
i
```

Expand

```text
0's region
```

Move

```java
i++;
j++;
```

---

### Case 2

```java
nums[j] == 1
```

Already in the correct region.

Simply

```java
j++;
```

---

### Case 3

```java
nums[j] == 2
```

Swap with

```text
k
```

Expand

```text
2's region
```

Move only

```java
k--;
```

---

# Why Does This Algorithm Work?

At every iteration,

we maintain four regions:

```text
0's

↓

1's

↓

Unknown

↓

2's
```

Every operation reduces the unknown region by at least one element.

Eventually,

the unknown region disappears,

meaning the array is fully sorted.

---

# Complexity

### Time

```text
O(n)
```

Each element is processed at most once.

---

### Space

```text
O(1)
```

Only three pointers are used.

---

# Comparison

| Approach | Time | Space | Interview |
|----------|------|--------|-----------|
|Built-in Sort|O(n log n)|Depends|❌|
|Counting Sort|O(n)|O(1)|✅|
|Dutch National Flag|O(n)|O(1)|⭐⭐⭐|

---

# Common Mistakes

## Mistake 1

Incrementing `j` after swapping with `k`.

❌ Wrong

The swapped element hasn't been processed yet.

---

## Mistake 2

Loop condition

```java
while(j < k)
```

Wrong.

Should be

```java
while(j <= k)
```

Otherwise,

the last unknown element won't be processed.

---

## Mistake 3

Incrementing `i` when current element is `1`.

Wrong.

`1` is already in its correct region.

---

## Mistake 4

Not understanding the pointer regions.

Always remember:

```text
0's | 1's | Unknown | 2's
```

---

# Edge Cases

```text
[0]

[1]

[2]

[0,0,0]

[2,2,2]

[1,1,1]

Already sorted

Reverse sorted

Only two colors present
```

---

# Interview Questions

## Q1. Why don't we increment `j` after swapping a `2`?

Because the element swapped from the end has not been processed yet. We must examine it before moving on.

---

## Q2. Why do we increment both `i` and `j` when we find a `0`?

After swapping, the element placed at index `i` is guaranteed to be `0`, and everything before `j` has already been processed. So both pointers can safely move forward.

---

## Q3. Why is this called the Dutch National Flag Algorithm?

The three partitions represent the three colors of the Dutch national flag:

- Red → `0`
- White → `1`
- Blue → `2`

---

## Q4. Can this be generalized?

Yes.

This algorithm works perfectly when there are exactly **three distinct values**. For more colors or arbitrary values, other sorting techniques are required.

---

# Pattern Recognition

Whenever you see

```text
Three distinct values

In-place sorting

Constant space
```

Think

```text
Dutch National Flag Algorithm
```

---

# Similar Problems

### Easy

- Move Zeroes
- Remove Element

### Medium

- Partition Array According to Pivot
- Wiggle Sort

### Hard

- First Missing Positive
- Sort Items by Groups

---

# 30-Second Revision

- Maintain four regions:
  ```text
  0's | 1's | Unknown | 2's
  ```
- Use three pointers:
  - `i` → next `0`
  - `j` → current element
  - `k` → next `2`
- If `0`:
  - Swap with `i`
  - `i++`, `j++`
- If `1`:
  - `j++`
- If `2`:
  - Swap with `k`
  - `k--`
  - **Do not increment `j`**
- Time Complexity: **O(n)**
- Space Complexity: **O(1)**

> **Interview takeaway:** The Dutch National Flag Algorithm sorts an array with three distinct values in a single pass using constant extra space by maintaining well-defined pointer regions.