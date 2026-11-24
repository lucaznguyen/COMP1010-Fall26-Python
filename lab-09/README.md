# Lab 09: Sorting — Detailed Study Guide

**Course:** COMP1010 - Introduction to Programming  
**Preparation:** Lecture 16 (Algorithm Design and Linear Search) and Lecture 17 (Sorting and Selection Sort), as well as lists, loops, strings, tuples, dictionaries, and functions from the earlier labs.

This guide supports **IPRFAL261–IPRFAL264** through independent examples, traces, boundary cases, and debugging advice. It does not contain the assigned question sheets or their complete solutions. Use the latest official Lab 09 handout for **your own section**: the sections have different problems and algorithms.

Submit the same four Python 3 programs to **both Codeforces and Canvas** by your section's deadline. All times are Vietnam time (**UTC+7**).

| Section | Lab starts | Deadline: 48 hours after the start |
| --- | --- | --- |
| IPRFAL261 | Wednesday, 25 November 2026, 10:30 | **Friday, 27 November 2026, 10:30** |
| IPRFAL262 | Wednesday, 25 November 2026, 15:00 | **Friday, 27 November 2026, 15:00** |
| IPRFAL263 | Friday, 27 November 2026, 08:30 | **Sunday, 29 November 2026, 08:30** |
| IPRFAL264 | Friday, 27 November 2026, 13:30 | **Sunday, 29 November 2026, 13:30** |

## What to prepare

- Read a sorting problem as a contract: input lines, required algorithm, tie rules, output, and any required function.
- Trace one comparison, swap, shift, pass, step, or round without confusing the terms.
- Use indexes safely, including the first and last adjacent pairs.
- Explain and implement the required sorting algorithm instead of replacing it with a built-in sort.
- Compare numbers, words, times, dates, and records according to stated rules.
- Use a dictionary for frequencies and a loop to examine or process sorted records.
- Test equal values, already sorted input, reversed input, exact boundaries, and one-element cases where allowed.
- Print lists, labels, spaces, times, and counts exactly as required.

## 1. Start with the contract

Read the complete handout before writing code. The problems share sorting ideas, but they do **not** all accept the same algorithm or output.

| Question | What to check |
| --- | --- |
| Input | Is there one line of values, a count followed by records, or a limit followed by values? |
| Allowed values | Can values repeat? Are negative values or one-element inputs allowed? Are inputs guaranteed valid? |
| Required method | Is a particular sort, direction, or order of passes required? |
| Helper functions | What are the required names, parameters, return values, and side effects? |
| Ordering | What comes first? How are ties resolved? Are names case-sensitive? |
| Progress output | Does the problem print after every comparison, swap, pass, step, or round? |
| Termination | Is a final pass with zero swaps included? |
| Output | How many lines are printed, and which labels, spaces, and punctuation are required? |

An output such as `Pass 2: [2, 4, 7]` is not the same as `Pass 2: 2 4 7`. One prints Python's list representation; the other prints space-separated values. Use the representation requested by the problem.

## 2. Function types, docstrings, and side effects

Plain annotations tell the reader what each value represents. They do not convert or validate input.

```python
def count_pairs(values: list) -> int:
    """Return the number of adjacent pairs in the list values.

    values is not modified. An empty or one-item list has zero pairs.
    """
    return max(0, len(values) - 1)


print(count_pairs([8, 3, 6]))  # 2
```

`values: list` describes a parameter; `-> int` describes the return. `max()` is used here only for an independent arithmetic example: follow the assigned problem if it forbids a particular built-in.

The handout requires a docstring for **every function**, including `main` and helpers. Describe the purpose, the parameters, the result, and whether a mutable input changes. Plain `list`, `tuple`, `dict`, `str`, `int`, `float`, `bool`, and `None` annotations are enough; describe the elements in the docstring.

### In-place mutation versus a returned list

```python
original = [6, 1, 4]
other_name = original
separate = original.copy()

original[0] = 9
print(other_name)  # [9, 1, 4]: the same list
print(separate)    # [6, 1, 4]: a different list
```

If a function sorts a list **in place**, the caller sees the changed list. If it returns `None`, do not write `print(sort_function(items))` unless the output is supposed to include `None`. If a function returns a new list, save and use that returned value. Follow the handout's exact function contract.

## 3. Read the input without prompts

One call to `input()` reads one line as a string.

