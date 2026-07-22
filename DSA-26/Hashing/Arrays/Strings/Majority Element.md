# Majority Element (LeetCode 169)

> **Difficulty:** Easy  
> **Tags:** Array, HashMap, Boyer-Moore Voting Algorithm

---

# Problem Statement

Given an integer array `nums` of size `n`, return the **majority element**.

A **majority element** is an element that appears **more than**:

```text
⌊ n / 2 ⌋
```

times.

You may assume that the majority element **always exists**.

---

# Example 1

### Input

```text
nums = [3,2,3]
```

### Output

```text
3
```

---

# Example 2

### Input

```text
nums = [2,2,1,1,1,2,2]
```

### Output

```text
2
```

---

# Constraints

```text
1 <= nums.length <= 5 * 10⁴

-10⁹ <= nums[i] <= 10⁹
```

---

# Understanding the Problem

A majority element appears **strictly more than half** the size of the array.

Example

```text
n = 7

Majority count > 3

So it must appear at least 4 times.
```

Important guarantee:

> **The majority element always exists.**

This guarantee is what makes the **Boyer-Moore Voting Algorithm** work without requiring a second verification pass.

---

# Approach 1 — Brute Force

## Idea

For every element,

count how many times it appears.

If its count is greater than `n/2`,

return it.

---

## Algorithm

```text
For every element

    Count its frequency

    If frequency > n/2

        Return it
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

    public int majorityElement(int[] nums) {

        int n = nums.length;

        for (int i = 0; i < n; i++) {

            int count = 0;

            for (int j = 0; j < n; j++) {

                if (nums[i] == nums[j]) {
                    count++;
                }
            }

            if (count > n / 2)
                return nums[i];
        }

        return -1;
    }
}
```

---

# Approach 2 — HashMap

## Intuition

Instead of counting repeatedly,

count every element once.

Use a HashMap.

---

# What Does HashMap Store?

```text
Key   -> Number

Value -> Frequency
```

Example

```text
nums = [2,2,1]
```

HashMap

```text
2 -> 2

1 -> 1
```

---

# Algorithm

Traverse array

Increase frequency

If frequency becomes greater than

```text
n/2
```

Return the number.

---

# Dry Run

Input

```text
[2,2,1,1,1,2,2]
```

Initially

```text
Map = {}
```

---

| Number | Map After |
|---------|-----------|
|2|{2=1}|
|2|{2=2}|
|1|{2=2,1=1}|
|1|{2=2,1=2}|
|1|{2=2,1=3}|
|2|{2=3,1=3}|
|2|{2=4,1=3}|

Since

```text
4 > 7/2
```

Answer

```text
2
```

---

# Complexity

### Time

```text
O(n)
```

### Space

```text
O(n)
```

---

# Java Code

```java
class Solution {

    public int majorityElement(int[] nums) {

        Map<Integer, Integer> map = new HashMap<>();

        int limit = nums.length / 2;

        for (int num : nums) {

            map.put(num, map.getOrDefault(num, 0) + 1);

            if (map.get(num) > limit)
                return num;
        }

        return -1;
    }
}
```

---

# Approach 3 — Boyer-Moore Voting Algorithm (Optimal) ⭐⭐⭐

This is the expected interview solution.

---

# Intuition

This algorithm is based on **cancellation**.

Think of every different element as **canceling one occurrence** of the current majority candidate.

Since the real majority element appears **more than n/2 times**, it can never be completely canceled.

Eventually,

only the majority element survives.

---

# Understanding the Cancellation Idea

Suppose

```text
2 2 1
```

Cancel

```text
2

with

1
```

Remaining

```text
2
```

---

Another example

```text
2 2 1 1 1 2 2
```

Cancel one `2` with one `1`

Remaining

```text
2 1 1 2 2
```

Cancel again

```text
2 1
```

Remaining

```text
1 2 2
```

Cancel

```text
1 2
```

Remaining

```text
2
```

The majority element survives all cancellations.

---

# Core Idea

Maintain

```text
Candidate

Count
```

Initially

```text
Candidate = nums[0]

Count = 1
```

Rules

### If current element equals candidate

Increase count.

---

### If current element is different

Decrease count.

---

### If count becomes zero

Previous candidate has been completely canceled.

Choose the current element as the new candidate.

---

# Why Does This Work?

Imagine every occurrence of the majority element is paired with one occurrence of a different element.

Since the majority element appears more than half the time,

there will always be some occurrences left after all possible cancellations.

Those remaining occurrences become the final candidate.

---

# Dry Run

Input

```text
nums = [2,2,1,1,1,2,2]
```

Initially

```text
Candidate = 2

Count = 1
```

| Index | Number | Candidate | Count | Action |
|------|--------|-----------|-------|--------|
|0|2|2|1|Initialize|
|1|2|2|2|Same → Count++|
|2|1|2|1|Different → Count--|
|3|1|2|0|Different → Count--|
|4|1|1|1|Count was 0 → New Candidate|
|5|2|1|0|Different → Count--|
|6|2|2|1|Count was 0 → New Candidate|

