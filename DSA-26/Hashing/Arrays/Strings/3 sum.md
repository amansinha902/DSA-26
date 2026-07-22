# 3Sum (LeetCode 15)

> **Difficulty:** Medium  
> **Tags:** Array, Sorting, Two Pointers, Hashing

---

# Problem Statement

Given an integer array `nums`, return **all the unique triplets** `[nums[i], nums[j], nums[k]]` such that:

- `i != j`
- `i != k`
- `j != k`
- `nums[i] + nums[j] + nums[k] == 0`

The solution set **must not contain duplicate triplets.**

---

# Example 1

### Input

```text
nums = [-1,0,1,2,-1,-4]
```

### Output

```text
[[-1,-1,2],[-1,0,1]]
```

### Explanation

```
-1 + -1 + 2 = 0

-1 + 0 + 1 = 0
```

No other unique triplet exists.

---

# Example 2

```text
Input:
nums = [0,1,1]

Output:
[]
```

---

# Example 3

```text
Input:
nums = [0,0,0]

Output:
[[0,0,0]]
```

---

# Constraints

```
3 <= nums.length <= 3000

-10^5 <= nums[i] <= 10^5
```

---

# Understanding the Problem

We need to find **three different numbers** whose sum is zero.

Important points:

- Triplets should be unique.
- Order inside triplet doesn't matter.
- Order of answer doesn't matter.

Example

```
[-1,0,1]

and

[1,-1,0]

are SAME triplet.
```

---

# Observations

Brute force checks every possible combination.

Can we reduce one loop?

Yes.

If we fix one number,

then remaining problem becomes

> Find Two Sum.

This immediately hints us toward

- HashMap
- Two Pointer

---

# Approach 1 — Brute Force

## Idea

Try every possible combination of 3 numbers.

Three nested loops.

Check whether sum becomes zero.

To avoid duplicates, store sorted triplets inside a Set.

---

## Algorithm

```
for i

    for j

        for k

            if sum == 0

                sort triplet

                add into set
```

---

## Dry Run

```
nums

[-1,0,1,2,-1,-4]
```

Triplets checked

```
(-1,0,1) ✔

(-1,2,-1) ✔

(-1,0,2)

(-4,2,1)

...

```

Total combinations

```
6C3 = 20
```

---

## Complexity

Time

```
O(n³)
```

Space

```
O(number of triplets)
```

---

## Java Code

```java
class Solution {

    public List<List<Integer>> threeSum(int[] nums) {

        Set<List<Integer>> set = new HashSet<>();

        int n = nums.length;

        for (int i = 0; i < n - 2; i++) {

            for (int j = i + 1; j < n - 1; j++) {

                for (int k = j + 1; k < n; k++) {

                    if (nums[i] + nums[j] + nums[k] == 0) {

                        List<Integer> triplet =
                                Arrays.asList(nums[i], nums[j], nums[k]);

                        Collections.sort(triplet);

                        set.add(triplet);
                    }
                }
            }
        }

        return new ArrayList<>(set);
    }
}
```

---

## Pros

Simple

Easy to understand

---

## Cons

Very slow

Fails for large input

---

# Approach 2 — HashSet (Better)

## Main Idea

Fix one element.

Now remaining problem becomes

```
Find Two Sum
```

Example

```
-1 0 1 2 -1 -4

Fix -1

Need target

= 1
```

Now find

```
x+y=1
```

using HashSet.

---

## Algorithm

```
Fix i

Create HashSet

For every j

needed = -(nums[i]+nums[j])

if needed exists

Triplet found

Else

Insert nums[j]
```

---

## Dry Run

Input

```
[-1,0,1,2,-1,-4]
```

Fix

```
-1
```

Need

```
x+y=1
```

HashSet

```
{}

0 added

{0}

1

Need 0

Found

Triplet

[-1,0,1]
```

Continue.

---

## Complexity

Time

```
O(n²)
```

Space

```
O(n)
```

---

## Java Code

```java
class Solution {

    public List<List<Integer>> threeSum(int[] nums) {

        Set<List<Integer>> answer = new HashSet<>();

        int n = nums.length;

        for (int i = 0; i < n; i++) {

            Set<Integer> seen = new HashSet<>();

            for (int j = i + 1; j < n; j++) {

                int third = -(nums[i] + nums[j]);

                if (seen.contains(third)) {

                    List<Integer> triplet =
                            Arrays.asList(nums[i], nums[j], third);

                    Collections.sort(triplet);

                    answer.add(triplet);

                } else {

                    seen.add(nums[j]);
                }
            }
        }

        return new ArrayList<>(answer);
    }
}
```

---

## Why Sorting Triplet?

Suppose

```
[-1,0,1]

and

[0,-1,1]
```

Without sorting

HashSet thinks they are different.

Sorting converts both into

```
[-1,0,1]
```

Duplicate removed.

---

# Approach 3 — Optimal (Sorting + Two Pointers) ⭐⭐⭐

This is the approach expected in interviews.

---

# Intuition

Suppose array is

```
-4 -1 -1 0 1 2
```

Instead of checking every triplet,

sort the array.

Fix one element.

Remaining problem is

```
Two Sum
```

Since array is sorted,

use two pointers.

---

# Why Sorting Helps?

Suppose

```
left + right > target
```

Move right leftwards.

Because numbers decrease.

Suppose

```
left + right < target
```

Move left forward.

Because numbers increase.

No need to check everything.

---

# Algorithm

Sort array.

For every i

```
left=i+1

right=n-1
```

While

```
left<right
```

Compute

```
sum
```

If

```
sum==0
```

Store answer.

Move both pointers.

Skip duplicates.

