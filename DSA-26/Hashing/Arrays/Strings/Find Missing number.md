# Missing Number (LeetCode 268)

> **Difficulty:** Easy  
> **Tags:** Array, Math, Bit Manipulation

---

# Problem Statement

Given an array `nums` containing `n` distinct numbers in the range:

```text
[0, n]
```

Return the **only number** in the range that is missing from the array.

---

# Example 1

### Input

```text
nums = [3,0,1]
```

### Output

```text
2
```

---

# Example 2

### Input

```text
nums = [0,1]
```

### Output

```text
2
```

---

# Example 3

### Input

```text
nums = [9,6,4,2,3,5,7,0,1]
```

### Output

```text
8
```

---

# Constraints

```text
n == nums.length

1 <= n <= 10⁴

0 <= nums[i] <= n

All numbers are unique.
```

---

# Understanding the Problem

Suppose

```text
n = 5
```

Then valid numbers are

```text
0 1 2 3 4 5
```

Array contains only

```text
5
```

numbers.

Exactly **one number is missing**.

Need to find it.

---

# Approach 1 — Brute Force

## Idea

For every number from

```text
0 → n
```

search the entire array.

If not found,

return it.

---

## Algorithm

```text
For number = 0 to n

    Search array

    If not found

        Return number
```

---

## Dry Run

Input

```text
nums = [3,0,1]
```

Check

```text
0 → Found

1 → Found

2 → Not Found
```

Answer

```text
2
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

    public int missingNumber(int[] nums) {

        int n = nums.length;

        for (int number = 0; number <= n; number++) {

            boolean found = false;

            for (int num : nums) {

                if (num == number) {
                    found = true;
                    break;
                }
            }

            if (!found)
                return number;
        }

        return -1;
    }
}
```

---

# Approach 2 — Sorting

## Idea

Sort the array.

After sorting,

every index should contain the same number.

If

```text
index != value
```

that index is missing.

---

## Dry Run

Input

```text
[3,0,1]
```

Sorted

```text
[0,1,3]
```

| Index | Value |
|------|------|
|0|0|
|1|1|
|2|3|

Index

```text
2
```

doesn't match value.

Missing number

```text
2
```

---

## Complexity

Time

```text
O(n log n)
```

Space

Depends on sorting.

---

## Java Code

```java
class Solution {

    public int missingNumber(int[] nums) {

        Arrays.sort(nums);

        for (int i = 0; i < nums.length; i++) {

            if (nums[i] != i)
                return i;
        }

        return nums.length;
    }
}
```

---

# Approach 3 — HashSet

## Idea

Store every number.

Then check from

```text
0 → n
```

Which one is missing?

---

## Dry Run

Input

```text
[3,0,1]
```

HashSet

```text
{0,1,3}
```

Check

```text
0 ✓

1 ✓

2 ✗
```

Answer

```text
2
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

    public int missingNumber(int[] nums) {

        Set<Integer> set = new HashSet<>();

        for (int num : nums)
            set.add(num);

        for (int i = 0; i <= nums.length; i++) {

            if (!set.contains(i))
                return i;
        }

        return -1;
    }
}
```

---

# Approach 4 — Sum Formula ⭐⭐

## Intuition

Numbers are

```text
0...n
```

Their total sum is known.

Formula

```text
Sum = n × (n + 1) / 2
```

Subtract every array element.

Remaining value is the missing number.

---

# Formula

```
Expected Sum

−

Actual Sum

=

Missing Number
```

---

## Dry Run

Input

```text
nums = [3,0,1]
```

Expected Sum

```text
3 × 4 /2 = 6
```

Actual Sum

```text
3+0+1=4
```

Missing

```text
6-4=2
```

---

## Complexity

### Time

```text
O(n)
```

### Space

```text
O(1)
```

---

## Java Code

```java
class Solution {

    public int missingNumber(int[] nums) {

        int n = nums.length;

        int expectedSum = n * (n + 1) / 2;

        int actualSum = 0;

        for (int num : nums)
            actualSum += num;

        return expectedSum - actualSum;
    }
}
```

---

# Common Mistake in Sum Formula ❌

Many beginners write:

```java
for(int i=0;i<n;i++){

    res = sum - nums[i];
}
```

This is **incorrect**.

It only subtracts the **last element** because `res` gets overwritten in every iteration.

### Wrong

```java
res = sum - nums[i];
```

### Correct

```java
actualSum += nums[i];
```

Then

```java
return expectedSum - actualSum;
```

---

# Approach 5 — XOR (Optimal) ⭐⭐⭐

This is the most elegant interview solution.

---

# XOR Properties

```
A ^ A = 0
```

```
A ^ 0 = A
```

```
XOR is commutative
```

Meaning

```
a^b^c

=

c^a^b
```

Order doesn't matter.

---

# Intuition

Suppose

```text
0 1 2 3
```

Array

```text
3 0 1
```

Take XOR of

```
All indices

AND

All numbers
```

