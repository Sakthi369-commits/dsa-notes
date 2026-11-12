# Hashing in DSA – Complete Beginner-to-Interview Guide

Hashing is one of the most important DSA topics, especially when preparing for coding interviews using Grind 75 / Blind 75 / NeetCode 150.

Since I'm using Java, these notes focus on:

- Understanding the underlying concepts.
- Recognizing when to use hashing in problems.
- Implementing it using Java Collections.
- Understanding time and space complexity.
- Preparing for common interview patterns.

The goal isn't to memorize solutions. It's to understand why hashing works.

---

# 1. What is Hashing?

Hashing is a technique used to store and retrieve data efficiently using a key.

Imagine you have an array:

```java
int[] arr = {10, 20, 30, 40, 50};
```

Suppose I ask:

"Does the number 40 exist in this array?"

Without hashing, you might have to check every element one by one.

This is called **Linear Search**.

### Without Hashing – Linear Search

We check each element until we find the required element.

```text
10 → 20 → 30 → 40 → 50
             ↑
           Found
```

In the worst case, we might need to check all n elements.

**Time Complexity: O(n)**

### With Hashing – Direct Lookup

Hashing allows us to find elements much faster on average.

**Average Time Complexity: O(1)**

Hashing helps us perform operations such as:

- Searching for an element.
- Checking whether an element exists.
- Counting frequencies.
- Finding duplicates.
- Finding pairs that satisfy a condition.
- Grouping related elements.

**Important:** Hashing doesn't guarantee O(1) in every situation. O(1) is the average-case complexity for common hash table operations.

---

# 2. How Does Hashing Work Internally?

There are three important concepts:

1. Key
2. Hash Function
3. Hash Table

Let's understand them.

## A. Key

A key is the value that we want to store or search for.

For example:

```java
int number = 25;
```

Here, 25 is our key.

## B. Hash Function

A hash function takes a key and converts it into a hash value.

For example, imagine this simple hash function:

```text
h(key) = key % 10
```

If the key is 25:

```text
25 % 10 = 5
```

So, the hash function maps 25 to index 5.

### Hash Function Visualization

```text
       Input Key
           |
           v
          25
           |
           v
     Hash Function
        key % 10
           |
           v
       25 % 10
           |
           v
      Array Index
           |
           v
           5
```

## C. Hash Table

A hash table is a data structure that stores key-value pairs using a hash function.

Think of it as an array where the hash function helps determine where data should be stored.

For example:

| Index | Value |
|---|---|
| 0 | - |
| 1 | - |
| 2 | - |
| 3 | - |
| 4 | - |
| 5 | 25 |
| 6 | - |
| 7 | - |
| 8 | - |
| 9 | - |

This is a simplified illustration. Real hash tables have additional logic to handle collisions.

---

# 3. What Is a Hash Collision?

A hash collision happens when **two different keys generate the same hash table index**.

This is one of the most important concepts in hashing.

Let's understand why collisions happen.

## A. Why Do Collisions Occur?

Consider a hash table with only 10 available indexes:

```text
0, 1, 2, 3, 4, 5, 6, 7, 8, 9
```

Suppose we use the following hash function:

```text
h(key) = key % 10
```

Now calculate the indexes for different keys.

| Key | Hash Function | Index |
|---|---|---|
| 10 | 10 % 10 | 0 |
| 15 | 15 % 10 | 5 |
| 20 | 20 % 10 | 0 |
| 25 | 25 % 10 | 5 |
| 30 | 30 % 10 | 0 |
| 35 | 35 % 10 | 5 |

Notice something?

- 10 and 20 both map to index 0.
- 15 and 25 both map to index 5.
- 10, 20 and 30 all map to index 0.

This is called a **Hash Collision**.

### Why is this unavoidable?

A hash table generally has a limited number of indexes, but the possible keys can be much larger.

For example:

- Possible integer keys: Billions of values.
- Available hash table indexes: Maybe 100.

