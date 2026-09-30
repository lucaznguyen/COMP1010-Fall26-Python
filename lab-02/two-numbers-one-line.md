# Lab 02 Tutorial: Reading Two Numbers from One Line

**Course:** COMP1010 - Introduction to Programming  
**Topic:** Reading and converting input in Python 3

Some programming problems put two numbers on the same line, separated by spaces. For example:

```text
17 5
```

This tutorial shows how to read those values, convert them to integers, and use them in a program.

## The Short Version

```python
first, second = map(int, input().split())
```

If the input is `17 5`, then `first` is the integer `17` and `second` is the integer `5`.

## What Happens Step by Step

### 1. `input()` reads a whole line as text

```python
line = input()
```

If the user types `17 5` and presses Enter, `line` contains the string:

```text
"17 5"
```

It is still text, not two numbers.

### 2. `.split()` separates the values at whitespace

```python
parts = input().split()
```

For the line `17 5`, the result is a list of two strings:

```python
["17", "5"]
```

With no argument, `.split()` handles one space, several spaces, or tabs between values.

### 3. `map(int, ...)` converts each piece to an integer

```python
numbers = map(int, input().split())
```

`map` applies `int` to every piece of text. Assigning the two results to two variables gives:

```python
first, second = map(int, input().split())
```

This is a compact version of the longer form:

```python
parts = input().split()
first = int(parts[0])
second = int(parts[1])
```

Both forms work when the input line contains exactly two values.

## Complete Example

This small program reads two integers and prints their sum:

```python
first, second = map(int, input().split())
total = first + second
print(total)
```

Example input:

```text
17 5
```

Output:

```text
22
```

The example is only to demonstrate input handling. For an assignment, calculate and print exactly what its own instructions request.

## Text Versus Integers

This version splits the input but leaves both values as strings:

```python
first, second = input().split()
```

For example, `first` is `"17"`, not `17`. This matters because `+` joins strings:

```python
first, second = input().split()
print(first + second)  # Input: 17 5; prints 175
```

Convert to integers when doing arithmetic:

```python
first, second = map(int, input().split())
print(first + second)  # Input: 17 5; prints 22
```

## Common Mistakes

| Code or habit | Why it causes trouble | Better approach |
| --- | --- | --- |
| `number = int(input())` when the line is `17 5` | `int()` cannot convert the entire two-value string | Split the line, then convert each part |
| `first, second = input().split()` followed by arithmetic | The variables are strings, so `+` joins text | Use `map(int, input().split())` |
| `first, second = input()` | Python tries to unpack individual characters, not space-separated values | Add `.split()` to separate tokens |
| Typing `17, 5` when the format says two space-separated numbers | A comma is not whitespace, so the input does not split into two values | Type `17 5`, unless the problem explicitly specifies commas |
| Printing `Enter two numbers:` before reading input | Online judges expect only the requested output | Read with `input()` without a prompt |

## If Each Pair Is on Its Own Line

Some problems first give the number of pairs, then put each pair on a separate line. For example:

```text
3
8 2
10 4
7 1
```

The first line is the count. Read it separately, then read two integers during each loop iteration:

```python
pair_count = int(input())

for _ in range(pair_count):
    first, second = map(int, input().split())
    print(first + second)
```

Follow the exact layout in the problem statement. If it says there is only one pair, do not add a loop or read extra lines.

## How to Test It

1. Run the program in PyCharm.
2. In the run console, type two integers separated by a space, such as `17 5`, then press Enter.
3. Check that the result matches the operation in your code.
4. Try a second pair, such as `0 12`, to check that it is not hard-coded for one example.
5. For an online judge, remove any interactive prompt text and match the required output exactly.

## Quick Checklist

- Read the number of values and their line layout from the input specification.
- Use `input().split()` to separate whitespace-delimited values on a line.
- Convert to `int` before doing integer arithmetic.
- Unpack into the expected number of variables.
- Do not print prompts unless explicitly requested.
- Test with more than one valid input.

For more on strings, types, and arithmetic expressions, return to the [Lab 02 guide](README.md#2-input-types-and-conversion).
