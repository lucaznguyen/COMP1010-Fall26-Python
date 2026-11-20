# Lab 08: While Loops, Tuples and Dictionaries — Detailed Study Guide

**Course:** COMP1010 - Introduction to Programming

**Week:** 08

**Preparation:** Lecture 14 (While-Loops and Loop Invariants) and Lecture 15 (Tuples and Dictionaries), together with functions, conditions, strings, and lists from earlier labs.

This guide supports Fall 2026 Lab 08 for **IPRFAL261–IPRFAL264**. It explains the shared Python techniques through independent examples, worked traces, extreme cases, debugging questions, and optional practice. Use the latest official question sheet for **your own section** for the assigned problems, required functions, limits, and exact output. The examples here are separate from the four assigned programs.

**Public tests:** from Lab 08, all test inputs and expected outputs are available in the problem's **`all-tests.zip` materials** on Codeforces. The two examples carry 0 points; the other five tests carry 20 points each, for 100 points per problem. Section 24 explains how to use these files for learning and debugging.

## What you should be able to do

- Trace a `while` loop, including the case where its body runs zero times.
- Distinguish a counter, an accumulated result, and the state being processed.
- Explain an invariant and a separate reason that a loop eventually stops.
- Use `break`, `continue`, and `while...else` with the intended scope.
- Reset the inner state when processing another case in nested loops.
- Create, index, slice, concatenate, unpack, and compare tuples.
- Explain how an immutable tuple can contain a mutable object.
- Read and update dictionary entries and understand what membership checks.
- Use a pair of strings as one dictionary key.
- Distinguish an alias, a shallow copy, and a change to the caller's dictionary.
- Process commands until a sentinel without printing an extra result for it.
- Preserve state after a rejected request and produce reports in an explicit order.
- Test valid zero results, empty collections, exact boundaries, and command sequences.

The examples use basic Python 3 features. Plain annotations such as `int`, `str`, `tuple`, `list`, and `dict` are enough. Classes, recursion, regular expressions, advanced type annotations, and comprehensions are unnecessary here. A required loop remains part of a problem's contract even if a shortcut produces the same output.

## Navigation