It is impossible to assign every possible key a unique index.

Therefore, collisions are unavoidable in practical hash tables.

This is also related to the Pigeonhole Principle:

If we place more items into fewer available positions, at least two items must occupy the same position.

## B. Collision Example

Consider:

```text
h(key) = key % 10
```

For key 25:

```text
25 % 10 = 5
```

For key 35:

```text
35 % 10 = 5
```

Both keys want index 5.

```text
        Key 25                 Key 35
           |                      |
           v                      v
        25 % 10                35 % 10
           |                      |
           v                      v
         Index 5               Index 5
           |                      |
           +----------+-----------+
                      |
                      v
                 COLLISION
```

But we cannot simply discard one of the keys.

Both keys need to be stored and retrieved correctly.

So how do hash tables handle this?

There are two major techniques:

1. Separate Chaining
2. Open Addressing

---

# 4. How Are Hash Collisions Addressed?

## A. Separate Chaining

Separate Chaining is a collision-handling technique in which each index (bucket) can store multiple entries.

Instead of storing only one element at an index, we maintain a collection of entries at that index.

A linked list is a common implementation.

### Example

Consider:

```text
h(key) = key % 10
```

We want to insert:

```text
15, 25, 35
```

All three keys generate index 5.

```text
15 % 10 = 5
25 % 10 = 5
35 % 10 = 5
```

Instead of overwriting the previous value, we store all three entries in the same bucket.

```text
Hash Table

Index 0  →  null
Index 1  →  null
Index 2  →  null
Index 3  →  null
Index 4  →  null

Index 5  →  [15] → [25] → [35] → null

Index 6  →  null
Index 7  →  null
Index 8  →  null
Index 9  →  null
```

Now all three keys can be stored successfully.

### How does searching work?

Suppose we want to search for 25.

**Step 1:** Calculate the hash index.

```text
25 % 10 = 5
```

**Step 2:** Go to index 5.

**Step 3:** Traverse the collection stored at index 5.

```text
Index 5 → [15] → [25] → [35]
                      ↑
                    Found
```

**Step 4:** Return the corresponding value.

### How does insertion work?

Suppose we want to insert 45.

```text
45 % 10 = 5
```

The index is already occupied.

But because we use Separate Chaining, we simply add the new entry to the collection.

```text
Index 5 → [15] → [25] → [35] → [45]
```

### Advantages

- Multiple elements can share the same index.
- Simple collision-handling mechanism.
- Deletion is relatively straightforward.
- The hash table can store more elements than its number of buckets.

### Disadvantages

- Additional memory is required for linked structures or other bucket collections.
- Searching becomes slower if too many entries accumulate in one bucket.
- Poor hash functions can create long chains.

### Time Complexity

| Operation | Average | Worst Case |
|---|---|---|
| Insert | O(1) | O(n) |
| Search | O(1) | O(n) |
| Delete | O(1) | O(n) |

The average performance depends on how evenly keys are distributed across buckets.

---

## B. Open Addressing

Open Addressing is another collision-handling technique.

Unlike Separate Chaining, we don't create a separate linked list or collection for each bucket.

Instead, **all elements are stored directly inside the hash table array**.

When a collision occurs, we search for another available index.

This process is called **Probing**.

There are three common probing techniques:

1. Linear Probing
2. Quadratic Probing
3. Double Hashing

Let's understand them individually.

### 1. Linear Probing

In Linear Probing, whenever a collision occurs, we check the next available index sequentially.

The formula is:

```text
h(key, i) = (h(key) + i) % tableSize
```

Where:

- `h(key)` = Original hash index.
- `i` = Number of probing attempts.
- `tableSize` = Total number of available indexes.

### Example

Consider a hash table of size 10.

Hash function:

```text
h(key) = key % 10
```

We want to insert:

```text
15, 25, 35
```

**Step 1: Insert 15**

```text
15 % 10 = 5
```

Index 5 is empty.

Insert 15 at index 5.

