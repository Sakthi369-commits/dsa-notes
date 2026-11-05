# Sliding Window — Complete DSA Learning Guide (Java)

Let's learn Sliding Window from the ground up, but without wasting time revisiting every basic DSA concept.

Since you're building your DSA knowledge for Java developer interviews and want to maintain notes in GitHub, I'll structure this as a learning document that you can directly convert into Markdown (`sliding-window.md`).

The goal is not just to memorize solutions. By the end, you should be able to:

1. Understand what Sliding Window is and why we use it.
2. Recognize when a problem requires Sliding Window.
3. Understand Fixed Size and Variable Size Sliding Window.
4. Write both approaches in Java without memorizing code.
5. Understand the time and space complexity.
6. Recognize common interview patterns.
7. Solve problems independently, starting from easy to medium.

# 1. What is Sliding Window?

Sliding Window is a problem-solving technique used to process a contiguous portion of an array or string efficiently.

Instead of repeatedly calculating information for every possible subarray or substring, we maintain a window and move it through the input.

Let's understand that.

Suppose we have this array:

```
int[] arr = {2, 1, 5, 1, 3, 2};
```

Our task:

Find the maximum sum of any 3 consecutive elements.

A straightforward approach would calculate the sum of every possible group of three elements.

Array visualization


## Example: Maximum Sum of 3 Consecutive Elements

Given:

`arr = [2, 1, 5, 1, 3, 2]`

`k = 3`

Find the maximum sum of any 3 consecutive elements.

### Window movements

**Window 1 — Initial window**

| 2 | 1 | 5 | 1 | 3 | 2 |
|---|---|---|---|---|---|
| 🔵 | 🔵 | 🔵 | | | |

Sum = 2 + 1 + 5 = **8**

**Window 2 — Slide right**

| 2 | 1 | 5 | 1 | 3 | 2 |
|---|---|---|---|---|---|
| | 🔵 | 🔵 | 🔵 | | |

Remove 2, add 1.

New sum = 8 - 2 + 1 = **7**

**Window 3 — Slide right**

| 2 | 1 | 5 | 1 | 3 | 2 |
|---|---|---|---|---|---|
| | | 🔵 | 🔵 | 🔵 | |

Remove 1, add 3.

New sum = 7 - 1 + 3 = **9**

**Window 4 — Slide right**

| 2 | 1 | 5 | 1 | 3 | 2 |
|---|---|---|---|---|---|
| | | | 🔵 | 🔵 | 🔵 |

Remove 5, add 2.

New sum = 9 - 5 + 2 = **6**

### Final result

| Window | Elements | Sum |
|---|---|---:|
| 1 | [2, 1, 5] | 8 |
| 2 | [1, 5, 1] | 7 |
| 3 | [5, 1, 3] | 9 |
| 4 | [1, 3, 2] | 6 |

**Maximum Sum = 9**

### Core formula

`New Window Sum = Previous Sum - Outgoing Element + Incoming Element`

Notice something?

When we move from the first window to the second window:

- The element `2` leaves the window.
- The element `1` enters the window.
- We don't need to calculate the entire sum again.

Instead:

\NewSum = OldSum - Outgoing + Incoming \

That is the fundamental idea behind Sliding Window.

## 2. Why do we need Sliding Window?

Let's compare two approaches.

Approach 1: Brute Force

For every possible group of 3 elements, calculate its sum from scratch.

```
int maxSum = Integer.MIN_VALUE;

for (int i = 0; i <= arr.length - 3; i++) {

    int sum = 0;

    for (int j = i; j < i + 3; j++) {
        sum += arr[j];
    }

    maxSum = Math.max(maxSum, sum);
}
```

Time complexity:

O(n * k)

Here:

- `n` = array length
- `k` = window size

Approach 2: Sliding Window

```
int k = 3;

int windowSum = 0;

// Calculate the first window
for (int i = 0; i < k; i++) {
    windowSum += arr[i];
}

int maxSum = windowSum;

// Slide the window
for (int i = k; i < arr.length; i++) {

    windowSum += arr[i];
    windowSum -= arr[i - k];

    maxSum = Math.max(maxSum, windowSum);
}
```

Time complexity:

O(n)

| Approach       | Time complexity     |
| -------------- | ------------------- |
| Brute Force    | O(n * k) |
| Sliding Window | O(n)         |

Important: Sliding Window is useful because we reuse information from the previous window instead of recalculating everything.

# 3. Types of Sliding Window

There are two major patterns you must understand.

## Sliding Window

1\. Fixed Size Window

Window size remains constant (K).

2\. Variable Size Window

Window expands and shrinks based on a condition.

Let's understand both in detail.

# 4. Fixed Size Sliding Window

