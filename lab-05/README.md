# Lab 05: Specification, Testing, Conditions, and Flow Control — Detailed Study Guide

**Course:** COMP1010 - Introduction to Programming

**Week:** 05

**Preparation:** Lecture 08 (Specification and Testing) and Lecture 09 (Conditionals and Control Flow)

This guide supports the current Fall 2026 Lab 05 for **IPRFAL261–IPRFAL264**. It explains shared techniques through independent examples, traces, boundary cases, and optional practice. It does **not** contain the assigned question sheets, PDFs, or complete section solutions. Get the latest sheet for **your own section** from the official course source. If there is a difference, follow that sheet.

Unlike Labs 03–04, Lab 05 asks you to make decisions and handle the invalid inputs specified in each problem. Do not carry over the previous assumption that every input is valid. At the same time, do not invent stricter rules than your current specification requires.

**Submission:** Submit each of the four problems to **both Codeforces and Canvas**, using identical code, **before the end of your lab session**. Lab 04's two-file P4 exception does not apply to the current Lab 05. See [Section 21](#21-submission-checklist).

## What you should be able to do

- Turn a problem statement into an input, validation, decision, and output contract.
- Describe parameters, return values, preconditions, and exceptional cases in docstrings.
- Use simple parameter and return annotations without confusing them with conversion or validation.
- Build Boolean expressions with comparisons, `and`, `or`, and `not`.
- Choose between independent `if` statements, an `if/elif/else` chain, and nested conditions.
- Respect rule priority, equalities, ties, and special cases.
- Raise appropriate exceptions and explain every part of `try/except/else/finally`.
- Distinguish malformed input from a validly formatted but impossible or unsuccessful result.
- Test normal cases, boundaries, invalid input, and combinations of conditions.
- Produce exact text and decimal formatting without prompts or debugging output.

No loops, comprehensions, classes, regular expressions, or advanced return types are needed for the techniques here. Basic access to the pieces returned by `split()` is enough for fixed-size numeric lines. All worked examples below are separate from the assigned programs.

## Navigation

