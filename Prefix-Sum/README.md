# Prefix Sum — Complete DSA Notes (Java)

## 1. What Is Prefix Sum?

**Prefix Sum is a technique used to calculate the sum of elements in a subarray efficiently by precomputing cumulative sums.**

Instead of calculating the sum of elements repeatedly, we store the running sum.

### Example

```java
int[] nums = {2, 4, 1, 3, 5};
```

Prefix sums:

| Index | Element | Prefix Sum |
|---|---|---|
| 0 | 2 | 2 |
| 1 | 4 | 6 |
| 2 | 1 | 7 |
| 3 | 3 | 10 |
| 4 | 5 | 15 |

```text
Original:   [2, 4, 1, 3, 5]
Prefix Sum: [2, 6, 7, 10, 15]
```

**Key idea:** Each prefix sum represents the sum of all elements from the beginning of the array up to the current index.

---

## 2. Why Do We Need Prefix Sum?

Suppose we want the sum of elements from index `1` to `3`.

```java
nums = [2, 4, 1, 3, 5]
```

Required sum:

```text
4 + 1 + 3 = 8
```

### Brute Force

```java
int sum = 0;

for (int i = left; i <= right; i++) {
    sum += nums[i];
}
```

- Each range-sum query takes O(n) in the worst case.
- For `q` queries, total time is O(n × q).

### Prefix Sum Approach

Precompute the cumulative sums once.

```text
prefix = [2, 6, 7, 10, 15]
```

Now:

```text
sum(1, 3) = prefix[3] - prefix[0]
          = 10 - 2
          = 8
```

**Advantage:** After preprocessing, each range-sum query takes O(1) time.

---

## 3. Building a Prefix Sum Array

### Approach 1: Prefix Array of Size n

```java
int[] nums = {2, 4, 1, 3, 5};

int[] prefix = new int[nums.length];

prefix[0] = nums[0];

for (int i = 1; i < nums.length; i++) {
    prefix[i] = prefix[i - 1] + nums[i];
}
```

**Formula:**

```text
prefix[i] = prefix[i - 1] + nums[i]
```

### Complexity

- Time: O(n)
- Space: O(n)

### Important Formula: Range Sum

For a subarray from index `left` to `right` (inclusive):

```java
sum = prefix[right] - (left > 0 ? prefix[left - 1] : 0);
```

**Why subtract `prefix[left - 1]`?**

Because `prefix[right]` includes all elements from index 0 to `right`. We remove the elements before `left`.

---

## 4. Prefix Array of Size n + 1 (Recommended for Range Queries)

Another approach is to create an extra element at index 0.

```java
int[] nums = {2, 4, 1, 3, 5};

int[] prefix = new int[nums.length + 1];

for (int i = 0; i < nums.length; i++) {
    prefix[i + 1] = prefix[i] + nums[i];
}
```

Result:

```text
nums:    [2, 4, 1, 3, 5]
prefix:  [0, 2, 6, 7, 10, 15]
```

### Formula

```java
sum = prefix[right + 1] - prefix[left];
```

Example:

```text
sum(1, 3) = prefix[4] - prefix[1]
          = 10 - 2
          = 8
```

### Why Is This Approach Useful?

- Avoids special handling when `left == 0`.
- Makes range-sum calculations cleaner.
- Often used in LeetCode 303 (Range Sum Query – Immutable).

**Remember:** Both prefix array conventions are correct. Just don't mix their formulas.

---

# 5. Prefix Sum Without an Extra Array

Sometimes we don't need to store every prefix sum.

We can maintain a running sum.

```java
int prefixSum = 0;

for (int num : nums) {
    prefixSum += num;
}
```

### When to Use

Use a running sum when:

- We only need the current cumulative sum.
- We are combining prefix sum with a HashMap.
- We don't need to answer arbitrary historical range-sum queries.

**Complexity:**
- Time: O(n)
- Extra space: O(1)

This is especially useful in subarray problems.

---

# 6. Prefix Sum + HashMap (Very Important)

This is one of the most important prefix-sum patterns for DSA interviews.

It helps find subarrays satisfying a particular sum condition, even when negative numbers are present.

## Core Concept

Suppose:

```text
nums = [1, 2, 3, -2, 2]
target = 3
```

We want to identify subarrays whose sum equals `3`.

Instead of checking every possible subarray, maintain a running prefix sum.

### Mathematical Idea

For a subarray starting at index `left` and ending at `right`:

```text
subarraySum = prefix[right] - prefix[left - 1]
```

If the required subarray sum is `k`:

```text
prefix[right] - previousPrefix = k
```

Rearranging:

```text
previousPrefix = prefix[right] - k
```

**This is the most important prefix-sum formula for HashMap problems.**

```java
required = prefixSum - k;
```