Final Candidate

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

    public int majorityElement(int[] nums) {

        int count = 1;
        int maj = nums[0];

        for (int i = 1; i < nums.length; i++) {

            if (count == 0) {

                maj = nums[i];
                count++;

            } else if (nums[i] == maj) {

                count++;

            } else {

                count--;
            }
        }

        return maj;
    }
}
```

---

# Line-by-Line Explanation

### Step 1

```java
int count = 1;
int maj = nums[0];
```

Start by assuming the first element is the majority candidate.

---

### Step 2

```java
if(count == 0)
```

Previous candidate has been completely canceled.

Choose a new candidate.

---

### Step 3

```java
maj = nums[i];
count++;
```

Current number becomes the new candidate.

---

### Step 4

```java
nums[i] == maj
```

Same candidate,

increase support.

```java
count++;
```

---

### Step 5

Different element

```java
count--;
```

This represents cancellation.

---

# Alternative Boyer-Moore Implementation

Many people write it like this:

```java
class Solution {

    public int majorityElement(int[] nums) {

        int candidate = 0;
        int count = 0;

        for (int num : nums) {

            if (count == 0) {
                candidate = num;
            }

            if (candidate == num)
                count++;
            else
                count--;
        }

        return candidate;
    }
}
```

Both implementations are correct.

The second version is more commonly seen in interviews because it doesn't require special initialization.

---

# What If Majority Element Was NOT Guaranteed?

Suppose

```text
[1,2,3,4]
```

Boyer-Moore still returns a candidate.

But

there is actually **no majority element**.

Therefore,

we must verify the candidate.

---

## Verification Step

```java
int frequency = 0;

for(int num : nums){

    if(num == candidate)
        frequency++;
}

if(frequency > nums.length / 2)
    return candidate;

return -1;
```

For LeetCode 169,

this verification is **not needed** because the problem guarantees that a majority element exists.

---

# Complexity

### HashMap

Time

```text
O(n)
```

Space

```text
O(n)
```

---

### Boyer-Moore

Time

```text
O(n)
```

Space

```text
O(1)
```

---

# Comparison

| Approach | Time | Space | Interview |
|----------|------|--------|-----------|
|Brute Force|O(n²)|O(1)|❌|
|HashMap|O(n)|O(n)|✅|
|Boyer-Moore Voting|O(n)|O(1)|⭐⭐⭐|

---

# Common Mistakes

## Mistake 1

Thinking Boyer-Moore works without majority guarantee.

Wrong.

Without the guarantee,

a second verification pass is required.

---

## Mistake 2

Resetting candidate before count becomes zero.

Wrong.

Only when

```java
count == 0
```

should the candidate change.

---

## Mistake 3

Using HashMap when the interviewer asks for constant space.

HashMap uses

```text
O(n)
```

space.

---

# Edge Cases

```text
[1]

[2,2]

[3,3,4]

All elements same

Negative numbers

Large arrays
```

---

# Interview Questions

## Q1. Why does Boyer-Moore work?

Every non-majority element can cancel one occurrence of the majority element.

Since the majority element appears more than half the time,

it can never be fully canceled.

---

## Q2. Why is HashMap not the optimal solution?

HashMap requires

```text
O(n)
```

extra space.

Boyer-Moore achieves

```text
O(1)
```

space.

---

## Q3. What if there is no guaranteed majority element?

Run Boyer-Moore to find a candidate,

then perform one additional pass to verify its frequency.

---

## Q4. Can sorting solve this?

Yes.

After sorting,

the majority element will always occupy the middle position.

Time

```text
O(n log n)
```

Space

Depends on sorting implementation.

Not optimal.

---

# Pattern Recognition

Whenever you hear

```text
Majority

More than n/2

Most frequent

Constant space
```

Think

```text
Boyer-Moore Voting Algorithm
```

---

# Similar Problems

### Easy

- Majority Element II (variation)
- Find the Duplicate Number

### Medium

- Majority Element II (n/3)
- Top K Frequent Elements

### Hard

- Majority Checker
- Online Majority Element

---

# 30-Second Revision

- **Brute Force:** Count every element → `O(n²)`.
- **HashMap:** Store frequency → `O(n)` time, `O(n)` space.
- **Boyer-Moore:** Maintain a candidate and a count.
- Same element → `count++`.
- Different element → `count--`.
- When `count == 0`, choose a new candidate.
- If the problem does **not** guarantee a majority element, verify the candidate in a second pass.
- Time Complexity: **O(n)**
- Space Complexity: **O(1)**

> **Interview takeaway:** Boyer-Moore is based on the idea that the majority element cannot be completely canceled by all other elements combined because it appears more than half the time.