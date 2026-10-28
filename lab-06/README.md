# Lab 06: Lists and Sequences — Detailed Study Guide

**Course:** COMP1010 - Introduction to Programming

**Week:** 06

**Preparation:** Lecture 10 (Memory in Python) and Lecture 11 (Objects, Lists and Sequences, Part 1)

This guide supports Fall 2026 Lab 06 for **IPRFAL261–IPRFAL264**. It teaches the shared Python techniques through independent examples, explanations, extreme cases, and optional practice. Use the latest official question sheet for **your own section** for the assigned problems, exact function signatures, input constraints, and output labels. The examples here are separate from those assigned programs.

**Submission:** Submit each of the **five** problems as a separate Python 3 program to **both Codeforces and Canvas**, using identical code, **before the end of your lab session**. See [Section 24](#24-submission-checklist).

Lab 06 changes an important assumption from earlier guides: a function may be required to **print directly**, **return a list**, or **return a Boolean**, depending on its contract. Follow that contract. A list is one returned object even when it contains several elements.

## What you should be able to do

- Explain how names, objects, references, and function calls relate to list mutation.
- Read numeric lists, lists of words, and whole-line names correctly.
- Use positive and negative indices, slices, and slice assignment.
- Choose between aliasing an existing list and making a copy.
- Distinguish changing a list from returning a new value.
- Use list methods, built-in functions, concatenation, and repetition correctly.
- Handle duplicate values, absent targets, empty results, and changing indices.
- Distinguish equality of ordered lists, equality with multiplicity, and equality of totals.
- Explain why a mutable default argument can retain data between calls.
- Test state changes and output formatting as well as the final numeric answer.

The examples use basic functions, conditionals, and list operations. You do not need classes, recursion, comprehensions, custom sorting algorithms, or advanced type annotations. Some assigned problems explicitly prohibit loops or require particular list methods; equivalent-looking output alone does not satisfy those requirements.

## Navigation

- [1. Read the function and program contracts](#1-read-the-function-and-program-contracts)
- [2. Names, objects, and memory](#2-names-objects-and-memory)
- [3. Lists and other sequences](#3-lists-and-other-sequences)
- [4. Read input without changing its meaning](#4-read-input-without-changing-its-meaning)
- [5. Indexing and valid positions](#5-indexing-and-valid-positions)
- [6. Slicing and boundary cases](#6-slicing-and-boundary-cases)
- [7. Aliases, equality, and copies](#7-aliases-equality-and-copies)
- [8. Concatenation, repetition, append, and extend](#8-concatenation-repetition-append-and-extend)
- [9. What list methods return](#9-what-list-methods-return)
- [10. Removal, search, and duplicates](#10-removal-search-and-duplicates)
- [11. Sorting and reversing](#11-sorting-and-reversing)
- [12. Function types and docstrings](#12-function-types-and-docstrings)
- [13. Mutation, rebinding, and slice assignment](#13-mutation-rebinding-and-slice-assignment)
- [14. Safe default arguments](#14-safe-default-arguments)
- [15. Independent lists and unchanged originals](#15-independent-lists-and-unchanged-originals)
- [16. Counts, totals, and numeric results](#16-counts-totals-and-numeric-results)
- [17. Operation order and changing state](#17-operation-order-and-changing-state)
- [18. Comparing lists in different ways](#18-comparing-lists-in-different-ways)
- [19. Counts, partitions, and zero](#19-counts-partitions-and-zero)
- [20. Exact output and empty lines](#20-exact-output-and-empty-lines)
- [21. Extreme cases and section testing prompts](#21-extreme-cases-and-section-testing-prompts)
- [22. Local testing and common questions](#22-local-testing-and-common-questions)
- [23. Optional extra practice](#23-optional-extra-practice)
- [24. Submission checklist](#24-submission-checklist)

## 1. Read the function and program contracts

A function call and a complete submitted program have related but different inputs and outputs.

| Contract | Questions to answer before coding |
| --- | --- |
| Program input | How many lines? Which lines contain counts, lists, or individual values? |
| Function parameters | Which already-read values are passed to the function? What are their types? |
| Function behavior | Does it modify the supplied list, create another list, print, return, or combine some of these? |
| Required operations | Must it use a named method? Are loops prohibited? |
| Valid inputs | Can the list be empty? Is an index guaranteed valid? Is zero permitted as a count? |
| Program output | How many lines? Does each line show a number, elements without brackets, a Python list, or a Boolean? |
| State after a call | Must the caller's original list have changed, or must it remain unchanged? |

For an independent example, suppose a function receives a list of labels and **returns its length**. The program may read those labels from one input line, call the function, then print the integer. The function should receive the list; it should not ask the user to enter that line again.

If a different function is required to print two lines itself, calling `print(function_call(...))` can add an unwanted third line containing `None`.

Keep these details on your planning sheet:

- **Index** means a position. **Value** means an element stored at a position.
- **In-place** means the existing list object must change.
- **A copy** means another list object is created.
- **First occurrence** means only one matching position is affected.
- **A new default list** means independent calls that omit the argument must not share accumulated data.
- **Original first/last element** refers to the state before the required modifications.

Treat the handout's input guarantees as guarantees. Handle the exceptional cases it explicitly specifies, but do not invent extra error messages, retry prompts, or stricter limits. Lab 05's validation patterns are not a requirement to wrap every Lab 06 function in `try/except`.

## 2. Names, objects, and memory

An assignment binds a name to an object. Assigning an existing list to another name does not automatically copy its elements.

```python
readings = [6, 13]
other_name = readings
other_name.append(21)

print(readings)              # [6, 13, 21]
print(other_name is readings)  # True
```

Both names refer to the same list. You can sketch this as `readings -> list A <- other_name`; list A contains the three values after the append.

Contrast that with rebinding an integer name:

```python
count = 6
other_count = count
other_count = other_count + 1

print(count)        # 6
print(other_count)  # 7
```

Integer objects are immutable. The addition produces a result and the assignment rebinds `other_count`; it does not change the value referred to by `count`.

### How this connects to Lecture 10

Use the lecture's global-space, heap, and call-frame model to answer practical questions:

- A name defined at the main program level belongs to that module's global namespace.
- A function call has a frame containing its parameter bindings and local bindings.
- Passing a list binds the parameter to the supplied object. It does not create a list copy merely because the parameter has a different name.
- A local binding can disappear after the call while its list remains reachable through the caller or a returned reference.

Python manages object memory automatically. CPython uses reference counting together with garbage collection for cycles. Exact reference counts, object IDs, and reclamation timing are implementation details; do not hard-code them into a lab program or expect the same numbers on every Python version.

`del some_name` removes that binding. It does not mean “erase this list even if another name still refers to it.” You do not need manual memory allocation or explicit garbage-collector calls for this lab.

## 3. Lists and other sequences

A list stores an **ordered** collection of element references. Lists can contain repeated values, and their elements can be changed.

```python
temperatures = [12, 8, 12]
empty = []

print(len(temperatures))  # 3
print(temperatures[1])    # 8
temperatures[1] = 9
print(temperatures)       # [12, 9, 12]
print(len(empty))         # 0
```

Order matters: `[3, 7]` and `[7, 3]` are different lists. Repetition also matters: `[3, 3, 7]` has three elements, not two.

Python permits mixed element types, but keep the type required by the problem. Numeric input should become a list of numbers; card labels, item names, and book titles remain strings.

### A string and a list of strings are different

```python
text = "Sky Blue"

print(list(text))    # ['S', 'k', 'y', ' ', 'B', 'l', 'u', 'e']
print(text.split())  # ['Sky', 'Blue']
print([text])        # ['Sky Blue']
```

- `list(text)` produces individual characters, including the space.
- `text.split()` produces whitespace-separated words.
- `[text]` creates a one-element list containing the complete string.

Strings support indexing and slicing, but they are immutable. A list can support element assignment; `text[0] = "s"` would raise `TypeError`. A list containing a string can replace that whole string element without changing the string object's characters.

## 4. Read input without changing its meaning

### Integers on one line

```python
numbers = list(map(int, input().split()))
print(numbers)
```

For input `8 -3 8 0`, output is `[8, -3, 8, 0]` on one line.

Read the expression from the inside out:

1. `input()` reads one line and removes its terminating newline.
2. `.split()` separates whitespace-delimited tokens, each initially a string.
3. `map(int, ...)` converts each token to an integer.
4. `list(...)` collects the converted values into a list.

Repeated spaces and tabs are accepted by `split()`. `8,-3,8` is not the same format: commas are not whitespace separators. A numeric line normally contains no literal list brackets or quote marks.

### A count followed by a list

```python
size = int(input())
numbers = list(map(int, input().split()))
print(size)
print(*numbers)
```

Input is **two lines**:

```text
3
8 -3 0
```

Output is **two lines**: the supplied count, then the numbers separated by spaces.

```text
3
8 -3 0
```

The first line is metadata about the second line. Do not add it to the list. If the handout guarantees exactly `size` values, you can rely on that guarantee. During local testing, check that your own test input satisfies it.

### A list of tokens versus a whole-line name

```python
labels = input().split()
print(labels)
```

Input `Oak Elm Pine` produces `['Oak', 'Elm', 'Pine']`. Each word is a separate label.

```python
name = input()
print([name])
```

Input `North Studio` produces `['North Studio']`. This is one complete name containing a space. If the specification asks for four whole-line names, use four input calls and preserve each line as a single string. Do not split every name into words.

### Two integers on the same line

```python
parts = input().split()
position = int(parts[0])
amount = int(parts[1])
print(position)
print(amount)
```

For input `2 4`, output is **two lines**, `2` and `4`. Both values were read with **one** input call. Convert the values individually; `int(parts)` would fail because `parts` is a list.

### Empty line, spaces-only line, and missing line

| Situation | What the numeric-list expression does |
| --- | --- |
| An empty line is supplied | `input()` returns `""`; splitting produces `[]`; the result is an empty list. |
| A line contains only whitespace | `split()` also produces `[]`. |
| No line exists because input has ended | `input()` raises `EOFError`. |
| A token is not a valid integer | `int(...)` raises `ValueError`. |

An empty list is not universally valid or invalid. Follow the specific problem. A task may require an explicit message for fewer than two elements, while another guarantees a positive count and never supplies an empty list.

Do not use `eval(input())` to read these lists. The required input is tokens or whole lines, not Python expressions. Do not call `.lower()` or `.strip()` on names unless the statement permits changing their case or outer whitespace.

## 5. Indexing and valid positions

For a list of length `n`, ordinary nonnegative element indices run from `0` through `n - 1`.

```python
values = [14, 6, 19, 2]

print(values[0])   # 14
print(values[3])   # 2
print(values[-1])  # 2
print(values[-4])  # 14
```

Negative indices count from the end. `-1` means the last element; `-len(values)` means the first element. Both `values[4]` and `values[-5]` would raise `IndexError` here.

### Check the contract before accepting a negative index

Python supports negative indices. A problem that requires a valid **0-based nonnegative index** may deliberately reject or ignore `-1`.

For a toy list lookup with that contract:

```python
labels = ["Oak", "Elm"]
position = -1

if 0 <= position < len(labels):
    print(labels[position])
else:
    print("Outside the permitted positions")
```

Output is `Outside the permitted positions`. The `else` follows this toy contract, even though Python itself could evaluate `labels[-1]`.

### Empty and one-element lists

- An empty list has no first or last element. Both `[0]` and `[-1]` would fail.
- In a one-element list, `[0]` and `[-1]` refer to the same position.
- A rule requiring at least two elements must be checked before reading its endpoints or carrying out later work.
- After removing an element, check bounds against the **current** length. An old last index may no longer exist.

Insertion positions behave differently from element lookup. `items.insert(len(items), value)` appends after the last element. Python also clips oversized insertion positions to an end; it does not use exactly the same bounds behavior as `items[index]` or `items.pop(index)`.

## 6. Slicing and boundary cases

The general form is `items[start:stop:step]`. The stop boundary is excluded. Omitting `step` means `1`.

```python
values = [14, 6, 19, 2, 11]

print(values[:2])    # [14, 6]
print(values[2:])    # [19, 2, 11]
print(values[1:4])   # [6, 19, 2]
print(values[::2])   # [14, 19, 11]
print(values[::-1])  # [11, 2, 19, 6, 14]
print(values)        # [14, 6, 19, 2, 11]
```

For a list, slicing creates a new outer list. It does not reverse or remove elements from the original merely because a slice looks different.

### A boundary is different from an element index

```python
values = [4, 9]

print(values[:20])   # [4, 9]
print(values[20:])   # []
print(values[1:1])   # []
print(values[1:0])   # []
print(values[1:0:-1])  # [9]
```

Out-of-range slice boundaries are clipped. A direct lookup such as `values[20]` still fails. With a positive step, a start boundary after the stop boundary produces an empty result. A negative step changes the direction of traversal.

For `values[::-1]`, omitted boundaries adapt to backward traversal. Do not assume that explicitly writing a stop of `-1` has exactly the same meaning as omitting the stop. A step of `0` raises `ValueError`.

### The negative-zero trap

```python
values = [4, 9, 12]
amount = 0

print(values[:amount])   # []
print(values[-amount:])  # [4, 9, 12]
print(values[:-amount])  # []
```

`-0` is `0`. Therefore `values[-amount:]` means `values[0:]` when `amount` is zero. It does **not** mean “the last zero elements.” Reason about the zero-count behavior explicitly wherever a count can be zero.

A slice yielding `[]` is often a legitimate result. It does not automatically require an error message or permission to skip a required output line.

## 7. Aliases, equality, and copies

### Same values versus the same object

```python
first = [2, 9]
alias = first
copy = first[:]

print(first == copy)    # True
print(first is copy)    # False
print(first is alias)   # True

alias.append(15)
print(first)            # [2, 9, 15]
print(copy)             # [2, 9]
```

- `==` compares list contents, including order and repeated elements.
- `is` checks whether two references designate the same object.
- Use `==` for value comparisons and `is None` for the special default marker.
- Use `is` in a local test when a contract specifically requires preserving or changing object identity.

For a flat list, `items[:]`, `items.copy()`, and `list(items)` all create a new outer list. `second = items` creates another reference to the existing list.

### A shallow copy still shares nested objects

This small example explains a memory limitation; nested-list processing is not needed for the assigned programs.

```python
original = [[2], [9]]
outer_copy = original[:]
outer_copy[0].append(5)

print(original)             # [[2, 5], [9]]
print(outer_copy is original)  # False
print(outer_copy[0] is original[0])  # True
```

The outer lists differ, but corresponding inner lists are still shared. A shallow copy is normally enough to protect a flat list of integers or strings from element replacement, addition, removal, or reordering. It does not recursively duplicate every object inside a nested structure.

## 8. Concatenation, repetition, append, and extend

### Concatenation creates a list containing both sequences

```python
morning = [3, 8]
afternoon = [5]
combined = morning + afternoon

print(combined)  # [3, 8, 5]
print(morning)   # [3, 8]
```

This joins elements; it does not add corresponding numbers. Both operands of list concatenation must be lists. To concatenate one scalar value, put it in a one-element list, such as `[5]`.

### Append adds one object; extend adds its elements

```python
first = [3, 8]
second = [3, 8]
extra = [5, 1]

first.append(extra)
second.extend(extra)

print(first)   # [3, 8, [5, 1]]
print(second)  # [3, 8, 5, 1]
```

Appending a list creates a nested list element. Extending with a list adds its elements individually. This difference matters when a function expects a flat numeric list.

Extending with a string adds its characters. Appending a string adds one whole string:

```python
whole = []
characters = []
whole.append("Elm")
characters.extend("Elm")

print(whole)       # ['Elm']
print(characters)  # ['E', 'l', 'm']
```

### Repetition repeats elements

```python
print([3, 8] * 2)  # [3, 8, 3, 8]
print([0] * 3)     # [0, 0, 0]
print([3, 8] * 0)  # []
```

List repetition does not multiply each numeric element. It repeats references to the elements. With immutable integers this is usually straightforward; with nested mutable lists it can create shared inner objects.

```python
rows = [[]] * 2
rows[0].append(7)
print(rows)  # [[7], [7]]
```

Both positions refer to the same inner list. Do not use this pattern when you intend independent mutable containers.

## 9. What list methods return

Distinguish the **list after the call** from the **value returned by the call**.

| Operation | Effect on the existing list | Return value |
| --- | --- | --- |
| `items.append(value)` | Adds one element at the end. | `None` |
| `items.extend(other)` | Adds the elements from `other`. | `None` |
| `items.insert(index, value)` | Inserts one element before a position. | `None` |
| `items.remove(value)` | Removes the first equal element; fails if absent. | `None` |
| `items.pop(index)` | Removes the element at an index; fails if invalid. | The removed element |
| `items.pop()` | Removes the last element; fails if empty. | The removed element |
| `items.sort()` | Sorts this list in ascending order by default. | `None` |
| `items.reverse()` | Reverses the current order. | `None` |
| `items.count(value)` | Does not change the list. | An integer count |
| `items.index(value)` | Does not change the list; fails if absent. | The first matching index |
| `items.copy()` | Does not change the list. | A new shallow list |
| `sorted(items)` | Does not change the list. | A new sorted list |

```python
readings = [9, 3]
method_result = readings.append(7)

print(readings)       # [9, 3, 7]
print(method_result)  # None
```

Avoid `readings = readings.append(7)`, `readings = readings.sort()`, and `readings = readings.reverse()`. They replace your list variable with `None`. Call these methods as their own statements, then use the list variable.

`pop()` is different:

```python
readings = [9, 3, 7]
removed = readings.pop()

print(removed)   # 7
print(readings)  # [9, 3]
```

If a function must return the updated list, it can mutate the list with a method and later return the list variable. Returning the result of `append()` would return `None` instead.

## 10. Removal, search, and duplicates

### Removing a value versus removing a position

```python
by_value = [4, 1, 4]
by_position = [4, 1, 4]

by_value.remove(1)
removed = by_position.pop(1)

print(by_value)     # [4, 4]
print(by_position)  # [4, 4]
print(removed)      # 1
```

These happen to end with the same list because the value `1` occupies index `1`. That coincidence can hide a bug. Compare a second example:

```python
by_value = [1, 4, 1]
by_position = [1, 4, 1]

by_value.remove(1)
by_position.pop(1)

print(by_value)     # [4, 1]
print(by_position)  # [1, 1]
```

`remove` searches for a value. `pop` selects a position. Numeric equality between a value and an index does not make the operations interchangeable.

### Only the first occurrence

```python
values = [8, 2, 8, 8]
print(values.count(8))  # 3
print(values.index(8))  # 0
values.remove(8)
print(values)           # [2, 8, 8]
```

One removal does not remove all duplicates. Similarly, `.index(target)` finds the first match, not every matching position. If a task says to replace the first match, preserve later occurrences.

### An absent value is a normal case when the contract says so

```python
values = [8, 2, 8]
target = 6

if target in values:
    values.remove(target)

print(values)  # [8, 2, 8]
```

`target in values` returns a Boolean. It is not a position. `values.count(target)` returns `0` when absent; `.index(target)` and `.remove(target)` would raise `ValueError` if called without a suitable check.

Use `target in values`, not `if values.index(target)`. A valid first match at index `0` is false in a Boolean context, and an absent target raises an exception.

After insertion or removal, later positions may move. Re-evaluate a target's position in the current list rather than applying an index saved for an earlier state.

## 11. Sorting and reversing

### Sort the same list or obtain a sorted copy

```python
values = [7, -2, 7, 3]
ordered = sorted(values)

print(ordered)  # [-2, 3, 7, 7]
print(values)   # [7, -2, 7, 3]

values.sort()
print(values)   # [-2, 3, 7, 7]
```

Both sorting approaches preserve duplicates. Choose according to the function's mutation contract and the specifically required method. If the question requires `.sort()`, silently substituting `sorted()` does not satisfy that instruction.

### Reversing is not descending sorting

```python
values = [7, -2, 3]
backward = values[::-1]
descending = sorted(values, reverse=True)

print(backward)    # [3, -2, 7]
print(descending)  # [7, 3, -2]
print(values)      # [7, -2, 3]
```

Reversing keeps the existing order but walks through it backward. Descending sorting orders values by magnitude or comparison rules. Reversing an unsorted list generally does not produce a sorted list.

`.reverse()` changes the existing list and returns `None`; `[::-1]` returns a new outer list. Use the required approach when the statement names one.

### Numeric order and string order

```python
print(sorted(["12", "3", "8"]))  # ['12', '3', '8']
print(sorted([12, 3, 8]))        # [3, 8, 12]
```

String comparison is lexicographic, based on character ordering; numeric-looking strings are still text. Convert actual numeric input before numeric sorting. For names and labels, preserve strings and use the specified comparison rules.

Default string sorting is case-sensitive. Do not add `.lower()` or case-insensitive sort keys unless requested. A list mixing integers and strings normally cannot be sorted together in Python 3; this is often a sign of incorrect input conversion.

## 12. Function types and docstrings

Use the simple annotation form `parameter: type` and `-> return_type`. For this lab, `list`, `int`, `float`, `str`, `bool`, and `None` cover the examples without `list[int]`, tuple annotations, or union-type syntax.

```python
def label_count(labels: list) -> int:
    """Return how many labels are supplied.

    Args:
        labels: A list of strings; it may be empty.

    Returns:
        The number of labels. The supplied list is unchanged.
    """
    count = len(labels)
    return count


result = label_count(["Oak", "Elm"])
print(result)  # 2
```

`labels: list` describes the container type; the docstring describes its element type. `-> int` describes the returned count. An annotation does not parse input or enforce a type at runtime.

| Function's required behavior | Suitable simple return annotation |
| --- | --- |
| Returns an integer count | `-> int` |
| Returns a numeric value required to be a float | `-> float` |
| Returns a new or updated list | `-> list` |
| Returns a membership result | `-> bool` |
| Prints its results and returns no useful value | `-> None` |

### Printing and returning

```python
def display_labels(labels: list) -> None:
    """Print labels on one line without changing the list.

    Args:
        labels: A list of strings; it may be empty.

    Returns:
        None. Output is written directly to the console.
    """
    print(*labels)


display_labels(["Oak", "Elm"])
```

Output is one line: `Oak Elm`. The function's return type is `None` even though it prints strings. Calling `print(display_labels(...))` would print the labels and then an extra `None` line.

If the handout requires a numeric float, returning an integer and only formatting it with `.1f` outside the function does not change that returned object's type. Conversely, `f"{value:.1f}"` is a string. Match the required return type and format only at the required output step.

Some command-based functions return a list for one action and a Boolean for another. Document the return behavior **for each action**. Do not falsely annotate the whole function `-> list` if a valid action returns `bool`. You can leave that return annotation off while using a precise docstring; advanced union annotations are not needed here.

Every function, including a helper or inner function, needs a docstring explaining its purpose, parameters, and return value. For a list parameter, also explain whether the function mutates it. A comment outside the function is not a substitute for a docstring.

## 13. Mutation, rebinding, and slice assignment

A parameter is a local name. Rebinding it and mutating the object it refers to have different effects on the caller.

```python
def rebind_demo(values: list) -> None:
    """Rebind a local parameter without changing the caller's list.

    Args:
        values: A list supplied by the caller.

    Returns:
        None. The caller's list is unchanged.
    """
    values = [90, 91]


def replace_demo(values: list) -> None:
    """Replace the contents of the caller's existing list.

    Args:
        values: A list whose contents will become two demo values.

    Returns:
        None. The supplied list object is modified in-place.
    """
    values[:] = [90, 91]


original = [2, 5]
alias = original
rebind_demo(original)
print(original)          # [2, 5]
replace_demo(original)
print(original)          # [90, 91]
print(alias)             # [90, 91]
print(alias is original)  # True
```

`values = ...` changes a local binding. `values[:] = ...` assigns to a slice of the existing list, so aliases observe the change. These statements can produce similar-looking values locally while satisfying different mutation contracts.

### Slice assignment can change length

```python
values = [2, 5, 8, 11]
values[1:3] = [40]
print(values)  # [2, 40, 11]

values[1:1] = [6, 7]
print(values)  # [2, 6, 7, 40, 11]
```

The first assignment replaces two positions with one element. The second inserts two elements into an empty slice. Ordinary slice assignment with a step of `1` can change the list length; element assignment such as `values[1] = 6` changes one element without changing length.

For an in-place task, verify both the resulting contents and what a second reference to the original list sees. Merely returning a newly arranged list may not satisfy an in-place requirement.

## 14. Safe default arguments

Default argument values are evaluated when Python executes the function definition. A mutable default list can therefore survive across several calls.

### An intentionally unsafe example

```python
def remember_bad(value: int, history=[]) -> list:
    """Demonstrate an unsafe shared default list.

    Args:
        value: An integer to append.
        history: A list of integers; its default is shared between calls.

    Returns:
        The supplied or shared default list after appending value.
    """
    history.append(value)
    return history


print(remember_bad(2))  # [2]
print(remember_bad(5))  # [2, 5]
```

The second omitted argument reuses the first call's default list. It does not start with an independent empty history. This example illustrates a bug; do not use this default in your submitted functions.

### Use None to mean that no list was supplied

```python
def remember(value: int, history=None) -> list:
    """Append one value to a supplied history or a new independent list.

    Args:
        value: An integer to append.
        history: A list of integers, or None to create a fresh list.

    Returns:
        The updated list. A supplied list is modified and returned;
        omitted or None history creates a new list for this call.
    """
    if history is None:
        history = []
    history.append(value)
    return history


first = remember(2)
second = remember(5)
print(first)             # [2]
print(second)            # [5]
print(first is second)   # False

shared = []
returned = remember(8, shared)
remember(9, shared)
print(shared)            # [8, 9]
print(returned is shared)  # True
```

This example has one simple action. It is not an implementation of any assigned command manager or its prescribed sequence of calls.

The optional `history` parameter deliberately has no type annotation here: it accepts either a list or `None`. The docstring states both possibilities. This avoids advanced annotation syntax and avoids claiming that `None` is itself a list.

### None and an empty supplied list are different

`if not history` is true for an empty list as well as for `None`. If the caller explicitly supplies `shared = []`, replacing it with a new list can break the requirement to update that supplied object.

Use `if history is None` for this safe-default pattern. A supplied empty list is valid state, not an omitted argument.

Also distinguish these calls:

- Omitting the optional list requests a new list under this contract.
- Passing `None` explicitly also requests a new list.
- Passing the same existing list intentionally reuses its state.
- Passing a copy supplies a different outer list.

Default command strings belong to function calls, not automatically to standard input. Read command lines only when the input specification actually contains them. Do not invent a menu or an input loop for a fixed sequence of calls.

## 15. Independent lists and unchanged originals

If a function must leave an original list unchanged, plan where a new list is created before applying modifications.

```python
original = ["Oak", "Elm"]
proposal = original.copy()
proposal.append("Pine")

print(proposal)  # ['Oak', 'Elm', 'Pine']
print(original)  # ['Oak', 'Elm']
```

`proposal = original` would produce an alias and would let the append change `original`. Assigning a new name is not enough to protect the original.

For a function that returns a new report or preview:

- Distinguish the returned list from the caller's list.
- Include any extra supplied item in the result only as specified.
- Keep repeated values unless the problem explicitly removes duplicates.
- Sort the data portion according to the required rule. A fixed heading is not a data item to sort among those values.
- Verify the original after the call, including its order and length.

For a function that must update the supplied list, making a private copy instead can be the opposite bug: the caller still sees the old state. Choose from the contract, not from a universal rule that copies are always better.

A membership check should inspect the correct list and return a Boolean without changing that list. In particular, checking a newly created independent list is different from checking a previously populated list.

### Keep a reference or keep a snapshot

An alias is useful when you intentionally want to observe later changes. A shallow copy is useful when you want to remember a flat list's contents at a particular time.

```python
live = [3, 6]
watch = live
snapshot = live[:]
live.append(9)

print(watch)     # [3, 6, 9]
print(snapshot)  # [3, 6]
```

When debugging a sequence of calls, a saved variable can change later if it is an alias. Record which variables share an object before treating their displayed values as independent history.

## 16. Counts, totals, and numeric results

### Empty input affects different built-ins differently

| Operation on an empty list | Result |
| --- | --- |
| `len([])` | `0` |
| `sum([])` | `0` |
| `sorted([])` | `[]` |
| `[].count(7)` | `0` |
| `min([])` or `max([])` | Raises `ValueError`. |
| `sum([]) / len([])` | Raises `ZeroDivisionError`. |

Do not assume that all list operations require a non-empty list, or that all of them support an empty list. Identify the precondition of the operation you are about to use.

For all-negative data, the maximum can still be negative. Do not assume a maximum starts at zero. Likewise, a list of zeros has valid minimum, maximum, and total values; zero is not evidence that a value is missing.

### Mean, median, and the size of the actual dataset

The mean uses a total and a count. The median depends on middle position(s) in an ordered dataset. An outlier can affect these quantities differently; they are not interchangeable.

For example, in the ordered dataset `[1, 4, 30]`, the middle value is `4`, while the arithmetic mean is `35 / 3`.

Useful position checks to work out on paper:

| Dataset length | Middle position(s), using 0-based indexing |
| --- | --- |
| `1` | Index `0`. |
| `2` | Indices `0` and `1`. |
| `5` | Index `2`. |
| `6` | Indices `2` and `3`. |

When two middle values are combined by an average, the result may be fractional or negative. Integer floor division is not a substitute for ordinary division in that calculation. Work out the assigned function yourself from its definition and required methods.

If a task combines two datasets, reason about the length of the combined dataset. Averaging the two separate medians is not generally the median of the combined data. Concatenation and `extend()` retain duplicates; `append(other_list)` introduces a nested list instead of merging its elements.

### A float result versus its displayed precision

```python
whole_value = 12
numeric_result = float(whole_value)

print(type(numeric_result) is float)  # True
print(f"{numeric_result:.1f}")       # 12.0
print(f"{numeric_result:.2f}")       # 12.00
```

`.1f` and `.2f` control display precision. `round(value, 2)` alone does not guarantee two trailing decimal places when printed. Apply the precision from the particular problem, not one fixed precision to every Lab 06 output.

### Distinct values and selected counts

Where the specification explicitly requires the number of distinct values, `len(set(values))` counts distinct values. For `[4, 4, 9]`, the list length is `3`, but the distinct count is `2`. Sets do not preserve duplicates and should not replace a list when multiplicity or order matters.

If a task requests the first `k` elements of an ordered result, slicing naturally stops at the available length. A request larger than the list length is allowed only where the contract permits it. Do not deduplicate the result merely because several selected elements have the same value.

The statistical range of data describes the distance between its minimum and maximum. It is unrelated to Python's `range(...)` object.

## 17. Operation order and changing state

Methods operate on the list as it exists **at that moment**. Reordering statements can change the answer even when each individual method call is valid.

```python
first = [4, 9]
first.append(2)
first.reverse()

second = [4, 9]
second.reverse()
second.append(2)

print(first)   # [2, 9, 4]
print(second)  # [9, 4, 2]
```

This independent two-operation example is enough to expose the issue. For an assigned pipeline, make your own state table after each required step rather than combining the entire pipeline into one expression.

| Point to record | Reason |
| --- | --- |
| Current contents | Shows whether the preceding operation did what you expected. |
| Current length | Removing or inserting changes which positions exist. |
| The original endpoint value, when required | Reading the endpoint after modification may select a different value. |
| The number of copies of a target | One removal or replacement must not silently affect all duplicates. |
| Which names share the list | A later call may also change an earlier variable. |

Use a local trace while debugging; remove trace prints from the submission. Do not simplify away explicitly required steps just because the two provided examples happen to end with the same output.

If a target is inserted earlier in a pipeline, a later membership or removal check must inspect the updated list. If an operation removes one smallest or largest element, repeated extreme values remain unless the task says to remove every occurrence.

## 18. Comparing lists in different ways

Different comparisons answer different questions.

| Question | Appropriate basic comparison |
| --- | --- |
| Same values in the same order, including duplicates? | `first == second` |
| Same values with the same multiplicities, ignoring order? | `sorted(first) == sorted(second)` for comparable element types. |
| Same distinct values, ignoring order and multiplicities? | `set(first) == set(second)` |
| Same numeric total? | `sum(first) == sum(second)` |
| The exact same list object? | `first is second` |

```python
first = [2, 2, 9]
second = [2, 9, 9]

print(set(first) == set(second))        # True
print(sorted(first) == sorted(second))  # False
```

Both lists contain the same distinct values, but not the same number of copies of each value. This is why a set is unsuitable for a **multiset** comparison: a multiset comparison must preserve multiplicity.

Equal sums are weaker still:

```python
first = [2, 7]
second = [4, 5]

print(sum(first) == sum(second))        # True
print(sorted(first) == sorted(second))  # False
```

Neither equal totals nor equal distinct-value sets prove that the ordered sequences match.

### Comparisons can overlap

If two numeric lists are identical, their sums are also equal. In a classification task where a more specific relation takes priority, test that relation before a weaker fallback. Otherwise the weaker condition can hide the required classification.

For comparisons involving two halves or two ends:

- Establish the exact slices required by the statement.
- For odd lengths, determine whether the middle element is excluded, included once, or included in both views.
- Distinguish comparing in the original order from comparing a reversed view or an order-independent collection.
- Check whether the two views may overlap. Overlap is not automatically invalid.
- Compare the specified sums in the specified direction; greater-than and less-than are not interchangeable.
- Use the exact output words from your own section. Similar section tasks may use different labels.

## 19. Counts, partitions, and zero

A position identifies a boundary or element; a count describes how many elements are selected. They can have different valid ranges.

Before writing slicing code for a sequence task, draw the current sequence and mark:

1. Its first and last elements.
2. The split boundary, if one is used.
3. Which part is selected or moved.
4. Whether selection starts from the beginning or the end.
5. The order in which the selected elements must be displayed.

Do not assume that a task selecting from the beginning also describes a task selecting from the end. Do not accidentally take a selection from the original sequence if the specification first changes that sequence.

| Permitted count | Questions to test |
| --- | --- |
| `0` | Is the selected part empty? Is the other part unchanged? Does negative-zero slicing change your interpretation? |
| `1` | Is exactly one element selected from the correct end? |
| The full sequence length | Is the other part empty? Does the selected part still have the required order? |
| Larger than the length | Is this explicitly permitted, such as a capped preview, or outside this problem's domain? |

If a count of zero is allowed, it is a genuine case to implement. Do not change it to one just to avoid an empty slice. If the contract starts at one, zero is outside that domain and should not acquire an invented output rule.

An empty remaining list and an empty selected list can require different blank lines in a two-line output. Keep track of **which** result is empty rather than removing all empty output.

## 20. Exact output and empty lines

### A Python list versus its space-separated elements

```python
numbers = [6, -1, 8]
print(numbers)   # [6, -1, 8]
print(*numbers)  # 6 -1 8
```

`print(numbers)` displays list notation with brackets and commas. `print(*numbers)` passes the elements as separate arguments and uses a space between them by default. Follow the output representation requested for that particular line.

The `*` here unpacks arguments to `print`; it does not multiply the list or alter its elements.

For strings:

```python
labels = ["Oak", "Elm"]
print(labels)           # ['Oak', 'Elm']
print(*labels)          # Oak Elm
print(" / ".join(labels))  # Oak / Elm
```

`join()` expects strings. Joining a list of integers directly raises `TypeError`. For numeric output without brackets, `print(*numbers)` avoids that conversion issue.

### An empty result still occupies its required line

```python
print("Above")
print(*[])
print("Below")
```

Output is **three lines**, with the second line blank:

```text
Above

Below
```

By contrast, `print([])` prints the two visible brackets `[]`. A branch that skips printing altogether produces no line. These are three different outputs.

In local debugging, `repr(captured_output)` helps reveal newlines. The three-line output above has the representation `'Above\n\nBelow\n'`. The quote marks and backslashes belong to the debugging representation; do not print them as the submitted answer.

### Lists of names and Booleans

- If the contract asks for ordinary Python list output, print the list directly. Python selects appropriate quote styles and escaping for its string elements; do not try to build bracketed output manually.
- Preserve spaces within a name such as `North Studio`.
- Keep case-sensitive values distinct: `"Oak"` and `"oak"` need not match.
- Print Boolean values as `True` and `False` where required, not lowercase text or `1` and `0`.
- A false membership result is valid output. Do not suppress it with an `if result:` wrapper.
- Print each required line exactly once, without labels, input prompts, menus, or debug messages unless the output specification requests them.

## 21. Extreme cases and section testing prompts

Extreme cases are valid inputs that stress a boundary or a likely mistaken assumption. They are not permission to invent behavior for input excluded by the specification.

### Shared test categories

| Category | Independent data to consider | What it checks |
| --- | --- | --- |
| Empty list, when permitted | `[]` | Endpoint guards, empty slices, explicit short-list behavior, blank output lines. |
| One element | `[11]` | First and last refer to the same element; a middle is not automatically a pair. |
| Two elements | `[-7, 4]` | Smallest meaningful endpoint pair and even-length boundaries. |
| All values equal | `[6, 6, 6, 6]` | Duplicates, first-only removal, zero spread, and preserved counts. |
| All values negative | `[-12, -5, -9]` | Incorrect assumptions that a maximum or a total must be nonnegative. |
| Contains zero | `[0, -3, 0, 7]` | Confusing a valid value or index `0` with an absent result. |
| Already ordered | `[-2, 3, 8]` | Sorting a sequence that needs no visible change. |
| Reverse ordered | `[8, 3, -2]` | Direction errors hidden by nicer samples. |
| Duplicate extreme values | `[1, 1, 8, 8]` | Removing one extreme versus removing every extreme. |
| Repeated target | `[7, 2, 7, 7]` | First-only search, replacement, or removal. |
| Target absent | `[7, 2]` with target `9` | Required no-change behavior and guarded search. |
| Zero and full counts, if allowed | An unrelated four-element sequence | Empty selections, empty remainders, and the `-0` trap. |
| Same totals, different values | `[2, 7]` and `[4, 5]` | A sum comparison cannot prove element equality. |
| Same values, different order | `[2, 7]` and `[7, 2]` | Ordered equality versus equality ignoring order. |
| Same distinct values, different counts | `[2, 2, 7]` and `[2, 7, 7]` | A set comparison cannot prove multiset equality. |
| Names containing spaces or differing in case | `North Studio`, `north Studio` | Preserving whole-line strings and exact matching. |
| Repeated calls | Omitted list, then another omitted list, then a supplied list | Independent defaults and intentional state reuse. |
| Maximum allowed size | Construct from your handout's actual limit | Length assumptions and output completeness. |

Do not use an empty numeric dataset for a task whose declared size starts at one. Do test an empty **intermediate** result when valid operations can create one.

### IPRFAL261

- For list statistics, include one element, repeated values, and negative values. Check line order and the distinction between reversed elements and list notation.
- For middle-value work, cover odd and even lengths, duplicate middle values, a fractional result, and a negative result. Verify the function's return type separately from its printed precision.
- For successive edits, test the smallest valid starting size, duplicate minimum/maximum values, an absent removal target, and a target introduced by an earlier step. Work with the current state after each operation.
- For half comparisons, cover even and odd lengths, an ignored middle value, the strongest relation, a weaker relation with different elements, and a case satisfying neither. Check priority.
- For persistent lists, compare independent default calls with calls using a supplied list. Include repeated names, an absent removal target, and a membership check that must not change state.

### IPRFAL262

- For score analysis, include a single score, all-equal scores, zeros, and the allowed score extremes. Check the exact number of decimal places.
- For endpoint work, include empty or one-element input where the problem permits it, exactly two elements, repeated endpoint values, and a list whose endpoints happen to be equal. Confirm any in-place requirement through an alias.
- For sequence selection, test the first and last valid split positions, zero selected elements where permitted, and selecting the whole sequence. Check the order of both output lines.
- For end comparisons, test the smallest and largest allowed window, overlapping views if permitted, duplicate values, equal sums with different contents, and a relation that must take priority.
- For a result that must be a new list, verify the original before and after the call. Check both a supplied existing list and a fresh default call, and keep any fixed heading outside the data's ordering rule.

### IPRFAL263

- Recheck the shared list operations with duplicates, one value, negative values, and nontrivial original order.
- When combining two datasets, use different sizes, different odd/even combinations, duplicate values shared between them, and extremes in different source lists. Use the combined size for position reasoning.
- For controlled edits, include the first and last permitted removal positions, out-of-range positions handled by the statement's rule, absent targets, and several copies of a target. An insertion may change which occurrence is first.
- For half comparisons, vary an excluded middle value without changing the compared halves. Check the exact labels used by this section rather than borrowing another section's wording.
- For default-based state, check that two omitted-list calls do not share data and that a supplied empty list still receives required updates. A membership action should not add the queried item.

### IPRFAL264

- For a compact numeric report, include all-equal data, repeated scores, a one-element list, and a selection count larger than the data where explicitly permitted. Distinct count, total count, and selected count are different quantities.
- For rearrangement, inspect both the final contents and object identity. Preserve whichever original endpoint the statement asks about, including when several elements equal it.
- For selection from the opposite end, test zero and full counts if permitted, and check that the selected part comes from the specified current sequence. The beginning and the end are different selection rules.
- For comparisons of ends, test repeated values, windows that overlap if permitted, equal totals without equal multiplicities, and the direction of the fallback numeric comparison.
- For whole-line titles, preserve internal spaces, repeated titles, and letter case. Verify that any returned snapshot is a new list and leaves the source unchanged, while update actions affect the intended existing list.

These are testing prompts, not complete lab implementations or a replacement for your current question sheet. If a bound or rule is unclear, confirm it with the teaching staff rather than choosing behavior from a different section.

## 22. Local testing and common questions

### A complete independent example

This toy program adds a numeric border to a sequence. It does not implement any of the assigned Lab 06 programs.

**Function:** `make_border(readings: list, border: int) -> list` returns a **new** list with one border value at each end. It leaves `readings` unchanged.

**Input:** exactly **two lines**:

- Line 1 contains space-separated integers; an empty line is permitted.
- Line 2 contains one integer border value. All supplied tokens are valid integers.

**Output:** exactly **two lines**:

- Line 1 contains the new list's elements, separated by spaces.
- Line 2 contains the original list's elements, separated by spaces; this line is blank when the original list is empty.

```python
def make_border(readings: list, border: int) -> list:
    """Create a sequence with a border value at both ends.

    Args:
        readings: A possibly empty list of integers.
        border: The integer to place at each end.

    Returns:
        A new list with the border values. readings is unchanged.
    """
    bordered = [border] + readings + [border]
    return bordered


readings = list(map(int, input().split()))
border = int(input())
result = make_border(readings, border)
print(*result)
print(*readings)
```

Example 1 input:

```text
3 7
-2
```

Example 1 output:

```text
-2 3 7 -2
3 7
```

Example 2 input has an **empty first line**, followed by `0` on line 2:

```text

0
```

Example 2 output has `0 0` on line 1 and a **blank line 2**:

```text
0 0

```

For debugging, its complete output representation is `'0 0\n\n'`. Do not replace that second line with `[]`, a space, or the word `None`.

### Test the return value and the side effect separately

After defining `make_border` above, the following is a separate local test block. It is not extra standard output to include in the submission.

```python
source = [5, 8]
before = source[:]
result = make_border(source, -1)

assert result == [-1, 5, 8, -1]
assert source == before
assert result is not source

empty = []
empty_result = make_border(empty, 0)
assert empty_result == [0, 0]
assert empty == []
assert empty_result is not empty
```

The first assertion checks returned contents. The next two check the source's preserved contents and the new result's separate identity. One check does not replace the others.

For an assigned in-place function, write analogous tests that expect the supplied list to change and any existing alias to observe that change. For a Boolean action, test `False` as well as `True`. For safe defaults, make several calls in the same interpreter session; restarting the program between calls can hide shared-default bugs.

### A practical debugging routine

1. Write the expected output or list state yourself before running the test.
2. Run one normal case, then one small boundary case.
3. In PyCharm, place a breakpoint before the first mutation. Watch the current list, its length, saved original values, and any aliases.
4. Step over one method call at a time. Check both its return value and the resulting list where relevant.
5. Run a case with duplicates and another with an absent target.
6. Run related function calls in one session to inspect persistent state.
7. Run the complete program with exactly the required input lines and compare all output lines, including blank ones.
8. Remove diagnostic output and rerun the examples with the final submitted file.

### Common questions

**Why did my list become None?** A modifying method such as `.sort()`, `.reverse()`, or `.append()` returns `None`. Keep the original list variable and call the method on a separate line.

**Why did a list outside the function change?** The parameter referred to the supplied list object, and your method or assignment mutated that object. A local parameter name does not imply a copy.

**Why did the caller's list not change?** You may have rebound the local parameter or changed a copy when the contract required mutation of the existing object.

**Why does the second call contain data from the first?** A mutable default or another intentionally shared list may be reused. Check the default declaration and the actual argument passed in each call.

**Can I use if not items instead of if items is None?** An empty supplied list is false too. Use `is None` when distinguishing an omitted list from an explicitly supplied empty list.

**Does a slice of an empty list fail?** An ordinary slice returns another empty list. A direct element lookup, `pop()`, `min()`, or `max()` has different preconditions.

**Why does selecting the last zero elements select everything?** `-0` is `0`, so a suffix slice starting at `-0` starts at the beginning. See [Section 6](#6-slicing-and-boundary-cases).

**Can I replace a required list method with my own loop?** Follow the method and loop restrictions in the handout. Matching a sample's output is only part of the contract.

**Do I have to make a copy before every operation?** Copy only when the contract requires independent state or preservation of the original. An in-place task requires mutation instead.

**Is sorted data with duplicates automatically invalid?** No. Sorting preserves duplicates. Remove or ignore duplicates only where the statement explicitly says to do so.

**Is a count of zero always an error?** No. A zero count, an empty result, and invalid input are different situations. Apply the particular problem's allowed range.

**Should I print the result of every function call?** Print returned values when required. Call print-based functions directly. A returned list, a returned Boolean, and a printed sequence need different handling.

**Why do identical values make a weak test?** Equal endpoints can hide a faulty rearrangement, all-equal data can hide ordering errors, and a target equal to its index can hide confusion between `remove` and `pop`. Include a case that separates those possibilities.

**Should I check exact object IDs or reference counts?** Use value comparisons and, when needed, identity comparisons with `is`. Numeric IDs and reference counts are not required output and need not be stable across runs.

### Troubleshooting table

| Symptom | Likely issue to inspect |
| --- | --- |
| `IndexError` | Direct lookup or `pop` on an empty list or an invalid current index. |
| `ValueError` from `remove` or `index` | The target is absent in the current state. |
| `ValueError` during input conversion | Numeric input contains an unexpected token, comma, or bracket. |
| `TypeError` during sorting | Numbers and strings were mixed, or input was not converted as intended. |
| A nested list appears unexpectedly | `.append(other_list)` was used when its elements were intended. |
| A multiword name becomes several elements | A whole-line name was split into tokens. |
| A first-position match is ignored | Index `0` was treated as a false “not found” result. |
| A full sequence appears for a zero selection | A suffix slice used `-0` as if it meant “none.” |
| Values are correct but the judge rejects output | Brackets, commas, decimal precision, labels, `None`, or missing blank lines differ. |
| A returned snapshot later changes | It may still be an alias to the original mutable list. |
| Local manual calls work but the submitted program fails | The main program's input reading, function calls, or printing differ from the tested function contract. |

## 23. Optional extra practice

These exercises are for additional study. They are separate from the five assigned problems and have no provided solutions here. Use simple annotations and docstrings, and keep each practice program separate.

### A. Repeated rhythm

Define `repeat_rhythm(tokens: list, times: int) -> list`. Return a new list that repeats the supplied token sequence `times` times, leaving the supplied list unchanged. Use list repetition rather than a loop.

**Input:** exactly **two lines**:

- Line 1 contains whitespace-separated string tokens and may be empty.
- Line 2 contains an integer `times` from `0` through `4`, inclusive.

**Output:** exactly **one line** containing the repeated tokens separated by spaces. Print a blank line if the result is empty.

Example 1 input:

```text
tap rest
3
```

Example 1 output:

```text
tap rest tap rest tap rest
```

Example 2 input:

```text
tap rest
0
```

Example 2 output is **one blank line**, whose debugging representation is `'\n'`.

Extra tests: one token, an empty first line, `times = 1`, repeated tokens in the input, and the maximum permitted count. Verify that the returned list is a different object even when its contents equal the input.

### B. A corrected copy

Define `correct_reading(readings: list, position: int, replacement: int) -> list`. Return a copy in which exactly the selected position has the replacement value. The original list must remain unchanged.

**Input:** exactly **three lines**:

- Line 1 contains between `1` and `8` space-separated integers, each from `-50` to `50`, inclusive.
- Line 2 contains a valid nonnegative 0-based index for that list.
- Line 3 contains an integer replacement from `-50` to `50`, inclusive.

**Output:** exactly **two lines**:

- Line 1 contains the corrected list's elements, separated by spaces.
- Line 2 contains the original list's elements, separated by spaces.

Example 1 input:

```text
4 9 4
1
-2
```

Example 1 output:

```text
4 -2 4
4 9 4
```

Example 2 input:

```text
7
0
7
```

Example 2 output:

```text
7
7
```

Extra tests: replacing the first or last element, replacing one position among duplicates, and replacing a value with itself. In the second example the two printed lines match, but the returned list must still be a separate object.

### C. Every third label

Define `every_third_label(labels: list) -> list`. Return a new list selecting positions `0`, `3`, `6`, and so on, using slicing. Keep the original order of the selected labels and leave the input list unchanged.

**Input:** exactly **one line** containing between `0` and `10` whitespace-separated string labels. An empty line represents no labels.

**Output:** exactly **two lines**:

- Line 1 contains the selected labels separated by spaces, or is blank for an empty selection.
- Line 2 contains the number of selected labels.

Example 1 input:

```text
a b c d e f g
```

Example 1 output:

```text
a d g
3
```

Example 2 input:

```text
only
```

Example 2 output:

```text
only
1
```

Extra tests: an empty input line, lengths `3` and `4`, repeated labels, and ten labels. For empty input, output must be a blank first line followed by `0` on line 2.

## 24. Submission checklist

- Use the latest official Lab 06 sheet for **your assigned section**. There are five problems; section variants are not interchangeable.
- Keep each assigned problem in its own complete Python 3 program. Do not combine them into a menu.
- Name each file using `Lab6_PY_Z.py`, replacing `Y` with the problem number and `Z` with your actual student ID. For example: `Lab6_P1_<YourStudentID>.py`.
- Match the required function name, parameter order, defaults, and behavior. Use simple type annotations that truthfully describe the function; do not add unsupported advanced type syntax.
- Include a purpose/parameter/return docstring for every function. Explain list mutation and action-specific return behavior where relevant.
- Read exactly the specified input lines. Distinguish list tokens from a whole-line name and preserve case or spaces where required.
- Follow required built-ins, list methods, in-place behavior, and any prohibition on loops.
- Test duplicates, absent targets, allowed short inputs, and empty intermediate results. Check numeric boundaries using the actual limits in your section's sheet.
- Check ordered equality, multiplicities, and sums as distinct requirements. Respect classification priority and exact result wording.
- Check safe defaults across multiple calls in one session. Verify both intentional shared state and independent fresh lists.
- Test whether original lists change or remain unchanged as required; checking only the returned value is insufficient.
- Print each required line with the exact representation and decimal precision. Keep required blank lines; remove prompts, debugging output, and accidental `None` lines.
- Submit the **same final code** to the correct **Codeforces contest and Canvas before the end of the lab session**. The current instructions require both platforms; do not apply an earlier lab's exception to Lab 06.
- Follow the current course instructions for your Codeforces display name and other submission details.

See the [platform submission guide](../lab-tutorial.md), [Lab 03 functions and annotation guide](../lab-03/README.md), [Lab 04 string and slicing guide](../lab-04/README.md), and [Lab 05 conditions and testing guide](../lab-05/README.md) when reviewing earlier techniques. Use the current Lab 06 contract wherever the required behavior differs.

## Further reading

These official Python references include features beyond the lab. Focus on the linked concepts and the basic operations used in this guide.

- [Lists, indexing, slicing, and mutation](https://docs.python.org/3/tutorial/introduction.html#lists)
- [List methods and data structures](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
- [Common sequence operations and mutable sequence operations](https://docs.python.org/3/library/stdtypes.html#common-sequence-operations)
- [Default argument values and the mutable-default warning](https://docs.python.org/3/tutorial/controlflow.html#default-argument-values)
- [Names, local namespaces, and aliasing](https://docs.python.org/3/tutorial/classes.html#a-word-about-names-and-objects)
- [Python's memory-management model](https://docs.python.org/3/reference/datamodel.html#objects-values-and-types)