```python
# One line containing several integers
numbers = list(map(int, input().split()))

# One record with a word and an integer on the same line
label, score_text = input().split()
score = int(score_text)
```

For a count followed by records:

```python
count = int(input())
records = []
for _ in range(count):
    name, value_text = input().split()
    records.append((name, int(value_text)))
```

The count line is not one of the records. Do not use `input("Enter a value: ")` in a judged submission: the prompt becomes extra output. Do not invent error messages for inputs that the handout guarantees are valid.

## 4. Indexes, ranges, and neighbours

For a list of length `n`, valid indexes are `0` through `n - 1`. The adjacent pairs are `(0, 1)`, `(1, 2)`, ..., `(n - 2, n - 1)`.

```python
values = [11, 7, 9, 2]
for i in range(len(values) - 1):
    print(i, values[i], values[i + 1])
```

The loop runs for `i = 0, 1, 2`. Using `range(len(values))` while reading `values[i + 1]` causes an `IndexError` on the last iteration. A backward traversal of those same adjacent pairs can use `range(n - 2, -1, -1)`; the stop value `-1` is excluded.

`range(start, stop, step)` excludes `stop`. `range(0, n - 1, 2)` visits pair starts `0, 2, 4, ...`; starting at `1` visits `1, 3, 5, ...`. The final pair exists only when its second index is less than `n`.

## 5. Compare, swap, shift, and count

A comparison asks a question such as `left > right`. A **swap** exchanges two positions. A **shift** moves one existing element by one position to make room for another. They are different events.

```python
items = [9, 3]
items[0], items[1] = items[1], items[0]
print(items)  # [3, 9]
```

Count a swap only according to the problem's definition. In a selection step, selecting the item already at the current position may require **zero** counted swaps even though assigning it to itself would not change the list. When equal neighbours are not supposed to swap, use the correct strict comparison. Do not increment a counter merely because you compared two values.

## 6. Linear search and the first match

Lecture 16 examines a linear search: visit items in order until a target is found or the sequence ends. The first item can match, the last item can match, or there may be no match. A repeated target raises another question: should the function return the **first**, **last**, or **all** matching positions?

For `[7, 2, 9, 2]`, scanning for `2` from the left reaches its first occurrence at index `1` after two comparisons. Scanning for `5` checks all four entries and finds nothing. Always choose the missing-value marker specified by the caller or handout; do not silently use `0` when `0` is also a valid index.

Finding the minimum or maximum in an unsorted suffix also uses a left-to-right scan. This is useful inside selection sort. If equal best values must choose the **leftmost** occurrence, update the best index only for a **strictly** better value.

## 7. Selection sort

Selection sort divides a list into a sorted prefix and an unsorted suffix. At each position, find the best element in the suffix and put it at that position. The direction depends on the required order: choose the smallest for ascending, or the largest for descending.

Independent trace for ascending order; equal minima use the leftmost index:

| Step | Unsorted suffix before selection | Result after the step | Counted swap? |
| --- | --- | --- | --- |
| Start | `[8, 3, 6, 3]` | `[8, 3, 6, 3]` | — |
| 1 | `[8, 3, 6, 3]` | `[3, 8, 6, 3]` | Yes |
| 2 | `[8, 6, 3]` | `[3, 3, 6, 8]` | Yes |
| 3 | `[6, 8]` | `[3, 3, 6, 8]` | No |

There are three steps for four values. A handout may still require a line for step 3, even though the list did not change. The sorted prefix becomes one position longer after every step.

### One independent selection step

The following performs only **one** step, not a complete lab program:

```python
values = [8, 3, 6, 3]
start = 0
best = start
for j in range(start + 1, len(values)):
    if values[j] < values[best]:
        best = j
if best != start:
    values[start], values[best] = values[best], values[start]
print(values)  # [3, 8, 6, 3]
```

For descending order, reverse the comparison, not the input line or the printed indexes. If the items are records, use the record's required comparison rules rather than comparing unrelated fields.

## 8. Bubble sort and complete passes

A left-to-right bubble pass compares each adjacent pair and swaps out-of-order neighbours. For `[5, 2, 4]`:

| Action | List | Swaps in this pass |
| --- | --- | --- |
| Start | `[5, 2, 4]` | 0 |
| Compare 5 and 2; swap | `[2, 5, 4]` | 1 |
| Compare 5 and 4; swap | `[2, 4, 5]` | 2 |
| Next full pass | `[2, 4, 5]` | 0 |