If

```
sum<0
```

Move left.

If

```
sum>0
```

Move right.

---

# Complete Dry Run

Input

```
[-1,0,1,2,-1,-4]
```

Sorted

```
[-4,-1,-1,0,1,2]
```

---

## i = 0

```
-4
```

Need

```
4
```

| Left | Right | Sum | Action |
|-------|--------|------|---------|
|-1|2|-3|Left++|
|-1|2|-3|Left++|
|0|2|-2|Left++|
|1|2|-1|Left++|

No answer.

---

## i = 1

```
-1
```

Left

```
-1
```

Right

```
2
```

Sum

```
0
```

Answer

```
[-1,-1,2]
```

Move both.

Now

```
left=0

right=1
```

Again

```
sum=0
```

Answer

```
[-1,0,1]
```

Done.

---

# Duplicate Skipping

This is the MOST IMPORTANT part.

Suppose sorted array

```
[-4,-1,-1,-1,0,1,2]
```

Without duplicate skipping

We'll generate

```
[-1,0,1]

three times.
```

---

## Skip duplicate i

```java
if(i>0 && nums[i]==nums[i-1])
    continue;
```

Meaning

```
Already processed this value.

Skip it.
```

---

## Skip duplicate Left

```java
while(left<right &&
      nums[left]==nums[left-1])
    left++;
```

---

## Skip duplicate Right

```java
while(left<right &&
      nums[right]==nums[right+1])
    right--;
```

---

# Why left-1?

Suppose

```
0 0 0 0
```

After first answer,

left moves

```
1→2
```

Now

```
nums[left]==nums[left-1]
```

means

```
Duplicate.

Skip.
```

---

# Complexity

Sorting

```
O(n log n)
```

Loop

```
O(n²)
```

Overall

```
O(n²)
```

Space

```
O(1)
```

Ignoring answer list.

---

# Java Code (Optimal)

```java
class Solution {

    public List<List<Integer>> threeSum(int[] nums) {

        List<List<Integer>> answer = new ArrayList<>();

        Arrays.sort(nums);

        int n = nums.length;

        for (int i = 0; i < n - 2; i++) {

            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            int left = i + 1;
            int right = n - 1;

            while (left < right) {

                int sum = nums[i] + nums[left] + nums[right];

                if (sum == 0) {

                    answer.add(Arrays.asList(
                            nums[i],
                            nums[left],
                            nums[right]
                    ));

                    left++;
                    right--;

                    while (left < right &&
                            nums[left] == nums[left - 1]) {
                        left++;
                    }

                    while (left < right &&
                            nums[right] == nums[right + 1]) {
                        right--;
                    }

                } else if (sum < 0) {

                    left++;

                } else {

                    right--;
                }
            }
        }

        return answer;
    }
}
```

---

# Why Does Two Pointer Work?

Because array is sorted.

Suppose

```
Current Sum

= 8
```

Need

```
0
```

Largest value is too big.

Only way to reduce sum is

Move right.

Likewise,

If sum is too small,

Move left.

This guarantees we never miss any possible answer.

---

# Comparison

| Approach | Time | Space | Interview |
|------------|---------|---------|--------------|
|Brute Force|O(n³)|O(1)|❌|
|HashSet|O(n²)|O(n)|✅|
|Sorting + Two Pointer|O(n²)|O(1)|⭐⭐⭐|

---

# Common Mistakes

### Forgetting to sort

Two pointer won't work.

---

### Forgetting duplicate skipping

Produces repeated triplets.

---

### Skipping duplicates before storing answer

Wrong.

Always

```
Store

Move pointers

Skip duplicates
```

---

### Using Set in optimal solution

Not required.

Duplicate skipping already handles it.

---

# Edge Cases

```
[]

[1]

[0]

[0,0]

[0,0,0]

[-1,-1,-1]

Large negatives

Large positives

Already sorted array

Reverse sorted array
```

---

# Pattern Recognition

Whenever you see

```
Three numbers

Target sum
```

Think

```
Sort

↓

Fix one number

↓

Two Pointer
```

This pattern also applies to:

- 3Sum Closest
- 4Sum
- k-Sum
- Triangle Count
- Pair Sum in Sorted Array

---

# Interview Questions

### Q1. Why sort the array?

To enable the two-pointer technique and efficiently skip duplicates.

---

### Q2. Why not use HashMap?

Possible, but it uses extra space.

Sorting + Two Pointer achieves the same `O(n²)` time with `O(1)` extra space, making it preferable in interviews.

---

### Q3. Why skip duplicates only after finding an answer?

If you skip earlier, you might miss valid combinations. Once a triplet is recorded, skipping equal adjacent values prevents generating the same triplet again.

---

### Q4. Can this problem be solved in O(n)?

No. For the general 3Sum problem, no algorithm with guaranteed `O(n)` time is known. The best commonly accepted solution is `O(n²)`.

---

# Similar Problems

### Easy

- Two Sum
- Two Sum II

### Medium

- 3Sum Closest
- 4Sum
- Container With Most Water

### Hard

- k-Sum
- Subarrays With K Different Integers

---

# 30-Second Revision

- Sort the array.
- Fix one element at index `i`.
- Use two pointers (`left = i + 1`, `right = n - 1`).
- If sum is too small → `left++`.
- If sum is too large → `right--`.
- If sum is zero:
  - Save the triplet.
  - Move both pointers.
  - Skip duplicates.
- Skip duplicate values for `i`.
- Time: **O(n²)**.
- Space: **O(1)** (excluding the output list).

> **Interview takeaway:** Whenever you see **"find triplets with a target sum"**, think **Sort → Fix one element → Two Pointers**.