- [1. Read the program and function contracts](#1-read-the-program-and-function-contracts)
- [2. Parameter types, return types, and docstrings](#2-parameter-types-return-types-and-docstrings)
- [3. Read the required input lines](#3-read-the-required-input-lines)
- [4. How a while loop runs](#4-how-a-while-loop-runs)
- [5. Counters, accumulators, and working state](#5-counters-accumulators-and-working-state)
- [6. Boundaries and termination](#6-boundaries-and-termination)
- [7. Loop invariants](#7-loop-invariants)
- [8. Break, continue, and return](#8-break-continue-and-return)
- [9. While with else](#9-while-with-else)
- [10. Nested while loops](#10-nested-while-loops)
- [11. Integer arithmetic and decimal digits](#11-integer-arithmetic-and-decimal-digits)
- [12. Creating and using tuples](#12-creating-and-using-tuples)
- [13. Immutability and mutable tuple members](#13-immutability-and-mutable-tuple-members)
- [14. Dictionary keys, values, and membership](#14-dictionary-keys-values-and-membership)
- [15. Dictionary methods and unpacking](#15-dictionary-methods-and-unpacking)
- [16. Tuple keys and independent records](#16-tuple-keys-and-independent-records)
- [17. Copies, aliases, and function side effects](#17-copies-aliases-and-function-side-effects)
- [18. Comparisons, ordering, and ties](#18-comparisons-ordering-and-ties)
- [19. Reading commands until a sentinel](#19-reading-commands-until-a-sentinel)
- [20. Successful and rejected state changes](#20-successful-and-rejected-state-changes)
- [21. Exact output and empty output](#21-exact-output-and-empty-output)
- [22. A complete independent example](#22-a-complete-independent-example)
- [23. Extreme cases for all four sections](#23-extreme-cases-for-all-four-sections)
- [24. Local testing and public test files](#24-local-testing-and-public-test-files)
- [25. Common questions and debugging](#25-common-questions-and-debugging)
- [26. Optional extra practice](#26-optional-extra-practice)
- [27. Submission checklist](#27-submission-checklist)

## 1. Read the program and function contracts

A submitted program reads text and prints text. A function inside that program usually receives already-converted Python objects and returns a Python object. Keep these two interfaces separate.

| Part | Questions to answer before coding |
| --- | --- |
| Program input | How many lines? Does each line contain one number, several numbers, or a command? |
| Function parameters | Which values are passed, in which order, and with which types? |
| Function result | Is the result an integer, a name, a dictionary, a list, or a message? |
| Mutation | Must the original dictionary remain unchanged, or must this call update it? |
| Required constructs | Is `while`, nested `while`, `while...else`, or `break` explicitly required? |
| Stopping | Is there a count, a search boundary, a target, or a terminating command? |
| Program output | How many lines? Which values need conversion into an output label? |

The four sections share a progression:

| Problem | Shared focus | Details to check in your section |
| --- | --- | --- |
| 1 | A simple calculation using `while` | Initial state, numeric boundaries, and the meaning of zero. |
| 2 | A dictionary whose values are tuples | Preserve the original records, apply a replacement, and follow the comparison rules. |
| 3 | Several cases using nested `while` and `while...else` | Reset each case, return one result per case, and distinguish valid results from failure markers. |
| 4 | A dictionary with tuple keys and a command loop | Read through the final sentinel, preserve state after rejection, and format each response. |

### Follow the required construct where it is required

- Adding an unrelated `while` loop around a calculation does not make the calculation itself use the required loop.
- A function returning a list should return the complete list. The main program then prints the required line for each element.
- A function that must return a name should return that string, even if the final printed report also contains a total.
- A function that must change a dictionary should change the supplied object. Rebinding a local parameter to a different dictionary has a different effect.
- A Python result such as `0` or `-1` may need a specific word in console output. Follow the Output section when printing.

Lab 08 inputs satisfy the stated input format and constraints. You do not need to invent malformed-input messages or retry prompts. A valid command can still be rejected because its requested operation cannot currently succeed. Section 20 explains that distinction.

## 2. Parameter types, return types, and docstrings

An annotation after a parameter describes its expected type. The annotation after `->` describes the returned value.

```python
def has_word(words: tuple, target: str) -> bool:
    """Return whether target occurs in words.

    Args:
        words: A tuple of strings. It is not modified.
        target: The exact, case-sensitive word to look for.

    Returns:
        True if target occurs, otherwise False.
    """
    return target in words


print(has_word(("oak", "pine"), "pine"))  # True
print(has_word(("oak", "pine"), "Pine"))  # False
```

Here, `words: tuple` describes the container, `target: str` describes the searched value, and `-> bool` describes the result. The docstring explains the tuple's element type and the matching rule.

Annotations do not convert input or check it automatically:

```python
def repeat_label(label: str, times: int) -> str:
    """Return label repeated times times; label is a string and times is non-negative."""
    return label * times


text_count = "3"
count = int(text_count)
print(repeat_label("ha", count))  # hahaha
```

Calling a function with a string where it expects an integer does not become valid merely because an annotation says `int`. The main program must perform the necessary conversion.

### Simple annotations for this lab

| Meaning | Parameter or return annotation |
| --- | --- |
| An integer result, including a special integer marker | `int` |
| A name or a formatted response line | `str` |
| A tuple of numbers or a tuple of pairs | `tuple`, with its contents explained in the docstring |
| A list of results | `list`, with its element type explained |
| A dictionary containing records | `dict`, with key/value types and side effects explained |
| A Boolean answer | `bool` |
| A function that has no useful returned value | `None` |

Use plain container annotations. Syntax such as `tuple[int, int]`, `dict[tuple[str, str], int]`, or union annotations is unnecessary for this guide.

### Return values and printed output

- `return value` gives a value to the caller and ends the function call.
- `print(value)` writes text to the console.
- A function that reaches its end without a `return` returns `None`.
- A list or dictionary is **one returned object**, even if it contains many items.

For a function that returns a message, the caller can print that message. For a function that prints directly, adding `print(function_call(...))` can create an unwanted `None` line. Follow the assigned contract rather than changing the interface to match a convenient example.

Every function, including helpers, needs a docstring under the current submission instructions. Explain purpose, parameters, return value, and any effect on mutable arguments. The loop reasoning in this guide helps you understand and debug the code; follow the current handout for submission requirements.

## 3. Read the required input lines

One `input()` call reads **one line** and removes its terminating newline. It returns a string.

### One integer on a line

This fragment reads exactly one line:

```python
value = int(input())
```

### Two integers on the same line

This fragment also reads exactly one line:

```python
first, second = map(int, input().split())
```

For the input line `8 13`, the resulting values are integers `8` and `13`. `.split()` creates two string tokens; `map(int, ...)` converts the tokens; unpacking assigns them to the two names.

Two calls to `input()` would instead expect **two lines**. The following is the different, two-line format:

```python
first = int(input())
second = int(input())
```

Do not choose between these forms based on the number of variables. Choose from the required input line structure.

### Mixed text and numbers

This unrelated record format uses one code and one integer on a single line:

```python
code, quantity_text = input().split()
quantity = int(quantity_text)
```

For `Raven 12`, keep `code` as a string and convert only the quantity. Applying `int` to every token would also try to convert `Raven`.

### A tuple of numbers on one line

```python
values = tuple(map(int, input().split()))
```

For `5 11 17`, this produces `(5, 11, 17)`. By contrast, `tuple(input().split())` produces `("5", "11", "17")`, whose elements remain strings.

### A count followed by several pair lines

This input fragment reads one count line followed by exactly that many lines, each containing two integers:

```python
case_count = int(input())
collected = []
index = 0
while index < case_count:
    left, right = map(int, input().split())
    collected.append((left, right))
    index += 1
pairs = tuple(collected)
```

For a count of `3`, this consumes four lines in total. Each pair is a tuple, and `pairs` is the outer tuple containing all pairs. `tuple(collected)` does not turn the entire structure into a flat sequence of six integers.

### Counts and sentinels are different formats

If the handout gives a count, read that many entries. If it says commands continue until a sentinel, read until that sentinel. A bound such as “at most 100 commands” is a limit on valid input, not an extra input value and not a promise that every test contains 100 commands.

An empty line, an absent line, and the terminating command are different things. In the assigned command problems, the final `END` is guaranteed. Read and handle it explicitly. Do not wait for an extra line after it.

## 4. How a while loop runs

Python checks the condition **before** each iteration:

1. Evaluate the condition.
2. If it is false, leave the loop.
3. If it is true, run the body.
4. Return to the condition check.

```python
remaining = 9
while remaining > 3:
    print(remaining)
    remaining -= 3
print("After:", remaining)
```

Output:

```text
9
6
After: 3
```

| Condition check | Current `remaining` | Condition `remaining > 3` | Next action |
| --- | ---: | --- | --- |
| First | 9 | True | Print 9, then change it to 6. |
| Second | 6 | True | Print 6, then change it to 3. |
| Third | 3 | False | Leave the loop without printing 3 inside it. |

### A body may execute zero times

```python
remaining = 3
visits = 0
while remaining > 3:
    visits += 1
    remaining -= 3
print(visits)  # 0
```

The initial state must therefore make sense even when the body never runs. Initialize a result before the loop if you need to return it afterward.

### Choosing between for and while

| Situation | Typical choice |
| --- | --- |
| Visit each element of an existing sequence | `for` is often convenient. |
| Repeat until a changing state reaches a condition | `while` expresses the condition directly. |
| Read commands until a terminating word | `while` with an explicit stopping check. |
| A problem explicitly requires a particular loop | Use the required construct for that task. |

`while` can also process a fixed number of elements. It simply requires you to maintain the position yourself.

## 5. Counters, accumulators, and working state

Give each variable one clear meaning:

| Role | Example meaning |
| --- | --- |
| Position | Index of the next character to inspect. |
| Counter | Number of qualifying characters already seen. |
| Accumulator | A total built from the processed part of the input. |
| Working state | A temporary value that changes during processing. |
| Original input | The value you may still need after the calculation. |

This example totals the lengths of several words. It is separate from the assigned numeric tasks.

```python
words = ("fern", "oak", "elm")
index = 0
letter_total = 0
while index < len(words):
    letter_total += len(words[index])
    index += 1
print(letter_total)  # 10
print(index)         # 3
```

There are three words and ten letters. The position and the accumulated result describe different things.

### Count events at the point they occur

Ask whether you are counting input records, successful operations, iterations, or transitions between states. Counting the initial state as an operation can add an unwanted extra one. A rejected request can still require an output line even though it does not change the data.

### Preserve a value when you still need it

```python
original = 18
working = original
working -= 5
print(original)  # 18
print(working)   # 13
```

Integers are immutable. Rebinding `working` does not change `original`. With dictionaries, two names can refer to the same mutable object; Section 17 shows why that case behaves differently.

## 6. Boundaries and termination

A loop needs both a correct boundary and a reason it makes progress toward leaving that boundary.

For sequence traversal, `index < len(values)` allows the last valid index, `len(values) - 1`. The condition `index <= len(values)` also admits an invalid index equal to the length.

For a bounded calculation, decide what equality means. Depending on the problem, equality may mean “process this final value,” “the target has been reached,” or “there is nothing left to do.”

### A decreasing amount of unfinished work

In an index loop, `len(values) - index` describes how many elements remain. It starts non-negative, decreases when you advance the index, and cannot decrease forever while staying positive.

Other processes may reduce a remaining amount or read the next input command. Explain the actual change made by that loop. A comment saying “this loop stops” does not identify the reason.

### Equality alone may never become true

This deliberately broken example is shown as text. Do not run it:

```text
value = 14
while value != 0:
    value -= 4
```

Its states begin `14, 10, 6, 2, -2, ...`. The update skips zero, so the condition never becomes false. When designing your own loop, account for the process passing a boundary as well as reaching it exactly.

### Check every repeating path

- An ordinary iteration must advance the position or change the relevant state.
- A `continue` must not skip the only update that makes progress.
- A rejected command must still lead to reading the next command.
- An inner search needs its own progress argument.
- A mathematically correct condition can still be too slow if it checks far more candidates than the stated limits permit. Try the maximum valid input locally.

If a local program hangs, stop it with the IDE's stop button or `Ctrl+C` in a terminal. Inspect the changing variables before running it again.

## 7. Loop invariants

A loop invariant describes something true at a consistent point in every iteration, usually immediately before the condition check. It explains what the program has accomplished so far.

For the word-length example in Section 5, a useful invariant is:

> Before each condition check, `letter_total` is the sum of the lengths of `words[:index]`, and `0 <= index <= len(words)`.

This describes the processed prefix. It also covers an empty tuple, because the prefix before position zero is empty and its total length is zero.

| Part of the reasoning | Explanation for this example |
| --- | --- |
| Initialization | `index` starts at 0 and `letter_total` starts at 0, correctly describing an empty prefix. |
| Maintenance | Add the current word's length, then advance the position. The result now describes the next, longer prefix. |
| Result when the loop stops | The position has reached the tuple's length, so the processed prefix is the whole tuple. |
| Why it eventually stops | Each iteration increases the position by 1, decreasing the number of remaining words. |

The final two rows answer different questions. The invariant helps establish **correctness**; the progress argument establishes **termination**. A condition that stays true forever can preserve an invariant forever without returning an answer.

### State invariants

A dictionary can also have an invariant. An independent room booking system might maintain “each room appears once and its status is either `free` or `booked`.” Every accepted operation must preserve that property. A rejected operation should preserve the entire previous state.

Use precise descriptions: which prefix, which values, and at what point in the loop? Writing “the data is correct” leaves all of those questions unanswered.

## 8. Break, continue, and return

| Statement | Effect |
| --- | --- |
| `break` | Exit the nearest enclosing loop. Execution continues after that loop. |
| `continue` | Skip the rest of the current iteration and check the nearest loop again. |
| `return` | End the entire current function call. |

### Continue and position updates

```python
values = (4, -2, 7)
index = 0
while index < len(values):
    current = values[index]
    index += 1
    if current < 0:
        continue
    print(current)
```

Output:

```text
4
7
```

The index update occurs before `continue` can execute. If that update were placed only after the `if`, encountering `-2` could make the program inspect the same element forever.

Moving updates earlier is one possible design. Another is to structure the branches so that all repeating paths reach a common update. Choose a design whose variable meanings remain clear.

### Return placement

If a function processes several cases, a `return` inside the outer loop can end the call after the first case. When the contract asks for a result for every case, check both the list's length and the placement of the final return.

## 9. While with else

The `else` suite belongs to the loop when it aligns with `while`. It runs when the loop finishes because its condition is false. A `break` from that loop skips its `else` suite.

This independent example searches a tuple of words:

```python
words = ("fog", "rain", "sun")
target = "rain"
index = 0
while index < len(words):
    if words[index] == target:
        print("Found")
        break
    index += 1
else:
    print("Not present")
```

Output:

```text
Found
```

Try the following variations yourself:

| Variation | Result | Why |
| --- | --- | --- |
| `target = "sun"` | `Found` | The final element is inspected before the search breaks. |
| `target = "snow"` | `Not present` | The position reaches the length without a break. |
| `words = ()` | `Not present` | The condition is false immediately; the loop's `else` still runs. |

### Read else as normal completion

`else` does not inherently mean “failure” or “success.” Those meanings depend on the algorithm. In this search, a match causes `break`, so normal completion means the search exhausted its candidates without finding one.

- Zero iterations still count as normal completion when the condition starts false.
- `continue` does not suppress `else`; it continues the loop.
- A `return` ends the function, so execution does not later reach the loop's `else` in that call.
- An exception also interrupts the normal flow; it is not the condition becoming false.
- An inner loop's `break` does not break an outer loop.

Indentation decides which construct an `else` belongs to. An `else` aligned with `if` handles that condition; an `else` aligned with `while` handles that loop's normal completion.

## 10. Nested while loops

Each outer iteration usually starts a new unit of work. Give the inner loop fresh state for that unit.

```python
row = 0
while row < 2:
    column = 0
    labels = []
    while column < 3:
        labels.append(f"{row},{column}")
        column += 1
    print(" ".join(labels))
    row += 1
```

Output:

```text
0,0 0,1 0,2
1,0 1,1 1,2
```

The inner counter and the new row's labels are initialized **inside** the outer body. If `column = 0` appeared only before the outer loop, the second row could begin with `column` already equal to 3 and skip the inner body entirely.

### Keep the scopes separate

For each loop, identify:

- The state it owns.
- Its condition and update.
- Which `break` or `else` belongs to it.
- What must be reset before the next outer iteration.

An inner `break` continues with statements after that inner loop, still inside the outer body. A `return` from an inner loop exits the function completely.

For several input cases, results should normally follow input order, including repeated cases. A repeated number or pair is still a separate case and still needs its own result. Check that processing one case cannot leave an old result or flag behind for the next.

## 11. Integer arithmetic and decimal digits

For non-negative integers, `% 10` gives the last decimal digit and `// 10` gives the remaining integer prefix:

```python
value = 7306
print(value % 10)   # 6
print(value // 10)  # 730
print(730 % 10)     # 0
print(730 // 10)    # 73
```

| Expression | Meaning |
| --- | --- |
| `a / b` | Division producing a floating-point result for integer operands. |
| `a // b` | Floor division; with non-negative integers, the integer quotient. |
| `a % b` | The remainder after division. |
| `a ** b` | Exponentiation. |
| `a ^ b` | Bitwise XOR, which is a different operation. |

### Zero needs its own interpretation

The integer `0` has one decimal digit, but a loop guarded by `working > 0` executes zero iterations when `working` starts at zero. Decide what the **problem's result** should mean before choosing an initial result or loop condition.

Likewise, the number of operations performed by a process can be zero if its initial state already satisfies the goal. Zero operations is different from “the goal cannot be reached.”

### Integer values and displayed digits

- An integer does not preserve leading zeros. `int("0042")` is the integer `42`.
- A zero inside a decimal representation is still a digit. It is not an instruction to stop processing the rest of the number.
- A count of digits, a sum of digits, and a rearrangement of digits are different contracts, even if they use some of the same basic operations.
- Keep exact integer calculations as integers. Floating-point approximations are unnecessary when the required checks concern exact integer relationships.
- A built-in or a string shortcut does not replace an explicitly required `while` calculation.

For a larger numeric task, first identify the legal candidate range and the meaning of equality. Then test the minimum, an exact match, a non-match, and the largest permitted value. The guide intentionally leaves the assigned search algorithms for you to design.

## 12. Creating and using tuples

A tuple is an ordered sequence whose element references cannot be replaced in place. The **comma** creates a tuple; parentheses often make the grouping clearer.

```python
empty = ()
single = ("birch",)
pair = ("birch", 7)
grouped_word = ("birch")

print(len(empty))                # 0
print(len(single))               # 1
print(type(single).__name__)     # tuple
print(type(grouped_word).__name__)  # str
print(pair[0])                   # birch
print(pair[-1])                  # 7
```

`("birch")` is just the string inside parentheses. Remember the trailing comma in a one-element tuple.

### Indices, slices, membership, and basic operations

```python
values = (4, 9, 4, 12)
print(values[1])         # 9
print(values[:2])        # (4, 9)
print(values[2:2])       # ()
print(9 in values)       # True
print(values.count(4))   # 2
print(values.index(12))  # 3
print(sum(values))       # 29
print(min(values))       # 4
print(max(values))       # 12
```

An out-of-range index raises `IndexError`; a slice can safely produce an empty tuple. `.index()` raises `ValueError` if the searched value is absent. `min()` and `max()` need at least one element unless an appropriate default is provided. Follow the input guarantees rather than assuming every collection is non-empty.

### Concatenation and repetition create tuple results

```python
start = ("north", "east")
extended = start + ("south",)
repeated = (2, 5) * 2
print(start)     # ('north', 'east')
print(extended)  # ('north', 'east', 'south')
print(repeated)  # (2, 5, 2, 5)
```

`start + "south"` cannot concatenate a tuple and a string. `(value,)` creates the one-element tuple needed when adding a single element by concatenation.

### Unpacking

```python
position = (3, 8)
row, column = position
print(row)     # 3
print(column)  # 8

row, column = column, row
print(row, column)  # 8 3
```

Ordinary unpacking needs the same number of destination names as elements. Unpacking a three-element tuple into two names raises `ValueError`. A pair such as `(3, 8)` is still one object when passed as a single argument.

### A star can unpack arguments into a call

```python
def join_labels(left: str, right: str) -> str:
    """Return the two string labels joined by a slash, without changing either."""
    return left + "/" + right


labels = ("North", "Window")
print(join_labels(*labels))  # North/Window
```

`join_labels(*labels)` passes two string arguments. `join_labels(labels)` would pass only one tuple argument, leaving the second parameter without an argument.

In a parameter list, a star has the complementary role of collecting positional arguments:

```python
def count_labels(*labels: str) -> int:
    """Return the number of supplied string arguments; labels is a tuple inside the call."""
    return len(labels)


print(count_labels("Ash", "Pine", "Elm"))  # 3
print(count_labels())                      # 0
```

The annotation `*labels: str` describes each supplied argument. Inside the function, Python collects them in a tuple. These argument forms are lecture review; use the exact fixed parameter list required by an assigned function.

## 13. Immutability and mutable tuple members

You cannot assign to a tuple element in place. This deliberately invalid fragment is shown as text:

```text
position = (3, 8)
position[0] = 6
```

It raises `TypeError`. You can instead construct a different tuple and bind a name to it:

```python
position = (3, 8)
moved = (6,) + position[1:]
print(position)  # (3, 8)
print(moved)     # (6, 8)
```

The original tuple remains unchanged. Rebinding a name to a new tuple is allowed; replacing an element of the existing tuple is not.

### An immutable outer tuple can contain a mutable list

```python
record = ("draft", [2, 6])
record[1].append(9)
print(record)  # ('draft', [2, 6, 9])
```

The tuple still refers to the same string and the same list. The list's contents changed. Tuple immutability does not make every object reachable through the tuple immutable.

### Converting a container does not recursively copy its contents

```python
inner = [3]
outer_list = [inner]
outer_tuple = tuple(outer_list)
inner.append(8)
print(outer_list)   # [[3, 8]]
print(outer_tuple)  # ([3, 8],)
```

This matters when making snapshots. Tuples containing only integers or strings have different sharing consequences from tuples containing lists. Always inspect the actual element types.

## 14. Dictionary keys, values, and membership

A dictionary maps each key to a value. Access an entry by its key rather than by a positional index.

```python
durations = {"Raven": 12, "Lark": 5}
print(durations["Raven"])  # 12
print("Raven" in durations)  # True
print(12 in durations)       # False
print(12 in durations.values())  # True
```

`key in dictionary` checks **keys**. It does not search through the values. An expression such as `dictionary[0]` asks for the key `0`; it does not mean “the first stored item.”

### Adding and replacing entries

```python
durations = {"Raven": 12}
durations["Lark"] = 5
durations["Raven"] = 9
print(len(durations))        # 2
print(durations["Raven"])   # 9
```

Assigning to an existing key replaces its value. It does not add a second entry for that key. Dictionary keys are unique, while different keys may have equal values.

### Absence and zero are different

```python
readings = {"East": 0}
print("East" in readings)       # True
print("West" in readings)       # False
print(bool(readings["East"]))   # False
```

The false truth value of an existing numeric value does not make its key absent. Use membership to answer whether a record exists.

Reading `readings["West"]` would raise `KeyError`. Use a membership check or a suitable `.get()` default when absence is a normal situation.

### Dictionary order in Python 3

Python 3.7 and later dictionaries preserve insertion order. That order is still **different from sorted order**. Updating an existing key does not move it to the end; deleting it and inserting it again places it at the end of insertion order.

Whenever an output contract asks for sorted names or sorted pairs, perform that sorting explicitly. Do not rely on the input order, a lucky insertion order, or the order shown by a single example.

## 15. Dictionary methods and unpacking

| Operation | Meaning | Effect on the dictionary |
| --- | --- | --- |
| `key in data` | Check whether a key exists. | No change. |
| `data.get(key, default)` | Read a value, using `default` if absent. | No change; does not insert the default. |
| `data.keys()` | Obtain a view of the keys. | No change. |
| `data.values()` | Obtain a view of the values. | No change. |
| `data.items()` | Obtain a view of `(key, value)` pairs. | No change. |
| `data.copy()` | Make a shallow dictionary copy. | Original unchanged. |
| `data.update(other)` | Add or replace entries from another mapping. | Changes `data`; returns `None`. |
| `del data[key]` | Remove an existing entry. | Changes `data`; absent key raises `KeyError`. |
| `data.pop(key)` | Remove an entry and return its old value. | Changes `data`; absent key raises `KeyError` unless a default is supplied. |
| `data.clear()` | Remove every entry. | Changes `data`; returns `None`. |

### Get does not insert

```python
levels = {"Cedar": 4}
print(levels.get("Elm", 0))  # 0
print("Elm" in levels)       # False
```

Choose a default that has the meaning required by that operation. A read default of zero should not accidentally turn a missing record into an existing zero-valued record.

### Keys, values, and items are views

In current Python 3, `.items()` returns a view rather than a ready-made list. It is iterable, so you can unpack each pair in a loop:

```python
levels = {"Cedar": 4, "Elm": 7}
for name, level in levels.items():
    print(name, level)
```

Output:

```text
Cedar 4
Elm 7
```

Use `list(levels.items())` if you specifically need a separate list of those pairs. A view reflects later changes to its dictionary:

```python
levels = {"Cedar": 4}
names_view = levels.keys()
levels["Elm"] = 7
print(list(names_view))  # ['Cedar', 'Elm']
```

### Update and dictionary unpacking

These configuration examples use scalar values, separate from the assigned record corrections:

```python
defaults = {"theme": "light", "zoom": 90}
preferences = {"zoom": 110}
combined = {**defaults, **preferences}
print(defaults["zoom"])  # 90
print(combined["zoom"])  # 110
```

`**` expands the entries of a mapping inside a new dictionary display. If a key appears more than once, the later entry supplies its value. This produces another dictionary, without updating `defaults`.

Compare that with a mutating method:

```python
settings = {"theme": "light", "zoom": 90}
returned = settings.update({"zoom": 110})
print(settings["zoom"])  # 110
print(returned)          # None
```

Writing `settings = settings.update(...)` would rebind `settings` to `None`. It would not store the updated dictionary in that name.

### Deleting an entry

```python
levels = {"Cedar": 4, "Elm": 7}
removed = levels.pop("Cedar")
print(removed)           # 4
print("Cedar" in levels)  # False
del levels["Elm"]
print(levels)            # {}
```

When an entry is removed, later operations should treat that key as absent. Keeping a zero value under the key is a different state from deleting the key.

## 16. Tuple keys and independent records

A pair of strings can identify one dictionary entry:

```python
seats = {
    ("North", "Window"): 3,
    ("South", "Window"): 8,
    ("North", "Aisle"): 2,
}
key = ("North", "Window")
print(seats[key])                  # 3
print(("South", "Window") in seats)  # True
print(("Window", "North") in seats)  # False
```

The whole pair is **one key**. Its components are ordered. Sharing one component does not make two records the same.

### Why a tuple can be a key

A dictionary key must be **hashable**, meaning Python can use a stable hash value for lookup and compatible equality checks. Strings and integers meet this requirement. A tuple of strings is also hashable because every element is hashable.

An ordinary list cannot be a dictionary key. A tuple containing a list is also unsuitable as a key: an immutable outer container does not make a mutable list inside it hashable.

For the assigned key pairs, keep both components as their original strings. Do not replace the tuple with a mutable list.

### Avoid ambiguous concatenation

The pairs `("AB", "C")` and `("A", "BC")` are different. Joining each pair without a separator would produce `"ABC"` for both and merge unrelated records. A tuple preserves the components without needing an encoding convention.

### Unpack a key when you need its components

```python
key = ("North", "Window")
wing, position = key
print(wing)      # North
print(position)  # Window
```

This is ordinary tuple unpacking. It does not modify the key or the dictionary that uses it.

## 17. Copies, aliases, and function side effects

### Assignment creates another name for the same dictionary

```python
original = {"mode": "quiet"}
alias = original
alias["mode"] = "active"
print(original["mode"])    # active
print(alias is original)   # True
```

Both names refer to one dictionary. The original state has changed through its alias.

### Copy creates another outer dictionary

```python
original = {"mode": "quiet"}
copied = original.copy()
copied["mode"] = "active"
print(original["mode"])  # quiet
print(copied["mode"])    # active
print(copied is original)  # False
```

`is` checks whether the names refer to the same object. `==` checks equality of contents. Two separate dictionaries can compare equal.

### Shallow copies still share inner objects

```python
original = {"draft": [1, 5]}
copied = original.copy()
copied["draft"].append(8)
print(original["draft"])  # [1, 5, 8]
```

Only the outer dictionary was copied. Its value still refers to the same list.

For dictionary values that are tuples of integers, those tuples and integers are immutable. Sharing those unchanged value objects is safe. A required change to one tuple's contents must instead create a tuple result with the intended contents. Think separately about the outer dictionary and each value object.

### A function can mutate its argument and return a different kind of result

```python
def enable_feature(options: dict, name: str) -> None:
    """Enable a named option in the supplied dictionary.

    Args:
        options: A dictionary mapping string names to Boolean values; it is modified.
        name: The option to set to True, creating it if absent.

    Returns:
        None. The caller can inspect options for the updated state.
    """
    options[name] = True


settings = {"sound": False}
returned = enable_feature(settings, "sound")
print(settings["sound"])  # True
print(returned)           # None
```

Do not assume that a function returning a string or a Boolean cannot also mutate its dictionary argument. Read its documented side effects.

For your own tests, check the returned value **and** the required state after the call. For a function that must preserve the original, test the original separately from the returned dictionary. Comparing only the final printed report can miss an unintended mutation.

## 18. Comparisons, ordering, and ties

Tuple comparisons are lexicographic: compare the first unequal corresponding elements. If one tuple is an equal prefix of another, the shorter tuple comes first.

```python
print((2, 9) < (3, 0))       # True
print((2, 9) < (2, 11))      # True
print((2, 9) < (2, 9, 0))    # True
print((2, 9) == [2, 9])      # False
```

The comparison `(2, 9) < (3, 0)` does not compare sums. It is decided by the first elements, 2 and 3. If a problem defines an aggregate measure, tuple ordering alone does not calculate that measure for you.

### Names and case

For English-letter names, Python string order places uppercase letters before lowercase letters:

```python
print(sorted(["maple", "Zinc", "Maple", "Ash"]))
```

Output:

```text
['Ash', 'Maple', 'Zinc', 'maple']
```

Names are case-sensitive in the current lab. Changing every name to lowercase would change key identity and could also change tie-breaking behavior.

### Explicit order for a report

```python
durations = {"Raven": 12, "Lark": 5, "Finch": 9}
for name in sorted(durations):
    print(name, durations[name])
```

Output:

```text
Finch 9
Lark 5
Raven 12
```

`sorted(durations)` returns a sorted list of keys. It does not change the dictionary's stored insertion order.

For tuple keys, sorting compares the first component, then the second:

```python
keys = [("South", "Window"), ("North", "Window"), ("North", "Aisle")]
print(sorted(keys))
```

Output:

```text
[('North', 'Aisle'), ('North', 'Window'), ('South', 'Window')]
```

### Check each comparison direction

| Requirement | Question to ask |
| --- | --- |
| Highest combined value | Which record is preferable when the primary values differ? |
| Lowest combined value | Does the preference reverse from the previous case? |
| Smaller name on a tie | Is name order still ascending even when the primary value is descending? |
| Report sorted by name | Is this reporting order separate from choosing a winner? |

Using `reverse=True` on an entire multi-part ordering reverses every component. It can also reverse a name tie-breaker that was supposed to remain ascending. Test a tie deliberately rather than relying on examples with distinct primary values.

An independent queue example makes the comparison order visible:

```python
queue = [(3, "Ember"), (1, "Willow"), (1, "Ash")]
print(sorted(queue))
```

Output:

```text
[(1, 'Ash'), (1, 'Willow'), (3, 'Ember')]
```

This example sorts ascending waiting-time/name pairs. Work out your assigned comparison and return contract separately.

## 19. Reading commands until a sentinel

A sentinel is a special input value meaning “stop.” It is a control instruction, so it often has no corresponding output line.

This independent fragment reads arbitrary text lines until `FINISH`. Each earlier line produces its length:

```python
while True:
    text = input()
    if text == "FINISH":
        break
    print(len(text))
```

Example input:

```text
oak
silver birch
FINISH
```

Output:

```text
3
12
```

If the first line is `FINISH`, the output is empty: **zero lines**. The sentinel itself does not produce a length.

### Commands with tokens

For a token-based protocol, `.split()` creates the token list. Inspect the command word before accessing fields that only longer commands possess.

For example, an unrelated `MEASURE Raven 12` command has three tokens, while `FINISH` has one. Trying to read the third token before recognizing `FINISH` would cause `IndexError`.

The assigned Lab 08 protocol guarantees uppercase, correctly formed commands and a final `END`. Keep the following distinctions clear:

- The initial record count is provided; the command count is not supplied as a separate value.
- A bound on the number of commands does not mean you should read a fixed number of lines.
- Read and recognize `END` in the main loop, before passing an ordinary command to the required function.
- A failed operation still consumes its command. The next iteration must read another line.
- Do not print an acknowledgment, an empty line, or a final summary for `END` unless the output contract explicitly requests one.

## 20. Successful and rejected state changes

Valid input does not guarantee that every requested operation is possible.

| Situation | Interpretation |
| --- | --- |
| A correctly formed request refers to a missing key | The request is valid input; apply the specified missing-record response. |
| A correctly formed request exceeds the available quantity | Apply the specified rejection response and preserve the current state. |
| A request exactly uses the remaining quantity | Equality is a boundary case, with its own required success behavior. |
| An earlier request removed a record | A later lookup must use the current state, where the key is absent. |

### Check before changing state

Consider a separate room booking system, where booking an already-booked room is rejected:

| Step | Request | State afterward |
| --- | --- | --- |
| Initially | — | Cedar is free. |
| 1 | Book Cedar | Cedar is booked. |
| 2 | Book Cedar again | Request rejected; Cedar remains booked. |
| 3 | Release Cedar | Cedar is free. |
| 4 | Book Cedar | Cedar is booked again. |

A correct handler examines the conditions for success before applying changes. Returning an error message does not automatically undo an earlier assignment or deletion.

For the assigned quantity logs, write a small state table yourself. Include the state before each command, the required response, and the state afterward. In a rejected case, compare the entire dictionary before and after, not just the addressed quantity.

### Two independent forms of correctness

- **Response correctness:** the returned string says the right thing.
- **State correctness:** the dictionary has exactly the required entries and values after the operation.

Printing the expected rejection word while silently changing a quantity can make the current line look correct and break a later `SHOW` or update.

### Removing and recreating a record

A dictionary containing a key with value zero is different from a dictionary without that key. When the contract requires removing zero-count records, test a later lookup and a later insertion of that same pair.

An initial count limit and a per-command amount limit do not necessarily impose the same limit on accumulated values after many accepted additions. Read the actual constraints; do not invent a cap or clamp a valid total.

## 21. Exact output and empty output

The Output section defines what the user sees. A valid Python representation may still be the wrong report format.

```python
pair = (6, 11)
print(pair)            # (6, 11)
print(pair[0], pair[1])  # 6 11
```

Both prints describe the same values differently. The second form omits tuple punctuation.

### Formatting a separate report

```python
durations = {"Raven": 12, "Lark": 5}
parts = []
for name in sorted(durations):
    parts.append(f"{name} -> {durations[name]}")
line = "; ".join(parts)
print(line)
```

Output:

```text
Lark -> 5; Raven -> 12
```

The separator goes **between** entries. `.join()` avoids adding a separator after the final entry. Its input elements must be strings; convert or format numeric values first.

This report's arrow and semicolon are specific to this independent example. Use the slash, colon, comma, spacing, and capitalization required by your own Lab 08 problem.

### Count the actual output lines

| Required behavior | Console output |
| --- | --- |
| Print a numeric zero | One line containing `0`. |
| Print an empty-state message | One line containing that exact message. |
| Print a blank line | One newline character. |
| Print nothing | Zero characters and zero lines. |

`print("")` prints a newline, so it is not empty output. If no output is required, do not call `print` for that case.

For the assigned command problems, every non-`END` command produces one line, including a rejected command and a report of an empty dictionary. Initial records do not produce response lines. If there are `m` command lines including `END`, the output has `m - 1` lines.

Do not print input prompts, debugging dictionaries, Python brackets, progress traces, or a final `Done` message unless the problem requests them. Avoid printing the return value of a function that already prints its own output.

## 22. A complete independent example

This program counts English uppercase letters in a line. It is separate from every assigned Lab 08 problem. It demonstrates parameter/return annotations, a docstring, a `while` traversal, and a result printed by the main program.

**Input:** exactly **one line** containing 0 to 60 characters. Characters are English letters or ordinary spaces. The line is guaranteed to exist and satisfy these constraints. An empty line is valid.

**Output:** exactly **one line** containing the number of characters from `A` through `Z`, with no label.

```python
def count_uppercase(text: str) -> int:
    """Return the number of English uppercase letters in text.

    Args:
        text: A string of English letters and spaces, possibly empty.
            The string is unchanged.

    Returns:
        A non-negative integer count of characters from A through Z.
    """
    position = 0
    count = 0
    # Invariant: count describes uppercase letters in text[:position].
    while position < len(text):
        if "A" <= text[position] <= "Z":
            count += 1
        position += 1
    return count


def main() -> None:
    """Read one valid text line and print its uppercase-letter count.

    Args:
        None. The text line comes from standard input.

    Returns:
        None. Exactly one integer result line is printed.
    """
    line = input()
    result = count_uppercase(line)
    print(result)


if __name__ == "__main__":
    main()
```

Example 1 input:

```text
Blue SKY
```

Example 1 output:

```text
4
```

The qualifying characters are `B`, `S`, `K`, and `Y`.

Example 2 input:

```text
quiet words
```

Example 2 output:

```text
0
```

### Trace the first example

| Character just processed | Next `position` | `count` |
| --- | ---: | ---: |
| Before the loop | 0 | 0 |
| `B` | 1 | 1 |
| `l` | 2 | 1 |
| `u` | 3 | 1 |
| `e` | 4 | 1 |
| Space | 5 | 1 |
| `S` | 6 | 2 |
| `K` | 7 | 3 |
| `Y` | 8 | 4 |

At position 8 the condition is false. The result is returned once, after all characters have been considered.

### Extreme cases for this independent program

| Input string passed to the function | Expected result | What it checks |
| --- | ---: | --- |
| `""` | 0 | Zero iterations, with the result still initialized. |
| `"Z"` | 1 | A single qualifying character at the boundary of the uppercase range. |
| `"a"` | 0 | A single non-qualifying character. |
| `"ABC"` | 3 | Every character qualifies. |
| `" Ab C "` | 2 | Leading, trailing, and internal spaces. |
| `"   "` | 0 | Spaces alone. |
| A line of 60 uppercase letters | 60 | The maximum length. |

For the empty-line test, the input file contains one newline. An entirely empty file has no line to read and is outside this example's input contract.

## 23. Extreme cases for all four sections

Use these as questions for your own test design. They clarify boundaries without supplying the assigned implementations. Always apply the limits and labels from your own question sheet.

### Shared checks

| Category | Questions to answer |
| --- | --- |
| Minimum input | Is zero allowed here? If the count must be positive, what is the smallest permitted case? |
| No iterations | Was a meaningful result initialized before the loop? Does an attached `else` run? |
| Exact equality | Is it a success, a final valid candidate, or the point where repetition ends? |
| No qualifying result | Which Python marker is returned, and which console word represents it? |
| Repeated cases | Does each occurrence produce another result in the original order? |
| Maximum size | Does the loop finish in reasonable time without adding a limit absent from the specification? |
| Names and keys | Are case and both tuple components preserved? |
| State changes | Does rejection preserve all entries and values? |
| Output | Is it zero lines, one numeric zero, a message, or a multi-line report? |

### Problem 1: section-specific zero and boundary meanings

| Section | Cases to include | Interpretation to verify |
| --- | --- | --- |
| IPRFAL261 | Zero days, one day, the smallest contribution, and the largest allowed values. | A zero-day period saves zero; check that the day's meaning and the number of processed days stay consistent. |
| IPRFAL262 | The number zero, one digit, an exact power of ten, a number containing internal zeros, and the maximum value. | The representation of zero has one decimal digit. Zeros inside another number still occupy positions. |
| IPRFAL263 | Zero, one digit, internal zeros, and many repeated non-zero digits. | A zero digit contributes zero while remaining part of the number's representation. |
| IPRFAL264 | Zero, one digit, trailing zeros, internal zeros, and the maximum value. | The result is an integer; zeros that would lead its written form do not create leading-zero output. |

The numeric upper bound `10**12` is itself a valid input in the relevant sections. Count its written digits carefully when checking a boundary by hand.

### Problem 2: corrections, preservation, and ties

Test each of the following:

- Correct the record read first, then make a separate test correcting the one read second.
- Use a replacement value smaller than the old value, equal to it, larger than it, and zero.
- Arrange a case where the preferred record changes after correction.
- Arrange an exact tie after correction, including names whose order differs from the input order.
- Include uppercase/lowercase names that remain distinct and have a clear Python string order.
- Use the largest allowed field values for your section.
- Compare the original dictionary before and after the correction function.
- Check that the returned dictionary keeps both records and all unaffected fields.
- Check that the selection function returns a name string, even though the final report also prints a total.

| Sections | Preferred combined value | Name order when that value is tied |
| --- | --- | --- |
| IPRFAL261 and IPRFAL262 | Highest | Ascending Python string order. |
| IPRFAL263 and IPRFAL264 | Lowest | Ascending Python string order. |

Report order is a separate rule: the corrected records appear sorted by name. Choosing the preferred record does not mean moving its record to the first printed line.

### Problem 3: searches and batches

| Section | Test prompts |
| --- | --- |
| IPRFAL261 | The smallest batch, a prime-sized batch, a composite batch with several valid pack sizes, and a composite with a relatively large smallest factor. A valid pack needs at least two prizes and at least two packs. |
| IPRFAL262 | Two equal numbers, one dividing the other, numbers with several common factors, and numbers with no common factor above 1. Unlike the prize-pack task, a common divisor may equal one of the input values. |
| IPRFAL263 | Zero tiles, one tile, an exact square, a value immediately below a square, and a value immediately above a square. A valid side length can be zero. |
| IPRFAL264 | A target already equal to 1, a target reached after one press, a later reachable target, and an unreachable target between reachable values. Zero presses is a valid answer. |

For each section, also test:

- A single case.
- The maximum of 40 cases.
- Identical consecutive cases.
- A successful case followed by an unsuccessful one and then another successful one.
- Several cases whose searches finish at different times.
- A result at the final legal boundary.

The returned list must have one element per input case. Check the whole list and its order, not only whether an individual result looks reasonable. Use exact comparisons with the specified missing-result marker: zero may be a valid result, while `-1` is truthy in Python.

### Problem 4: test sequences, not just isolated commands

The four variants use these command words:

| Section | Increase | Decrease | Report | Stop |
| --- | --- | --- | --- | --- |
| IPRFAL261 | `ADD` | `TAKE` | `SHOW` | `END` |
| IPRFAL262 | `LOG` | `ERASE` | `SHOW` | `END` |
| IPRFAL263 | `ADD` | `SELL` | `SHOW` | `END` |
| IPRFAL264 | `BORROW` | `RETURN` | `SHOW` | `END` |

Build your own sequences covering these situations:

| Situation | What to verify |
| --- | --- |
| No initial records; `END` is the first command | No output at all. |
| Initial records exist; `END` is the first command | Still no output; loading records does not itself print a report. |
| A report on an empty dictionary | Exactly one empty-state message line. |
| A decrease for an absent pair | The missing-record response, with no new entry created. |
| A decrease larger than the current count | The insufficient-quantity response, with the dictionary unchanged. |
| A decrease equal to the current count | Successful zero result in the response, followed by removal of that record. |
| A report after removal | The removed entry is absent. |
| Another decrease after removal | It now addresses an absent pair. |
| An increase after removal | The pair can be created again using its current, absent state. |
| Two pairs share only their first component | They remain independent records. |
| Two pairs share only their second component | They also remain independent records. |
| Names differ only in case | They remain distinct and sort according to Python string order. |
| Repeated reports with no intervening update | The report does not change the stored data. |
| Several large increases on the same pair | The accumulated value follows the rules without an invented cap. |
| 50 initial records and 100 command lines including `END` | All initial records are read; exactly 99 command responses are printed. |

Check the **order** of failure rules in the handout. A missing pair is not an existing pair with an insufficient quantity. Keep the response and state checks separate when debugging.

## 24. Local testing and public test files

### A useful testing order

1. Read the contract and predict a small case by hand.
2. Test the function directly with Python objects.
3. Check any required state change or preservation of the original.
4. Run the complete program with input lines in the exact required structure.
5. Compare output characters and line counts.
6. Run the public test files, then add your own boundary cases.
7. Re-run earlier tests after a fix to make sure another case still works.

### Test a function before its input parser

For the independent program in Section 22:

```python
# Run after the count_uppercase definition from Section 22.
assert count_uppercase("") == 0
assert count_uppercase("Blue SKY") == 4
assert count_uppercase("quiet words") == 0
assert count_uppercase("A" * 60) == 60
```

These checks isolate calculation errors from input-reading and report-formatting errors. The same testing idea applies to your own functions, using the contract and expected results that you work out.

For dictionary arguments, use a fresh test setup for each independent test. A mutating call from a previous test can otherwise alter the starting state of the next one.

### Download and understand all-tests.zip

Open the correct Lab 08 problem in your section's Codeforces contest and download **`all-tests.zip`** from its materials. The archive contains input files and the corresponding expected-answer files:

| File | Meaning |
| --- | --- |
| `tests/01` | First example's complete input. |
| `tests/01.a` | Expected output for that input. |
| `tests/02` and `tests/02.a` | Second example and its expected output. |
| `tests/03` through `tests/07` | The five scored input files. |
| `tests/03.a` through `tests/07.a` | Their expected outputs. |

Each input file is a **separate program run**. Reset the program's data for that run. Do not concatenate all seven files into one input stream or treat the filename number as another value to read.

An answer file can legitimately be empty when its input requests no output. Its size being zero does not mean the file is missing or damaged.

The input and answer files let you investigate what failed. Your submitted program should calculate the result for its input; it should not read the `.a` files or contain hard-coded answers for published test files.

### Compare output without hiding differences

For debugging, `repr()` reveals newlines and spaces:

```python
print(repr(""))        # ''
print(repr("\n"))      # '\n'
print(repr("0\n"))     # '0\n'
print(repr("oak \n"))  # 'oak \n'
```

These strings represent four different outputs. Avoid calling `.strip()` on both outputs merely to make a local comparison pass; it can hide the blank-line and extra-space errors you need to find.

### Optional: run one test file from a separate local Python script

This is a testing utility, separate from your submitted program. It uses standard-library tools to run a saved file and compare output. The imports and process-handling code are not requirements for your lab solution.

Replace the filename with your own saved program, extract the archive so the `tests` folder is beside the utility, and choose a matching input/answer pair:

```python
from pathlib import Path
import subprocess
import sys

program = Path("Lab8_P1_YourStudentID.py")
input_path = Path("tests/03")
answer_path = Path("tests/03.a")

test_input = input_path.read_text(encoding="utf-8")
expected = answer_path.read_text(encoding="utf-8")
completed = subprocess.run(
    [sys.executable, str(program)],
    input=test_input,
    text=True,
    capture_output=True,
    timeout=3,
)

# Normalize Windows line endings, while keeping other spaces and newlines.
actual = completed.stdout.replace("\r\n", "\n")
expected = expected.replace("\r\n", "\n")
print("Return code:", completed.returncode)
print("Output matches:", actual == expected)
if completed.returncode != 0 or actual != expected:
    print("Expected:", repr(expected))
    print("Actual:", repr(actual))
    print("Errors:", completed.stderr)
```

- A return code of 0 normally means the process exited successfully; it does not by itself prove the answer is correct.
- A timeout suggests that you should inspect the loop's progress and the number of input lines it tries to read. Adjust the local timeout if your environment needs it; this utility does not change the judge's limits.
- Run this helper as a different file from `program`, or it would try to launch itself repeatedly.
- Keep the helper's diagnostic output out of the submitted program.

### What public tests cannot check for you

Console output alone does not establish that you used the required function, preserved the original object, supplied a docstring, or used the required loop constructs. Inspect and test those requirements separately. Passing the published tests is a useful check, while the problem's complete contract still applies.

## 25. Common questions and debugging

**Why does my while body never run?** Its condition is already false at the first check. Check whether that is the intended boundary case and whether the initial result is correct.

**Why does while...else run for an empty tuple?** The condition is false immediately, and that is normal completion. No `break` skipped the loop's `else`.

**Why did my loop else run after an inner break?** The inner `break` leaves only its own loop. The outer loop may later finish normally and run its own `else`.

**Does continue prevent the loop's else?** No. If the loop eventually finishes through a false condition without a `break`, its `else` runs.

**Why are only the first case's results returned?** Look for a `return` inside the outer loop. Also check that results are collected in the intended list before the function returns.

**Can zero mean “not found”?** Only if that exact contract chooses zero as its marker. In other current tasks, zero is a valid result. Use the specified marker and an exact comparison rather than `if not result`. Remember that `bool(-1)` is `True`.

**Why does converting input to a tuple still leave strings?** `tuple()` changes the container. Use numeric conversion for the elements that must be integers.

**Can I append to a tuple?** Tuples have no `.append()` method. Build a sequence in a list and convert it when appropriate, or use permitted tuple operations. Match the required type at the function boundary.

**Why did changing a list inside a tuple work?** The tuple retains the same element references. The referred-to list can still change its own contents.

**Is every tuple a valid dictionary key?** No. Its elements must also be hashable. A pair of strings works; a tuple containing a list does not.

**Why does an existing zero value look absent?** A truth-value check tests the value's truthiness. Membership checks whether the key exists. Those are different questions.

**Does get create a record when it returns its default?** No. `.get()` only reads. An assignment or another mutating operation is needed to insert an entry.

**Why did my original dictionary change?** You may have used an alias, changed a shared mutable inner value, or modified the original before making a copy. Check object identity and the types of the stored values.

**Do immutable tuples mean the dictionary itself cannot change?** No. A dictionary can replace an entire value, add keys, and remove entries even when its values happen to be tuples.

**Why did assigning a new dictionary to my parameter not update the caller's dictionary?** That assignment rebinds the local name. It does not change the original object. Follow the specified choice between returning a new dictionary and mutating the supplied one.

**Why is a report's order wrong after deletion and reinsertion?** Insertion order changes when an entry is removed and added again. Use the explicit sorted report order required by the problem.

**Why does a failed operation cause a later test to fail?** Inspect whether the rejected operation changed state before returning its message. Also check that processing advances to the next command.

**Why does the program wait after END?** You may be trying to read a fixed number of commands, or reading another line before checking the command you already have. The sentinel ends the assigned command stream.

**Why is there an extra empty line or None?** Check for `print("")` when no output is required, a printed sentinel acknowledgment, or `print()` wrapped around a function that already prints.

**Should I submit the testing utility or the public answer files?** Submit your four separate solution programs. The testing utility and downloaded answer files are local study tools.

### Troubleshooting table

| Symptom | Likely cause to inspect |
| --- | --- |
| `IndexError` at the last element | Condition admits an index equal to the sequence length. |
| `IndexError` on a short command | Fields were accessed before recognizing the command kind. |
| Infinite repetition on one element | A branch or `continue` skipped the position update. |
| Later cases produce no result | Inner state was initialized only once. |
| Both match and no-match responses appear | Misplaced `else`, incorrect indentation, or the intended `break` is missing. |
| Numeric code starts producing floats | `/` was used where an integer quotient was intended. |
| A corrected value appears added twice | Replacement and increase were confused. |
| A one-element value has the wrong type | The tuple's trailing comma is missing. |
| `TypeError` for a dictionary key | The proposed key contains an unhashable object. |
| Dictionary variable becomes `None` | It was assigned the result of `.update()` or `.clear()`. |
| A record disappears after a rejected request | Validation happened after a destructive update. |
| Two independent records interfere | Only one component, or an ambiguous concatenation, was used as the key. |
| Correct winner but wrong report order | Selection order and display order were treated as the same thing. |
| Correct isolated commands but wrong combined run | State was reset too often or retained from the wrong run. |

## 26. Optional extra practice

These exercises are **separate from the four assigned problems**. They provide more practice without complete solutions. Use simple parameter and return annotations, add docstrings, and write your own tests.

### A. A trailing run of punctuation

Implement `trailing_marks(text: str, mark: str) -> int`. Return how many consecutive copies of `mark` occur at the **end** of `text`. Use a `while` loop and leave the text unchanged. A matching character elsewhere in the text does not belong to this trailing run.

**Input:** exactly **two lines**:

- Line 1 contains 0 to 40 characters, each an English letter or one of `!`, `?`, and `.`. The line may be empty.
- Line 2 contains exactly one character from `!`, `?`, and `.`.

All input is valid.

**Output:** exactly **one line** containing the integer length of the trailing run.

Example 1 input:

```text
Really!!!
!
```

Example 1 output:

```text
3
```

Example 2 input:

```text
Hi?!
?
```

Example 2 output:

```text
0
```

Extra tests: empty text, one matching character, one different character, all 40 characters matching, and a matching run that stops at an internal letter. Explain the condition that keeps every inspected index valid. As an extension, process a tuple of several texts with an outer `while` and collect one count per text.

### B. Rotate a tuple of labels

Implement `rotate_left(labels: tuple, shift: int) -> tuple`. Return a tuple with the same labels rotated left by `shift` positions. A label that passes the left end reappears at the right end. Leave the input tuple unchanged. Rotating an empty tuple returns an empty tuple.

**Input:** exactly **three lines**:

- Line 1 contains `n`, an integer from 0 to 12.
- Line 2 contains exactly `n` space-separated English-letter labels, each 1 to 8 characters long. This line is empty when `n` is zero. Repeated labels are allowed and labels are case-sensitive.
- Line 3 contains `shift`, an integer from 0 to 100.

All input is valid. Convert the labels to a tuple before the function call.

**Output:** exactly **two lines**:

- Line 1 contains the rotated labels separated by single spaces, with no brackets or quotes. It is blank if the tuple is empty.
- Line 2 contains the number of labels in the returned tuple.

Example 1 input:

```text
4
Ash Elm Oak Pine
6
```

Example 1 output:

```text
Oak Pine Ash Elm
4
```

Example 2 input:

```text
1
Birch
100
```

Example 2 output:

```text
Birch
1
```

Extra tests: an empty tuple, zero shift, a shift equal to the tuple's length, repeated labels, and a shift much larger than the length. If you use a remainder to reduce the shift, explain why an empty tuple must be considered before dividing by its length. For the empty case, the output's debugging representation is `'\n0\n'`.

### C. A word frequency report

Implement `word_frequencies(words: tuple) -> dict`. Use a `while` loop to return a new dictionary mapping each distinct word to its occurrence count. Preserve case and leave the input tuple unchanged.

**Input:** exactly **one line** containing 0 to 20 space-separated words. Each word contains 1 to 8 English letters. An empty line means no words. All input is valid.

**Output:** exactly **`u + 1` lines**, where `u` is the number of distinct words:

- Line 1 contains `u`.
- Each of the next `u` lines contains `word count`, separated by one space, in ascending Python string order of words.
- For empty input, print only the single line `0`.

Example 1 input:

```text
moss pine moss
```

Example 1 output:

```text
2
moss 2
pine 1
```

Example 2 input:

```text
Oak oak Oak
```

Example 2 output:

```text
2
Oak 2
oak 1
```

Extra tests: no words, one word, 20 identical words, 20 distinct words, and several case-sensitive variants. Check that the sum of the recorded frequencies equals the number of input words and that creating the output report does not modify the counts.

## 27. Submission checklist

- Use the latest official Lab 08 question sheet for your own section. There are **four** assigned problems.
- Keep each problem in a separate, complete Python 3 program.
- Use `Lab8_PY_Z.py`, replacing `Y` with the problem number and `Z` with your student ID; for example, `Lab8_P1_<YourStudentID>.py`.
- Follow the required function names, parameter order, container types, return values, and side effects.
- Use simple, accurate parameter and return annotations. Explain container contents and any special integer markers in the docstring.
- Include a purpose/parameter/return docstring for every function, including helpers.
- Use `while` for the required calculations, nested `while` and `while...else` where required, and `break` for the assigned command sentinel.
- Read exactly the specified input structure, including records or pairs on the same line. Do not read a command count that the format does not supply.
- Preserve original data where required. For mutable command logs, check the actual dictionary state after both success and rejection.
- Preserve case, tuple-key component order, and independent records.
- Test valid zero, exact equality, no result, repeated cases, maximum sizes, and deletion followed by reinsertion.
- Check selection rules, name tie-breakers, and report order separately.
- Run the example files, all five scored test files, and additional boundary cases of your own.
- Check exact labels, punctuation, spacing, and output line counts. A first-command `END` requires no output.
- Remove input prompts, temporary traces, diagnostic output, and accidental extra `None` lines.
- Submit identical code to **both Codeforces and Canvas** by **23:59 Vietnam time (UTC+7)** on **Friday, 20 November 2026** for IPRFAL261–262, or **Sunday, 22 November 2026** for IPRFAL263–264.
- Follow the current course rules for your Codeforces display name and other submission details.

Review the [platform submission guide](../lab-tutorial.md), [Lab 03 function guide](../lab-03/README.md), [Lab 06 lists and mutation guide](../lab-06/README.md), and [Lab 07 iteration guide](../lab-07/README.md) as needed. Use the current Lab 08 handout for this lab's exact requirements and deadlines.

## Further reading

These official references also discuss features beyond this lab. Focus on the basic operations used above and follow the assigned implementation requirements.

- [Python tutorial: break, continue, and loop else](https://docs.python.org/3/tutorial/controlflow.html#break-and-continue-statements)
- [Python tutorial: else clauses on loops](https://docs.python.org/3/tutorial/controlflow.html#else-clauses-on-loops)
- [Python tutorial: tuples and sequences](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences)
- [Python tutorial: dictionaries](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)
- [Python reference: dictionary methods, views, and ordering](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict)
- [Python tutorial: function annotations](https://docs.python.org/3/tutorial/controlflow.html#function-annotations)