If the contract says to stop **after a pass with no swaps** and print that pass, the unchanged pass is part of the output. For an already sorted list, it is the first pass. For all-equal values, strict `>` also gives zero swaps.

Some presentations optimize bubble sort by shortening later passes. Do not make that change when the assigned definition explicitly requires comparing the **whole** list on every pass or printing its exact intermediate states.

## 9. Insertion sort and shifts

Insertion sort treats the part to the left of the current position as sorted. Take the next item, move larger items right, and put the saved item into the opening.

For `[5, 2, 4]` in ascending order:

| Step | Value inserted | List after insertion | Shifts |
| --- | --- | --- | --- |
| 1 | 2 | `[2, 5, 4]` | 1 |
| 2 | 4 | `[2, 4, 5]` | 1 |

Save the current item **before** overwriting positions. Each existing item moved right counts as one shift; placing the saved item in the gap is not automatically another shift. With a strict comparison, equal values are not moved past one another.

When comparing structured records, compare by the primary key first and use the tie-breaker only when that key is equal. Descending primary order and ascending name order need two different comparison directions.

## 10. Two-direction, odd-even, and gnome variants

Read the exact rules in your own section; the following small traces explain vocabulary without replacing those rules.

### Forward and backward passes

A two-direction round may first compare adjacent pairs left to right and then right to left. Starting with `[4, 1, 3, 2]`, a complete forward pass gives `[1, 3, 2, 4]`; the backward pass then gives `[1, 2, 3, 4]`. If a **round** means both passes, reset the round's count before the forward pass and add the backward swaps to the same count. A final unchanged round may still have to be printed.

### Odd-even sweeps

With `[4, 1, 3, 2]`, an even-start sweep checks `(0, 1)` and `(2, 3)`, producing `[1, 4, 2, 3]`. The following odd-start sweep checks `(1, 2)`, producing `[1, 2, 4, 3]`. These are **disjoint pairs within each sweep**. For an odd-length list, the final item may have no partner in one sweep; do not access beyond the end.

### Moving backward after a swap

In a gnome-style process, compare the current item with its left neighbour, move right when they are in order, and move left after an out-of-order swap. For `[3, 1, 2]`, the swap results are `[1, 3, 2]` and then `[1, 2, 3]`. Position zero needs special handling: in Python, index `-1` refers to the **last** element, not an out-of-bounds marker.

## 11. Ordering records and ties

Before implementing a comparator, write down its decision order. For example:

1. A larger numeric score comes first.
2. When scores are equal, the alphabetically smaller name comes first.

For the independent records `[("lea", 7), ("bo", 9), ("amy", 7)]`, the required order is `[("bo", 9), ("amy", 7), ("lea", 7)]`. The name tie-breaker applies only to the equal scores.

Python compares strings lexicographically and case-sensitively. If the handout says all names are lowercase, do not invent a case-insensitive rule. Equal primary values, equal strings, and a single record should be tested separately.

### Shared rank versus list position

If equal scores share rank and the next rank is the player's **position**, scores `100, 100, 80` receive ranks `1, 1, 3`, not `1, 1, 2`. A name tie-breaker orders people with equal scores but does not automatically make their ranks different. Use the precise ranking convention in your section.

## 12. Sorting words by more than one rule

For the independent words `"pear"`, `"kiwi"`, and `"fig"`, ordering by length and then alphabetically gives `fig`, `kiwi`, `pear`. Compare the lengths first; compare the words only when the lengths match.

Repeated identical words are still separate entries when the input list contains repeats. If the question chooses the leftmost equal best element, use a strict update in the scan. Swapping equal values may not change printed text but can change a swap count, so follow the stated rule.

## 13. Times and dates as values

For a time in `HH:MM`, converting to `hours * 60 + minutes` makes subtraction and chronological comparison easier. For example, `07:35` is `455` minutes after midnight and `08:10` is `490`; the difference is `35` minutes. For a duration in `mm:ss`, multiply minutes by `60` and add seconds instead. Do not mix minutes after midnight with seconds of elapsed time.

To format a non-negative integer number of minutes back into a clock string:

```python
minutes_after_midnight = 7 * 60 + 5
hours = minutes_after_midnight // 60
minutes = minutes_after_midnight % 60
print(f"{hours:02d}:{minutes:02d}")  # 07:05
```

The `:02d` format uses at least two digits with a leading zero. Save original input strings where the problem asks to print them unchanged.

