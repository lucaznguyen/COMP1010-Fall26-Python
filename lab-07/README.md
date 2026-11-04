# Lab 07: Lists, Iterations and Loops — Detailed Study Guide

**Course:** COMP1010 - Introduction to Programming

**Week:** 07

**Preparation:** Functions and docstrings, strings, conditions, exceptions, lists, and mutation from the earlier labs.

This guide supports Fall 2026 Lab 07 for **IPRFAL261–IPRFAL264**. It explains the shared techniques through independent examples, debugging questions, extreme cases, and optional practice. Use the latest official question sheet for **your own section** for the assigned problems, required functions, constraints, allowed operations, and exact output labels. The examples below are separate from the assigned programs.

**Submission:** Submit the **four** problems as separate Python 3 programs to **both Codeforces and Canvas**, using identical code, **before the end of your lab session**. See [Section 24](#24-submission-checklist).

A correct final value is only part of this lab. Your program may also need to preserve spaces, stop at the right moment, print a complete trace, change an existing object, or leave all data unchanged after a failed operation. A required loop or a prohibited method is part of the contract even if another approach produces the same output.

## What you should be able to do

- Choose a `for` loop or a `while` loop and explain how it stops.
- Use `range()` without missing an endpoint or processing it twice.
- Keep separate counters, accumulated results, positions, and current state.
- Explain a one-pass solution without relying on prohibited shortcuts.
- Build strings and grid rows while preserving significant spaces.
- Work with integer digits using `%` and `//`.
- Record steps, count transitions, and recognize repeated states.
- Scan characters and candidate substring positions deliberately.
- Parse delimiters without hiding invalid empty groups.
- Validate a command fully before changing a list or dictionary.
- Apply multiple ordering rules in the correct directions.
- Document parameter types, return types, and side effects.

The examples use ordinary loops, functions, lists, strings, and dictionaries. Advanced annotations, classes, recursion, regular expressions, and comprehensions are not needed. A helper shown here is usable in an assignment only when that assignment permits its operations.

## Navigation

- [1. Read the contract and restrictions](#1-read-the-contract-and-restrictions)
- [2. Function types, docstrings, and returned results](#2-function-types-docstrings-and-returned-results)
- [3. Input lines and significant spaces](#3-input-lines-and-significant-spaces)
- [4. Validation and normal outcomes](#4-validation-and-normal-outcomes)
- [5. For loops: values and positions](#5-for-loops-values-and-positions)
- [6. Range and boundary reasoning](#6-range-and-boundary-reasoning)
- [7. While loops and termination](#7-while-loops-and-termination)
- [8. Counters, accumulators, and early stopping](#8-counters-accumulators-and-early-stopping)
- [9. One-pass processing and invariants](#9-one-pass-processing-and-invariants)
- [10. Nested loops and building rows](#10-nested-loops-and-building-rows)
- [11. Pattern output and invisible spaces](#11-pattern-output-and-invisible-spaces)
- [12. Integer digits and arithmetic operators](#12-integer-digits-and-arithmetic-operators)
- [13. Traces, step counts, and repeated states](#13-traces-step-counts-and-repeated-states)
- [14. Character scanning and exact alphabets](#14-character-scanning-and-exact-alphabets)
- [15. Substring windows and overlapping matches](#15-substring-windows-and-overlapping-matches)
- [16. Delimiters, groups, and lookup tables](#16-delimiters-groups-and-lookup-tables)
- [17. List mutation and operation order](#17-list-mutation-and-operation-order)
- [18. Dictionaries and related state](#18-dictionaries-and-related-state)
- [19. Validate commands before making changes](#19-validate-commands-before-making-changes)
- [20. Ordering, ties, and final reports](#20-ordering-ties-and-final-reports)
- [21. Extreme cases for all four sections](#21-extreme-cases-for-all-four-sections)
- [22. Local testing and common questions](#22-local-testing-and-common-questions)
- [23. Optional extra practice](#23-optional-extra-practice)
- [24. Submission checklist](#24-submission-checklist)

## 1. Read the contract and restrictions

Before coding, separate the complete program's input/output from the function's parameters and returned value.

| Part of the contract | Questions to answer |
| --- | --- |
| Input | How many lines? Are values separated by whitespace, commas, or exactly one space? |
| Validation | Which conditions make the input invalid? What message is required? |
| Function | What values does it receive? Does it return a row, a number, a pair, or a Boolean? |
| Side effects | Must it change the existing list or dictionary? What must stay unchanged on failure? |
| Loop | Is a single traversal required? Must a particular operation use a loop? |
| Stopping | Is there a target, a first match, a repeated state, a maximum step count, or a combination? |
| Output | How many lines? Does the count come before the trace? Which spaces and labels are literal? |

The current sections emphasize different applications:

| Section | Main applications |
| --- | --- |
| IPRFAL261 | Number patterns, first-event processing, repeated-state traces, ordered list edits. |
| IPRFAL262 | Symmetric patterns, distinct-value selection, bounded numeric traces, league data. |
| IPRFAL263 | Character categories, overlapping patterns, text/code conversion, repairing string helpers. |
| IPRFAL264 | Coordinate-based patterns, records in a sequence, numeric traces, library requests. |

### Restrictions apply to the problem that states them

- Some numeric tasks require one pass and prohibit `max()`, sorting, or additional operations. Do not use a convenient replacement before checking that task's full list.
- An arithmetic digit-reversal task requires `%`, `//`, and a loop. A string-slicing shortcut does not meet that requirement.
- A pattern-search task prohibits particular search/count methods and regular expressions.
- The string-repair task prohibits several trimming, splitting, joining, replacing, and searching methods. Techniques using those methods elsewhere in this guide do not override that rule.
- Some row functions must return text; a coordinate-based row function must build it through a column loop.

Follow the current sheet if it requests multiple function versions. Multiple versions do not automatically mean multiple printed answers: the program's **Output** section decides the line count. If a required function name or version is unclear, ask the teaching staff rather than borrowing another section's interface.

## 2. Function types, docstrings, and returned results

An annotation goes **after a parameter name** and **after the closing parenthesis** for the return type:

```python
def count_spaces(text: str) -> int:
    """Return the number of ordinary space characters in text.

    Args:
        text: The string to inspect; it may be empty.

    Returns:
        An integer count. The string is unchanged.
    """
    count = 0
    for character in text:
        if character == " ":
            count += 1
    return count


answer = count_spaces("go  now")
print(answer)  # 2
```

`text: str` describes the parameter. `-> int` describes the returned object. Annotations do not read input, convert strings, validate ranges, or automatically reject a wrongly typed argument.

| Required behavior | Simple annotation |
| --- | --- |
| Return one row or formatted message | `-> str` |
| Return a count, position, or computed integer | `-> int` |
| Return success or failure | `-> bool` |
| Return a list of recorded steps | `-> list` |
| Return a pair of related values | `-> tuple`, with both components described in the docstring |
| Print directly and return no useful value | `-> None` |

Use plain `list`, `dict`, and `tuple` annotations where appropriate; describe element types in the docstring. Advanced syntax such as `tuple[str, int]` or union annotations is not needed for this guide.

### A pair is one returned object

Some current Lab 07 functions explicitly return a pair. This is part of their contract, even if earlier labs used only a single numeric or string result.

```python
def label_info(label: str) -> tuple:
    """Return the label and its length as a pair.

    Args:
        label: A string, including any significant spaces.

    Returns:
        A tuple containing the original string and an integer length.
    """
    return label, len(label)


text, size = label_info("Map")
print(text)  # Map
print(size)  # 3
```

The comma creates the pair. Unpacking assigns its two components to two names. Returning the string `"Map, 3"` would instead return one string; it could not be used in the same way.

### A missing result is different from zero

A function may have a legitimate integer result or return `None` when no result exists. You can leave off the return annotation and describe both cases precisely in its docstring. Do not annotate it `-> int` and silently return `None`.

When distinguishing these cases, use `result is None`. The test `if not result` also treats `0` as false, even though position `0` or a numeric answer of `0` may be correct.

Printing and returning remain different. If a function returns a row, the caller prints it. If it prints directly, wrapping its call in `print(...)` adds an unwanted `None` line. If it mutates an object and returns a Boolean, inspect the changed object separately from that Boolean.

Every helper, including an inner helper, needs a docstring describing purpose, parameters, and return value. For mutable parameters, explain what changes and what remains unchanged on failure.

## 3. Input lines and significant spaces

One `input()` call reads one line and removes its terminating newline. Other spaces remain.

```python
line = "  violet  "
print(repr(line))          # '  violet  '
print(repr(line.strip()))  # 'violet'
```

`repr()` is useful during debugging because it shows the boundaries of a string. Its quotes are not normally part of the required output.

### Choose the split rule from the contract

```python
line = "one  two"
print(line.split())     # ['one', 'two']
print(line.split(" "))  # ['one', '', 'two']
```

- `.split()` treats runs of whitespace as separators and ignores surrounding whitespace.
- `.split(" ")` uses the literal space as the separator and preserves empty fields between consecutive spaces.
- `.split(",")` separates comma-delimited entries, including empty entries when delimiters are repeated or appear at an end.

For a space-separated integer list, the familiar expression is:

```python
values = list(map(int, input().split()))
```

For comma-separated labels, an independent parsing example is:

```python
line = "Amber, Cyan , Plum"
labels = []
for entry in line.split(","):
    labels.append(entry.strip())
print(labels)  # ['Amber', 'Cyan', 'Plum']
```

Use trimming only when the contract permits it. In particular:

- A password line is inspected exactly as entered; stripping can hide an invalid space.
- A pattern may contain meaningful spaces. Trimming it can change what is being searched for.
- Encoded groups separated by **single spaces** need their empty groups preserved for validation.
- A string-cleaning task receives the original text; do not pre-clean the data that the function is supposed to process.

Do not apply this parsing example to a task that prohibits `.split()` or `.strip()`.

### Read all required lines

If the interface specifies three lines and says the third is ignored for one mode, read it anyway. “Ignored” means its contents do not affect that mode's result; it does not change the input format to two lines.

An empty input line is `""`. A line containing three spaces is `"   "`. A missing line causes `input()` to raise `EOFError`. These are different situations. For numeric lists, splitting an empty or spaces-only line produces `[]`; converting its elements does not raise `ValueError`, so a non-empty-list requirement needs a separate check.

## 4. Validation and normal outcomes

Distinguish invalid input from a valid case with an unusual result.

| Situation | Interpretation |
| --- | --- |
| A numeric token such as `"pear"` where an integer is required | Conversion failure. |
| A parsed value outside an explicitly valid range | Domain validation failure. |
| A non-empty list whose elements are all equal | Valid data; a distinct-value answer may not exist. |
| A process already at its target state | Valid zero-step process. |
| A well-formed request that cannot currently succeed | Failed operation, handled by that task's rule. |
| A malformed request among other requests | Usually count/skip that request as specified; do not discard the whole day's valid work. |

### Validate conversion and meaning separately

This independent fragment accepts an integer from `2` through `6` and prints twice its value:

```python
try:
    amount = int(input())
    if amount < 2 or amount > 6:
        raise ValueError
except (ValueError, EOFError):
    print("Invalid amount")
else:
    print(amount * 2)
```

**Input:** exactly one line containing the candidate integer. **Output:** exactly one line containing the doubled value or `Invalid amount`.

| Input line | Output line |
| --- | --- |
| `2` | `4` |
| `6` | `12` |
| `1` | `Invalid amount` |
| `3.5` | `Invalid amount` |
| `three` | `Invalid amount` |

The `else` runs only when the `try` block completes without a handled exception. A domain failure can be raised as `ValueError` and handled in the same place as a conversion failure.

Do not repeatedly ask for replacement input in a submitted program. Print the required error once and finish, unless the interface explicitly describes repeated input. Do not print a normal answer after the error.

`int()` accepts more forms than an exact digit-only text grammar. Use the numeric conversion required by the contract; when a task specifies an exact character grammar, validate that grammar too. Checking that conversion succeeds is not a universal check for every textual format.

Do not catch every exception just to print “invalid input.” An `IndexError` caused by accessing the wrong position is usually a bug in your program, not evidence that the user's input is invalid.

## 5. For loops: values and positions

A `for` loop takes successive items from an iterable:

```python
labels = ["Fig", "Oak", "Elm"]
for label in labels:
    print(label)
```

Output is three lines, in the original order:

```text
Fig
Oak
Elm
```

The loop variable contains a **value** here. It is not automatically a position.

```python
labels = ["Fig", "Oak", "Elm"]
for position in range(len(labels)):
    print(position, labels[position])
```

Output:

```text
0 Fig
1 Oak
2 Elm
```

`enumerate(labels)` supplies both a position and a value. `enumerate(labels, 1)` starts its displayed counter at one; list indexing still starts at zero. Do not use a 1-based displayed position directly as a 0-based list index without accounting for that difference.

For an empty list, the loop body runs zero times. Any result initialized before the loop still exists, but a name first assigned only inside the body may never be created.

## 6. Range and boundary reasoning

`range(start, stop, step)` includes `start` and excludes `stop`. Omitting `start` gives zero; omitting `step` gives one.

| Expression | Values visited |
| --- | --- |
| `range(4)` | `0, 1, 2, 3` |
| `range(1, 5)` | `1, 2, 3, 4` |
| `range(2, 9, 3)` | `2, 5, 8` |
| `range(5, -1, -2)` | `5, 3, 1` |
| `range(3, 3)` | No values. |
| `range(1, 5, -1)` | No values; the step points away from the stopping boundary. |

`range(0, 4, 0)` raises `ValueError`: a zero step cannot make progress.

For a list of length `size`:

- Valid indices run from `0` through `size - 1`.
- A loop over `range(size)` covers those indices.
- `range(1, size + 1)` is useful for 1-based row or step numbers.
- `range(size - 1, -1, -1)` visits valid indices in reverse order.

The extra `+1` is justified by an inclusive endpoint; it is not a fix to add blindly whenever a loop seems short.

### Separate two halves at their shared boundary

When two phases of a pattern meet, decide which phase owns the shared row. If both include it, it appears twice; if neither includes it, it disappears. Write the intended row widths or indices on paper before constructing the ranges.

Use the smallest permitted input to check this. A pattern that looks correct at a large size may repeat its middle row at the minimum size.

## 7. While loops and termination

A `while` loop repeats while its condition is true. Its body must change something that can eventually make that condition false, or leave the loop through `break` or a function return.

```python
remaining = 11
while remaining > 2:
    print(remaining)
    remaining -= 3
print("Stopped:", remaining)
```

Output:

```text
11
8
5
Stopped: 2
```

The condition is checked **before** each iteration. If `remaining` starts at `2`, the body runs zero times.

For every `while` loop, identify:

1. The starting state.
2. The condition that permits another iteration.
3. The state update inside the body.
4. Every reason the loop can stop.

### Break, continue, and return

- `break` exits the nearest enclosing loop.
- `continue` skips the rest of the current iteration and goes to the next one.
- `return` ends the entire current function, even when it is inside nested loops.

An index-based `while` loop can become infinite if `continue` skips its index update. Make sure every path that repeats also makes the required progress. In nested loops, a `break` in the inner loop does not automatically stop the outer loop.

If a function version must stop through its `while` condition, using a late `break` may fail that implementation requirement despite producing the right final answer.

## 8. Counters, accumulators, and early stopping

A counter records **how many** events occurred. An accumulator combines data such as a total or an output string. A position identifies **where** something happened. Do not interchange them.

```python
values = [4, -1, 6, 0]
positive_count = 0
positive_total = 0
for value in values:
    if value > 0:
        positive_count += 1
        positive_total += value
print(positive_count)  # 2
print(positive_total)  # 10
```

Initialize these variables **before** the loop. Initializing `positive_total = 0` inside the loop would erase earlier contributions on every iteration.

### Stop at the first qualifying item when asked

An independent search for the first sufficiently long label can return immediately:

```python
def first_long_label(labels: list, minimum: int):
    """Return the first qualifying 0-based position, or None.

    Args:
        labels: A list of strings, possibly empty.
        minimum: The minimum qualifying length, a non-negative integer.

    Returns:
        The first position whose label is long enough, or None if absent.
        The supplied list is unchanged.
    """
    for position in range(len(labels)):
        if len(labels[position]) >= minimum:
            return position
    return None


print(first_long_label(["Bee", "Cedar", "Maple"], 5))  # 1
print(first_long_label(["Cedar", "Bee"], 5))          # 0
print(first_long_label(["Bee"], 5))                   # None
```

The final `return None` is outside the loop. Putting it inside the loop's first non-matching branch would reject the remaining labels without examining them.

If a threshold is strict, equality does not trigger it. Keep an explicit equality test among your local cases: “below zero,” “at most zero,” and “different from zero” describe different conditions.

## 9. One-pass processing and invariants

One pass means processing the sequence in one traversal while maintaining enough information to describe the part already processed. It does not mean “one line of Python.”

For example, this independent loop keeps the longest label length seen so far:

```python
labels = ["Ox", "Birch", "Ash", "Willow"]
longest_length = 0
for label in labels:
    if len(label) > longest_length:
        longest_length = len(label)
print(longest_length)  # 6
```

The invariant is: **after each iteration, `longest_length` is the greatest length among the labels processed so far**. The initial zero works because string lengths cannot be negative.

That reasoning does not justify initializing an arbitrary numeric maximum to zero. For the data `[-12, -4, -9]`, zero is not even an element. When a non-empty numeric list is guaranteed, initializing a running extreme from actual input avoids an invented starting value.

### Reason about duplicates and missing information

- Two equal elements can be two occurrences but only one distinct value.
- A value equal to a current record is not a strictly larger record.
- If a state variable can be a valid zero, zero cannot also mean “not initialized.”
- If later updates depend on an earlier stored value, decide whether they need its old or new version.
- A nested loop that re-scans the input for every item is not one pass.
- Calling a prohibited built-in indirectly through a helper does not remove the prohibition.

For a required `for` and `while` version, write down the same invariant for both. The `while` version also needs an explicit position update. Compare their returned results on the same small tests, then print only the output required by the program contract.

## 10. Nested loops and building rows

In a grid, an outer loop often chooses a row and an inner loop chooses a column. Use separate variable names so the column loop does not overwrite the row number.

This independent example builds a row with one marked column:

```python
def marked_row(columns: int, marked_column: int) -> str:
    """Return a row of dots with one vertical-bar marker.

    Args:
        columns: A positive number of columns.
        marked_column: A valid 0-based column position.

    Returns:
        A string of exactly columns characters; it prints nothing.
    """
    row_text = ""
    for column in range(columns):
        if column == marked_column:
            row_text += "|"
        else:
            row_text += "."
    return row_text


for row in range(2):
    print(marked_row(4, 1))
```

Output is exactly two lines of four characters:

```text
.|..
.|..
```

For a pattern with several drawing conditions, decide on the character for a cell and append **one character** for that cell. Several independent `if` statements that each append a marker can accidentally append multiple characters at an intersection.

Reset `row_text` for every new row. Resetting it only once before the outer loop makes later rows contain all earlier rows as well.

## 11. Pattern output and invisible spaces

Think about a row as separate pieces:

```text
leading padding + visible content + any explicitly required trailing content
```

For an independent text example:

```python
padding = " " * 2
row_text = padding + "CAT"
print(row_text)
print(repr(row_text))  # '  CAT' -- debugging only
print(len(row_text))   # 5 -- debugging only
```

The first output line begins with two spaces. Replacing them with tabs is not equivalent. Applying `.strip()` to the finished row removes the leading padding.

### Avoid accidental separators

```python
padding = " " * 2
content = "CAT"
print(padding + content)       # Exactly two leading spaces.
print(padding, content)        # Three: print adds its default separator.
print(padding, content, sep="")  # Exactly two leading spaces.
```

For a task with no trailing spaces, do not add spaces merely to make every line as wide as the last one. A row's indentation plus its content may legitimately have a different length from another row.

For a fixed-width grid, however, every row must contain exactly the specified number of cells. Interior spaces are cells and must remain present. Check the **row length**, not just whether the shape looks plausible in the terminal.

If a pattern contains digits, determine whether it uses single-digit values or larger numbers. `str(12)` contains two characters. Do not extrapolate a single-digit layout beyond the handout's permitted size.

Use a monospaced terminal. During debugging, surround a line with visible boundaries, such as `print("[" + row_text + "]")`; remove these boundaries and all length/`repr()` output before submission.

## 12. Integer digits and arithmetic operators

For a non-negative integer `number`:

- `number % 10` gives its last decimal digit.
- `number // 10` removes that digit by integer division.
- `number / 10` produces a floating-point result and is not an interchangeable digit operation.

```python
number = 6080
last_digit = number % 10
remaining_digits = number // 10
print(last_digit)        # 0
print(remaining_digits)  # 608
```

Repeated extraction proceeds from **right to left**:

| Current integer | Last digit | Remaining integer |
| --- | --- | --- |
| `6080` | `0` | `608` |
| `608` | `8` | `60` |
| `60` | `0` | `6` |
| `6` | `6` | `0` |

If an output expression lists digits from left to right, directly printing this extraction order would be wrong. The order used for computing a sum and the order used for formatting its explanation are separate requirements.

Preserve the original number if later steps still need it. A loop that repeatedly rebinds its working number eventually reduces that working number to zero.

### Zeros and operator notation

- Reversing decimal digits into an integer does not preserve leading zeros; integers do not store that formatting.
- A zero digit still participates in a displayed digit expression when the contract includes every digit.
- A `while number > 0` digit loop runs zero times for zero. Decide whether zero is invalid, a permitted input needing special handling, or an intermediate value.
- The arithmetic operator for a square is `**`, as in `digit ** 2`.
- `^` is a bitwise operator in Python. When an output specification uses `^2`, those are **printed characters**, not instructions to use `^` for arithmetic.

Likewise, a printed division symbol `/` can be part of the required trace text while the actual computation uses `//` to keep an integer result.

## 13. Traces, step counts, and repeated states

A state is the value before or after an update. A step is a **transition** between states. Starting with one state does not mean one step has already happened.

This independent process subtracts three until it reaches a value at most two or uses its permitted number of steps:

```python
def subtraction_trace(start: int, limit: int) -> list:
    """Return the formatted transitions of a bounded subtraction process.

    Args:
        start: A positive starting integer.
        limit: A non-negative maximum number of transitions.

    Returns:
        A new list of step strings. No input is read and nothing is printed.
    """
    value = start
    lines = []
    while value > 2 and len(lines) < limit:
        next_value = value - 3
        lines.append(f"{value} - 3 = {next_value}")
        value = next_value
    return lines


steps = subtraction_trace(11, 5)
print(len(steps))
for line in steps:
    print(line)
```

Output is exactly four lines: a count followed by three transitions.

```text
3
11 - 3 = 8
8 - 3 = 5
5 - 3 = 2
```

| Call | Number of recorded transitions | Reason for stopping |
| --- | --- | --- |
| `subtraction_trace(2, 5)` | `0` | The starting state already satisfies the stopping condition. |
| `subtraction_trace(11, 0)` | `0` | No transitions are permitted. |
| `subtraction_trace(11, 2)` | `2` | The limit is reached before the target condition. |
| `subtraction_trace(11, 5)` | `3` | The target condition is reached before the limit. |

If the required first output line is the final number of steps, storing the formatted transitions lets you know that count before printing them. Do not print the trace early and try to place its count above it later.

### Detect a cycle before it runs forever

Consider an unrelated state sequence:

```text
red -> blue -> green -> blue
```

The initial history contains `red`. There are **three transitions**, and the final transition produces an already seen state. If the contract says to show the step that creates the repetition, that step still belongs in the trace.

Distinguish these moments:

1. Read the old state.
2. Compute the next state and the text describing that transition.
3. Record the transition.
4. Check whether the new state is a target or a repetition, in the order required by the task.
5. Add a genuinely new state to the history if another iteration is needed.

Adding a new state to the history **before** checking whether it was already present makes that check immediately true. Checking a transition only after an extra update can print one step too many.

Use the history structure required by the sheet. If it asks for a list of visited values, replacing it with a set is not automatically acceptable.

For a fixed limit, test success **on the last permitted transition** as well as failure at that limit. Reaching the target on that transition is still success. For a reported peak, include the starting state even if no transition occurs, and do not clamp later states to a bound that applies only to the input.

## 14. Character scanning and exact alphabets

A loop over a string visits its characters in order. A position loop also gives access to neighboring positions when needed.

```python
text = "a-b--c"
dash_count = 0
for character in text:
    if character == "-":
        dash_count += 1
print(dash_count)  # 3
```

When each character belongs to exactly one category, an `if/elif/else` structure can prevent double counting. A character classified as invalid should not also be counted as an allowed special character merely because it is not a letter or digit.

### ASCII ranges are narrower than Unicode character methods

If the sheet explicitly says `A`–`Z`, `a`–`z`, and `0`–`9`, those are exact ranges. Methods such as `.isalpha()` and `.isdigit()` also recognize characters outside those ASCII ranges.

```python
print("é".isalpha())            # True
print("a" <= "é" <= "z")      # False
print("²".isdigit())            # True
print("0" <= "²" <= "9")      # False
```

A fixed list of permitted symbols is also exact. “Special character” does not mean every punctuation mark on the keyboard. Use only the symbols in your section's current sheet.

The number of categories present is different from the total number of characters. Ten lowercase letters contribute ten to a character count but only one to the number of present categories.

For a rating rule, apply its priority in the stated order. Having all the categories present does not cancel a minimum-length requirement. Invalid characters are a validation issue before a valid password can be rated.

## 15. Substring windows and overlapping matches

A pattern of length `width` can start only where the entire pattern still fits. The last candidate position is:

```text
len(text) - width
```

For a non-empty pattern, the corresponding candidate range is `range(len(text) - width + 1)`.

The following independent example prints candidate windows rather than solving a counting or last-index problem:

```python
text = "xoxox"
width = 3
for start in range(len(text) - width + 1):
    print(start, text[start:start + width])
```

Output:

```text
0 xox
1 oxo
2 xox
```

Positions `0` and `2` both contain `xox`; their windows overlap. Advancing by the pattern's full length after a match skips valid overlapping starts.

### Counting and last-position searches retain different information

- A count records how many candidate positions matched.
- A last-position result records the highest matching position.
- A missing position such as `-1` is different from a valid match at `0`.
- A match at the last candidate position must still be examined.

If the pattern is longer than the text, there are no full candidate windows. Empty text with a non-empty pattern likewise gives no matches. An empty pattern needs the assignment's explicit handling before the normal search; do not silently accept Python's built-in empty-string search behavior when the sheet declares it invalid.

For a case-insensitive comparison, compare consistently normalized text and pattern. Keep spaces and punctuation unless the task says to remove them. For an ASCII example, `"Up"` and `"uP"` compare equal after `.lower()`, while `"u p"` still has an extra character.

Python's string `.count()` uses non-overlapping occurrences. A task that requires overlapping matches and prohibits `.count()` needs its own positional loop. Check the separate method restrictions for a last-index task as well.

## 16. Delimiters, groups, and lookup tables

When separators are part of a grammar, do not repair malformed input before validating it.

```python
code = "77  8"
print(code.split())     # ['77', '8']
print(code.split(" "))  # ['77', '', '8']
```

If groups must be separated by exactly one space, the empty middle group is evidence of invalid input. `.split()` hides it. Leading and trailing separators also create empty groups with `.split(" ")`.

Do not discard those empty groups just to make conversion succeed. The same principle applies to an empty comma-separated command: do not silently remove it if the task treats it as a malformed operation.

### A lookup table maps one representation to another

```python
symbols = {"!": "hit", "~": "pause"}
token = "~"
if token in symbols:
    print(symbols[token])  # pause
else:
    print("Unknown symbol")
```

This is an independent two-symbol example. A larger encoder/decoder needs its own mapping from the official contract.

For grouped codes, consider the following separate checks before selecting a mapped character:

- Is the group empty?
- Is its key allowed?
- Do all its characters follow the group's required form?
- Is its repetition count permitted for that particular key?
- Does a special group represent a space rather than an ordinary mapped letter?

A 1-based repetition count and a 0-based string position differ by one. Different keys need not contain equal numbers of choices.

When a group decodes to a space, that space is part of the result. Leading, trailing, or repeated decoded spaces must not disappear through a final `.strip()`. A string containing one space is not an empty string.

For string-cleaning work, distinguish an ordinary space `" "` from arbitrary whitespace. If the problem defines runs of spaces, broad whitespace normalization may change characters the function was not asked to change. When the task prohibits trimming/splitting/joining helpers, design a character loop that tracks the relevant previous state instead of calling those helpers first.

## 17. List mutation and operation order

List edits act on the **current** list. Earlier successful edits can introduce or remove the target of a later edit.

```python
labels = ["Amber", "Cyan", "Plum"]
alias = labels
labels.insert(1, "Teal")
labels.remove("Cyan")
print(labels)          # ['Amber', 'Teal', 'Plum']
print(alias is labels)  # True
```

An insertion at a position shifts that position's old element to the right. Inserting **after** an existing element requires reasoning from that element's current position; inserting before it is a different edit.

`list.index(value)` and `list.remove(value)` raise `ValueError` for an absent value. A task that treats an absent target as a skipped operation needs a guard rather than an unhandled exception.

### Separate the status from the changed list

This independent helper replaces one label, requiring the replacement to be unused:

```python
def replace_label(labels: list, old: str, new: str) -> bool:
    """Replace the first old label if the new label is unused.

    Args:
        labels: The existing list of string labels to modify.
        old: The label to find.
        new: A label that must not already occur in labels.

    Returns:
        True after one replacement; False if old is absent or new is
        already present. On failure the original list is unchanged.
    """
    if old not in labels or new in labels:
        return False
    position = labels.index(old)
    labels[position] = new
    return True


labels = ["Amber", "Cyan"]
success = replace_label(labels, "Cyan", "Teal")
print(success)  # True
print(labels)   # ['Amber', 'Teal']
```

The returned Boolean is not the updated list. Assigning `labels = replace_label(...)` would replace the caller's list reference with a Boolean.

List methods such as `.reverse()` change the list and return `None`. Do not write `labels = labels.reverse()`. Rebinding a parameter to `labels[::-1]` creates a different local list instead of changing the caller's existing one; use the required in-place operation when the contract asks for mutation.

Do not remove elements from the same list you are traversing without understanding how positions shift. Processing a separate command list while changing the data list avoids that particular conflict.

## 18. Dictionaries and related state

A dictionary associates a key with a value. For this lab, a key is often a name and a value is a count or other recorded data.

```python
totals = {"Amber": 4, "Cyan": 0}
print(totals["Amber"])  # 4
print("Teal" in totals)  # False
totals["Cyan"] += 2
totals["Teal"] = 1
print(totals["Cyan"])  # 2
print(totals["Teal"])  # 1
```

`name in totals` checks keys. Reading `totals[name]` when the key is missing raises `KeyError`. Check membership when a missing key is a normal rejected case.

Do not make up a missing record with a default value if the task requires an unknown name to fail. Conversely, if a successful operation is allowed to create a new record, initialize all of that record's required fields together.

### Different quantities need different meanings

For a general inventory record, a capacity, the current quantity available, and a cumulative activity count are three different quantities:

- Returning an item can increase availability without undoing an earlier activity count.
- Adding new capacity need not count as an activity of a different type.
- A failed action should not affect any of the quantities.
- The initial conditions determine which values start equal and which start at zero.

For example, if an unrelated visitor log stores a current occupancy of `2` and a cumulative arrival count of `9`, one departure can make occupancy `1` while arrivals remain `9`. Subtracting from both would change the meaning of the arrival count.

If two dictionaries describe the same named entities, keep their keys and updates consistent. A result that depends on two records must validate both before changing either.

If a record's value contains a mutable list, copying only the outer dictionary still shares those inner lists. For debugging, take a copy of each mutable inner record when you need an independent snapshot of its earlier contents.

## 19. Validate commands before making changes

Use two stages: **validate completely**, then **commit the update**.

| Validation stage | Example question |
| --- | --- |
| Command structure | Is the command known, and does it have exactly the required number of words? |
| Fixed words | Are required connecting words present in their exact positions? |
| Numeric syntax | Can the quantity or score components be parsed as required? |
| Domain | Is a quantity positive or non-negative as specified? |
| References | Do the referenced names exist? Must two names be different? |
| Current state | Is the requested action currently possible? |

Check the length **before** reading token positions:

```python
parts = "move Amber Cyan".split()
if len(parts) == 3 and parts[0] == "move":
    print(parts[1], parts[2])  # Amber Cyan
else:
    print("Invalid command")
```

Short-circuit evaluation prevents `parts[0]` from being read when the length check is false. A later access such as `parts[3]` needs its own justified size condition.

An independent two-dictionary update illustrates atomic behavior:

```python
def add_credit(totals: dict, updates: dict, name: str, amount: int) -> bool:
    """Add a positive credit and count the successful update.

    Args:
        totals: A dictionary mapping names to integer credits.
        updates: A dictionary mapping the same names to update counts.
        name: The existing name to update.
        amount: An integer credit; only positive amounts are accepted.

    Returns:
        True after updating both dictionaries, or False with both
        dictionaries unchanged if validation fails.
    """
    if name not in totals or name not in updates or amount <= 0:
        return False
    totals[name] += amount
    updates[name] += 1
    return True


totals = {"Amber": 4}
updates = {"Amber": 0}
print(add_credit(totals, updates, "Amber", -2))  # False
print(totals)   # {'Amber': 4}
print(updates)  # {'Amber': 0}
```

This is a small independent transaction, not a command dispatcher for an assigned problem. Its important property is that **no mutation occurs before all rejection checks finish**.

Do not subtract something, discover another field is invalid, and then return `False` without undoing the subtraction. Returning a failure status does not automatically restore data.

A main loop should count each failed operation once. It should not increment a failure counter for every individual reason that the same operation is invalid.

## 20. Ordering, ties, and final reports

Processing order and report order are different. Apply operations in the supplied order. After processing, build the report with the ranking rules specified by the problem.

Dictionary insertion order is not an alphabetical or numeric ranking rule. Sorting a list of numeric strings also differs from sorting their converted integer values.

### A separate ordering example

Suppose a help desk orders records by **priority descending**, then **arrival time ascending**, then **name ascending**:

```python
def queue_key(record: tuple) -> tuple:
    """Return a comparison key for a help-desk record.

    Args:
        record: A tuple containing a string name, integer priority,
            and integer arrival time, in that order.

    Returns:
        A tuple for descending priority, ascending arrival, then name.
    """
    name, priority, arrival = record
    return -priority, arrival, name


records = [("Lin", 2, 15), ("Ari", 2, 10), ("Bo", 1, 20), ("Zed", 2, 10)]
ordered = sorted(records, key=queue_key)
names = []
for record in ordered:
    names.append(record[0])
print(" ".join(names))
```

Output is one line:

```text
Ari Zed Lin Bo
```

Python compares the key components in order. Negating an integer reverses its contribution to an ascending sort. The unnegated arrival time and name keep their ascending directions.

Using `reverse=True` on a combined key reverses **all** its components, including any tie-breakers that were supposed to remain ascending. Work out each direction independently. This sorting example is not permitted as a shortcut in a different task that prohibits sorting.

### Build exactly the report requested

If the report excludes zero-activity records, remove them from the **report selection**; that does not mean deleting them from the stored state during processing. If none qualify, print the exact empty-report label.

A `Name:Count` item is a formatted string, not the raw representation of a dictionary or tuple. Where joining is allowed, collecting formatted items and joining them with `" "` produces single separators without an extra trailing separator.

Do not print an internal ranking field merely because it was needed to order records. Output only the fields and lines specified by the task.

## 21. Extreme cases for all four sections

Test both **valid boundaries** and **invalid input explicitly covered by the sheet**. Do not invent a behavior for unspecified modes or inputs; confirm an unclear contract with the teaching staff.

### Shared testing categories

| Category | What to check |
| --- | --- |
| Minimum and maximum valid input | Inclusive bounds, output size, loop endpoints. |
| Just outside a declared valid domain | Exactly the specified error and no normal output afterward. |
| Empty or spaces-only line | Preserve text when required; reject a numeric list if it must be non-empty. |
| One element or one candidate window | Initialization and first/last positions. |
| Duplicate values and equal records | Strict versus non-strict comparisons; occurrences versus distinct values. |
| All-negative data and valid zero | No invented zero maximum and no false “missing result.” |
| Immediate stop | Zero transitions; required summary lines still printed. |
| Stop on the last permitted step | Success at the limit is not failure. |
| Failed operation followed by a valid one | Failure leaves state intact and processing continues. |
| An earlier operation changes a later target | Use the updated state in the original operation order. |
| Several records tied in different ways | Every ranking level and its direction. |
| Repeated calls | Counters, output lists, and history are initialized afresh. |

### IPRFAL261

| Problem | Testing prompts |
| --- | --- |
| Number Mountain | Check heights `1` and `9`, then `0`, `10`, and non-integer text for the specified error. Inspect leading spaces, content order, and absent trailing spaces on every row. |
| First Overdraft | Try a result on the first or last transaction, a balance that becomes exactly zero, a later recovery after an earlier overdraft, no overdraft, one transaction, and all `100` permitted transactions. Reject a negative starting balance and a non-integer token as specified. |
| Happy Number Trace | Check starting at `1`, reaching `1` after several steps, entering a cycle, zeros among the digits, and bounds `1` and `10^9`. Count transitions rather than stored states, and verify the step that closes a cycle is included. |
| Bus Route Editor | Try edits at either end, duplicate insertion, absent targets, unknown commands, the wrong connecting word, and the wrong number of tokens. Remove all stops, then attempt another edit. Distinct stop names are case-sensitive; swapping an existing stop with itself is not excluded by the stated existence rule. |

For the first-event task, a later deposit cannot erase the fact that an earlier transaction was the first overdraft. Keep input validity separate from the state becoming negative during valid processing.

### IPRFAL262

| Problem | Testing prompts |
| --- | --- |
| Star Hourglass | Check `2` and `50`, the invalid lower neighbor `1`, the upper neighbor `51`, and non-integer text. Verify the total row count, symmetry, indentation, and a single center row. |
| Runner-Up Value | Try one value, all equal values, repeated greatest values, all-negative values, zero as a real answer, and a distinct candidate appearing late. Check the `1`- and `200`-value bounds and invalid numeric text. Verify the required one-pass implementation and every prohibited operation. |
| Reverse-and-Add Palindrome | Try an already palindromic input, trailing zeros in the starting integer, success after an update, success at the step limit, and no success within the limit. Check `1`, `10^6`, and out-of-range input. Inspect integer results and arithmetic reversal rather than a slicing shortcut. |
| League Table | Include a draw, a zero score, repeated valid matches, unknown teams, self-play, a malformed score, and wrong token counts. Test ties on points, then on the next ranking field, then on all numeric ranking fields. Invalid matches must leave both dictionaries unchanged. |

If all matches are invalid, the teams still exist with their initial statistics. A numeric field that decides a tie need not appear in the final printed report.

### IPRFAL263

| Problem | Testing prompts |
| --- | --- |
| Password Strength Meter | Test lengths `7` and `8`, several category counts, all four categories in a short password, one repeated category, every permitted special symbol, a disallowed symbol, and a space at either end. Read the original line without stripping; one invalid character is enough to invalidate the password. |
| Pattern Counter | Try overlapping occurrences, mixed case, a match at the last valid start, a one-character pattern, equal-length text/pattern, a longer pattern, empty text, and an empty pattern. Include literal spaces and punctuation. |
| Phone Keypad Code | Try the first and last permitted repetition counts on keys with different capacities, a space-only text, leading/trailing spaces in text, invalid characters, mixed digits within a code group, excessive repetitions, and doubled/leading/trailing code separators. Preserve decoded spaces. |
| Fix the String Tools | For cleaning, try no spaces, leading/trailing runs, internal runs, empty text, and all spaces. For the last index, try overlap, a result at `0`, a result at the last candidate, an absent or longer needle, and an empty needle. Read all three lines in either mode, and recheck all prohibited methods. |

The two string-search tasks have different case rules: the pattern counter is explicitly case-insensitive; do not automatically make the last-index task case-insensitive too. For space collapse, the required empty-result label is distinct from printing a blank line.

### IPRFAL264

| Problem | Testing prompts |
| --- | --- |
| Boxed X | Check odd sizes `3` and `49`, an even size, and values outside the valid interval. At intersections of drawing conditions, append only one character per cell. Check both the row count and each row's exact width. |
| Record-Breaking Days | Try one reading, all equal readings, strictly decreasing readings, strictly increasing readings, negatives, a repeated current record, and a late larger value. The first day counts; equality does not create another record. Check one-pass processing and the method restrictions. |
| Collatz Trace | Check `1`, an even start, an odd start, a start whose peak is above the starting value, and the upper valid input `1000`. Include the starting value in the peak and keep computed states as integers. The input bound does not cap intermediate values. |
| Library Loans | Try borrowing the last available copy, borrowing again with none available, returning when everything is already on the shelf, unknown titles, valid creation through an add request, and invalid quantities or command shapes. Test repeated successful borrow/return cycles, tied borrow counts, and no successful loans. |

For library state, successful borrowing activity is cumulative. A return does not erase an earlier successful borrow. Adding copies changes stock but does not itself count as borrowing. A failed request changes nothing and contributes one to the failed-request count.

## 22. Local testing and common questions

### A complete independent program

This example counts how many input integers are divisible by seven. It is separate from all assigned Lab 07 programs.

**Input:** exactly **one line**, containing between `1` and `8` whitespace-separated integers. A missing line, an empty numeric list, a non-integer token, or a list outside that size interval is invalid for this example.

**Output:** exactly **one line**, containing the count or `Invalid readings`.

```python
def count_sevens(values: list) -> int:
    """Return how many supplied integers are divisible by seven.

    Args:
        values: A list of integers; the list is not modified.

    Returns:
        The number of elements whose remainder modulo seven is zero.
    """
    count = 0
    for value in values:
        if value % 7 == 0:
            count += 1
    return count


def main() -> None:
    """Read one list line and print its count or validation error.

    Args:
        None. Input is read from standard input.

    Returns:
        None. Exactly one result line is printed.
    """
    try:
        values = list(map(int, input().split()))
        if len(values) < 1 or len(values) > 8:
            raise ValueError
    except (ValueError, EOFError):
        print("Invalid readings")
    else:
        result = count_sevens(values)
        print(result)


if __name__ == "__main__":
    main()
```

Example 1 input:

```text
14 -7 0 5
```

Example 1 output:

```text
3
```

Zero is divisible by seven because its remainder is zero. Negative multiples also qualify.

Example 2 input:

```text
5 9
```

Example 2 output:

```text
0
```

That zero is a valid answer. For input `14 pear`, the only output is `Invalid readings`.

### Test values, side effects, and output separately

For a mutating helper, keep an independent snapshot, call it, then compare both the returned status and the current data:

```python
labels = ["Amber", "Cyan"]
before = labels.copy()
success = replace_label(labels, "Missing", "Teal")
print(success)           # False
print(labels == before)  # True
```

This snippet uses the `replace_label` definition from Section 17. For nested mutable records, a shallow outer copy is not an independent snapshot of inner contents.

For a row or trace, inspect `repr(line)` and `len(line)` temporarily. Count the actual output lines, including any required blank ones. Remove debugging output from the submitted version.

### A practical debugging routine

1. Write a small input and predict every required output line by hand.
2. Run it in PyCharm or a terminal using the expected number of input lines.
3. Trace the loop's current value, position, counters, and stopping condition.
4. For mutation, inspect state immediately before and after each operation.
5. Change one feature of the test: equality, a duplicate, a boundary, a malformed token, or a missing target.
6. Run the latest saved file after the fix, then repeat the official examples.

### Common questions

**Can I use a built-in if it gives the same output?** Only when the assigned problem allows it. Implementation restrictions are still requirements.

**Why does my loop print only the first item?** Check for a `return` inside the body or a wrongly indented final output. A `return` ends the function immediately.

**Why does my loop never finish?** Check that every repeating path updates the loop's state or position. A `continue` can skip an update.

**Why does a correct search return “not found”?** Check whether you tested `if not result` when `0` is a valid position. Also inspect the final candidate position.

**Why is my step count one too large?** You may have counted the initial state as a transition, updated before testing the stopping condition, or included an extra state after reaching the limit.

**Should I strip every input line?** No. Strip only where normalization is permitted. Passwords, code groups, patterns, and strings being cleaned can contain significant spaces.

**May I print the Boolean returned by a state-changing helper?** Print it only if the Output section asks for it. The caller may instead use it internally to count failed requests.

**Does returning False undo a partial update?** No. Validate before mutation and check unchanged state in failure tests.

**Can I lower-case names to simplify matching?** Only when the contract requests case-insensitive matching. Route stops, teams, and book titles are case-sensitive in the current sheets.

**Does returning a pair require an advanced type annotation?** No. `-> tuple` with a precise docstring is enough here. Unpack the two components into meaningful names.

**If my function prints its own trace, can I still get the count first?** Follow the required function contract and program output order. A returned step description or stored trace can be printed by the caller when the contract permits it; do not silently change a required return into printing.

**What if a detail is missing from the sheet?** Ask the teaching staff. Do not copy an error label, mode rule, function name, or bound from a different section.

### Troubleshooting table

| Symptom | Likely cause to inspect |
| --- | --- |
| Last row or candidate never processed | Exclusive `range` endpoint. |
| Center row repeated | Both pattern phases include their shared boundary. |
| Grid line is too long | Several drawing conditions append at the same cell. |
| Shape is shifted by one space | `print()` added a separator, or indentation used a tab. |
| All-negative input gives a result absent from the data | Running extreme initialized to zero. |
| Duplicate maximum is treated as a new distinct value | Equality and distinctness were conflated. |
| Cycle reported immediately | New state added to history before membership was checked. |
| Overlapping occurrence missed | Search advances too far after a match. |
| Invalid code silently accepted | Whitespace collapsing hid an empty group. |
| Later command behaves strangely | Earlier failed command partially changed state. |
| List becomes None or True | Caller assigned a method's/status function's return to the data variable. |
| Tied names appear in reverse alphabetic order | A global reverse also reversed the final tie-breaker. |
| Correct results plus an extra None line | `print()` wrapped a function that already prints. |

## 23. Optional extra practice

These exercises are for additional study and are **separate from the four assigned problems**. No complete practice solutions are provided here. Use the stated function contracts, simple annotations, and docstrings. Each program is independent.

### A. Alternating checksum

Implement `alternating_checksum(values: list) -> int`. Starting at position zero, add the first value, subtract the second, add the third, and continue alternating. Traverse the list once with a loop. Leave it unchanged.

**Input:** exactly **one line**, containing `1` to `10` space-separated integers, each from `-20` to `20`, inclusive. Input is guaranteed valid for this practice exercise.

**Output:** exactly **one line** containing the integer checksum.

Example 1 input:

```text
4 1 7
```

Example 1 output:

```text
10
```

Example 2 input:

```text
-3 -3
```

Example 2 output:

```text
0
```

Extra tests: one value, all zeros, negative values at both kinds of positions, odd/even lengths, and the maximum length. Check that subtracting a negative value increases the total.

### B. Consecutive token groups

Implement `consecutive_groups(tokens: list) -> list`. Return a new list of strings in the form `Token:Count`, grouping only **adjacent equal tokens**. Preserve the groups' order. Use a loop and leave the input list unchanged.

**Input:** exactly **one line** with `0` to `10` whitespace-separated, case-sensitive words. An empty line means no tokens. Words contain only English letters, so a colon cannot occur inside a word.

**Output:** exactly **two lines**:

- Line 1 contains the group strings separated by single spaces, or is blank when there are no groups.
- Line 2 contains the number of groups.

Example 1 input:

```text
red red blue red
```

Example 1 output:

```text
red:2 blue:1 red:1
3
```

Example 2 input:

```text
Tap tap tap
```

Example 2 output:

```text
Tap:1 tap:2
2
```

Extra tests: no tokens, one token, every token equal, every adjacent pair different, and a final group longer than the first. For empty input, the output's debugging representation is `'\n0\n'`: a blank first line followed by `0`.

### C. A fixed-width name badge

Implement `badge_line(name: str, width: int) -> str`. Return `|`, then the original name, enough ordinary spaces to fill an interior of exactly `width` characters, and a final `|`. Build the padding with a loop. Print nothing inside the function.

**Input:** exactly **two lines**:

- Line 1 contains a name of `1` to `10` English letters or ordinary spaces. At least one character is a letter. Read the line as typed, preserving its spaces.
- Line 2 contains an integer width from `len(name)` to `20`, inclusive.

Input satisfies these requirements; no error output is needed.

**Output:** exactly **one line** containing the returned badge. Its length, including the two bars, is `width + 2`.

Example 1 input:

```text
Ivy
6
```

Example 1 output:

```text
|Ivy   |
```

Example 2 input:

```text
Li Na
5
```

Example 2 output:

```text
|Li Na|
```

Extra tests: a one-letter name, width equal to the name's length, maximum width, and names with leading or trailing spaces. Padding inside the bars is significant; there should be no extra characters after the final bar.

## 24. Submission checklist

- Use the latest official Lab 07 question sheet for **your own section**. There are four assigned problems.
- Keep each problem in its own complete Python 3 program; do not combine them into a menu.
- Use `Lab7_PY_Z.py`, replacing `Y` with the problem number and `Z` with your student ID; for example, `Lab7_P1_<YourStudentID>.py`.
- Follow required function names, parameter order, return values, loop variants, and in-place behavior from the current sheet.
- Use simple, truthful parameter and return annotations. Describe element types, optional missing results, pairs, and side effects in the docstring as needed.
- Include a purpose/parameter/return docstring for every function, including helpers.
- Read exactly the required number of lines and preserve significant spaces and case.
- Handle invalid input, skipped operations, empty results, and normal missing answers according to the specified rule for each one.
- Recheck required loops, one-pass requirements, and prohibited methods. A correct output does not waive these constraints.
- Test minimum/maximum sizes, equality, duplicates, all-negative data, valid zero, and the last possible position.
- For traces, check immediate termination, step order, counts, stopping on the limit, and the treatment of the transition that creates a repeated state.
- For list/dictionary operations, verify that success changes the required object and failure changes nothing.
- Check ranking directions and every tie-breaker; print only the fields required in the report.
- Check spaces, row widths, exact labels, output line counts, and any required empty line. Remove prompts, diagnostic output, menus, and accidental `None` lines.
- Submit the **same final code** to the correct **Codeforces contest and Canvas before the end of your lab session**.
- Follow the current course rules for your Codeforces display name and other submission details.

Review the [platform submission guide](../lab-tutorial.md), [Lab 03 function guide](../lab-03/README.md), [Lab 04 string guide](../lab-04/README.md), [Lab 05 validation and testing guide](../lab-05/README.md), and [Lab 06 list and mutation guide](../lab-06/README.md) as needed. Use the current Lab 07 contract wherever an earlier lab used different requirements.

## Further reading

These official references contain features beyond this lab. Focus on the basic operations discussed above and follow your problem's restrictions.

- [For loops, range, break, and continue](https://docs.python.org/3/tutorial/controlflow.html)
- [Lists and their mutation methods](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
- [Dictionaries and looping techniques](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)
- [String splitting, character classification, and other built-in types](https://docs.python.org/3/library/stdtypes.html)
- [Function definitions and annotations](https://docs.python.org/3/tutorial/controlflow.html#defining-functions)