**Step 2: Insert 25**

```text
25 % 10 = 5
```

Index 5 is occupied.

Check the next index: 6.

Index 6 is empty, so insert 25 there.

**Step 3: Insert 35**

```text
35 % 10 = 5
```

Index 5 is occupied.

Check index 6: occupied.

Check index 7: empty.

Insert 35 at index 7.

Final hash table:

```text
Index 0 → Empty
Index 1 → Empty
Index 2 → Empty
Index 3 → Empty
Index 4 → Empty
Index 5 → 15
Index 6 → 25
Index 7 → 35
Index 8 → Empty
Index 9 → Empty
```

### Problem with Linear Probing: Primary Clustering

Primary Clustering happens when consecutive occupied positions form long groups.

For example:

```text
Index 5 → 15
Index 6 → 25
Index 7 → 35
Index 8 → 45
Index 9 → 55
```

New elements may need to scan many occupied positions before finding an empty index.

This reduces performance.

---

### 2. Quadratic Probing

Quadratic Probing solves the primary clustering problem by using a quadratic function to determine the next position.

Instead of checking:

```text
5, 6, 7, 8, 9
```

We use a quadratic increment.

The formula is:

```text
h(key, i) = (h(key) + c1*i + c2*i²) % tableSize
```

Where:

- `h(key)` = Original hash index.
- `i` = Probing attempt.
- `c1` and `c2` = Constants.

For simplicity, assume:

```text
c1 = 0
c2 = 1
```

Then:

```text
h(key, i) = (h(key) + i²) % tableSize
```

Suppose the original index is 5.

The probing sequence becomes:

| Attempt (i) | Calculation | Index |
|---|---|---|
| 0 | 5 + 0² | 5 |
| 1 | 5 + 1² | 6 |
| 2 | 5 + 2² | 9 |
| 3 | 5 + 3² | 14 % 10 = 4 |

Instead of checking consecutive positions, we jump according to a quadratic sequence.

### Advantages

- Reduces primary clustering.
- Spreads probing positions more widely than linear probing.

### Disadvantages

- Secondary clustering can still occur.
- Some table sizes and constants can prevent certain slots from being reached.
- Proper parameter selection is important.

---

### 3. Double Hashing

Double Hashing uses two different hash functions to determine the next position.

The formula is:

```text
h(key, i) = (h1(key) + i * h2(key)) % tableSize
```

Where:

- `h1(key)` = First hash function.
- `h2(key)` = Second hash function.
- `i` = Probing attempt.

The first hash function determines the initial position.

The second hash function determines the step size for probing.

### Example

Assume:

```text
tableSize = 10

h1(key) = key % 10

h2(key) = 7 - (key % 7)
```

For key 25:

```text
h1(25) = 25 % 10 = 5

h2(25) = 7 - (25 % 7)
       = 7 - 4
       = 3
```

Initial position:

```text
5
```

If a collision occurs:

```text
i = 1

(5 + 1 * 3) % 10 = 8
```

If index 8 is occupied:

```text
i = 2

(5 + 2 * 3) % 10 = 1
```

The probing sequence is:

```text
5 → 8 → 1 → 4 → 7 → ...
```

### Advantages

- Reduces primary clustering.
- Generally distributes probes more effectively than linear and quadratic probing.
- Different keys can follow different probing sequences.

### Disadvantages

- Requires two hash functions.
- The second hash function must be designed carefully.
- Its step size should be relatively prime to the table size to ensure all slots can eventually be visited.

---

## C. Comparison of Collision-Handling Techniques

| Technique | How It Works | Main Problem |
|---|---|---|
| Separate Chaining | Stores multiple entries in the same bucket | Long chains |
| Linear Probing | Checks the next available slot | Primary clustering |
| Quadratic Probing | Uses quadratic jumps | Secondary clustering |
| Double Hashing | Uses a second hash function for jumps | More complex hash functions |

### Important Interview Point

You should understand:

