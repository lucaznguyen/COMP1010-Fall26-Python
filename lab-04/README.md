# Lab 04: Modules, Strings, Testing, and Debugging

**Course:** COMP1010 - Introduction to Programming

**Preparation lectures:** Lecture 06 - Creating and Importing Modules; Lecture 07 - Strings

This first pass uses the current IPRFAL261 assignment to check scope, then reviews the concepts in Lectures 06 and 07 with independent Python examples. It does not reproduce section question sheets or provide their solutions. Section variants may differ, so get the handout assigned to you from the [official course source](handouts/README.md).

## Learning Goals

- Tell local variables, global variables, and module-level names apart.
- Import and use Python's built-in modules and your own `.py` modules.
- Read and transform strings with indexing, slicing, operators, and methods.
- Recognize common input-conversion and string-index errors.
- Test normal, boundary, and invalid cases systematically.

## 1. Local and Global Variables

A variable created inside a function is local to that function. A name created outside functions is at module level and can be read inside a function.

```python
unit_name = "centimeter"  # module-level name

def describe_length(value):
    label = f"{value} {unit_name}"
    print(label)

describe_length(12)
```

`label` exists only while `describe_length` runs. Trying to use it after the call raises a `NameError`.

If a function assigns to a name, Python treats that name as local unless you declare otherwise:

```python
count = 4

def show_local_count():
    count = 9
    print(count)  # 9: the function's local variable

show_local_count()
print(count)      # 4: the module-level variable is unchanged
```

The `global` keyword allows a function to reassign a module-level variable:

```python
count = 4

def increase_count():
    global count
    count += 1

increase_count()
print(count)  # 5
```

For most programs, returning a value is easier to follow than changing global state:

```python
def increased(value):
    return value + 1

count = 4
count = increased(count)
```

In nested functions, `nonlocal` refers to a name in the nearest enclosing function scope. It does not refer to a module-level name. This is useful to recognize, though small lab programs can usually avoid it.

## 2. Importing Modules

A module is a Python file that can contain variables, functions, and executable statements. Python's standard library includes modules such as `math`, `random`, `string`, and `sys`.

```python
import math

radius = 3
area = math.pi * radius ** 2
print(area)
```

The `math.` prefix makes clear which module provides `pi`. You can also import selected names or use an alias:

```python
from math import pi, sqrt
import math as m

print(sqrt(81))
print(m.floor(pi))
```

Avoid `from module import *`: it can overwrite names in your program and make it unclear where a name came from.

### Creating a Small Module

Put related functions in a separate `.py` file. For example, create `conversions.py`:

```python
CENTIMETERS_PER_INCH = 2.54

def inches_to_centimeters(inches):
    return inches * CENTIMETERS_PER_INCH
```

Then create `main.py` in the same folder:

```python
import conversions

length = conversions.inches_to_centimeters(5)
print(length)
```

Run `main.py`. The import uses the module's file name without `.py`; the function is accessed as `module_name.function_name(...)`. Keep your module in a location Python can find, and avoid naming your files after standard modules such as `math.py` or `string.py`.

## 3. Strings as Sequences

A string is an ordered sequence of characters. Python starts indexing at `0`; negative indices count backward from the end.

```python
word = "planet"
print(word[0])   # p
print(word[2])   # a
print(word[-1])  # t
```

An index outside the valid range raises `IndexError`. Check the string's length before accessing a position when the position may be invalid.

### Slicing

The general form is `text[start:stop:step]`. The start is included and the stop is excluded. You can omit fields to use their defaults.

```python
word = "planet"
print(word[1:5])    # lane
print(word[::2])    # pae
print(word[::-1])   # tenalp
```

Slicing safely clips a stop value that extends beyond the string. Slicing and indexing are different: a missing index raises `IndexError`, while many out-of-range slice bounds simply produce a shorter string or an empty string.

### Strings Are Immutable

