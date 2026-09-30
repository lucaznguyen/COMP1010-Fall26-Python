# Lab 02: Expressions, Data Types, and Operators

**Course:** COMP1010 - Introduction to Programming  
**Week:** 02

This guide reviews Python concepts for Lab 02 with small examples and study guidance. It does not contain the section question sheets or solutions. Get the question sheet for your assigned section from the official course source before starting.

## Question Sheet

Question sheets are distributed through the official course source and are not stored in this repository. Make sure you use the sheet assigned to your section. The [handout notes](handouts/README.md) explain this repository's scope.

## What You Will Practice

- Build expressions with integer arithmetic, including `//`, `%`, and `**`.
- Predict expression results using operator precedence and parentheses.
- Convert input text into numbers before doing arithmetic.
- Compare values and combine Boolean results with `and`, `or`, and `not`.
- Use Python's built-in functions for number representations and character codes.
- Join and repeat strings while keeping labels, spaces, and punctuation exact.

## 1. Expressions and Operator Precedence

Python evaluates multiplication and exponentiation before addition. Parentheses make the intended grouping explicit.

```python
print(2 + 3 * 4)      # 14
print((2 + 3) * 4)    # 20
print(2 ** 3 + 1)     # 9
```

The common arithmetic operators are:

| Operator | Meaning | Result type for integers |
| --- | --- | --- |
| `+` | Addition | `int` |
| `-` | Subtraction | `int` |
| `*` | Multiplication | `int` |
| `/` | True division | `float` |
| `//` | Floor division | `int` |
| `%` | Remainder | `int` |
| `**` | Exponentiation | Usually `int` for non-negative integer powers |

`//` rounds down to the next integer, rather than truncating toward zero. For example, `-8 // 3` is `-3`. The remainder follows the divisor's sign: `-8 % 3` is `1`. Check negative inputs carefully when a problem asks for floor division or remainder.

## 2. Input Types and Conversion

`input()` returns a `str`. Convert it before using the value as a number:

```python
quantity_text = input()
quantity = int(quantity_text)
```

For multiple values on one line, split the line and convert each part:

```python
first_text, second_text = input().split()
first = int(first_text)
second = int(second_text)
```

Use `str(value)` when a numeric value must be joined to text with `+`. An f-string is another clear way to put a value inside a label:

```python
count = 7
label = f"Count: {count}"
print(label)
```

## 3. Comparisons and Boolean Logic

Comparisons such as `==`, `!=`, `<`, `<=`, `>`, and `>=` produce `True` or `False`. Combine conditions with:

- `and`: both conditions must be true.
- `or`: at least one condition must be true.
- `not`: reverses a Boolean value.

```python
is_open = True
has_pass = False
has_guest_badge = True

can_enter = is_open and (has_pass or has_guest_badge)
print(can_enter)
```

Parentheses help make compound conditions readable. A chained comparison such as `low <= value <= high` checks both bounds, including the endpoints.

In Python, the Boolean values are `True` and `False` with capital first letters. `bool("False")` is still `True` because it is a non-empty string; if an input line is specified as the exact word `True` or `False`, compare that text explicitly or convert it according to the handout's instructions.

## 4. Number Representations and Character Codes

Python provides built-in functions for converting an integer to common bases and for mapping between a character and its Unicode code point:

```python
number = 29
print(bin(number))  # 0b11101
print(oct(number))  # 0o35
print(hex(number))  # 0x1d

print(ord("K"))    # 75
print(chr(75))      # K
```

The prefixes `0b`, `0o`, and `0x` identify binary, octal, and hexadecimal output. `ord()` takes one character and returns its numeric code; `chr()` takes a code and returns the corresponding character.

## 5. Strings and Exact Output

Strings can be joined with `+` and repeated with `*`:

```python
word = "ha"
print(word * 3)  # hahaha

course = "CS" + "-" + "101"
print(course)    # CS-101
```

The hyphen in the second example is text because it is inside quotation marks. Outside a string, `-` is the subtraction operator. When an output includes labels or punctuation, match the required spaces and characters exactly.

For Codeforces submissions, read only the input described by the problem and print only the requested output. Do not add interactive prompts such as `Enter a value:` unless the statement explicitly requests them.

## A Good Study Routine

1. Read the input constraints and output format for your assigned section.
2. Work through the provided examples by hand.
3. Identify each input's type before choosing an operator.
4. Add parentheses when an expression or Boolean condition is difficult to read.
5. Run your program with the sample and at least one additional valid input.
6. Compare the output character by character, including spaces, prefixes, capitalization, and punctuation.
7. Keep one `.py` file per problem and submit the same files to Codeforces and Canvas.

## Submission Checklist

- Use the question sheet for your assigned section; sheets may differ between sections.
- Write each solution in Python 3 and save a separate `.py` file for each problem.
- Follow the handout's naming format `LabX_PY_Z.py` (for Lab 02, use `Lab2_P1_<YourStudentID>.py` for Problem 1).
- Follow all input, output, and implementation requirements in the handout.
- Submit the same code to the assigned Codeforces contest and Canvas before the end of the lab session.
- Use meaningful names and comments where they clarify the code; do not copy or share another student's work.

See the general [Codeforces and Canvas submission guide](../lab-tutorial.md) for the Fall 2026 group link.