### Why HashMap?

The HashMap helps us quickly determine whether the required previous prefix sum has appeared before.

Average lookup time: O(1).

---

## 7. Pattern 1: Count Subarrays With Sum K

**Example problem:** LeetCode 560 — Subarray Sum Equals K

### Approach

1. Initialize `prefixSum = 0`.
2. Use a HashMap to store **prefix sum → frequency**.
3. Initialize the map with `{0: 1}`.
4. Traverse the array.
5. Add the current element to `prefixSum`.
6. Calculate `required = prefixSum - k`.
7. If `required` exists, add its frequency to the answer.
8. Store/update the frequency of the current `prefixSum`.

### Java Code

```java
public int subarraySum(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();

    map.put(0, 1);

    int prefixSum = 0;
    int count = 0;

    for (int num : nums) {
        prefixSum += num;

        int required = prefixSum - k;

        count += map.getOrDefault(required, 0);

        map.put(
            prefixSum,
            map.getOrDefault(prefixSum, 0) + 1
        );
    }

    return count;
}
```

### Dry Run

```text
nums = [1, 2, 1, 2]
k = 3
```

Initially:

```text
map = {0=1}
prefixSum = 0
count = 0
```

| Element | Prefix Sum | Required (prefix-k) | Frequency Found | Count |
|---|---|---|---|---|
| 1 | 1 | -2 | 0 | 0 |
| 2 | 3 | 0 | 1 | 1 |
| 1 | 4 | 1 | 1 | 2 |
| 2 | 6 | 3 | 1 | 3 |

Final answer: **3 subarrays**.

```text
[1, 2]
   [2, 1]
      [1, 2]
```

### Why `map.put(0, 1)`?

It represents one empty prefix before the array begins.

Example:

```text
nums = [1, 2]
k = 3
```

When the running sum becomes `3`:

```text
required = 3 - 3 = 0
```

The initial `{0=1}` allows us to count the subarray starting from index 0.

### Why Store Frequencies?

Different previous positions may have the same prefix sum.

Example:

```text
nums = [0, 0, 0]
k = 0
```

All six non-empty subarrays have sum 0.

A Boolean check or simple HashSet cannot count all of them.

**Important:** For counting subarrays, use prefix-sum frequencies, not just existence.

### Complexity

- Time: O(n) average.
- Space: O(n).

---

# 8. Pattern 2: Longest Subarray With Sum K

**Example:** Find the maximum length of a contiguous subarray whose sum equals `k`.

### Core Idea

Use the same complement formula:

```text
required = prefixSum - k
```

But instead of storing frequencies, store the **earliest index** where each prefix sum appeared.

### Why Earliest Index?

To maximize subarray length, we want the greatest distance between the current index and a previous matching prefix sum.

### Approach

1. Initialize `prefixSum = 0`.
2. Store `{0: -1}` in the HashMap.
3. Traverse the array.
4. Calculate the running sum and required prefix sum.
5. If the required sum exists, calculate the possible subarray length.
6. Update the maximum length.
7. Store the current prefix sum only if it hasn't appeared before.

### Java Code

```java
public int longestSubarray(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();

    map.put(0, -1);

    int prefixSum = 0;
    int maxLength = 0;

    for (int i = 0; i < nums.length; i++) {
        prefixSum += nums[i];

        int required = prefixSum - k;

        if (map.containsKey(required)) {
            int length = i - map.get(required);
            maxLength = Math.max(maxLength, length);
        }

        map.putIfAbsent(prefixSum, i);
    }

    return maxLength;
}
```

### Key Difference from Subarray Sum Equals K

| Requirement | What HashMap Stores |
|---|---|
| Count subarrays | Prefix sum → Frequency |
| Longest subarray | Prefix sum → Earliest index |
| Detect existence | Previously seen prefix sums |

### Complexity

- Time: O(n) average.
- Space: O(n).

---

# 9. Pattern 3: Subarrays Divisible by K

**Example problem:** LeetCode 974 — Subarray Sums Divisible by K

### Core Concept

A subarray sum is divisible by `k` if the two prefix sums have the same remainder modulo `k`.

```text
(prefixRight - prefixLeft) % k == 0
```

Therefore:

```text
prefixRight % k == prefixLeft % k
```

### Approach

1. Maintain a running prefix sum.
2. Calculate its remainder when divided by `k`.
3. Track the frequency of previously encountered remainders.
4. Matching remainders indicate subarrays divisible by `k`.

### Important Java Detail

Java's `%` operator can return negative remainders.

Normalize using:

```java
int remainder = ((prefixSum % k) + k) % k;
```

Assumes `k > 0`.

### Java Code