In Fixed Size Sliding Window, the window size is given in the problem and remains constant throughout the process.

For example:

- Maximum sum of K consecutive elements.
- Average of every K consecutive elements.
- Maximum number of vowels in a substring of length K.
- First negative number in every window of size K.

### How does it work?

Suppose:

```
int[] arr = {2, 1, 5, 1, 3, 2};
int k = 3;
```

We need to find the maximum sum of 3 consecutive elements.

The algorithm follows three steps:

1. Calculate the sum of the first K elements.
2. Move the window one position forward.
3. Add the new element and remove the element that left the window.

### Java template

```
int windowSum = 0;

// Step 1: Build first window
for (int i = 0; i < k; i++) {
    windowSum += arr[i];
}

int answer = windowSum;

// Step 2: Slide the window
for (int i = k; i < arr.length; i++) {

    // Add incoming element
    windowSum += arr[i];

    // Remove outgoing element
    windowSum -= arr[i - k];

    // Update answer
    answer = Math.max(answer, windowSum);
}
```

### Understand the indices

This is particularly important.

| Expression  | Meaning                |
| ----------- | ---------------------- |
| `i`         | Incoming element index |
| `i - k`     | Outgoing element index |
| `k`         | Fixed window size      |
| `windowSum` | Current window sum     |

Why `i - k`?

When `i = 3` and `k = 3`:

- Incoming index = 3
- Outgoing index = 3 - 3 = 0

So we remove `arr[0]` and add `arr[3]`.

The window moves from indices `[0, 1, 2]` to `[1, 2, 3]`.

# 5. Variable Size Sliding Window

This is where Sliding Window becomes more interesting.

In Variable Size Sliding Window, the window size is not fixed.

We expand and shrink the window depending on a condition.

For example:

Find the length of the smallest subarray whose sum is greater than or equal to 7.

Given:

```
int[] arr = {2, 3, 1, 2, 4, 3};
int target = 7;
```

Possible valid subarrays:

- `[2, 3, 1, 2]` → sum 8 → length 4
- `[3, 1, 2, 4]` → sum 10 → length 4
- `[4, 3]` → sum 7 → length 2

Answer = 2.

## 5.1 How does Variable Size Sliding Window work?

We generally use two pointers:

- `left` – starting index of the window.
- `right` – ending index of the window.

We also maintain a variable such as `sum`, which represents the current window's sum.

The process:

1. Expand the window by moving `right`.
2. Add the new element to the current sum.
3. If the condition is satisfied, update the answer.
4. Shrink the window by moving `left` when necessary.
5. Repeat until `right` reaches the end.


### Visualize the Process

**Problem:** Find the smallest subarray with sum >= 7.

Given:

`Array = [2, 3, 1, 2, 4, 3]`

**Step 1: Expand the Window**

```text
Index:    0   1   2   3   4   5
Array:   [2,  3,  1,  2,  4,  3]
Window:  [2,  3,  1,  2]
```

Current window: `[2, 3, 1, 2]`

Current sum = 8

Condition satisfied (8 >= 7).

**Step 2: Shrink the Window**

Remove `2` from the left.

```text
Array:   [2,  3,  1,  2,  4,  3]
             [3,  1,  2]
```

Current window: `[3, 1, 2]`

Current sum = 6

Condition not satisfied (6 < 7).

**Step 3: Expand the Window**

Add `4`.

```text
Array:   [2,  3,  1,  2,  4,  3]
             [3,  1,  2,  4]
```

Current window: `[3, 1, 2, 4]`

Current sum = 10

Condition satisfied (10 >= 7).

**Step 4: Shrink the Window**

Remove `3` from the left.

Current window: `[1, 2, 4]`

Current sum = 7

Condition satisfied (7 >= 7).

Minimum length = 3.

Remove `1` from the left.

Current window: `[2, 4]`

Current sum = 6

Condition not satisfied (6 < 7).

**Step 5: Expand the Window**

Add `3`.

Current window: `[2, 4, 3]`

Current sum = 9

Condition satisfied (9 >= 7).

**Step 6: Shrink the Window**

Remove `2` from the left.

Current window: `[4, 3]`

Current sum = 7

Condition satisfied (7 >= 7).

Minimum length = 2.

Remove `4` from the left.

Current window: `[3]`

Current sum = 3

Condition not satisfied (3 < 7).

### Final Result

```text
Array: [2, 3, 1, 2, 4, 3]

Smallest valid subarray: [4, 3]

Minimum length = 2
```

**Answer: 2**


### Java implementation