You cannot replace one character in place:

```python
word = "cat"
# word[0] = "b"  # TypeError: strings do not support item assignment
word = "b" + word[1:]
print(word)  # bat
```

String operations return new strings. Store the result if you want to keep it.

## 4. String Operators and Methods

Use `in` to check whether one string occurs inside another. Use `==` to compare string values. The `is` operator checks object identity and is not a substitute for comparing text.

```python
message = "Python is fun"
print("fun" in message)       # True
print(message == "Python is fun")  # True
```

Useful built-in functions and methods include:

| Operation | What it does | Example |
| --- | --- | --- |
| `len(text)` | Counts characters | `len("hello")` gives `5` |
| `text.upper()` | Returns an uppercase copy | `"Hi".upper()` gives `"HI"` |
| `text.title()` | Capitalizes the start of each word | `"hello world".title()` gives `"Hello World"` |
| `text.strip()` | Removes leading and trailing whitespace | `"  hi  ".strip()` gives `"hi"` |
| `text.split()` | Splits around whitespace and returns a list | `"red blue".split()` gives `["red", "blue"]` |
| `text.count(part)` | Counts non-overlapping occurrences | `"banana".count("a")` gives `3` |
| `text.find(part)` | Returns the first position, or `-1` | `"banana".find("z")` gives `-1` |
| `text.index(part)` | Returns the first position, or raises `ValueError` | `"banana".index("n")` gives `2` |
| `text.replace(old, new)` | Returns a copy with replacements | `"red".replace("r", "b")` gives `"bed"` |
| `separator.join(items)` | Joins strings using a separator | `"-".join(["A", "B"])` gives `"A-B"` |

`strip(chars)` treats `chars` as a set of characters to remove from the two ends, not as one exact substring. Also, `.split()` returns a list; use the resulting elements as strings or convert them before numeric calculations.

## 5. Errors, Assertions, and Debugging

This is a short extension for invalid-input and string-index cases. The uploaded Lecture 06 and Lecture 07 slides do not explicitly teach `try`/`except` or `assert`.

Some operations fail for invalid data. For example, `int("twelve")` raises `ValueError`, and accessing a missing string position raises `IndexError`. The exception type tells you what kind of operation failed.

Use `try` and `except` when your program is expected to handle a particular failure:

```python
text = "twelve"

try:
    amount = int(text)
    print(amount)
except ValueError:
    print("Please provide a whole number.")
```

Catch the specific exception you can handle instead of hiding unrelated errors with a bare `except`. Python's built-in exception messages can differ across versions; when a task specifies a required output message, follow that specification exactly.

An `assert` checks a condition that the programmer expects to be true:

```python
temperature = 18
assert temperature >= -273.15, "Temperature is below absolute zero"
```

Assertions help detect broken assumptions during development. They are not a replacement for handling ordinary invalid user input.

When debugging, reduce the issue to a small input, inspect variable values and types, then test the boundary where behavior changes. Remove temporary diagnostic prints before submitting to an online judge.

## 6. A Practical Testing Routine

For each program, try:

1. A normal input that follows the specification.
2. A boundary input, such as an empty string if allowed, one character, or the first/last valid position.
3. An invalid input only when the specification says your program must handle it.
4. Mixed capitalization, spaces, and punctuation when working with text.
5. A check that every output line, space, and message matches the required format.

For online judge submissions, read only the specified input and print only the requested output. Do not add prompts such as `Enter a string:`.

## Before You Submit

- Read the handout assigned to your section from the official course source.
- Confirm the input format before choosing `input()`, `.split()`, or a conversion function.
- Check indexes and slice boundaries carefully.
- Remember that string methods return new values.
- Test normal and boundary cases and remove debugging output.
- Submit the required `.py` files to both assigned platforms using the course naming convention.

See the general [Codeforces and Canvas submission guide](../lab-tutorial.md) for submission steps.