```java
public int subarraysDivByK(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();

    map.put(0, 1);

    int prefixSum = 0;
    int count = 0;

    for (int num : nums) {
        prefixSum += num;

        int remainder = ((prefixSum % k) + k) % k;

        count += map.getOrDefault(remainder, 0);

        map.put(
            remainder,
            map.getOrDefault(remainder, 0) + 1
        );
    }

    return count;
}
```

### Complexity

- Time: O(n) average.
- Space: O(min(n, k)) for positive `k`, using remainder frequencies.

---

# 10. Prefix Sum vs Sliding Window

Both techniques help solve subarray problems, but they are not interchangeable.

| Feature | Prefix Sum | Sliding Window |
|---|---|---|
| Main idea | Track cumulative sums | Maintain a moving range |
| Range-sum queries | Excellent | Not ideal for arbitrary queries |
| Negative numbers | Works | Standard sum-based shrinking logic may fail |
| Subarray sum equals K | HashMap pattern | Often suitable when all numbers are positive |
| Time | Usually O(n) | Often O(n) |
| Space | O(n) with HashMap | Often O(1) |

### Why Can Sliding Window Fail With Negative Numbers?

Consider:

```text
nums = [2, -1, 2]
target = 3
```

With positive numbers:

- Expanding the window increases the sum.
- Shrinking the window decreases the sum.

But with negative numbers, this behavior is not guaranteed.

Therefore, the usual expand/shrink logic may fail.

**Remember:** Prefix Sum + HashMap is generally more flexible for exact-sum subarray problems involving negative numbers.

---

# 11. Prefix Sum vs Two Sum Hashing

Both patterns use complements, but they solve different problems.

### Two Sum

```java
required = target - nums[i];
```

We search for an individual element that complements the current number.

### Prefix Sum + HashMap

```java
required = prefixSum - target;
```

We search for a previous cumulative sum that identifies a valid **contiguous subarray**.

| Pattern | HashMap Stores | Used For |
|---|---|---|
| Two Sum | Individual elements | Finding pairs |
| Prefix Sum | Cumulative sums | Finding contiguous subarrays |

**Remember:** Two Sum finds matching elements; Prefix Sum finds matching subarray boundaries.

---

# 12. 2D Prefix Sum (Matrix)

Prefix Sum also works with two-dimensional arrays.

Used for quickly calculating the sum of a rectangular region within a matrix.

### Formula

Using a 1-indexed prefix matrix:

```text
prefix[r][c] =
    matrix[r-1][c-1]
    + prefix[r-1][c]
    + prefix[r][c-1]
    - prefix[r-1][c-1]
```

Why subtract the last term?

Because the overlapping top-left region was added twice.

### Rectangle Sum

For a rectangle with zero-based inclusive boundaries `(r1, c1)` to `(r2, c2)`:

```text
sum =
    prefix[r2+1][c2+1]
    - prefix[r1][c2+1]
    - prefix[r2+1][c1]
    + prefix[r1][c1]
```

### Complexity

- Preprocessing: O(rows × columns).
- Each rectangle-sum query: O(1).
- Space: O(rows × columns).

**Practice:** LeetCode 304 — Range Sum Query 2D – Immutable.

---

# 13. Difference Array — Related Concept

A difference array is closely related to prefix sums, but solves a different kind of problem.

- **Prefix Sum:** Efficiently query range sums.
- **Difference Array:** Efficiently apply additions to entire ranges.

For example, to add `5` to all elements from index `left` to `right`:

```java
diff[left] += 5;

if (right + 1 < diff.length) {
    diff[right + 1] -= 5;
}
```

After processing all range updates, calculate the running sum of the difference array to reconstruct the updated values.

### When to Use
- Many range-increment operations.
- Range updates followed by reconstruction.
- Problems like LeetCode 1109 — Corporate Flight Bookings.

**Note:** Difference arrays are worth learning after mastering basic prefix sums.

---

# 14. Common Mistakes

### Mistake 1: Incorrect Range Formula

For an inclusive prefix array:

```java
sum = prefix[right] - prefix[left - 1];
```

This fails when `left == 0`.

Use a boundary check or a prefix array of size `n + 1`.

### Mistake 2: Forgetting Initial HashMap Entry

For count-based problems:

```java
map.put(0, 1);
```

For longest-subarray problems:

```java
map.put(0, -1);
```

These represent the empty prefix.

### Mistake 3: Updating HashMap Too Early

For counting non-empty subarrays, check for the required previous prefix **before** inserting the current prefix.

Otherwise, especially when `k == 0`, you may incorrectly count an empty subarray.

### Mistake 4: Confusing Count and Length

- Counting subarrays → Store frequencies.
- Longest subarray → Store earliest indices.

### Mistake 5: Using Sliding Window With Negative Numbers

A standard variable-size sliding window for exact target sums is not generally reliable when negative numbers exist.

