# Two Sum using HashMap (LeetCode 1)

> **Difficulty:** Easy  
> **Tags:** Array, HashMap

---

# Problem Statement

Given an array of integers `nums` and an integer `target`, return the **indices** of the two numbers such that they add up to the target.

You may assume:

- Exactly one valid answer exists.
- You cannot use the same element twice.
- Return the indices in any order.

---

# Example 1

### Input

```text
nums = [2,7,11,15]
target = 9
```

### Output

```text
[0,1]
```

### Explanation

```
nums[0] + nums[1]

2 + 7 = 9
```

---

# Example 2

```text
Input

nums = [3,2,4]

target = 6

Output

[1,2]
```

---

# Example 3

```text
Input

nums = [3,3]

target = 6

Output

[0,1]
```

---

# Constraints

```text
2 <= nums.length <= 10^4

-10^9 <= nums[i] <= 10^9

Only one valid answer exists.
```

---

# Understanding the Problem

We need to find **two numbers** whose sum equals the target.

The important thing is:

- We need to return **indices**, not values.
- We cannot use the same element twice.

---

# Approach 1 - Brute Force

## Intuition

Check every possible pair.

For every element,

compare it with every other element.

If the sum equals the target,

return their indices.

---

## Algorithm

```
for i

    for j=i+1

        if nums[i]+nums[j]==target

            return indices
```

---

## Dry Run

Input

```text
nums = [2,7,11,15]
target = 9
```

| i | j | Pair | Sum | Action |
|---|---|------|-----|--------|
|0|1|(2,7)|9|Return [0,1]|

---

## Complexity

### Time

```
O(n²)
```

### Space

```
O(1)
```

---

## Java Code

```java
class Solution {

    public int[] twoSum(int[] nums, int target) {

        int n = nums.length;

        for (int i = 0; i < n; i++) {

            for (int j = i + 1; j < n; j++) {

                if (nums[i] + nums[j] == target) {
                    return new int[]{i, j};
                }
            }
        }

        return new int[]{};
    }
}
```

---

# Approach 2 - HashMap (Optimal) ⭐⭐⭐

## Core Idea

Instead of checking every previous element again and again,

store the elements that we have already visited in a HashMap.

For every current number,

instead of asking

> **"Which number together with me makes the target?"**

calculate the number you need.

That number is called the **complement**.

---

## What is Complement?

Suppose

```text
nums = [2,7,11,15]

target = 9
```

Current number

```text
2
```

Question

```
Which number do I need to make 9?
```

Answer

```
9 - 2 = 7
```

So,

```
Complement = target - currentNumber
```

General Formula

```text
complement = target - nums[i]
```

---

# Why HashMap?

HashMap allows us to check

```
Does this number already exist?
```

in

```
O(1)
```

time.

Instead of searching the whole array again,

we search the HashMap.

---

# What do we store in HashMap?

We store

```text
Key   -> Number

Value -> Index
```

Example

```
nums = [2,7,11]
```

HashMap

```
2 -> 0

7 -> 1

11 -> 2
```

---

# Algorithm

For every element

```
Find complement

If complement exists

Return answer

Else

Store current element
```

---

# Dry Run

Input

```text
nums = [2,7,11,15]

target = 9
```

Initially

```
Map = {}
```

---

## Iteration 1

```
i = 0

Current = 2

Complement = 9 - 2 = 7
```

Map

```
{}
```

Is 7 present?

```
No
```

Store

```
2 → 0
```

Map

```
{2=0}
```

---

## Iteration 2

```
Current = 7

Complement = 2
```

Map

```
{2=0}
```

Found!

Return

```
[0,1]
```

Done.

---

# Complete Dry Run Table

| i | Current | Complement | Map Before | Action | Map After |
|---|----------|------------|------------|--------|-----------|
|0|2|7|{}|Store 2|{2=0}|
|1|7|2|{2=0}|Found 2|Return [0,1]|

---

# Why Do We Check First and Store Later?

This is one of the most frequently asked interview questions.