- Why collisions occur.
- Why collisions cannot be completely avoided.
- How Separate Chaining works.
- How Open Addressing works.
- Differences between Linear, Quadratic and Double Hashing.

You generally do not need to implement all these mechanisms from scratch for Java-based DSA interviews.

---

# 5. How Does Java HashMap Handle Collisions?

This is particularly important because we use `HashMap` extensively in Java DSA.

Java's HashMap uses a bucket-based implementation.

It uses:

- Hashing to determine the bucket.
- Linked nodes to handle collisions.
- Red-Black Trees for certain heavily populated buckets.

Let's understand the process.

## A. Insertion in HashMap

Consider:

```java
HashMap<Integer, String> map = new HashMap<>();

map.put(15, "Java");
map.put(25, "Python");
map.put(35, "Spring");
```

Conceptually, if all three keys map to the same bucket:

```text
Bucket 5

[15, Java] → [25, Python] → [35, Spring]
```

Java can store multiple entries in the same bucket.

## B. How Does HashMap Search for an Element?

Suppose:

```java
map.get(25);
```

The general process is:

1. Calculate the hash of the key.
2. Determine the bucket index.
3. Access the corresponding bucket.
4. Compare keys using hash information and `equals()`.
5. Return the matching value.

Even if multiple keys share the same bucket, Java checks which key actually matches.

This is why collisions do not cause incorrect retrieval.

## C. What Happens When a Bucket Becomes Too Large?

Java HashMap can convert a heavily populated bucket into a Red-Black Tree.

A Red-Black Tree is a self-balancing binary search tree.

This helps improve searching when a bucket contains many colliding entries.

Under standard Java HashMap implementation conditions:

- Treeification threshold: 8 entries.
- Minimum table capacity for treeification: 64.
- Untreeification threshold: 6 entries (in the relevant resize/splitting conditions).

These are implementation details, not universal rules for all hash tables.

### Why Red-Black Trees?

Suppose a bucket contains many colliding entries.

With a linked list:

```text
10 → 20 → 30 → 40 → 50 → 60 → 70
```

Searching may require traversing multiple nodes.

With a balanced tree, searching can be approximately:

```text
O(log n)
```

rather than:

```text
O(n)
```

for that bucket, under the relevant tree-bin conditions.

### HashMap Collision Handling Summary

```text
             HashMap
                |
                v
         Calculate Hash
                |
                v
         Find Bucket Index
                |
                v
          Is Bucket Empty?
             /       \
           Yes        No
            |          |
            v          v
       Insert Node   Compare Keys
                         |
                         v
                   Same Key Exists?
                     /       \
                   Yes        No
                    |          |
                    v          v
              Update Value   Add Node
                                 |
                                 v
                       Bucket Becomes Large?
                           /          \
                         Yes           No
                          |             |
                          v             v
                    Treeification   Keep Nodes
```

**Note:** The diagram is conceptual. Actual HashMap insertion, resizing and treeification depend on capacity, thresholds and key comparisons.

---

# 6. Hashing Time Complexity

You should know these complexities for interviews.

| Operation | Average | Worst Case |
|---|---|---|
| Insert | O(1) | O(n) |
| Search | O(1) | O(n) |
| Delete | O(1) | O(n) |
| Frequency Update | O(1) | O(n) |

For modern Java HashMap implementations, tree bins can improve collision-heavy bucket operations. However, the general DSA convention is to remember average O(1) and worst-case O(n).

**Space Complexity:** O(n) when storing up to n distinct elements.

---

# 7. Java Collections Used in Hashing

There are three main classes you should understand.

1. HashSet
2. HashMap
3. LinkedHashMap

For most interview problems, focus on HashSet and HashMap.

## A. HashSet

A HashSet stores unique elements.

Example:

```java
import java.util.HashSet;

public class Main {
    public static void main(String[] args) {

        HashSet<Integer> set = new HashSet<>();

        set.add(10);
        set.add(20);
        set.add(30);
        set.add(20);

        System.out.println(set);
    }
}
```