### Mistake 6: Integer Overflow

Prefix sums may exceed Java's `int` range even when individual elements fit within it.

Use `long` when needed:

```java
long prefixSum = 0L;
Map<Long, Integer> map = new HashMap<>();
```

Also consider whether the number of matching subarrays can exceed `Integer.MAX_VALUE`.

### Mistake 7: Confusing Subarray and Subsequence

- **Subarray:** Elements must be contiguous.
- **Subsequence:** Elements may be skipped while maintaining order.

Prefix Sum is primarily useful for contiguous ranges.

---

# 15. How to Recognize Prefix Sum Problems

Look for these clues:

| Problem Statement | Pattern |
|---|---|
| Sum of elements between indices L and R | Prefix Array |
| Multiple range-sum queries | Prefix Array of size n+1 |
| Count subarrays with sum K | Prefix Sum + Frequency HashMap |
| Longest subarray with sum K | Prefix Sum + Earliest Index HashMap |
| Check if a subarray sum equals K | Prefix Sum + HashSet/HashMap |
| Count subarrays divisible by K | Prefix Sum + Remainder Frequency |
| Equal number of 0s and 1s | Transform 0 to -1 + Prefix Sum |
| Rectangle sum in a matrix | 2D Prefix Sum |
| Multiple range additions | Difference Array |

### Golden Rule

Whenever you see:

**"Find/count/measure contiguous subarrays satisfying a sum condition"**

Ask yourself:

> Can I express the required subarray using the difference between two prefix sums?

---

# 16. Important Prefix Sum Problems

Practice in this order:

| Order | LeetCode | Problem | Main Concept |
|---|---|---|---|
| 1 | 1480 | Running Sum of 1D Array | Basic prefix sum |
| 2 | 303 | Range Sum Query – Immutable | Prefix array |
| 3 | 560 | Subarray Sum Equals K | Prefix + HashMap frequency |
| 4 | 525 | Contiguous Array | Transform + prefix sum |
| 5 | 974 | Subarray Sums Divisible by K | Prefix remainder |
| 6 | 523 | Continuous Subarray Sum | Remainder + indices |
| 7 | 930 | Binary Subarrays With Sum | Prefix frequency / sliding window |
| 8 | 304 | Range Sum Query 2D – Immutable | 2D prefix sum |
| 9 | 1109 | Corporate Flight Bookings | Difference array |

### Important Practice Clues (No Solutions)

**1480 — Running Sum**
- Think about carrying forward the previous cumulative sum.

**303 — Range Sum Query**
- Precompute once, answer many queries efficiently.

**560 — Subarray Sum Equals K**
- Look for `prefixSum - k` in previously seen prefix sums.
- Think about why frequencies matter.

**525 — Contiguous Array**
- What happens if you convert every `0` into `-1`?
- What does a subarray sum of zero represent?

**974 — Subarray Sums Divisible by K**
- Think about two prefix sums having equal remainders.

**523 — Continuous Subarray Sum**
- Store the earliest position of each remainder.
- Remember the minimum required subarray length.

**930 — Binary Subarrays With Sum**
- Compare prefix-sum counting with the special properties of binary arrays.

---

# 17. Quick Revision Cheat Sheet

### Basic Prefix Sum

```java
prefix[i + 1] = prefix[i] + nums[i];
```

### Range Sum (n+1 Prefix)

```java
sum = prefix[right + 1] - prefix[left];
```

### Prefix Sum + HashMap

```java
required = prefixSum - k;
```

### Count Subarrays

```java
map.put(0, 1);
count += map.getOrDefault(required, 0);
```

HashMap stores **frequency**.

### Longest Subarray

```java
map.put(0, -1);
maxLength = Math.max(maxLength, i - map.get(required));
```

HashMap stores **earliest index**.

### Subarrays Divisible by K

```java
remainder = ((prefixSum % k) + k) % k;
```

Track frequencies of remainders.

### Complexity Summary

| Pattern | Time | Extra Space |
|---|---|---|
| Build prefix array | O(n) | O(n) |
| Range sum after preprocessing | O(1) | O(1) per query |
| Running sum only | O(n) | O(1) |
| Prefix + HashMap | O(n) average | O(n) |
| 2D prefix preprocessing | O(R × C) | O(R × C) |
| 2D rectangle query | O(1) | O(1) per query |

---

## Final Mental Model

**Prefix Sum = Sum from the beginning up to a position.**

**Subarray Sum = Difference between two prefix sums.**

**Prefix Sum + HashMap = Quickly find previous prefix sums that satisfy a condition.**

### Remember These Three Questions

1. Can I calculate the answer using two prefix sums?
2. Do I need to store prefix-sum frequencies or indices?
3. Am I counting subarrays, finding the longest one, or answering range queries?