Dates written `dd/mm/yyyy` cannot generally be compared as ordinary strings in chronological order: `02/12/2026` comes before `15/01/2026` as text, but December is later than January. Compare year first, then month, then day. The order-preserving integer representation named in the official handout can help; keep the original date for the final report. Test equal dates, different years, month changes, and leading zeroes. Follow the handout's valid-date assumption.

## 14. Dictionaries and counting

A dictionary can associate each key with a count. These operations illustrate a single update; they are not a complete assigned program:

```python
counts = {"tea": 2}
name = "juice"
counts[name] = counts.get(name, 0) + 1
print(counts["juice"])  # 1
```

Check the first occurrence, repeated occurrences, one distinct key, and equal counts. Counting and sorting are separate tasks: a dictionary's iteration order should not be confused with the order specified for a report. When converting records to tuples, document which component is the name and which is the count.

## 15. Intervals and equal endpoints

An interval represents a start and an end. Two questions that look similar may use different meanings for equal endpoints:

- If overlapping **or touching** bookings are combined, `09:10–09:30` and `09:30–09:45` form one continuous block.
- If an occupancy interval includes arrival but excludes departure, someone leaving at `09:30` is not present with someone arriving at `09:30`.

Do not assume one convention applies to both tasks. For a sorted set of intervals, test disjoint intervals, identical starts, one interval contained in another, chains of overlaps, and equal endpoints. A later interval can end **before** the current block ends; an end time must not move backward when blocks are combined.

## 16. Two positions in sorted data

Some tasks process a sorted list from both ends. One position begins at the smallest item and another at the largest. After each decision, update every position required by the rule and make sure the loop eventually ends.

Check these separately:

- Two values add to the limit **exactly**: decide whether equality is allowed.
- Two values exceed the limit by one.
- One unprocessed item remains: it must be handled exactly once.
- All values are equal, and duplicate values represent distinct items.
- Only one item was provided, if the official constraints allow it.

A pair of positions is an approach to navigating a list, not permission to change the required ordering, output labels, or chosen algorithm.

## 17. How expensive is a sorting algorithm?

Big-O describes how the amount of work grows with input size; it is not an exact running time in seconds. A linear scan of `n` values is **O(n)** in the worst case. A standard selection sort makes

```text
(n - 1) + (n - 2) + ... + 1 = n * (n - 1) / 2
```

comparisons: **O(n²)**. It still searches the unsorted suffix when the input is already sorted. An in-place version uses **O(1)** extra algorithmic space beyond the input list, but a separate copy or a stored output history consumes additional space. Count comparisons, swaps, and shifts separately; their numbers need not be equal.

Python's `sorted()` creates a new list and `list.sort()` changes a list in place. Lecture 17 discusses both, including `key` and `reverse`. Use them to explore or check your understanding **only when allowed**. The assigned Lab 09 problems require you to write your own sorting logic; follow any additional restrictions on `min()` or `max()` in the individual handout.

## 18. Printing exactly what is requested

```python
items = [2, 5, 9]
print("Values:", items)            # Values: [2, 5, 9]
print("Values:", " ".join(map(str, items)))  # Values: 2 5 9
```

For a variable number of output lines, calculate how many the problem asks for: a line per event plus a final summary is not necessarily the same as one line per input item. When a function itself prints events and returns a count, print the returned count only where requested; do not print the function's `None` result.

Check labels, capitalization, colons, brackets, spaces, decimal precision, and the order of output lines. Temporary debug prints and input prompts must be removed before submission.

## 19. Boundary cases and debugging

The handout for your section defines which cases are valid. Within those limits, try:

| Case | Questions to ask |
| --- | --- |
| Already sorted | Is an unchanged pass, round, or step required in the output? |
| Reverse order | Are all swaps or shifts counted, and are the indexes still safe? |
| All equal | Are equal items left in the required relative order? Are unnecessary swaps counted? |
| Two items | Are the first and last pair both handled? |
| One item where allowed | Does a record report still contain exactly one row? |
| Duplicate best values | Is the leftmost best index selected when required? |
| First or last match | Does a scan include both boundaries? |
| Same primary key | Is the correct secondary key applied? |
| Touching intervals | Does the problem treat the shared endpoint as overlap or separation? |
| Equal-to-limit pair | Is `<=` required rather than `<`? |
| Day or year boundary | Are values compared chronologically, not by the text's day-first order? |
| Maximum input size | Does the code finish and use the stated algorithm? |