- [1. Read the specification before writing branches](#1-read-the-specification-before-writing-branches)
- [2. Three kinds of outcomes](#2-three-kinds-of-outcomes)
- [3. Functions, annotations, and docstrings](#3-functions-annotations-and-docstrings)
- [4. Read exactly the required input](#4-read-exactly-the-required-input)
- [5. Boolean values and comparisons](#5-boolean-values-and-comparisons)
- [6. Combining conditions safely](#6-combining-conditions-safely)
- [7. Choosing the right branch structure](#7-choosing-the-right-branch-structure)
- [8. Priority, nesting, and modifiers](#8-priority-nesting-and-modifiers)
- [9. Boundaries and special cases](#9-boundaries-and-special-cases)
- [10. Raising and handling exceptions](#10-raising-and-handling-exceptions)
- [11. A complete try-except-else-finally example](#11-a-complete-try-except-else-finally-example)
- [12. Assertions, tests, and debugging](#12-assertions-tests-and-debugging)
- [13. Strict text formats and safe parsing](#13-strict-text-formats-and-safe-parsing)
- [14. Numbers, precision, and exact output](#14-numbers-precision-and-exact-output)
- [15. Reasoning about intervals and states](#15-reasoning-about-intervals-and-states)
- [16. Section-specific testing prompts](#16-section-specific-testing-prompts)
- [17. Building a useful test plan](#17-building-a-useful-test-plan)
- [18. Common questions](#18-common-questions)
- [19. Troubleshooting](#19-troubleshooting)
- [20. Optional extra practice](#20-optional-extra-practice)
- [21. Submission checklist](#21-submission-checklist)

## 1. Read the specification before writing branches

A **specification** describes what a program must do. Your job is to implement that contract, including the cases that the examples do not show.

Before coding, write a small planning sheet:

| Part | Record these details |
| --- | --- |
| Input | Number of lines; values on each line; numeric or text types; whether an empty line is permitted. |
| Representation | Which spaces and letter cases matter; whether a value has a fixed width or separator. |
| Validation | Which failures raise an exception; which numeric ranges are guaranteed; which ranges must be checked. |
| Decisions | Ordered rules, exceptional cases, equality boundaries, and permitted result labels. |
| Function | Required signature, one return value, docstring, and separation from input/output. |
| Output | Number of lines; what each line means; exact case, punctuation, spaces, units, and precision. |

For a toy locker service, a contract might say:

- Input is **one line** containing one integer number of hours.
- Hours must be from `1` to `6`, inclusive; other input is an error.
- A stay of at most `2` hours is `Short`; a longer permitted stay is `Extended`.
- Output is **one line** containing that label, or the specified error message.

Notice the different jobs of the validation range and the classification threshold. An input of `5` is valid and reaches a different branch. An input of `0` is not another category.

### Preconditions and postconditions

A **precondition** is something a caller must ensure before an operation. For example, a divisor must be nonzero before division, or a string must have enough characters before indexing.

A **postcondition** describes what must be true when the operation completes successfully: perhaps a returned number lies within a range, or the result is exactly one permitted label.

Some lab functions accept raw strings so that conversion errors occur **inside the function**, as required by the handout. In that design, numeric validity is checked by the function rather than assumed by its caller. Read the actual contract before deciding where conversion belongs.

## 2. Three kinds of outcomes

Do not treat every negative answer as a Python exception.

| Kind | Meaning | Lab example |
| --- | --- | --- |
| Successful classification | Input is acceptable and a normal decision is available. | A move comparison returns a draw. |
| Semantic negative or impossible state | Input has the required form, but the described object or situation fails the problem's rules. | Positive sides do not form a triangle; a well-shaped board is unreachable. |
| Input error | A required value cannot be interpreted or violates an explicitly checked input requirement. | An integer line contains `hello`; a rectangle has zero width. |

The second kind often returns an ordinary result string. The third kind raises an exception that the main program converts to the required `Error: ...` line.

For example, the current triangle task distinguishes positive lengths `2, 3, 5` from a zero side. The first set produces `Not a Triangle`; a zero side produces `Error: Invalid input`. Both require attention, but they are different paths.

Similarly, in the current game tasks:

- A malformed board row produces an input-error message; an unreachable, correctly shaped board produces `Invalid Board`.
- A non-integer or negative score produces an input-error message; a nonnegative integer score that violates game history produces `Invalid Score`.

Create tests for **both** types of failure. Do not replace all failures with one generic message.

## 3. Functions, annotations, and docstrings

Keep the pattern from [Lab 03](../lab-03/README.md): define a function, call it, store one returned value, and print that value outside the function.

```python
def describe_stay(hours: int) -> str:
    """Describe a permitted locker stay.

    Args:
        hours: A whole number of hours from 1 to 6, inclusive.

    Returns:
        Short for at most two hours, otherwise Extended.

    Raises:
        ValueError: If hours is outside the permitted range.
    """
    if not 1 <= hours <= 6:
        raise ValueError("hours out of range")

    if hours <= 2:
        description = "Short"
    else:
        description = "Extended"
    return description


result = describe_stay(4)
print(result)  # Extended
```

- `hours: int` documents the parameter's expected type.
- `-> str` documents that a successful call returns a string.
- These annotations do **not** convert `"4"` to `4`, enforce a range, or catch an exception.
- This example expects an integer from its caller. The range check alone does not make it a universal runtime type checker.
- `raise` ends the normal path of this call. An unsuccessful call does not also return a classification.

If the function is responsible for parsing text, annotate the **actual** parameter as `str`, convert inside it, and document the possible error. Section 11 demonstrates that pattern.

Use simple annotations such as `str`, `int`, `float`, and `bool`. Return **one scalar or one result string**, not `tuple[int, int]`, multiple comma-separated values, or a list of results. A result string can contain two formatted numbers when the specification calls for them on one line.

Every function, including any helper or inner function, needs a docstring describing its purpose, parameters, and return value. Add `Raises:` where useful. Do not merely write “This function solves Problem 2.” Describe its behavior.

### Returning and printing remain different

`return description` sends data to the caller. `print(description)` displays text and returns `None`. Printing in a helper and then printing its return value can add an unwanted `None` line. Keep normal output in the main program unless your handout explicitly says otherwise.

## 4. Read exactly the required input

### One value on each of two lines

For a contract asking for two separate integer lines:

```python
first_text = input()
second_text = input()
first_value = int(first_text)
second_value = int(second_text)
print(first_value + second_value)
```

Input is **two lines** (`8` on line 1, `11` on line 2). Output is **one line**, `19`. This illustrates reading only; Section 11 adds exception handling. If the function must perform conversion, pass the raw strings to it instead.

### Two numbers on the same line

For a different contract asking for exactly two integers on one line:

```python
parts = input().split()
if len(parts) != 2:
    raise ValueError("expected two values")

first_value = int(parts[0])
second_value = int(parts[1])
print(first_value + second_value)
```

Input is **one line**, `8 11`; output is **one line**, `19`. Repeated whitespace is accepted by `split()`. `8,11` is one token, not two. A line with one or three tokens fails the count check.

This small parsing fragment deliberately raises an exception on invalid input; a complete program handles it at the main level. Check the count **before** accessing `parts[1]`, so that a missing value does not become an accidental `IndexError`.

For a fixed line of three or four numbers, use the same approach with that exact count and individual variables. Do not silently ignore an extra token. Do not add extra `input()` calls just because more values are on the same line.

### Preserve or normalize text deliberately

`input()` removes the line-ending newline, but preserves other characters, including leading and trailing spaces.

| Requirement | Appropriate approach |
| --- | --- |
| Outer spaces may be ignored | Read, then use `.strip()`. |
| Letter case may be ignored | Normalize a copy with `.lower()` or `.upper()`. |
| Spaces are themselves an error | Preserve the original line for that check. |
| Text must be exactly `True` or `False` | Compare the unchanged line to those two spellings. |
| A fixed-width board row has no spaces | Check the original row length and characters; do not repair it with `.strip()`. |

Removing spaces can change an invalid input into a valid one. Normalization is an explicit rule, not a universal improvement.

### Empty line versus missing line

Pressing Enter on an empty line gives `""`. Reading when no line exists raises `EOFError`. An empty address is a normal input to the email checker; a missing address line is an input error. A spaces-only move becomes empty after permitted stripping and is invalid. These are separate cases.

Quoted strings in tables below use **Python notation** to show invisible characters. For example, `" False "` contains two outer spaces. Do not type those quote marks unless the specification requires them.

## 5. Boolean values and comparisons

`True` and `False` are Boolean values. `"True"` and `"False"` are strings. They are not interchangeable.

```python
print(7 > 3)          # True
print(7 == 3)         # False
print(7 != 3)         # True
print(0 <= 4 <= 10)   # True
print("a" == "A")     # False
print(bool("False"))  # True
print(bool(""))       # False
```

`bool("False")` is `True` because the string is nonempty. Therefore, **do not use `bool(input())`** to parse a line that must say `True` or `False`.

A strict Boolean parser can use this independent fragment:

```python
flag_text = "False"
if flag_text != "True" and flag_text != "False":
    raise ValueError("expected True or False")
flag = flag_text == "True"
print(flag)  # False
```

This validates before conversion. Under an exact-token contract, `false`, `TRUE`, `0`, `yes`, `""`, and `" False "` are not accepted alternatives.

### Assignment, equality, and identity

- `value = 5` assigns a value.
- `value == 5` asks whether values are equal.
- `value != 5` asks whether they differ.
- Use `==`, not `is`, for ordinary text or numeric comparisons.

An `if` header needs a comparison or another Boolean expression, a colon, and an indented body. `if value = 5:` is a syntax error. An `else:` does not take a condition; use `elif condition:` when there is another test.

## 6. Combining conditions safely

For Boolean operands:

| A | B | `A and B` | `A or B` | `not A` |
| --- | --- | --- | --- | --- |
| `False` | `False` | `False` | `False` | `True` |
| `False` | `True` | `False` | `True` | `True` |
| `True` | `False` | `False` | `True` | `False` |
| `True` | `True` | `True` | `True` | `False` |

“Both requirements” suggests `and`; “at least one requirement” suggests `or`; “must not happen” suggests `not`. In general Python, `and` and `or` can return an operand rather than a Boolean. Using actual comparisons for each part keeps these lab conditions clear.

### Repeat the comparison

```python
choice = "square"
allowed = choice == "circle" or choice == "square"
print(allowed)  # True
```

`choice == "circle" or "square"` is **wrong**: its second operand is always a nonempty string, so an `if` using it accepts every choice. Likewise, `a == b or c` does not mean “a equals b or a equals c.”

Use parentheses to show groups. Comparisons bind more tightly than `not`, then `and`, then `or`. An expression such as `(a == b and c == d) or (a == d and c == b)` makes the intended pairs visible.

### Invalid-range checks use the correct connector

For a permitted range from `4` through `9`, inclusive:

- Valid: `4 <= value <= 9`.
- Invalid: `value < 4 or value > 9`.
- Also invalid: `not (4 <= value <= 9)`.

`value < 4 and value > 9` can never be true. It leaves invalid values undetected.

### Short-circuit evaluation protects operations

Python evaluates these Boolean operands from left to right. `and` stops at a false operand; `or` stops at a true operand.

```python
text = ""
starts_with_hash = len(text) > 0 and text[0] == "#"
print(starts_with_hash)  # False

divisor = 0
is_large_ratio = divisor != 0 and 12 / divisor > 2
print(is_large_ratio)  # False
```

The index and division are skipped. Reversing their order would attempt the unsafe operation first. A safe slice does not prove an index is safe: `text[:2]` works on `""`, but `text[0]` does not.

### Negating a combined condition

“Not both” differs from “neither”:

- `not (A and B)` is equivalent to `(not A) or (not B)`.
- `not (A or B)` is equivalent to `(not A) and (not B)`.

Test all four Boolean combinations if an expression feels confusing. Do not replace the expression from memory without checking its meaning.

## 7. Choosing the right branch structure

### One optional action: `if`

```python
items = 0
if items == 0:
    print("Empty")  # Empty
```

There is no action when the condition is false. A classifier still needs a returned result on every successful path.

### Exactly one of two paths: `if/else`

```python
items = 3
if items == 0:
    label = "Empty"
else:
    label = "Not Empty"
print(label)  # Not Empty
```

The `else` belongs to the `if` at the same indentation level.

### Exactly one of several paths: `if/elif/else`

```python
items = 8
if items == 0:
    label = "Empty"
elif items < 5:
    label = "Few"
else:
    label = "Several"
print(label)  # Several
```

Only the **first true branch** runs. After the `items == 0` branch fails, the next branch already knows that `items` is not zero. Any required nonnegative-input check belongs before this classification.

### Independent `if` statements can all run

```python
score = 92
if score >= 50:
    print("Passed")       # Passed
if score >= 90:
    print("High score")   # High score
```

This intentionally prints **two lines**, each reporting an independent property. It is wrong for a problem requiring exactly one category. Independent assignments can also overwrite an earlier result without printing an extra line.

### An unreachable branch is a bug

If `score >= 50` appears first in a chain, a later `elif score >= 90` can never handle a high score. All high scores already matched the first condition. Order narrower cases before broader ones when they overlap, or express non-overlapping ranges clearly.

## 8. Priority, nesting, and modifiers

Words such as **first**, **otherwise**, **only if**, **unless**, and **in this order** change the logic.

Consider a toy library reservation rule: a closed desk always gives `Closed`; otherwise a reserved book gives `Reserved`; otherwise the result is `Available`.

```python
desk_closed = True
book_reserved = True
if desk_closed:
    result = "Closed"
elif book_reserved:
    result = "Reserved"
else:
    result = "Available"
print(result)  # Closed
```

Both facts are true, but only the first rule applies. Reversing the branches changes the result.

### Nested conditions describe a decision inside another decision

For a separate toy storage service, frozen accounts cannot access storage. Other accounts get a quota based on membership:

```python
account_frozen = False
premium_member = True
if account_frozen:
    result = "Blocked"
else:
    if premium_member:
        quota = 12
    else:
        quota = 5
    result = f"Quota: {quota}"
print(result)  # Quota: 12
```

Indentation keeps the quota logic inside the eligible-account path. A membership benefit must not accidentally override `Blocked`.

### Base decision versus later adjustment

When a rule modifies an amount, write down **which base cases it modifies**. Do not assume it applies to every numeric result.

For example, a fictional shop could double ordinary reward points on a promotion day while leaving a fixed welcome gift unchanged. Calculate the base reward, identify its type, then apply only the permitted adjustment. Applying one global multiplier at the end would also double the gift incorrectly.

This matters in the lab's penalty and watering tasks: a replacement amount need not receive the same modifier as a base amount, and a modifier must not alter a higher-priority skip result.

## 9. Boundaries and special cases

### Inclusive and exclusive endpoints

For a toy level rule, `Low` means below `8`, `Middle` means from `8` up to but not including `15`, and `High` means `15` or above.

| Test value | Expected label | Purpose |
| --- | --- | --- |
| `7` | `Low` | Just below the first boundary. |
| `8` | `Middle` | Exactly on an included boundary. |
| `9` | `Middle` | Just above it. |
| `14` | `Middle` | Just below the next boundary. |
| `15` | `High` | Exactly on the next boundary. |
| `16` | `High` | Just above it. |

For integer thresholds use `boundary - 1`, `boundary`, and `boundary + 1`. For real quantities, choose a small meaningful step such as `0.01`, within the allowed input domain. Test the **unrounded** value when the decision is defined on that value.

### Zero is a case, not automatically an error

Zero may be valid for coordinates, coefficients, scores, remaining charge, debt, or moisture. It may be invalid for a required positive side, income, weight, or dimension. Decide from the variable's contract, not from a general preference for positive numbers.

Before a formula, ask whether its denominator could be zero and whether any square root could receive a negative argument. A degenerate case can require a different mathematical question, rather than a forced application of the ordinary formula.

### Equality and ties

“Exactly two equal,” “all equal,” and “one strictly largest” are different conditions. Test all-equal data separately from a two-way tie. If the first two values happen to tie **below** a third, there is still a unique largest value.

For a toy three-sensor comparison:

| Readings | Property to recognize |
| --- | --- |
| `6, 6, 6` | All equal. |
| `9, 9, 2` | Two tie for the largest. |
| `2, 2, 9` | Two tie below a unique largest. |
| `1, 4, 8` | All distinct. |

Do not assume “some pair is equal” means “there is no unique maximum.”

## 10. Raising and handling exceptions

`int("abc")` raises `ValueError`. You can also use `raise ValueError(...)` when conversion succeeds but the value violates an explicitly checked requirement.

```python
try:
    quantity = int("0")
    if quantity <= 0:
        raise ValueError("quantity must be positive")
except ValueError:
    print("Error: Invalid quantity")  # Error: Invalid quantity
```

The developer-facing reason in `ValueError("...")` does not have to be the public output. Print the **exact error wording from the handout**, rather than Python's varying conversion message.

### An exception can travel back to the caller

If a function raises an exception and does not handle it, the caller can catch it around the function call. You do not need to print an error inside the helper as well. That could produce duplicate messages.

Catch the expected error types. Two handlers can give the same lab message for conversion/range errors and missing lines:

```python
try:
    number = int("bad")
except ValueError:
    print("Error: Invalid input")  # Error: Invalid input
except EOFError:
    print("Error: Invalid input")
else:
    print(number)
```

`except ValueError` does not catch `EOFError`, `NameError`, or every other exception. A bare `except:` or an unnecessarily broad `except Exception:` can hide programming mistakes. A wrong variable name should be fixed, not disguised as a user's input error.

Do not catch `ZeroDivisionError` as a substitute for reasoning about a valid zero-coefficient case. When zero is allowed and changes the problem, branch to the appropriate case before dividing.

## 11. A complete try-except-else-finally example

This independent program computes a toy printing allowance. It is **not** a solution to an assigned Lab 05 problem.

**Input:** exactly **one line**, containing one integer number of tokens, from `0` to `9` inclusive.

**Output:** exactly **one line**:

- On valid input, `Pages: N`, where `N` is three times the token count.
- Otherwise, `Error: Invalid tokens`.

```python
def printing_allowance(token_text: str) -> str:
    """Build an allowance message from a raw token-count line.

    Args:
        token_text: Text to interpret as an integer from 0 to 9.

    Returns:
        A Pages message containing the available page count.

    Raises:
        ValueError: If conversion fails or the count is out of range.
    """
    tokens = int(token_text)
    if not 0 <= tokens <= 9:
        raise ValueError("token count out of range")
    pages = tokens * 3
    message = f"Pages: {pages}"
    return message


try:
    raw_tokens = input()
    result = printing_allowance(raw_tokens)
except ValueError:
    print("Error: Invalid tokens")
except EOFError:
    print("Error: Invalid tokens")
else:
    print(result)
finally:
    pass  # No resources to clean up and no extra output is required.
```

| Input in Python notation | Exactly one output line |
| --- | --- |
| `"4"` | `Pages: 12` |
| `"0"` | `Pages: 0` |
| `"9"` | `Pages: 27` |
| `"10"` | `Error: Invalid tokens` |
| `"four"` | `Error: Invalid tokens` |
| `"4.0"` | `Error: Invalid tokens` |
| `""` | `Error: Invalid tokens` |

A missing line also prints the error, through the `EOFError` handler. Numeric conversion with `int()` accepts outer whitespace and a sign; the example does not impose a stricter text grammar on integer tokens.

### What each clause means

| Clause | When it executes | Responsibility here |
| --- | --- | --- |
| `try` | First. | Read the line and attempt the function call. |
| Matching `except` | If an exception of that type occurs in `try`. | Print the one error result. |
| `else` | If `try` completes without raising an exception. | Print the successful return value. |
| `finally` | On normal completion or while an exception leaves this construct. | Cleanup if needed; here, deliberately do nothing. |

On an ordinary success the path is `try`, `else`, `finally`. On a handled conversion failure it is `try`, the matching `except`, `finally`. The remainder of `try` after the failure is skipped. Only a matching handler runs.

An exception raised **inside `else`** is not handled by these sibling `except` clauses. An unhandled exception still passes through `finally` before propagating. Normal Python control flow also runs `finally` before a pending function return; an abruptly terminated process cannot guarantee cleanup.

### Why no `Done` in `finally`?

Lab output is still exactly one line. A `print("Done")` in `finally` would add an incorrect second line on both success and error. `pass` is a statement meaning “do nothing”; it supplies a legal body for an intentionally empty block.

In a program without cleanup, `finally` is not normally necessary. It appears here to demonstrate the complete Lecture 08 structure. If you include it for the lab, keep it silent unless the official output explicitly requires otherwise. Do not put `return` in `finally`: it can override a result or suppress an error that should reach the caller.

## 12. Assertions, tests, and debugging

An **assertion** checks a condition that you expect to be true while developing a program. If it is false, Python raises `AssertionError`.

```python
def add_service_fee(base: int) -> int:
    """Add a fixed fee to an already validated base amount.

    Args:
        base: A nonnegative integer amount supplied by the caller.

    Returns:
        The base amount plus a fee of two units.
    """
    assert base >= 0, "caller must supply a nonnegative amount"
    total = base + 2
    assert total >= base, "fee should not decrease the total"
    return total


assert add_service_fee(0) == 2
assert add_service_fee(7) == 9
print("Local tests passed")  # Local tests passed
```

The first assertion documents a precondition of this toy function. The second checks a postcondition. The calls at the bottom compare actual and expected results. They belong in local test code, not in a submitted program's output path.

### Assertions do not replace input validation

Python can disable assertions when run with optimization (`python -O`). Use explicit `if ...: raise ValueError(...)` for checks that **must** reject user input. Do not use `assert int(input()) > 0`: when assertions are disabled, even the input call disappears.

An assertion also does not prove that a function is correct for all other inputs. Passing two tests says only that those checks passed. Choose cases that challenge the rules.

### Three useful error categories

| Category | Example | What to do |
| --- | --- | --- |
| Syntax/indentation error | A missing colon or misaligned `elif`. | Correct the structure before execution can begin. |
| Runtime exception | Conversion fails, an index is missing, or division uses zero. | Distinguish expected bad input from a bug in your code. |
| Logic error | The program runs but uses `<` where `<=` is required. | Trace the branch and compare with a known expected result. |

Read the last line of a traceback for the exception type, then find the relevant line in your own code. The place where Python notices a syntax error can be just after the actual mistake.

### PyCharm debugging routine

1. Save a failing test and its expected output.
2. Put a breakpoint before the first decision it could reach.
3. Run in Debug mode and step through the conditions.
4. Inspect the raw text, converted values, Boolean flags, and current result.
5. Note the first place where the executed path differs from the specification.
6. Fix that cause, rerun the failing test, then rerun earlier passing cases.

Temporary `print(repr(raw_text))` calls can reveal invisible spaces. Remove all diagnostic prints before submitting. Debug the earliest wrong intermediate value instead of repeatedly changing the final print statement.

## 13. Strict text formats and safe parsing

### Numeric meaning does not establish a text format

`int("07")` and `int("7")` are both `7`. If a specification requires **two characters**, conversion alone cannot enforce it. Check the original shape first.

For an unrelated toy identifier made of two uppercase letters, a hyphen, and two ASCII digits, the reading order would be:

1. Apply only the permitted normalization.
2. Check the total length.
3. Check the separator at its required position.
4. Check each part's allowed character set.
5. Only then convert the numeric part if arithmetic is needed.

No regex or loop is necessary for a fixed number of positions. A short-circuit expression or a sequence of explicit checks can guard each access.

### ASCII digits versus broader digit tests

`isdigit()` recognizes more characters than just ASCII `0` through `9`. For an exact ASCII format, an individual character can be checked with `"0" <= character <= "9"`. Verify that a slice has the expected length before checking its positions. Likewise, `int()` accepting a text form does not mean that form meets an exact-width contract.

For hexadecimal text, converting with `int(piece, 16)` is useful **after** checking the required shape and permitted characters. Conversion alone can accept forms such as a leading sign that a fixed-width color code does not allow.

```python
piece = "2f"
value = int(piece, 16)
print(value)  # 47
```

This shows a base conversion only. It does not validate an entire assigned color code.

### Counting and searching have different jobs

`find()` returns a position or `-1`. `count()` reports how many occurrences exist. If exactly one separator is required, finding one does not prove there is not another.

```python
text = "left:right:extra"
print(text.find(":"))   # 4
print(text.count(":"))  # 2
```

Do not use a failed `find()` result as an index: `-1` refers to the last character in Python. Confirm the needed structure first, then slice around the known separator.

### A suffix is not an arbitrary substring

```python
name = "notes.txt.backup"
print(".txt" in name)        # True
print(name.endswith(".txt"))  # False
```

When a rule says “ends with,” use the ending of the relevant part. Do not replace it with a containment check. Do not add real-world validation rules that the simplified exercise never requested.

## 14. Numbers, precision, and exact output

### Convert the requested type

`int("6.0")` fails, even though a person may think the value is a whole number. `int(float("6.8"))` truncates to `6`; it should not be used to accept an input that must be an integer. A numeric value such as a weight can accept a decimal representation when its contract permits a real number.

For the tasks requiring a **finite** number, merely calling `float()` is not enough. `float("nan")` and `float("inf")` succeed, but those are not finite values. A narrow validation pattern is:

```python
import math

value = float("6.25")
if not math.isfinite(value):
    raise ValueError("a finite number is required")
print(value)  # 6.25
```

Check the required positive/nonnegative range as well. Avoid inventing an upper bound that your handout does not state.

### Round for display after making the decision

```python
measurement = 3.96
print(f"{measurement:.1f}")  # 4.0
print(measurement > 3.98)    # False
```

The displayed value can round across a boundary. Classify using the original computed value unless the specification explicitly says otherwise, then format the final message.

```python
amount = 6
minute = 4
print(f"{amount:.2f}")  # 6.00
print(f"{minute:02d}")  # 04
```

`.2f` gives two digits **after the decimal point**. `02d` gives an integer a minimum width of two, padded with zero. These serve different purposes. `round(value, 2)` alone does not guarantee that printing the result keeps two trailing decimals.

### Negative zero is a display issue

A very small negative value, or floating-point negative zero itself, can format as `-0.00`. If the required display is `0.00`, examine the final formatted text:

```python
value = -0.0002
display = f"{value:.2f}"
if display == "-0.00":
    display = "0.00"
print(display)  # 0.00
```

This changes the displayed text only. Do not round the underlying value early or change the equation's discriminant to make it fit an expected branch. Integer coefficients allow an exact integer discriminant; introducing an arbitrary tolerance can change which mathematical case applies.

When two numbers must appear in increasing order, compare their **numeric values** before formatting. String ordering and the order in which formulas produce values are not reliable numeric ordering rules. Store numbers in separate variables; a temporary variable is sufficient for a swap if needed.

### Output is part of correctness

For the current Lab 05, each problem prints **one result line**, whether successful or an error. In particular:

- Do not print `Enter a number:` or echo input values.
- Do not print Python quotes, a list, or a tuple around a result.
- Preserve the specified label's capitalization, colon, parentheses, spaces, and units.
- Do not insert thousands separators into an integer amount unless requested.
- Do not print a traceback for an input error that the problem asks you to handle.
- Keep tests, `repr()` diagnostics, and cleanup messages out of submitted output.

## 15. Reasoning about intervals and states

These are thinking tools, not complete algorithms for the assigned problems.

### One-dimensional intervals

Take a reference segment covering positions `2` through `7`. Compare another segment:

| Other segment | What they share |
| --- | --- |
| `8` through `10` | Nothing. |
| `7` through `10` | One endpoint. |
| `5` through `10` | A segment of positive length. |
| `3` through `6` | The entire smaller segment. |
| `2` through `7` | Identical segments. |

An endpoint contact differs from positive-length intersection. For two-dimensional shapes, reason about **both** coordinate directions. Boundary contact is not necessarily positive-area overlap. Containment can allow shared edges if the handout says so, and identical shapes may require a special priority decision.

### A state can look plausible but be unreachable

Suppose a toy counter increases by one per move and **stops permanently** as soon as it reaches `4`. State `3` is in progress, `4` is finished, and `5` is impossible. Merely asking whether the current count is at least `4` would incorrectly accept `5` as another finished state.

Game-state exercises similarly require more than a numerical comparison. Ask:

- Does the input have the correct shape and character set?
- Could the turn counts or scores occur under the rules?
- Could an earlier state already have ended the game?
- Do all winner-related facts agree?
- If no valid terminal result applies, is play still possible?

Separate malformed input from an unreachable state. Check impossible-state rules before printing an apparent winner. A full board is not automatically a draw if somebody has already won.

### Symmetry is a useful check

Swapping two rectangles should not change their relationship label. Reordering triangle sides should not change the classification. Swapping two players should exchange player-specific winner labels while preserving a draw or invalid state. Not every task is symmetric: donor/recipient order and the order of priority rules can matter.

## 16. Common questions

### Should I reject every value outside a displayed expected range?

Follow the contract. Some ranges describe guaranteed test inputs; other rules explicitly require a check and an error. A numeric game score can also be deliberately outside a game's reachable range and still require an ordinary `Invalid Score` result. Do not move a semantic decision into generic input validation.

### Can I use an `else` as the error case?

Only if it truly means every remaining input is invalid. After validating all allowed moves, an `else` can mean a legitimate remaining game result. Before validation, that same `else` could accidentally accept an unknown word. Establish what is known at that point.

### Should I always strip text and make it lowercase?

No. Move/type tasks permit normalization; exact Boolean lines, board rows, and the email space check have different requirements. Read Section 4's table for the distinction. Keep original text when an original character matters.

### Do I need to use loops to inspect a fixed board or identifier?

No. The assigned fixed-size tasks can use explicit character checks, indexing, `.count()`, and Boolean expressions. Validate length first. Do not introduce a loop, comprehension, or advanced parser merely to shorten a few fixed checks.

### Is a terminal game state just a winning comparison?

Not necessarily. The state must also be reachable under turn order, stopping, and cap rules. A valid-looking winner can coexist with a fact that makes the whole state impossible. Respect the handout's priority.

### Can I print a different helpful error explanation?

Keep the exact required `Error: ...` or result label. A detailed explanation can be a comment, docstring, or developer-facing exception reason, but it must not change the specified output.

### Does a failed `assert` mean the user's input is invalid?

Not by itself. It means a condition your test expected to hold did not hold. The bug could be in the program, the test's expected answer, or a violated caller precondition. Use explicit validation for input errors.

### Do I need `try/except` inside every function?

No. A helper can raise an appropriate exception; the main program catches it around input and the function call. That keeps one place responsible for printing an error. Do not return a success result after catching a failure unless the contract defines such recovery.

### Should I put all four problems into one file with a menu?

No. Keep a separate program for each problem. A menu would add input and output that the judge does not expect.

## 17. Troubleshooting

| Symptom | Likely cause | Check |
| --- | --- | --- |
| `SyntaxError` near `if` or `else` | Missing colon, `=` instead of `==`, or a condition after `else`. | Re-read the header and preceding line. |
| `IndentationError` or unexpected branch | Mixed indentation or an `else` aligned with the wrong `if`. | Use consistent four-space indentation and inspect the whole block. |
| Every choice seems valid | Used `choice == "a" or "b"`. | Repeat the complete comparison on both sides. |
| `False` behaves like true | Used `bool()` on nonempty text. | Validate exact tokens, then compare to `"True"`. |
| Error appears only at equality | Mixed `<` and `<=`, or wrong branch priority. | Test below/at/above the boundary. |
| `IndexError` on malformed input | Accessed a character/token before checking length. | Validate count first or put safe guards first. |
| `ZeroDivisionError` on an allowed zero | Applied a general formula before handling its special case. | Branch before dividing. |
| A bad input crashes without the specified message | Conversion/input call lies outside the protected path, or wrong exception type is caught. | Check function/main responsibilities and the `try` boundary. |
| A coding mistake becomes an input-error message | An overly broad handler hides it. | Catch expected errors and inspect the original traceback locally. |
| Two or more result lines | Independent branches, helper prints, or a message in `finally`. | Assign/return once and print only the required result. |
| Unexpected `None` or unassigned variable | A successful path never returns/assigns the result. | Trace every branch and keep success printing in `else`. |
| Text differs despite correct arithmetic | Wrong label, normalization, decimal precision, or number order. | Compare the exact final line, including spaces and signs. |
| Valid impossible-state example is accepted | Checked only current score/winning appearance. | Apply reachability and stop-at-win rules first. |
| Local sample passes but hidden cases fail | Only ordinary examples were tested. | Add equality, ties, multiple simultaneous conditions, and invalid input. |

## 18. Optional extra practice

These are **new, optional exercises**, not assigned section problems or required submissions. No full solutions are supplied. Use simple annotations, a docstring for every function, one returned result, and a main program with appropriate error handling. No loops are needed. Plan your tests before implementing.

### A. Reading-room capacity

Define `room_status(count_text: str) -> str` to validate and classify a room's current occupancy.

**Input:** exactly **one line**, containing one integer number of people from `0` through `12` inclusive. Non-integer or out-of-range input is invalid.

**Rules:** `0` means `Empty`; `1` through `11` means `Open`; `12` means `Full`.

**Output:** exactly **one line**:

- The room-status label for valid input.
- `Error: Invalid count` for invalid or missing input.

Example 1 input:

```text
0
```

Example 1 output:

```text
Empty
```

Example 2 input:

```text
12
```

Example 2 output:

```text
Full
```

Extra tests: `1`, `11`, `-1`, `13`, `2.5`, and an empty line. Explain why zero is valid here.

### B. Badge access

Define `badge_access(level_text: str, active_text: str) -> str`.

**Input:** exactly **two lines**:

- Line 1: an integer access level from `0` through `3` inclusive.
- Line 2: exactly `True` or `False`, without surrounding spaces, saying whether the badge is active.

**Rules:** an inactive badge gives `Denied` regardless of level. An active badge with level at least `2` gives `Allowed`; any other active badge gives `Ask Staff`.

**Output:** exactly **one line**:

- The first applicable decision above.
- `Error: Invalid badge` if either input is invalid or missing.

Example 1 input:

```text
3
False
```

Example 1 output:

```text
Denied
```

Example 2 input:

```text
1
True
```

Example 2 output:

```text
Ask Staff
```

Extra tests: levels `0`, `2`, and `3` with each valid flag; `true`; and a flag with outer spaces. Explain which rule wins when an inactive badge has a high level.

### C. Shelf-width comparison

Define `compare_widths(width_line: str) -> str`.

**Input:** exactly **one line**, containing exactly two space-separated integers, each from `1` through `50` inclusive. They describe the widths of shelf A and shelf B. Repeated whitespace is permitted; commas are not separators.

**Rules:** report whether the widths are equal; otherwise report which shelf is wider and by how many units.

**Output:** exactly **one line**:

- `Same Width` if the widths match.
- `A Wider: D` or `B Wider: D` otherwise, where `D` is the positive integer difference.
- `Error: Invalid widths` for invalid or missing input.

Example 1 input:

```text
7 12
```

Example 1 output:

```text
B Wider: 5
```

Example 2 input:

```text
50 50
```

Example 2 output:

```text
Same Width
```

Extra tests: `1 50`, `50 1`, `7`, `7 12 4`, `7,12`, `0 2`, and repeated spaces. Check token count before indexing.

### D. Message-label check

Define `check_message_label(text: str) -> str`.

**Input:** exactly **one line** containing at most `40` characters. The line may be empty. Preserve all characters, including outer spaces.

**Rules:** apply these checks in order:

1. An empty string gives `Invalid: empty`.
2. Any literal space gives `Invalid: spaces`.
3. A string not starting with `!` gives `Invalid: prefix`.
4. Otherwise, return `Accepted`.

**Output:** exactly **one line** containing that first result. If the input line is missing, print `Error: Missing label` instead. A length at most `40` is a guaranteed input constraint, not an extra validation rule.

Example 1 input:

```text
!ready
```

Example 1 output:

```text
Accepted
```

Example 2 input:

```text
ready now
```

Example 2 output:

```text
Invalid: spaces
```

Extra tests: an empty line, a spaces-only line, `!`, `ready`, and ` !ready`. Explain why a string failing both the space and prefix checks reports only the space failure.

## 21. Submission checklist

- Use the latest Fall 2026 question sheet for **your own section**.
- Keep all four problems as separate Python 3 programs; do not add a menu or an input-retry loop.
- Use `Lab5_PY_Z.py`, where `Y` is the problem number and `Z` is your student ID. For example: `Lab5_P1_<YourStudentID>.py`, replacing the placeholder with your actual ID.
- Match the required function behavior and any required names or parameter order. Include simple type annotations and a purpose/parameter/return docstring for every function, including helpers.
- Read exactly the required lines and values; preserve text where the specification requires it.
- Check explicitly required invalid inputs, and keep semantic-negative results distinct from input-error messages.
- Respect rule order, threshold equality, tie handling, special cases, and state reachability.
- Understand how `try`, `except`, `else`, and `finally` affect the output path. Do not print cleanup/status messages that the output does not request.
- Return one value from each function as required; print only the one specified result line at the main level.
- Test all examples plus normal, boundary, invalid, and overlapping-condition cases. Keep local tests and diagnostics out of submitted output.
- Submit the **same final code** to the assigned **Codeforces contest and Canvas before the end of the lab session**. Under the current instructions, a problem missing from either platform is not graded.
- Follow current course rules for your Codeforces display name and submission details; this tutorial does not replace course grading policy.

See the [Codeforces and Canvas guide](../lab-tutorial.md) for platform steps, [Lab 03](../lab-03/README.md) for functions and annotations, and [Lab 04](../lab-04/README.md) for strings. Follow **Lab 05's current contract** when a previous lab's assumptions or submission exceptions differ.

## Further reading

The official Python references below include features beyond this lab. Focus on the sections relevant to the techniques above.

- [`if`, `elif`, and `else`](https://docs.python.org/3.12/tutorial/controlflow.html#if-statements)
- [Truth values, Boolean operators, and comparisons](https://docs.python.org/3.12/library/stdtypes.html#truth-value-testing)
- [Raising and handling exceptions, `else`, and cleanup with `finally`](https://docs.python.org/3.12/tutorial/errors.html)
- [The `assert` statement and optimized execution](https://docs.python.org/3.12/reference/simple_stmts.html#the-assert-statement)