Every number cancels itself.

Only missing number remains.

---

# Dry Run

Input

```text
nums=[3,0,1]
```

Expected numbers

```text
0^1^2^3
```

Array

```text
3^0^1
```

Combined

```text
0^1^2^3^3^0^1
```

Cancel

```text
0^0

1^1

3^3
```

Remain

```text
2
```

Answer

```text
2
```

---

# Java Code

```java
class Solution {

    public int missingNumber(int[] nums) {

        int xor = nums.length;

        for (int i = 0; i < nums.length; i++) {

            xor ^= i;
            xor ^= nums[i];
        }

        return xor;
    }
}
```

---

# Why Initialize with n?

Suppose

```text
nums = [3,0,1]
```

Indices

```text
0 1 2
```

Need XOR of

```text
0 1 2 3
```

Loop only gives

```text
0 1 2
```

So initialize

```java
xor = n;
```

to include the last number.

---

# Complexity

### Time

```text
O(n)
```

### Space

```text
O(1)
```

---

# Approach 6 — Cyclic Sort ⭐⭐⭐

Works because numbers belong to

```text
0...n
```

Each number should be at its own index.

Example

```
index = value
```

Swap every number to its correct position.

Finally,

the index where

```text
nums[i] != i
```

is the missing number.

---

## Dry Run

Input

```text
[3,0,1]
```

After cyclic sort

```text
[0,1,3]
```

Index

```text
2
```

contains

```text
3
```

Answer

```text
2
```

---

## Java Code

```java
class Solution {

    public int missingNumber(int[] nums) {

        int i = 0;

        while (i < nums.length) {

            int correct = nums[i];

            if (correct < nums.length &&
                nums[i] != nums[correct]) {

                int temp = nums[i];
                nums[i] = nums[correct];
                nums[correct] = temp;

            } else {

                i++;
            }
        }

        for (i = 0; i < nums.length; i++) {

            if (nums[i] != i)
                return i;
        }

        return nums.length;
    }
}
```

---

# Complexity

Time

```text
O(n)
```

Space

```text
O(1)
```

But it **modifies the array**, so use only when allowed.

---

# Comparison

| Approach | Time | Space | Modifies Array | Interview |
|----------|------|--------|---------------|-----------|
|Brute Force|O(n²)|O(1)|No|❌|
|Sorting|O(n log n)|Depends|Yes|❌|
|HashSet|O(n)|O(n)|No|✅|
|Sum Formula|O(n)|O(1)|No|⭐⭐|
|XOR|O(n)|O(1)|No|⭐⭐⭐|
|Cyclic Sort|O(n)|O(1)|Yes|⭐⭐|

---

# Which Approach Should You Use?

### If interviewer says:

> Don't use extra space.

Use

```text
Sum Formula
```

or

```text
XOR
```

---

### If interviewer says:

> Don't modify array.

Avoid

```text
Cyclic Sort
```

---

### If interviewer asks:

> Most elegant solution?

Answer

```text
XOR
```

---

# Common Mistakes

## Mistake 1

Using

```java
res = sum - nums[i];
```

instead of accumulating the array sum.

---

## Mistake 2

Forgetting to initialize

```java
xor = nums.length;
```

---

## Mistake 3

Using Cyclic Sort when the problem says not to modify the array.

---

# Edge Cases

```text
[0]

[1]

Missing 0

Missing n

Large array

Already sorted

Reverse sorted
```

---

# Interview Questions

## Q1. Why does XOR work?

Every number appears exactly twice except the missing one.

Since

```
A ^ A = 0
```

all duplicates cancel.

---

## Q2. Why initialize XOR with n?

Because indices only go from

```
0 → n-1
```

Need to include

```
n
```

manually.

---

## Q3. Which solution is preferred?

Usually

```
XOR
```

or

```
Sum Formula
```

Both are

```
O(n)

O(1)
```

---

## Q4. Which one is easier to explain?

The **Sum Formula** is easier and more intuitive.

The **XOR** solution is often appreciated in interviews because it demonstrates knowledge of bit manipulation.

---

# Pattern Recognition

Whenever you see

```text
Numbers from 0...n

One missing

No duplicates
```

Think

- Sum Formula
- XOR
- Cyclic Sort

---

# Similar Problems

### Easy

- Find Missing and Repeated Values
- Single Number

### Medium

- First Missing Positive
- Find All Numbers Disappeared in an Array

### Hard

- Missing Number in Arithmetic Progression

---

# 30-Second Revision

- Brute Force → Search every number.
- Sorting → Compare index with value.
- HashSet → Store all numbers and find the missing one.
- Sum Formula:
  ```
  n(n+1)/2 - actualSum
  ```
- XOR:
  ```
  xor = n

  xor ^= i

  xor ^= nums[i]
  ```
- Cyclic Sort:
  - Place every number at its correct index.
  - First mismatch is the answer.
- Best interview solutions:
  - **Sum Formula**
  - **XOR**