Possible output (order may vary):

```text
[20, 10, 30]
```

Notice that 20 appears only once.

### Important HashSet Methods

| Method | Purpose | Average Time |
|---|---|---|
| `add()` | Insert element | O(1) |
| `contains()` | Check existence | O(1) |
| `remove()` | Remove element | O(1) |
| `size()` | Number of elements | O(1) |
| `isEmpty()` | Check emptiness | O(1) |

### When Should You Use HashSet?

Whenever the problem involves:

- Checking duplicates.
- Checking whether an element exists.
- Finding common elements.
- Finding unique elements.
- Tracking elements already visited.

### Example: Contains Duplicate

```java
import java.util.HashSet;

public class Main {

    public static void main(String[] args) {

        int[] nums = {1, 2, 3, 1};

        HashSet<Integer> set = new HashSet<>();

        for (int num : nums) {

            if (set.contains(num)) {
                System.out.println("Duplicate found");
                return;
            }

            set.add(num);
        }

        System.out.println("No duplicates");
    }
}
```

**Logic:**

1. Start with an empty HashSet.
2. Visit each element.
3. Check whether it already exists.
4. If it exists, we found a duplicate.
5. Otherwise, insert it.

Time Complexity: O(n) average.

Space Complexity: O(n).

---

## B. HashMap

HashMap stores information in key-value pairs.

Example:

```java
import java.util.HashMap;

public class Main {

    public static void main(String[] args) {

        HashMap<String, Integer> map = new HashMap<>();

        map.put("Java", 10);
        map.put("Python", 20);
        map.put("JavaScript", 30);

        System.out.println(map.get("Java"));
    }
}
```

Output:

```text
10
```

Here:

| Key | Value |
|---|---|
| Java | 10 |
| Python | 20 |
| JavaScript | 30 |

### Important HashMap Methods

| Method | Purpose | Average Time |
|---|---|---|
| `put()` | Insert/update key-value | O(1) |
| `get()` | Retrieve value | O(1) |
| `containsKey()` | Check key existence | O(1) |
| `containsValue()` | Check value existence | O(n) |
| `remove()` | Remove key | O(1) |
| `getOrDefault()` | Get value or default | O(1) |

### A Very Important Concept: Frequency Counting

Suppose:

```java
int[] nums = {1, 2, 2, 3, 3, 3};
```

We want to count how many times each number occurs.

Expected result:

```text
1 -> 1
2 -> 2
3 -> 3
```

Using HashMap:

```java
HashMap<Integer, Integer> map = new HashMap<>();

for (int num : nums) {

    map.put(num, map.getOrDefault(num, 0) + 1);

}
```

Let's understand this expression:

```java
map.getOrDefault(num, 0) + 1
```

- If the number already exists, retrieve its current count.
- If it doesn't exist, return 0.
- Add 1.
- Store the updated count.

This is one of the most frequently used HashMap patterns in DSA.

---

## C. LinkedHashMap

LinkedHashMap is similar to HashMap, but it maintains insertion order by default.

Example:

```java
LinkedHashMap<Integer, String> map = new LinkedHashMap<>();

map.put(3, "Java");
map.put(1, "Python");
map.put(2, "Spring");

System.out.println(map);
```

The iteration order follows insertion order.

**When to use it:** When you need key-value storage and predictable insertion-order iteration.

---

# 8. HashMap vs HashSet

This distinction must become second nature.

| Feature | HashMap | HashSet |
|---|---|---|
| Stores | Key-value pairs | Unique elements |
| Duplicate keys | Not allowed | Not applicable |
| Duplicate values | Allowed | Not allowed |
| Main purpose | Mapping/counting | Existence checking |
| Common method | `put()` | `add()` |
| Lookup | `get()` | `contains()` |

A simple way to remember:

- **HashSet:** Have I seen this element before?
- **HashMap:** What information is associated with this element?