Suppose

```text
nums = [3,3]

target = 6
```

If we store first,

Map becomes

```
3 -> 0
```

Then we search

```
Complement = 3
```

We find the same element.

That would incorrectly use the same index.

Instead,

we first check

then store.

This guarantees

```
Different indices.
```

---

# Java Code

```java
class Solution {

    public int[] twoSum(int[] nums, int target) {

        Map<Integer, Integer> map = new HashMap<>();

        for (int i = 0; i < nums.length; i++) {

            int complement = target - nums[i];

            if (map.containsKey(complement)) {

                return new int[]{
                        map.get(complement),
                        i
                };
            }

            map.put(nums[i], i);
        }

        return new int[]{};
    }
}
```

---

# Code Explanation

### Create HashMap

```java
Map<Integer, Integer> map = new HashMap<>();
```

Stores

```
Number -> Index
```

---

### Loop through array

```java
for(int i=0;i<nums.length;i++)
```

Visit every element once.

---

### Find complement

```java
int complement = target - nums[i];
```

Find the number required to complete the target.

---

### Check if complement exists

```java
map.containsKey(complement)
```

If yes,

answer found.

---

### Return indices

```java
return new int[]{
    map.get(complement),
    i
};
```

Current index

+

previous complement index.

---

### Otherwise store current element

```java
map.put(nums[i], i);
```

So future elements can use it.

---

# Complexity

### Time

```
O(n)
```

Each element is visited once.

HashMap lookup

```
O(1)
```

Average case.

---

### Space

```
O(n)
```

HashMap stores at most

```
n
```

elements.

---

# Comparison

| Approach | Time | Space | Interview |
|----------|------|--------|-----------|
|Brute Force|O(n²)|O(1)|❌|
|HashMap|O(n)|O(n)|⭐⭐⭐|

---

# Common Mistakes

## Mistake 1

Store first.

```java
map.put(nums[i],i);
```

Then check.

Wrong for

```
[3,3]
```

Always

```
Check

↓

Store
```

---

## Mistake 2

Store

```
Index -> Number
```

Wrong.

Need

```
Number -> Index
```

Because we search using numbers.

---

## Mistake 3

Using nested loops after HashMap.

Not required.

---

# Edge Cases

```
nums=[3,3]

nums=[0,4,3,0]

Negative numbers

Duplicate numbers

Large values

Target = 0
```

---

# Interview Questions

### Q1. Why use HashMap?

Because searching an array takes `O(n)`, while searching a HashMap takes approximately `O(1)` on average, reducing the overall time complexity from `O(n²)` to `O(n)`.

---

### Q2. Why store Number → Index instead of Index → Number?

We need to search by the **number** (the complement). HashMap keys are searched efficiently, so the number must be the key.

---

### Q3. Why calculate the complement?

Instead of searching for every possible pair, we directly compute the only number that can complete the target.

```
complement = target - currentNumber
```

---

### Q4. Why check before storing?

To avoid using the same element twice.

Example:

```
nums = [3,3]
target = 6
```

Checking first ensures we only match with a previously seen index.

---

# Pattern Recognition

Whenever you see

```
Find two elements

Target sum
```

Think

```
HashMap

↓

Complement

↓

One pass
```

---

# Similar Problems

### Easy

- Two Sum II (Sorted Array)
- Contains Duplicate

### Medium

- 3Sum
- 4Sum
- Subarray Sum Equals K

### Hard

- Two Sum Data Structure
- Count of Range Sum

---

# 30-Second Revision

- Store **Number → Index** in a HashMap.
- For every number, calculate:
  ```
  complement = target - nums[i]
  ```
- If the complement is already in the map:
  - Return its index and the current index.
- Otherwise, store the current number and its index.
- **Always check first, then store**.
- Time Complexity: **O(n)**
- Space Complexity: **O(n)**

> **Interview takeaway:** The key insight is to transform the problem from "find two numbers" into "find the complement of the current number" and use a HashMap for constant-time lookups.