```
public class Main {

    public static void main(String[] args) {

        int[] arr = {2, 3, 1, 2, 4, 3};
        int target = 7;

        int left = 0;
        int sum = 0;

        int minLength = Integer.MAX_VALUE;

        for (int right = 0; right < arr.length; right++) {

            // Expand window
            sum += arr[right];

            // Shrink window while condition is satisfied
            while (sum >= target) {

                minLength = Math.min(
                    minLength,
                    right - left + 1
                );

                sum -= arr[left];
                left++;
            }
        }

        System.out.println(minLength);
    }
}
```

### Understand the important expressions

| Code                    | Purpose                           |
| ----------------------- | --------------------------------- |
| `right++`               | Expand the window                 |
| `sum += arr[right]`     | Add incoming element              |
| `while (sum >= target)` | Check whether the window is valid |
| `right - left + 1`      | Calculate window length           |
| `sum -= arr[left]`      | Remove outgoing element           |
| `left++`                | Shrink the window                 |

Why `right - left + 1`?

Suppose:

```
left = 2;
right = 4;
```

The window contains indices 2, 3 and 4.

Number of elements = `4 - 2 + 1 = 3`.

We add 1 because both endpoints are included.

# 6. A very important concept: When should we use `if` and `while`?

This is a common source of confusion.

In Variable Size Sliding Window, you will frequently see both.

### Using `while`

```
while (sum >= target) {

    answer = Math.min(answer, right - left + 1);

    sum -= arr[left];
    left++;
}
```

We use `while` when we want to shrink the window repeatedly until the condition becomes invalid.

### Using `if`

```
if (sum > target) {
    sum -= arr[left];
    left++;
}
```

An `if` only shrinks once.

It may be appropriate for certain specialized algorithms, but it is not a replacement for the repeated shrinking required in the minimum-subarray problem.

Interview tip: Don't blindly use `while` or `if`. Understand whether the problem requires removing one element or repeatedly shrinking the window.

# 7. How to identify a Sliding Window problem?

This is probably the most important section for solving DSA problems independently.

Look for these clues in problem statements.

| Problem statement clue                            | Possible technique       |
| ------------------------------------------------- | ------------------------ |
| K consecutive elements                            | Fixed Window             |
| Subarray of size K                                | Fixed Window             |
| Substring of length K                             | Fixed Window             |
| Maximum sum of K elements                         | Fixed Window             |
| Smallest subarray satisfying a condition          | Variable Window          |
| Longest substring satisfying a condition          | Variable Window          |
| Longest subarray with at most K distinct elements | Variable Window          |
| Minimum window containing all characters          | Advanced Variable Window |

### Common keywords

- Contiguous
- Consecutive
- Subarray
- Substring
- Longest
- Smallest
- Maximum
- Minimum
- At most K
- At least K

These words are clues, not guarantees.

For example, a problem asking for a maximum sum might use Kadane's algorithm instead, depending on whether the subarray length is constrained.

### Sliding Window vs Two Pointers

These concepts are related, but they are not identical.

| Two Pointers                     | Sliding Window                                          |
| -------------------------------- | ------------------------------------------------------- |
| Uses two indices/pointers        | Usually uses two boundaries                             |
| Pointers can move independently  | Maintains a contiguous range                            |
| Can work on sorted arrays        | Commonly works on arrays and strings                    |
| Example: Two Sum in sorted array | Example: Longest substring without repeating characters |

Sliding Window is essentially a specialized use of two pointers.

# 8. Advanced Sliding Window patterns

Once you understand the two basic types, there are several patterns you should eventually learn.

Pattern 1: Fixed Window + HashMap

Example: Find the first negative number in every window of size K.

Maintain a data structure to track negative elements in the current window.

Pattern 2: Variable Window + HashMap

Example: Longest substring without repeating characters.

Maintain the last seen position or frequency of characters.

Pattern 3: Variable Window + Frequency Array

Example: Find all anagrams of a pattern in a string.

Maintain character frequencies and compare them as the window moves.

Pattern 4: Sliding Window + Deque

Example: Maximum of all subarrays of size K.

Maintain a decreasing deque so the maximum is always at the front.

Pattern 5: Sliding Window + Counting

Example: Count the number of subarrays with at most K distinct elements.

Maintain frequencies and count valid windows.

These are not separate fundamental types of Sliding Window. They are combinations of the basic technique with additional data structures.

# 9. Important Java concepts you should know

While solving Sliding Window problems, you will frequently use the following:

| Java concept      | Why you need it                        |
| ----------------- | -------------------------------------- |
| Arrays            | Store input elements                   |
| Strings           | Solve substring problems               |
| HashMap           | Maintain character/element frequencies |
| HashSet           | Track unique elements                  |
| ArrayDeque        | Implement deque-based windows          |
| Math.min()        | Find minimum answer                    |
| Math.max()        | Find maximum answer                    |
| Integer.MAX_VALUE | Initialize minimum answer              |
| Integer.MIN_VALUE | Initialize maximum answer              |

