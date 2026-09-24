# Lab 01: Getting Started With Python

**Course:** COMP1010 - Introduction to Programming  
**Week:** 01  
**Date:** September 23, 2026

This guide reviews the small Python building blocks that are useful before Lab 01: displaying text, storing values, reading input, converting types, doing simple arithmetic, and matching an output format. The examples and practice prompts below use different situations from the lab problems; they are preparation, not lab solutions.

For the official problem statements, examples, and rules, read the [Lab 01 handout](fall26_comp1010_lab01.pdf). Set up your environment with the [Python and PyCharm guide](../setup-tutorial.md), and see [submission instructions](../lab-tutorial.md) before turning in work.

## Learning Goals

By the end of this warm-up, you should be able to:

- Run a Python 3 file in PyCharm and find its output.
- Print a string and recognize when exact spacing and punctuation matter.
- Store text and whole numbers in variables.
- Read a line of input and convert it to an integer when arithmetic is needed.
- Follow a simple read, calculate, print sequence.
- Check a program against sample input and output.

## 1. Run a Python File

Create a Python file such as `warmup.py` in your PyCharm project. Enter a statement, then run the file using the green Run button.

```python
print("Python is ready.")
```

The output is:

```text
Python is ready.
```

`print()` displays a value. Text is written inside matching quotation marks. Python runs statements from top to bottom, so two `print()` calls produce two output lines.

```python
print("First line")
print("Second line")
```

## 2. Values, Types, and Variables

A variable is a name that refers to a value. Use `=` to assign a value to a variable. Python decides the value's type from what you assign:

```python
course_name = "COMP1010"  # str: text
week_number = 1            # int: a whole number

print(course_name)
print(week_number)
```

Use quotes for text, but not for numbers you plan to calculate with. Variable names can contain letters, digits, and underscores, but cannot start with a digit. Names such as `week_number` are easier to understand than names such as `x`.

Common beginner types include:

| Type | Stores | Example |
| --- | --- | --- |
| `str` | Text | `"COMP1010"` |
| `int` | Whole numbers | `12` |
| `float` | Decimal numbers | `2.5` |
| `bool` | True/false values | `True` |

## 3. Read Input and Convert It

`input()` reads one line from the user. It always returns text (`str`), even when the line contains digits.

```python
number_of_boxes = input()
print(number_of_boxes)
```

To do integer arithmetic with that value, convert it with `int()`:

```python
number_of_boxes = int(input())
```

If a program expects two input lines, call `input()` twice. Each call reads the next line. For a decimal value, Python provides `float()`; Lab 01's warm-up examples use whole numbers.

When running a program that reads input in PyCharm, click in the Run window and type the input there. The program pauses at `input()` until a line is entered.

## 4. Do a Calculation

Python uses familiar arithmetic operators:

| Operator | Operation | Example |
| --- | --- | --- |
| `+` | Addition | `4 + 3` gives `7` |
| `-` | Subtraction | `9 - 2` gives `7` |
| `*` | Multiplication | `4 * 3` gives `12` |
| `/` | Division | `9 / 2` gives `4.5` |

Here is a complete example in a different context. It reads a quantity and a unit price, multiplies them, and prints the subtotal:

```python
quantity = int(input())
unit_price = int(input())
subtotal = quantity * unit_price

print(subtotal)
```

For this example, input:

```text
3
4
```

produces:

```text
12
```

The program follows a useful pattern: read the values, convert them to the needed type, calculate, then print the result. Parentheses can make the intended order clear in longer expressions, for example `total = (a + b) * c`.

## 5. Format Output

An f-string inserts a value into a piece of text. Put `f` immediately before the opening quote and place a variable inside braces:

```python
subtotal = 12
print(f"Subtotal: {subtotal}")
```

Output:

```text
Subtotal: 12
```

For programming exercises, match the requested output exactly. Check capital letters, spaces, punctuation, and whether the answer should include a label. Do not add an explanation unless the instructions ask for one.

## 6. Input and Output on Codeforces

Codeforces compares your program's output with the expected output. Read input in the format described by the problem and print only what it asks for.

For example, this reads a number without displaying an extra prompt:

```python
count = int(input())
print(count)
```

For an interactive practice program, a prompt such as `input("Enter a count: ")` can be helpful. For an online judge, that prompt becomes extra output and can make an otherwise correct answer fail. Use plain `input()` unless the problem specifically requests prompt text.

## 7. A Simple Work Routine

1. Read the input and output sections before writing code.
2. Identify what values the program receives and what it must display.
3. Work through one example by hand.
4. Write the steps in order: read, convert if needed, calculate, print.
5. Run the file in PyCharm with the sample input.
6. Compare every character of your output with the expected output.
7. Try another reasonable input to check that the program uses the input rather than a value typed directly into the code.

## Common Mistakes

- **Input is text:** `input()` returns a string. Convert it with `int()` before integer arithmetic.
- **Unexpected output:** Extra prompts, labels, spaces, or punctuation may not match the required format.
- **Quotation marks do not match:** Text needs a matching pair, such as `"text"` or `'text'`.
- **A variable name is inconsistent:** Python treats `total` and `Total` as different names.
- **The program seems stuck:** It may be waiting for a line of input in the Run window.
- **A value is hard-coded:** Read values from input when the problem says they are provided as input.

## Practice Before the Lab

Try these independently. They are warm-up exercises and are not the Lab 01 problems.

1. Print the name of a hobby on one line and the number of hours you spend on it in a week on the next line.
2. Read two whole numbers, calculate their sum, and print only the sum.
3. Read a number of minutes, convert it to seconds, and print the result.
4. Read a quantity and a unit price, then print a labeled subtotal using an f-string.

For each exercise, check the input type, test with more than one set of values, and keep any text in the output exactly as required by your own instructions.

## Before You Submit Lab 01

- Read the full [handout](fall26_comp1010_lab01.pdf); it is the source for the actual questions and required output.
- Save each problem as its own `.py` file using the handout's filename pattern.
- Submit the same files to Codeforces and Canvas as described in the [submission guide](../lab-tutorial.md).
- Review the [completion checklist](../grading-criteria.md).