Use a tiny hand-worked case first. At each step, write down the index, the elements compared, the current list, and the event count. If one line is wrong, determine which earlier operation first changed the state unexpectedly. A zero-swap final pass is useful evidence that the list is sorted, but it does not excuse missing or extra required output.

## 20. Local testing and public tests

Run your program in Python or PyCharm and paste exactly the input from an example. Then compare every output line with the required output. To reveal invisible spaces, compare representations while debugging:

```python
print(repr("Step 1: 2 5"))
print(repr("Step 1: 2 5 "))  # the trailing space is visible here
```

These debugging prints do not belong in the final submission. If the course's Codeforces problem supplies public test files or an `all-tests.zip`, use them to check your program as well as your own additional cases. The availability of those materials depends on the published contest; check the actual problem page.

## 21. Common questions

**Can I call `sorted()` just to obtain the final answer?** Follow the handout: the assigned tasks require the stated manual sorting logic, so obtaining the same final order with a prohibited built-in is not sufficient.

**If the list becomes sorted during a pass, can I stop immediately?** Only if the handout allows it. When it requires a complete pass and a final zero-swap pass or round, both are part of the required trace.

**Do I count a comparison as a swap?** No. Count an exchange only under the problem's definition. Likewise, count shifts only when an element moves as specified.

**Why did the last item change when I was checking the first?** A negative index such as `items[-1]` refers to the final element. Check the boundary before reading the left neighbour.

**Why is my time or date in the wrong order?** Check that your comparison uses the relevant time unit and that a day-first date is compared by year, month, and day.

**Why is the last line `None`?** A function with no explicit return value returns `None`. A call can print its own lines without returning those lines as a value.

**Are spaces and repeated values important?** Yes. Exact formatting matters, and duplicate values can affect tie-breaking, counts, ranks, and the number of operations.

## 22. Optional extra practice

These are independent exercises, not assigned Lab 09 questions. They do not replace the official handout.

### A. Search a list of shelf labels

Implement `first_position(labels: list, target: str) -> int`. Return the first index whose label equals `target`, or `-1` if it is missing. Leave `labels` unchanged.

- **Input:** exactly two lines. Line 1 contains 0–20 lowercase labels separated by spaces; line 2 contains the target.
- **Output:** exactly one line containing the integer result.
- Example 1: labels `oak pine oak`, target `oak` → `0`.
- Example 2: labels `oak pine`, target `birch` → `-1`.
- Extra cases: an empty first line, match only at the last position, and repeated targets.

### B. Selection-sort a list of temperatures

Implement a selection sort that returns a **new** list of temperatures in ascending order. Leave the caller's original list unchanged. The selection scan should keep the leftmost occurrence when equal values are best.

- **Input:** one line containing 1–20 space-separated integers in `[-20, 50]`.
- **Output:** one line containing the resulting Python list.
- Example 1: `8 -2 5` → `[-2, 5, 8]`.
- Example 2: `4 4` → `[4, 4]`.
- Extra cases: one value, all equal, already sorted, reverse order, and both boundaries.

### C. Sort independent labels by length

Implement a comparator for lowercase labels: shorter labels come first; equal-length labels use alphabetical order. Use a sorting algorithm you write yourself.

- **Input:** one line containing 1–20 lowercase labels separated by spaces.
- **Output:** one line with the sorted labels separated by single spaces.
- Example 1: `map sun an` → `an map sun`.
- Example 2: `bb a aa` → `a aa bb`.
- Extra cases: one label, repeated labels, and several labels with equal lengths.

## 23. Submission checklist

- Use the latest official question sheet for **your own section** and submit four separate programs named `Lab9_PY_Z.py`, with the problem number and your student ID in place of `Y` and `Z`.
- Keep required function names, parameters, return types, side effects, algorithm, tie-breakers, and output format.
- Give every function a useful docstring, including helpers and `main`.
- Test the official examples, boundary cases, and any public tests provided in the contest.
- Remove prompts and debug prints. Submit identical code to **Codeforces and Canvas** by your section's deadline shown above.

For earlier topics, review the [function guide](../lab-03/README.md), [lists guide](../lab-06/README.md), [iteration guide](../lab-07/README.md), and [while/tuple/dictionary guide](../lab-08/README.md). The [platform submission guide](../lab-tutorial.md) explains the general workflow.
