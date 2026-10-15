# Lab 04: Modules and Strings — Detailed Study Guide

**Course:** COMP1010 - Introduction to Programming

**Week:** 04

**Preparation:** Lecture 06 (Creating and Importing Modules) and Lecture 07 (Strings)

This guide supports the current Fall 2026 Lab 04 for **IPRFAL261–IPRFAL264**. It teaches shared techniques with independent examples, worked traces, valid extreme cases, and optional practice. It does **not** contain the assigned question sheets, PDFs, or complete solutions to the section problems. Get the current sheet for **your own section** from the official course source. If this guide and your sheet differ, follow the sheet.

Assume all inputs satisfy the stated constraints. The current assigned tasks do not need `if`/`else`, loops, `try`/`except`, or `assert`. An extreme case means a **valid boundary case**, not invalid input your program must reject. Short strings and spaces-only strings deserve attention, but they are not valid for every problem.

**Submission:** Problems 1–3 go to **both Codeforces and Canvas**, using identical code. Problem 4 is a **two-file module exercise for Canvas only**. Submit before **the end of your lab session**. See [Section 19](#19-submission-checklist) for the exact module filenames.

## What you should be able to do

- Read a whole line without deleting meaningful spaces.
- Distinguish characters, words, positions, and empty results.
- Trace positive, negative, and stepped slices, including short-string results.
- Choose the requested case conversion and whitespace behavior.
- Count characters, literal spaces, occurrences, and whitespace-separated tokens.
- Keep digit identifiers as text so that zeros survive.
- Read a module constant inside a function without changing it.
- Use module imports, selected-name imports, and aliases correctly.
- Write functions with simple annotations, docstrings, and one return value.
- Separate a reusable module from the driver that reads input and prints output.
- Test your own section's boundaries without copying another section's format.

## Navigation

- [1. The program contract](#1-the-program-contract)
- [2. Whole-line input and whitespace](#2-whole-line-input-and-whitespace)
- [3. String values and immutability](#3-string-values-and-immutability)
- [4. Character indexing](#4-character-indexing)
- [5. Slicing and negative steps](#5-slicing-and-negative-steps)
- [6. Splitting, words, and joining](#6-splitting-words-and-joining)
- [7. Counts and searches](#7-counts-and-searches)
- [8. Case conversion and capwords](#8-case-conversion-and-capwords)
- [9. Trimming, replacement, and order](#9-trimming-replacement-and-order)
- [10. Digits stored as text](#10-digits-stored-as-text)
- [11. Functions, annotations, and docstrings](#11-functions-annotations-and-docstrings)
- [12. Local, global, and enclosing scope](#12-local-global-and-enclosing-scope)
- [13. Import styles and naming](#13-import-styles-and-naming)
- [14. An independent two-file example](#14-an-independent-two-file-example)
- [15. Exact output and empty lines](#15-exact-output-and-empty-lines)
- [16. Valid extreme cases for every section](#16-valid-extreme-cases-for-every-section)
- [17. Questions and debugging](#17-questions-and-debugging)
- [18. Optional extra practice](#18-optional-extra-practice)
- [19. Submission checklist](#19-submission-checklist)

## 1. The program contract

Before coding, make a checklist from your own handout:

| Detail | Questions to answer |
| --- | --- |
| Input | How many lines exist? Is the first line arbitrary text, two words, or a formatted identifier? Which lines need numeric conversion? |
| Constraints | Are outer spaces allowed? Can the line contain only spaces? Is punctuation allowed? What is the maximum **whole-line** length? |
| Structure | Is a particular function, constant, import style, or second file required? What must the function return? |
| Transformations | Does each view use the original text or a deliberately cleaned value? In what order do operations happen? |
| Output | How many lines? Which labels, separators, spaces, punctuation, and case? Can a result be empty? |
| Submission | Single-file problems for both platforms, or the two-file Canvas-only exercise? |

Two problems can use similar methods but require different results. Reversing the whole text changes word order. Reversing each word separately can preserve word order. Those are different contracts.

A sample illustrates the specification; it does not replace the constraints. A one-character input can be valid even when every sample is longer. An empty line is not automatically valid just because Python can process an empty string.

## 2. Whole-line input and whitespace

### One input call reads one line

```python
text = input()
```

`input()` keeps the line's spaces and removes its line-ending newline. A line containing two spaces, `Hi`, and another space becomes `"  Hi "`, not `"Hi"`.

Do not add `.strip()` to every input statement as a habit. Outer spaces can affect length, counts, case conversion, reversal, and slicing. Keep the original input and compute separate transformed values when needed.

For a text line followed by two separate integer lines, the reading pattern is:

```python
text = input()
position = int(input())
amount = int(input())
```

This demonstrates the layout only, not an assigned calculation. Do not split the first text line to obtain integers that belong to the next lines. Conversely, if the question specifies only one line, use only one `input()` call.

### Quotes and invisible characters

Quoted values in this guide's tables are **Python notation**, used to make spaces visible. Do not type the surrounding quotes into ordinary lab input unless the question makes them part of the data.

| Python notation | Characters contained | Length |
| --- | --- | --- |
| `""` | None | `0` |
| `" "` | One space | `1` |
| `"   "` | Three spaces | `3` |
| `" A "` | A space, `A`, a space | `3` |
| `"A B"` | Two letters and one space | `3` |
| `"A\tB"` | Two letters and a tab | `3` |
| `"A\nB"` | Two letters and a newline | `3` |

The tab/newline rows explain Python strings, not extra permitted lab input characters. Typing a backslash and `t` in a terminal is different from entering an actual tab: `input()` does not interpret typed escape sequences as Python source code.

Use `repr()` in local experiments to inspect spaces:

```python
text = "  Hi "
print(repr(text))          # '  Hi '
print(len(text))           # 5
print(repr(text.strip()))  # 'Hi'
```

`repr()` is a debugging representation containing quotes/escapes. Print the actual result in your submission unless that representation is explicitly requested. Remove diagnostic prints.

## 3. String values and immutability

A string is a sequence of characters. Spaces, digits, and punctuation count too. For the English-letter/digit inputs in this lab, count characters, not only letters or words.

```python
combined = "sun" + "set"
repeated = "?" * 4
print(combined)     # sunset
print(repeated)     # ????
print(len("go! "))  # 4
```

`+` concatenates without inserting a space. Add `" "` when needed. `*` repeats a string an integer number of times; repeating zero times produces `""`.

Python cannot concatenate a string and an integer directly. Use an f-string or an explicit `str(...)` conversion:

```python
count = 6
message = f"Box: {count} items"
print(message)  # Box: 6 items
```

### Methods return values

```python
word = "owl"
word.upper()       # Its returned value is discarded.
print(word)        # owl
upper_word = word.upper()
print(upper_word)  # OWL
print(word)        # owl
```

Strings are immutable: you cannot edit an existing string's characters in place. Assigning a newly constructed string to the same variable is allowed:

```python
word = "cat"
# word[0] = "b"  # This would raise TypeError.
word = "b" + word[1:]
print(word)      # bat
```

### Equality, containment, and identity

```python
text = "Tea time"
print(text == "Tea time")  # True
print("Tea" in text)       # True
print("tea" in text)       # False
print("" in text)          # True
```

`==` compares values. `in` checks for a substring, with case sensitivity. `is` checks whether two references point to the same object. Use `==`, not `is`, to compare text. Whether Python happens to reuse a string object is not a reliable rule for solving a problem.

## 4. Character indexing

For `text = "ABCDEF"`:

| Character | `A` | `B` | `C` | `D` | `E` | `F` |
| --- | --- | --- | --- | --- | --- | --- |
| Nonnegative index | `0` | `1` | `2` | `3` | `4` | `5` |
| Negative index | `-6` | `-5` | `-4` | `-3` | `-2` | `-1` |

```python
text = "ABCDEF"
print(text[0])   # A
print(text[2])   # C
print(text[-1])  # F
print(text[-6])  # A
```

An index selects **one existing character**. For length `n`, valid nonnegative indices are `0` through `n - 1`; negative indices are `-n` through `-1`. Index `n` is past the end. `-0` equals `0`, not the last position.

Each word in the two-word name tasks has at least one letter. That guarantees index `0` in each word. It does not guarantee index `1`: a one-letter name is valid.

Compare a single index and a slice on short input:

```python
text = "Q"
print(text[0])    # Q
print(text[:2])   # Q
print(text[-2:])  # Q
# print(text[1])  # This would raise IndexError.
```

Replacing a safe slice with several individual indices can cause an error on valid short input. Use the operation the question requests.

## 5. Slicing and negative steps

The general form is `text[start:stop:step]`. Start is included; stop is excluded. Step controls direction and spacing between selected positions. An omitted step is `1`.

For a positive step, omitted bounds cover the string left to right. Ordinary out-of-range slice bounds are clipped. A slice can return fewer characters or an empty string without raising `IndexError`.

### Positive steps

For `text = "ABCDEF"`:

| Expression | Selected positions | Result |
| --- | --- | --- |
| `text[:]` | All | `"ABCDEF"` |
| `text[1:4]` | `1, 2, 3` | `"BCD"` |
| `text[:2]` | Up to the first two | `"AB"` |
| `text[-2:]` | Up to the last two | `"EF"` |
| `text[::2]` | `0, 2, 4` | `"ACE"` |
| `text[1::2]` | `1, 3, 5` | `"BDF"` |
| `text[2:99]` | From position `2` to the available end | `"CDEF"` |
| `text[99:]` | None available | `""` |
| `text[4:2]` | None in the positive direction | `""` |

The stop is a **position**, not a character count. If a separate exercise specifies a starting position and a count, identify the end boundary first. “Take three characters” does not mean “stop at index three.”

### Negative steps

With a negative step, an omitted start begins at the last character. An omitted stop allows selection to include the first character.

For the same `text = "ABCDEF"`:

| Expression | Selected positions | Result |
| --- | --- | --- |
| `text[::-1]` | `5, 4, 3, 2, 1, 0` | `"FEDCBA"` |
| `text[::-2]` | `5, 3, 1` | `"FDB"` |
| `text[4:1:-1]` | `4, 3, 2` | `"EDC"` |
| `text[4:1:-2]` | `4, 2` | `"EC"` |
| `text[3::-2]` | `3, 1` | `"DB"` |
| `text[:0:-1]` | `5, 4, 3, 2, 1` | `"FEDCB"` |
| `text[:-1:-1]` | None before the excluded last position | `""` |

An explicit `-1` stop means the last character's position. It is **not** the same as omitting the stop in a negative-step slice. An omitted stop is usually clearest for reversing the whole string.

Step zero is not allowed: `text[::0]` would raise `ValueError`. This is a language-rule explanation, not a reason to add exception handling to the lab.

### Order matters, and one sample can hide a mistake

| Original | `text[::-2]` | `text[::2][::-1]` |
| --- | --- | --- |
| `"ABCDEF"` | `"FDB"` | `"ECA"` |
| `"ABCDE"` | `"ECA"` | `"ECA"` |

Selecting and then reversing happens to agree with backward alternating selection for this odd-length example, but not the even-length example. Test **both lengths** and trace the original indices.

### Length one and empty results

```python
text = "Z"
print(repr(text[::2]))   # 'Z'
print(repr(text[1::2]))  # ''
print(repr(text[::-2]))  # 'Z'
print(repr(text[-3:]))   # 'Z'
```

A non-empty input can produce an empty view. Print that empty result on its required line; do not replace it with a message. Reversal preserves the characters and their case. It does not strip spaces or remove punctuation.

## 6. Splitting, words, and joining

### `split()` and `split(" ")` differ

```python
text = "  fox   den  "
print(text.split())     # ['fox', 'den']
print(text.split(" "))  # ['', '', 'fox', '', '', 'den', '', '']
```

Without an argument, `split()` treats runs of whitespace as separators and omits empty pieces at the ends. With the explicit single-space separator, each literal space separates pieces, including empty pieces between repeated separators and at the ends.

| Expression | Result |
| --- | --- |
| `"fox den".split()` | `['fox', 'den']` |
| `"  fox   den  ".split()` | `['fox', 'den']` |
| `"   ".split()` | `[]` |
| `"".split()` | `[]` |
| `"   ".split(" ")` | `['', '', '', '']` |
| `"".split(" ")` | `['']` |
| `"red,blue".split()` | `['red,blue']` |
| `"red,blue".split(",")` | `['red', 'blue']` |

The result is a list of **strings**, not a string and not converted numbers. Basic list access and `len(...)` are enough for these tasks; no loop or list-return annotation is needed.

For a contract guaranteeing exactly two non-empty words:

```python
text = "Mira Vale"
parts = text.split()
left_word = parts[0]
right_word = parts[1]
print(left_word)   # Mira
print(right_word)  # Vale
```

This only demonstrates access to the pieces. Your section decides their transformations and output order.

### A word follows the specified token rule

Under `split()`, punctuation is not a separator unless it is whitespace. `"red,blue"` is one token, `"red blue"` is two, and `"---"` is one. Spaces-only text has zero tokens. Word count is not generally space count plus one.

```python
text = "  one   two!  "
print(len(text.split()))  # 2
print(text.count(" "))   # 7
```

### Joining

The separator goes **before** `.join(...)`:

```python
parts = "north south".split()
print("/".join(parts))  # north/south
print("/".join("CAT"))  # C/A/T
```

Joining word strings inserts separators between words. Joining a string inserts them between characters. No separator is added at the ends. Joining an empty list produces an empty string.

Do not split and rejoin when the question requires original spacing: that can destroy meaningful data.

## 7. Counts and searches

| Expression | Result | What it measures |
| --- | --- | --- |
| `len("Go go!")` | `6` | All characters, including the space and `!` |
| `"Go go!".count(" ")` | `1` | Literal spaces |
| `"Go go!".count("g")` | `1` | Case-sensitive lowercase `g` |
| `"Go go!".lower().count("g")` | `2` | Occurrences after case normalization |
| `len("Go go!".split())` | `2` | Non-empty whitespace-separated tokens |
| `"aaaa".count("aa")` | `2` | Non-overlapping substring occurrences |
| `"abc".count("z")` | `0` | No occurrences |

For a single-character target, `.count(...)` already counts all occurrences. For an English-letter case-insensitive count, normalize a separate copy; do not let that copy change other views that require original case.

`count(" ")` counts literal spaces. `split()` recognizes whitespace more generally, although the assigned character sets often allow ordinary spaces rather than tabs.

### Finding a position

```python
text = "coconut"
print(text.find("co"))  # 0
print(text.index("n"))  # 4
print(text.find("z"))   # -1
# text.index("z")      # This would raise ValueError.
```

`find()` returns `-1` when absent; `index()` raises an error. Do not use a failed `find()` result as a character index: index `-1` selects the last character, which is unrelated to “not found.” In the two-word name inputs, the required separating space is guaranteed to exist.

## 8. Case conversion and capwords

For `text = "  rED   FOX!  "`:

| Operation | Result in Python notation |
| --- | --- |
| `text.upper()` | `"  RED   FOX!  "` |
| `text.lower()` | `"  red   fox!  "` |
| `text.capitalize()` | `"  red   fox!  "` |
| `text.title()` | `"  Red   Fox!  "` |
| `string.capwords(text)` | `"Red Fox!"` |

`capitalize()` encounters a **space** as the first character here. It does not search for the first letter to uppercase. The remaining letters become lowercase. Capitalizing the original value therefore differs from capitalizing a trimmed value.

`title()` preserves spaces and follows Python's rules for groups of letters. It is not a grammar-aware name or brand formatter. For example, `"vINuNI".title()` is `"Vinuni"`, not the brand-specific `"VinUni"`.

### The default capwords behavior

With its default separator, `string.capwords(...)` splits on whitespace, capitalizes the words, and joins them with one space. It removes outer whitespace and collapses internal whitespace runs, changing spacing as well as case.

```python
import string

text = "  rAIN   bOW  "
formatted = string.capwords(text)
print(formatted)   # Rain Bow
print(repr(text))  # '  rAIN   bOW  '
```

Keep an explicitly requested `.strip()` step in your lab function even if another operation removes the same outer spaces. Required implementation steps can matter in addition to output.

### Punctuation reveals differences

| Original | `.capitalize()` | `.title()` | `string.capwords(...)` |
| --- | --- | --- | --- |
| `"we're reADy"` | `"We're ready"` | `"We'Re Ready"` | `"We're Ready"` |
| `"green-TEA"` | `"Green-tea"` | `"Green-Tea"` | `"Green-tea"` |
| `"ab7cd"` | `"Ab7cd"` | `"Ab7Cd"` | `"Ab7cd"` |

These are independent Python comparisons. The assigned **P3** inputs contain English-letter words and spaces, so punctuation/digits are not valid P3 stress tests. Other problems permit different characters.

Do not swap `.title()` and `capwords()` just because they agree on a simple sample. See the official [`capwords()` reference](https://docs.python.org/3.12/library/string.html#string.capwords).

## 9. Trimming, replacement, and order

### Ends versus internal characters

```python
text = "  a  b  "
print(repr(text.strip()))            # 'a  b'
print(repr(text.replace(" ", "")))   # 'ab'
print(repr(text.replace(" ", "_")))  # '__a__b__'
print(repr(text))                    # '  a  b  '
```

`.strip()` removes outer whitespace, not internal spaces. `.replace(" ", "_")` replaces **every literal space**, including each space in a run and those at the ends. Replacing with `""` deletes those characters.

An argument to `.strip(chars)` is a collection of removable characters, not an exact word/prefix:

```python
text = "abXba"
print(text.strip("ab"))  # X
```

This removes any `a` or `b` at the two ends until another character is reached. It does not remove internal separators generally.

### Every view may need the original

If several views are requested, avoid overwriting `text` and accidentally using the changed value later. Use names such as `trimmed_text` and `uppercase_text` while leaving the original available.

For `text = "  mIX  "`:

| Expression | Result |
| --- | --- |
| `text.capitalize()` | `"  mix  "` |
| `text.strip().capitalize()` | `"Mix"` |
| `text[:2]` | `"  "` |
| `text.strip()[:2]` | `"mI"` |
| `text[::-1]` | `"  XIm  "` |

These are different computations. Select the specified one, not the tidiest-looking output.

### Normalize and replace in the specified order

```python
import string

text = "  tINY   BOAT  "
direct_replacement = text.replace(" ", "/")
normalized_words = string.capwords(text)
print(direct_replacement)  # //tINY///BOAT//
print(normalized_words)    # Tiny Boat
```

Replacing original spaces substitutes for each original character. Normalizing words first produces a different starting value. Trace intermediate values in the handout's order. This example deliberately stops before constructing an assigned label.

## 10. Digits stored as text

An identifier is often text, not a quantity for arithmetic. Numeric conversion can discard meaningful formatting:

```python
identifier = "00708"
print(identifier)       # 00708
print(len(identifier))  # 5
print(int(identifier))  # 708
```

A suffix can have zeros of its own:

```python
identifier = "42007"
suffix = identifier[-3:]
print(suffix)       # 007
print(int(suffix))  # 7
```

Keep the identifier and suffix as strings when zeros must survive. `input()` already supplies text. An annotation documents the choice; it does not restore zeros discarded by `int(...)`.

### Removing separators

```python
raw_code = "00-70 8"
without_hyphens = raw_code.replace("-", "")
clean_code = without_hyphens.replace(" ", "")
print(clean_code)       # 00708
print(len(clean_code))  # 5
```

This tiny independent example is **not** a valid assigned card/account input: those require more digits. `.strip()` alone would leave its internal space and hyphen.

For masking, distinguish raw input length, cleaned identifier length, and visible character count. Check the intended final length and that the visible suffix matches the cleaned value exactly. Formatting separators are not digits.

Study repetition independently with `"?" * 5`, which makes five question marks. You still need to determine your assigned repetition count and suffix; no complete masking expression is supplied here. Do not add real-world card/account validation rules. Use fictional digit strings in practice.

## 11. Functions, annotations, and docstrings

The pattern from [Lab 03](../lab-03/README.md#3-parameter-and-return-type-annotations) still applies. A function has a clear purpose and returns the value its caller needs.

```python
def repeat_token(text: str, copies: int) -> str:
    """Return several consecutive copies of a token.

    Args:
        text: The token to repeat.
        copies: A nonnegative number of copies.

    Returns:
        One string containing the repeated token.
    """
    repeated_text = text * copies
    return repeated_text


result = repeat_token("ha", 3)
print(result)  # hahaha
```

Read the header carefully:

- `text: str` documents a text parameter.
- `copies: int` documents a whole-number parameter.
- `-> str` describes the **one value returned**.
- The final `:` begins the body.

Use `str` for text and `int` for counts/positions. A count-returning function uses `-> int`; a formatted-report function uses `-> str`, even if the report contains a number. Numeric functions from earlier labs can still use `float`.

Annotations do not enforce types or convert input. An integer read using `input()` needs `int(...)` before a function expecting an integer position/amount. An identifier whose zeros must survive should remain text.

Every function, including a helper or nested function, needs a docstring describing its **purpose, parameters, and return value**. Place it immediately inside the function, before executable statements. A nearby `#` comment does not replace it. If a demonstration has no parameters, say so.

### Return and print

`return` gives a result to the caller; `print()` writes output. If a string must be returned, print it at the call site:

```python
def add_braces(text: str) -> str:
    """Surround text with braces.

    Args:
        text: The text to enclose.

    Returns:
        One string with opening and closing braces.
    """
    enclosed_text = "{" + text + "}"
    return enclosed_text


result = add_braces("owl")
print(result)  # {owl}
```

A function that only prints and does not return a value returns `None`. Printing that return value can create an unwanted `None` line. Follow the required return behavior.

Use single-value outputs such as one `str` or one `int`. Do not add `tuple[int, int]`, list-return annotations, classes, or advanced structure. Several required output lines do not imply a function must return a tuple.

## 12. Local, global, and enclosing scope

### Reading a module-level constant

A name defined outside functions belongs to the module's global scope. A function can read it without a `global` declaration:

```python
UNIT = "cm"


def describe_width(width: int) -> str:
    """Describe a width using the module's unit.

    Args:
        width: The width measured in the module's unit.

    Returns:
        One width label.
    """
    message = f"Width: {width} {UNIT}"
    return message


print(describe_width(8))  # Width: 8 cm
print(UNIT)              # cm
```

`width` and `message` are local to a function call. `UNIT` is at module level. Using it in an expression reads it; it does not reassign it. Uppercase names are a constant convention, not automatic protection: your code must respect the requirement to leave a constant unchanged.

### Local assignment can hide a global name

```python
mode = "draft"


def local_mode() -> str:
    """Return a local mode without changing the module's mode.

    Args:
        None.

    Returns:
        The local mode text.
    """
    mode = "review"
    return mode


print(local_mode())  # review
print(mode)          # draft
```

If a function assigns to a name, Python normally treats that name as local throughout that function. Reading it before its assignment can cause `UnboundLocalError`, even if a global with that spelling exists. Distinct names for intermediate values avoid this confusion.

### Recognizing global and nonlocal from Lecture 06

| Situation | Meaning |
| --- | --- |
| Read a module-level name inside a function | No `global` declaration is needed. |
| Assign to a normal local name | A same-named module variable is not rebound. |
| Declare `global name` before assigning in a function | The assignment rebinds that name in the module's global scope. |
| Declare `nonlocal name` inside a nested function | Assignment targets an existing binding in an enclosing function scope, not the module's global scope. |

An independent `nonlocal` example for lecture revision:

```python
def revised_status() -> str:
    """Demonstrate changing a name in an enclosing function scope.

    Args:
        None.

    Returns:
        The status after the inner function changes it.
    """
    status = "draft"

    def revise() -> None:
        """Change the enclosing status.

        Args:
            None.

        Returns:
            None.
        """
        nonlocal status
        status = "review"

    revise()
    return status


print(revised_status())  # review
```

The assigned Lab 04 functions do not require this nested-state pattern. Do **not** use `global` or `nonlocal` to change a required constant. Return the result and keep intermediate values local.

Reference: [Python scopes and namespaces](https://docs.python.org/3.12/tutorial/classes.html#python-scopes-and-namespaces).

## 13. Import styles and naming

The standard `string` module provides helpers such as `capwords`. Text objects already have methods such as `.upper()` and `.strip()` without an import. A variable holding text and a module named `string` are different things.

### Three alternative import styles

Each example below is independent. Do not paste all three into an assigned problem unless actually requested.

```python
import string

result = string.capwords("quiet HILL")
print(result)  # Quiet Hill
```

```python
from string import capwords

result = capwords("quiet HILL")
print(result)  # Quiet Hill
```

```python
import string as textlib

result = textlib.capwords("quiet HILL")
print(result)  # Quiet Hill
```

| Import statement | Name introduced | Matching call |
| --- | --- | --- |
| `import string` | Module name `string` | `string.capwords(text)` |
| `from string import capwords` | Function name `capwords` | `capwords(text)` |
| `import string as textlib` | Module under the name `textlib` | `textlib.capwords(text)` |

For the current P3 variants, **261 and 264** request `import string`, **262** requests `from string import capwords`, and **263** requests `import string as textlib`. Follow your section's style. Correct output alone may not satisfy required structure.

Importing a selected function does not also introduce the module name. An alias is the module name to use after that import. Mixing styles can cause `NameError` even though the correct module is available.

Avoid `from module import *`: it makes name origins unclear and can replace your own names.

### Avoid shadowing

- Do not name your file `string.py`, `math.py`, or `random.py` when using those standard modules.
- Do not overwrite an imported module name with a text value.
- Avoid variables named `input`, `print`, `len`, or `str`, which hide useful built-ins.
- An import uses the module name without `.py`. `import measures.py` is not the syntax for importing a file called `measures.py`.
- Standard-library modules such as `string` do not need `pip install`.

`help(string)` after `import string`, or `help(str.strip)`, can be useful in a separate console. Do not leave documentation-printing calls in an online-judge submission.

Reference: [Python's module tutorial](https://docs.python.org/3.12/tutorial/modules.html).

## 14. An independent two-file example

This practice example converts a measurement. It demonstrates file/import structure without implementing an assigned text report, slice report, or masking function.

```text
module_practice/
    measures.py
    run_demo.py
```

### File 1: measures.py

```python
"""Reusable measurement conversions for this practice example."""

CENTIMETERS_PER_INCH = 2.54


def inches_to_centimeters(inches: float) -> float:
    """Convert an inch measurement to centimeters.

    Args:
        inches: A nonnegative measurement in inches.

    Returns:
        The corresponding measurement in centimeters.
    """
    centimeters = inches * CENTIMETERS_PER_INCH
    return centimeters
```

### File 2: run_demo.py

```python
import measures

inches = float(input())
centimeters = measures.inches_to_centimeters(inches)
print(f"{centimeters:.2f} cm")
```

Run the **driver**, `run_demo.py`, rather than the helper module:

```text
python run_demo.py
```

For input `2`, output is `5.08 cm`. For input `0`, output is `0.00 cm`. These are valid inputs for this independent example.

### What executes during a run

1. Python starts the driver and reaches `import measures`.
2. Python finds `measures.py` and executes its top-level statements on its first import in that interpreter session. The constant is assigned and the function is defined.
3. Defining the function does not execute its body yet.
4. The driver reads input, calls the function, receives the return value, and prints it.

A module can contain executable top-level code. An accidental `input()` or `print()` there can therefore execute during import. For the assigned helper, keep the required constants, function definitions, and documentation there. Keep lab input and output in `main.py`.

Repeated imports in the **same interpreter session** normally reuse the imported module. After editing a helper while using a persistent console, restart the console or run the driver as a fresh process. You do not need module reloading for this lab.

### Running in PyCharm

1. Create both real `.py` files in the same exercise folder. Check for hidden `.txt` extensions.
2. Open and run the driver. For the assigned module exercise, that is `main.py`.
3. Check the selected Python 3 interpreter and script path in the run configuration.
4. If import fails, check exact spelling/case and folder placement first. Restart after fixing a shadowing filename.
5. Test the submitted pair in a clean exercise folder. A different helper elsewhere in your project can otherwise hide a missing submitted file.

Python normally includes the executed script's directory in its module search path. Keeping the files together avoids `sys.path` changes, package setup, or extra installations. Running some other file elsewhere in the project is not a reliable test of your submitted pair.

Use your assigned helper name and `main.py` from [Section 19](#19-submission-checklist). The practice filenames above are not submission filenames.

## 15. Exact output and empty lines

### Actual values versus debugging notation

```python
text = "  owl "
print(text)        # Actual spaces, without surrounding quotes.
print(repr(text))  # '  owl '
```

These outputs differ. Quotes in a Python literal or `repr(...)` help you inspect a value; they are not automatically part of the required output.

### An empty result occupies a line

```python
text = "Q"
middle_view = text[1::2]
print("begin")
print(middle_view)
print("end")
```

Output:

```text
begin

end
```

There is an empty line between `begin` and `end`. Do not print a visible placeholder such as `None`, `0`, `[]`, a message, or two quote characters. `print("")` is correct: its quotes belong to Python syntax, so the output contains only a newline, not quote characters.

Five requested views still need five lines if a view is empty. A one-character string can have no characters at odd-index slice positions. A spaces-only string can become empty after stripping, while other views still contain spaces.

### Formatting details

- `print(first, second)` inserts a space by default; it may not match a comma, colon, or no separator.
- Add labels only where requested. Unlabeled views should not gain explanations such as `Uppercase:`.
- Preserve the specified case, periods, brackets, hyphens, underscores, and parentheses.
- Do not add prompts, debug output, headings, extra explanation, or a print from a helper module.
- For a reported length, identify which version it describes: original, trimmed, cleaned, or extracted text.

Use debugging representations to compare hard-to-see spaces locally, then submit the actual required output. Similar-looking results can differ in spaces or line count.

## 16. Valid extreme cases for every section

Run your own handout's samples first, then use these test ideas. They are **testing hints**, not complete implementations or answer keys.

Expressions such as `"Z" * 100` describe constructing a value in a scratch console. Copy the resulting string into input, not the expression or its surrounding representation quotes.

### Shared boundaries

| Case | What to investigate |
| --- | --- |
| One character | Safe short slices, reversal, unchanged punctuation, empty odd-index views |
| One literal space | Non-empty input whose trimmed value is empty, where permitted |
| Several spaces only | Original length versus zero words; space-containing views versus trimmed empty text |
| Outer spaces | Early stripping, changed slice positions, and capitalization behavior |
| Repeated internal spaces | Literal counts versus tokens; preserving versus normalizing spacing |
| Odd/even lengths | Which original positions negative-step slices select |
| Mixed case | Case-sensitive counts and the required case conversion |
| Punctuation without letters | Punctuation contributes to length and can form a token |
| Maximum valid length | Whole-line limits include outer spaces and separators |
| Repeated local function calls | Required constants remain unchanged after every call |

An empty string is useful in a scratch-console method experiment, but **none of the current assigned Lab 04 input lines are empty strings**. Some allow only spaces; others require a word or a non-whitespace character. Distinguish the guarantees.

### IPRFAL261

| Problem | Additional valid cases | What to check |
| --- | --- | --- |
| P1: two-word name | `q R`; mixed case; two 30-letter words with one separating space | Index `0` exists in both words. Whole-name reversal includes the separator and changes word order. Initial punctuation/case follow your sheet. |
| P2: string views | `!`; one space; 100 spaces; `"  aBc!  "`; odd/even lengths | Views use original text. `.title()` follows Python punctuation rules and preserves spacing. Trace the slice's starting position. |
| P3: label/scope | One letter; a 100-letter word; `"  sMALL    room  "`; padding to total length 100 | At least one English-letter word remains. Return one string, document the function, and preserve the module constant. |
| P4: text module | One punctuation character; 100 non-whitespace characters; padded text with repeated internal spaces | At least one non-whitespace character is required. Check the length of the text version specified by the report, including internal spaces/punctuation. Test the two files together. |

### IPRFAL262

| Problem | Additional valid cases | What to check |
| --- | --- | --- |
| P1: two-word formatter | `q R`; mixed case; each word at its 30-letter limit | Reversing each word differs from reversing the whole input. A lowercase view must not change later case-preserving views. |
| P2: mini analyzer | One letter; one space; `"   "`; `"  aB!  "`; 100 characters | Only the requested trimmed view removes outer spaces. Two-character slices work on length one. An empty trimmed view still occupies a line. |
| P3: course label/scope | One letter; `"  sMALL    room  "`; a 100-letter word; many one-letter words | Use the selected-function import style. Normalize and replace separators on the specified intermediate values. Read the constant without rebinding it. |
| P4: slice module | Start `0` and take all text; final-character start with length one; a region containing spaces; 100-space text | Read three lines. Start is below text length; requested length is at least one. Preserve substring spaces and verify its reported length. |

### IPRFAL263

| Problem | Additional valid cases | What to check |
| --- | --- | --- |
| P1: feedback statistics | `!`; `---`; mixed-case target letters; 300 spaces; repeated spaces | Punctuation-only tokens count under `split()`. Spaces-only text has zero words but nonzero length. Literal spaces and words are different quantities. |
| P2: feedback views | `Q`; one space; `"  mIX  "`; 100 spaces; odd/even lengths | The odd-index view can be empty. Capitalize the original, including any first-character space. Only the stated view strips. |
| P3: feedback label/scope | One letter; mixed case; repeated spaces; total length exactly 100 | Use the required alias. Check surrounding punctuation/spaces separately from normalization. Return one string and preserve the constant. |
| P4: card module | 12 or 19 cleaned digits; leading zeros; visible suffix with zeros; valid raw line of length 100 | Keep digits as text. Remove all permitted separators, not only outer whitespace. Final length follows the cleaned digit count; suffix zeros survive. |

### IPRFAL264

| Problem | Additional valid cases | What to check |
| --- | --- | --- |
| P1: product snapshot | One hyphen; 300 hyphens; 300 spaces; repeated separators/punctuation | Hyphens contribute to the required counts. Continuous punctuation is one `split()` token. Spaces-only text has zero words. |
| P2: product views | One letter; one space; two characters; `"  aB  "`; odd/even lengths; 100 characters | Each original space receives the specified replacement. A last-three slice can be shorter than three. Backward alternating selection starts at the last original position. |
| P3: product tag/scope | One letter; mixed case; repeated spaces; one 100-letter word | Follow the import style and transformation order. Keep the prefix separate from changes intended for words. Return one string. |
| P4: account module | 10 or 20 cleaned digits; leading zeros; visible suffix with zeros; valid formatting up to 100 raw characters | Visible suffix length differs from section 263. Check your own constant/limits rather than copying another section. |

### Constraint reminders

- Name inputs in 261/262 contain exactly two English-letter words of 1–30 letters, with **one** space and no outer spaces. Maximum whole-line length is `30 + 1 + 30 = 61`.
- P3 input in every section is at most 100 characters and contains at least one English-letter word. Spaces-only text, punctuation, and digits are not valid P3 inputs.
- A line of 49 spaces, one letter, and 50 spaces has length 100 and is a valid heavily padded P3 test. Fifty one-letter words need 99 characters with single separators; one extra outer space brings it to 100.
- P4 in 261 disallows spaces-only input. P4 in 262 allows non-empty spaces-only input, but a zero requested length or a start at `len(text)` is invalid.
- P4 in 263/264 allows at most 100 **raw** characters. Cleaned digit limits are **12–19** and **10–20**, respectively. Formatting separators consume raw length.
- The card/account constraints do not require a grouping pattern or a real-world validation algorithm. Do not invent those restrictions.

## 17. Questions and debugging

### Should I strip input immediately?

Only when the specification wants all later work to use trimmed text. Multi-view tasks often need original text, with stripping applied to one named view. Keep raw input and derive each view from the specified source.

### Does non-empty mean there is a letter?

No. `" "` and `"!"` are non-empty. “One or more English-letter words” and “at least one non-whitespace character” are stronger, different guarantees.

### Do short slices or empty views need an if statement?

No. Slices already return available characters, possibly an empty string. `print("")` already prints an empty line. Retain every requested output line.

### Can title and capwords replace each other?

Follow the named operation. Their spacing/punctuation rules differ. A simple sample on which they agree does not prove equivalence.

### Should acronyms stay uppercase after capitalization?

Only if the required operation preserves them. Python's functions have fixed rules. Do not invent brand/name/acronym special cases when the question requests those functions.

### Do I need global to use a constant?

Reading it needs no `global`. Give local results distinct names. Do not rebind a required constant to construct output.

### Why does running the helper show nothing?

Defining a function does not call it. A helper with only definitions/constants can correctly produce no output by itself. Run the driver that reads input and calls the function.

### Why does importing the helper request input or print output?

Top-level statements execute during the first import in an interpreter session. Keep assigned I/O in `main.py`. You do not need an `if __name__ == "__main__"` guard for the lab's helper-only structure.

### Can I submit the two P4 files to Codeforces?

The current P4 is Canvas-only. Upload both exact import-compatible filenames together to Canvas. P1–P3 remain separate single-file submissions on both platforms. An old single-file Polygon/Codeforces P4 does not replace the current module exercise.

### Common symptoms

| Symptom | Likely cause | Check |
| --- | --- | --- |
| Length/space count too small | Stripped, split, or rejoined too early | Preserve the original input. |
| Word count too large | Used `split(" ")` or spaces-plus-one | Apply the specified token rule. |
| Valid short input raises `IndexError` | Used an index instead of a slice | Trace available positions and bounds. |
| Wrong backward alternating characters | Selected from index `0` before reversing, or wrong step | Trace odd and even lengths. |
| First visible letter remains lowercase | `capitalize()` encountered an initial space | Check whether to use original or trimmed text. |
| Method seems ineffective | Discarded its returned value | Store or use the returned string. |
| Unwanted `None` line | Function printed but did not return its result | Match the required return behavior. |
| `NameError` for a local at top level | Accessed a function-local name outside | Store its return value in the caller. |
| `UnboundLocalError` | Assignment makes a same-spelled name local | Use distinct intermediate names. |
| `NameError` for an import/alias | Import and call styles disagree | Check Section 13's matching names. |
| `ModuleNotFoundError` for helper | Wrong name, folder, driver, or missing file | Keep the exact two files together and run `main.py`. |
| `AttributeError` for module function | Wrong spelling or a shadowing module | Inspect filenames and function names. |
| Waiting for input or `EOFError` online | Extra input call, possibly inside imported module | Count input lines; keep I/O in driver. |
| Identifier loses zeros | Converted identifier/suffix to integer | Keep those values as strings. |
| Wrong mask/report length | Counted the wrong intermediate value | Distinguish raw, trimmed, cleaned, extracted text. |
| Similar-looking output rejected | Prompts, labels, quotes, missing lines, wrong punctuation | Compare exact characters and line order. |

Error names help identify development mistakes; they are not instructions to add assertions, validation branches, or exception handlers. Fix the cause and retest valid inputs.

### Local testing routine

1. Identify valid inputs from your own section's constraints.
2. Copy a sample exactly and compare every output line.
3. Try minimum and maximum valid sizes.
4. Test spacing, punctuation, case, odd/even lengths where permitted.
5. Use temporary `repr(...)` prints if intermediate text is hard to inspect.
6. Call a function with a second input in a scratch test and check that constants stay unchanged.
7. Run the P4 pair in a clean folder as a fresh process.
8. Remove test calls/diagnostics, rerun once, and submit the same final P1–P3 code to both platforms.

## 18. Optional extra practice

These **new, optional exercises** are not section questions or required submissions. No solutions are supplied. Use simple annotations and a purpose/parameter/return docstring for every function. Assume the stated inputs are valid. Use this guide's concepts without loops or conditionals.

### A. Small end tag

Define `end_tag(text: str) -> str` to return the last two characters in uppercase. Read one line of 1–40 English letters. Print only the returned string.

| Input | Expected output |
| --- | --- |
| `lantern` | `RN` |
| `q` | `Q` |

Hint: A slice can return fewer than two characters. No one-letter special case is needed.

### B. Question note

Define `note_summary(text: str) -> str` to return `Note: W words, Q questions`, where `W` counts `split()` tokens and `Q` counts literal `?` characters. Read one non-empty line of at most 80 characters containing English letters, spaces, and `?`. Spaces-only lines are permitted.

| Input in Python notation | Expected output |
| --- | --- |
| `"Is it ready? Really?"` | `Note: 4 words, 2 questions` |
| `"   "` | `Note: 0 words, 0 questions` |

Hint: Two question marks do not necessarily imply two tokens. Quotes are notation only.

### C. Reading label

Import the standard `string` module under an alias of your choice. Define `reading_label(text: str) -> str` to return words normalized with `capwords()`. Read one line with at least one English-letter word, allowing outer/repeated spaces. Print the returned text with no prefix.

| Input in Python notation | Expected output |
| --- | --- |
| `"  qUIET   FOREST  "` | `Quiet Forest` |
| `"m"` | `M` |

Hint: Call through your alias. Input and return value remain strings.

### D. Character-spacing module

Create `spacing_tools.py` and `main.py` together in a practice folder. In the helper, define `SEPARATOR` as `"/"` and `space_characters(text: str) -> str`, returning input characters joined with that separator. The driver imports the module, reads one non-empty line of at most 40 English letters, and prints the return value. Keep I/O out of the helper.

| Input | Expected output |
| --- | --- |
| `OWL` | `O/W/L` |
| `a` | `a` |

Hint: Insert separators between characters, not at the ends. Keep the constant unchanged.

## 19. Submission checklist

### Problems 1–3: both platforms

- Use current Fall 2026 questions for your own section.
- Keep each problem in a separate Python 3 file.
- Follow `Lab4_PY_Z.py`: `Y` is the problem number, `Z` your student ID. For example, P1 uses `Lab4_P1_<YourStudentID>.py`, replacing the placeholder with your actual ID.
- Match required function names, parameter order, import style, constants, and return behavior.
- Give every function a docstring. Use the simple annotations explained above; they do not convert inputs.
- Preserve original text wherever requested. Do not normalize spaces a view must retain.
- Print the exact lines, including empty lines, labels, punctuation, and case.
- Submit identical code to the assigned **Codeforces contest and Canvas** before **the end of the lab session**. Under current instructions, a problem missing from either platform is not graded.

### Problem 4: Canvas only

| Section | Helper module | Driver |
| --- | --- | --- |
| IPRFAL261 | `text_tools.py` | `main.py` |
| IPRFAL262 | `slice_tools.py` | `main.py` |
| IPRFAL263 | `card_tools.py` | `main.py` |
| IPRFAL264 | `account_tools.py` | `main.py` |

- Keep the required helper and `main.py` together and test the pair.
- Put constants/functions in the helper; put input/output in `main.py`.
- Retain these exact names. Do not append a student ID to the helper or `main.py` unless the instructor changes the instructions: the import must match the file's module name.
- Upload **both files together to Canvas** before the end of the lab session. Follow Canvas instructions; do not assume a ZIP is required when the sheet asks for two Python files.
- Do **not** submit the current P4 to Codeforces or substitute an old single-file P4.

For general platform steps, see the [Codeforces and Canvas guide](../lab-tutorial.md). Lab 04's P4 exception and filenames above take precedence over generic single-file examples there. Review [Lab 03's function guide](../lab-03/README.md) for annotations, docstrings, and return values.

## Further reading

These official Python 3 references also cover features beyond this lab. Focus on the operations your handout requires.

- [Sequence operations and slicing](https://docs.python.org/3.12/library/stdtypes.html#common-sequence-operations)
- [String methods](https://docs.python.org/3.12/library/stdtypes.html#string-methods)
- [`string.capwords()`](https://docs.python.org/3.12/library/string.html#string.capwords)
- [Modules and module search paths](https://docs.python.org/3.12/tutorial/modules.html)
- [Scopes and namespaces](https://docs.python.org/3.12/tutorial/classes.html#python-scopes-and-namespaces)
- [Function annotations](https://docs.python.org/3.12/tutorial/controlflow.html#function-annotations)