---

# 9. Important Hashing Patterns in DSA

This is where hashing becomes powerful.

Don't just learn HashMap and HashSet methods. Learn to identify the patterns behind interview questions.

Here are the main patterns you should know.

## Pattern 1: Frequency Counting

Count occurrences of elements using HashMap.

Example: Top K Frequent Elements.

## Pattern 2: Duplicate Detection

Use HashSet to detect repeated elements.

Example: Contains Duplicate.

## Pattern 3: Complement Search

Find a required matching value using HashMap.

Example: Two Sum.

## Pattern 4: Grouping

Group elements based on a common property.

Example: Group Anagrams.

## Pattern 5: Prefix Sum + HashMap

Track previous prefix sums to find subarrays.

Example: Subarray Sum Equals K.

## Pattern 6: Membership Checking

Quickly check whether an element belongs to a collection.

Example: Longest Consecutive Sequence.

Let's understand each.

---

## Pattern 1: Duplicate Detection

Problem: Contains Duplicate.

Given:

```java
int[] nums = {1, 2, 3, 1};
```

Return true if any number appears more than once.

Approach:

Use HashSet.

```java
public boolean containsDuplicate(int[] nums) {

    HashSet<Integer> set = new HashSet<>();

    for (int num : nums) {

        if (set.contains(num)) {
            return true;
        }

        set.add(num);
    }

    return false;
}
```

Time: O(n) average.

Space: O(n).

---

## Pattern 2: Two Sum – Complement Search

Problem:

Given:

```java
int[] nums = {2, 7, 11, 15};
int target = 9;
```

Find two indices whose elements add up to the target.

Expected answer:

```text
[0, 1]
```

The mathematical idea:

```text
a + b = target
```

Rearrange:

```text
b = target - a
```

This is called finding the complement.

For every number, calculate the number you need and check whether it already exists in the HashMap.

```java
public int[] twoSum(int[] nums, int target) {

    HashMap<Integer, Integer> map = new HashMap<>();

    for (int i = 0; i < nums.length; i++) {

        int complement = target - nums[i];

        if (map.containsKey(complement)) {
            return new int[]{map.get(complement), i};
        }

        map.put(nums[i], i);
    }

    return new int[]{};
}
```

Why store the index as the value?

Because the question asks for indices, not just numbers.

Time: O(n) average.

Space: O(n).

---

## Pattern 3: Frequency Counting

Problem: Top K Frequent Elements.

Given:

```java
int[] nums = {1, 1, 1, 2, 2, 3};
int k = 2;
```

We need the two most frequent elements.

Frequency map:

```text
1 -> 3
2 -> 2
3 -> 1
```

Answer:

```text
[1, 2]
```

The important idea is that HashMap helps us count frequencies.

You can then use a heap or bucket sort to retrieve the top K elements efficiently.

---

## Pattern 4: Grouping – Group Anagrams

Problem:

```text
["eat", "tea", "tan", "ate", "nat", "bat"]
```

Group words that contain the same characters.

Expected output:

```text
[
  ["eat", "tea", "ate"],
  ["tan", "nat"],
  ["bat"]
]
```

How does hashing help?

We create a common key for anagrams.

For example:

```text
eat -> aet
tea -> aet
ate -> aet
```

Then use:

```java
HashMap<String, List<String>> map = new HashMap<>();
```

The sorted string becomes the key, and the anagrams become the values.

This is a very common interview pattern: **HashMap + grouping**.

---

## Pattern 5: Prefix Sum + HashMap

This is a slightly advanced but important combination.

Problem: Subarray Sum Equals K.

Given:

```java
int[] nums = {1, 2, 3};
int k = 3;
```

We want to count continuous subarrays whose sum equals 3.

Answer:

```text
2
```

Because:

```text
[1, 2]
[3]
```

The key mathematical relationship:

```text
currentPrefixSum - k = previousPrefixSum
```

We use a HashMap to remember how many times each prefix sum has occurred.