For String problems:

```
char ch = s.charAt(right);
```

For HashMap frequency:

```
map.put(ch, map.getOrDefault(ch, 0) + 1);
```

To decrease frequency:

```
map.put(ch, map.get(ch) - 1);
```

To remove a character when its frequency reaches zero:

```
if (map.get(ch) == 0) {
    map.remove(ch);
}
```

# 10. Time and Space Complexity

One thing you should understand properly is why Variable Size Sliding Window is often O(n), even though it contains a nested `while` loop.

Consider:

```
for (int right = 0; right < arr.length; right++) {

    while (condition) {
        left++;
    }
}
```

At first glance, you might think:

O(n²)

But that is incorrect for the usual sliding-window pattern.

Why?

- `right` moves from 0 to n-1.
- `left` also moves only forward.
- Neither pointer moves backwards.
- Each element enters the window at most once and leaves at most once.

Therefore:

O(n + n) = O(2n) = O(n)`

| Pattern             | Time complexity | Auxiliary space |
| ------------------- | --------------- | --------------- |
| Fixed Window sum    | O(n)            | O(1)            |
| Variable Window sum | O(n)            | O(1)            |
| Window + HashMap    | O(n) average    | O(k)            |
| Window + HashSet    | O(n) average    | O(k)            |
| Window + Deque      | O(n)            | O(k)            |

Here, `k` represents the maximum number of elements stored in the auxiliary data structure, depending on the problem.

# 11. Important mistakes to avoid

1. Forgetting `+1` in window length

   Incorrect: `right - left`

   Correct: `right - left + 1`
2. Using `if` instead of `while`

   When the problem requires repeatedly shrinking the window, use `while`.
3. Updating the answer at the wrong time

   For minimum valid window problems, update the answer before removing the left element.
4. Not removing the outgoing element

   When the left pointer moves, update the window's sum or frequency data structure accordingly.
5. Using Sliding Window for every subarray problem

   Not every subarray problem is a Sliding Window problem.
6. Ignoring negative numbers

   This is particularly important.

   The standard variable-size sum technique for finding the smallest subarray with sum at least a target relies on non-negative elements (typically positive elements).

   If negative numbers are allowed, shrinking the window may decrease or increase the sum unpredictably. The usual greedy approach is no longer valid. You may need prefix sums, monotonic deques, or other techniques.
7. Forgetting edge cases

   Consider:
   - Empty array or string.
   - Window size larger than array length.
   - No valid window.
   - All elements equal.
   - Negative values.
   - Single-element window.

# 12. Problems you should solve (Easy → Medium)

Don't jump directly into advanced Sliding Window questions. Build pattern recognition gradually.

I've arranged these in the order I'd recommend learning them.

## Level 1 — Fixed Size Window


| # | Problem                                                                       | Difficulty |
| - | ----------------------------------------------------------------------------- | ---------- |
| 1 | Maximum Average Subarray I                                                    | Easy       |
| 2 | Maximum Sum Subarray of Size K                                                | Easy       |
| 3 | Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold | Easy       |
| 4 | First Negative Integer in Every Window of Size K                              | Medium     |
| 5 | Maximum Number of Vowels in a Substring of Given Length                       | Medium     |

## Level 2 — Basic Variable Size Window

| #  | Problem                                           | Difficulty |
| -- | ------------------------------------------------- | ---------- |
| 6  | Minimum Size Subarray Sum                         | Medium     |
| 7  | Longest Subarray of 1s After Deleting One Element | Medium     |
| 8  | Max Consecutive Ones III                          | Medium     |
| 9  | Longest Substring Without Repeating Characters    | Medium     |
| 10 | Longest Repeating Character Replacement           | Medium     |

## Level 3 — HashMap + Sliding Window

| #  | Problem                       | Difficulty |
| -- | ----------------------------- | ---------- |
| 11 | Permutation in String         | Medium     |
| 12 | Find All Anagrams in a String | Medium     |
| 13 | Fruit Into Baskets            | Medium     |
| 14 | Subarray Product Less Than K  | Medium     |
| 15 | Minimum Window Substring      | Hard       |

## Level 4 — Advanced Sliding Window

| #  | Problem                                | Difficulty |
| -- | -------------------------------------- | ---------- |
| 16 | Sliding Window Maximum                 | Hard       |
| 17 | Subarrays with K Different Integers    | Hard       |
| 18 | Count Number of Nice Subarrays         | Medium     |
| 19 | Binary Subarrays With Sum              | Medium     |
| 20 | Minimum Operations to Reduce X to Zero | Medium     |