This pattern is extremely useful in subarray problems.

---

## Pattern 6: Membership Checking – Longest Consecutive Sequence

Given:

```java
int[] nums = {100, 4, 200, 1, 3, 2};
```

Find the length of the longest consecutive sequence.

Answer:

```text
4
```

Because:

```text
1, 2, 3, 4
```

Use HashSet to check whether the next number exists.

The clever part is to start counting only when the previous number does not exist.

For example, start at 1 because 0 doesn't exist. Don't start a new sequence at 2, 3, or 4.

This avoids repeatedly counting the same sequence.

---

# 10. Hashing and Other DSA Topics

Hashing isn't an isolated topic.

It frequently combines with other concepts.

| Topic | Hashing Combination |
|---|---|
| Arrays | Duplicate detection, Two Sum |
| Strings | Character frequency, Anagrams |
| Sliding Window | Frequency map inside a window |
| Prefix Sum | Subarray Sum Equals K |
| Binary Search | Sometimes used for membership checks |
| Graphs | Visited sets and adjacency maps |
| Heap | Top K Frequent Elements |

One important point: **Hashing is a technique, not a separate algorithm category that you use only once.**

You will continue using it throughout DSA.

---

# 11. Important Java Concepts You Must Know

There are a few Java-specific details worth learning.

## A. HashMap Does Not Maintain Insertion Order

```java
HashMap<Integer, String> map = new HashMap<>();
```

The iteration order is not guaranteed.

If you need insertion order, use `LinkedHashMap`.

## B. HashSet Does Not Maintain Insertion Order

```java
HashSet<Integer> set = new HashSet<>();
```

If you need sorted elements, use `TreeSet`.

## C. HashMap Allows One Null Key

```java
map.put(null, 100);
```

HashMap permits one null key and multiple null values.

## D. HashSet Internally Uses HashMap

This is a useful interview fact.

Java's HashSet internally uses a HashMap to store its elements.

## E. equals() and hashCode()

For custom Java objects, understanding these methods is essential.

- `hashCode()` helps determine the bucket.
- `equals()` checks whether two objects are logically equal.

If two objects are equal according to `equals()`, they must have the same `hashCode()`.

However, two objects having the same hash code does not necessarily mean they are equal.

Example:

```java
class Student {

    int id;
    String name;

    // equals() and hashCode() should be overridden
    // when objects are compared based on logical equality.
}
```

**Important contract:**

```text
If a.equals(b) is true,
then a.hashCode() == b.hashCode() must also be true.
```

The reverse is not required.

---

# 12. Grind 75 – Hashing Problems to Practice

Here is a recommended practice order.

| Order | Problem | Main Concept | Difficulty |
|---|---|---|---|
| 1 | Contains Duplicate | HashSet | Easy |
| 2 | Valid Anagram | Frequency Counting | Easy |
| 3 | Two Sum | Complement Search | Easy |
| 4 | Group Anagrams | HashMap Grouping | Medium |
| 5 | Top K Frequent Elements | Frequency + Heap/Bucket | Medium |
| 6 | Product of Array Except Self | Prefix/Suffix Products | Medium |
| 7 | Longest Consecutive Sequence | HashSet Membership | Medium |
| 8 | Subarray Sum Equals K | Prefix Sum + HashMap | Medium |

**Note:** Product of Array Except Self is included because it commonly appears near array/hash-map problems in interview roadmaps, but it is primarily a prefix/suffix problem rather than a hashing problem.

---


## Final Takeaway

Remember these five points:

1. Hashing converts keys into hash values to determine where data should be stored.
2. Hash collisions occur when different keys map to the same bucket.
3. Separate Chaining and Open Addressing are two major collision-handling techniques.
4. Java HashMap uses bucket-based storage, linked nodes and tree bins to handle collisions.
5. In DSA, HashSet and HashMap are primarily used to achieve efficient lookups, frequency counting, duplicate detection and grouping.

