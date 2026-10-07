# Lab 03: Functions and Modules — Detailed Study Guide

**Course:** COMP1010 - Introduction to Programming

**Week:** 03

**Preparation:** Lecture 04 (Functions) and Lecture 05 (Functions and Modules)

This guide supports the current Fall 2026 Lab 03 for sections IPRFAL261–IPRFAL264. It teaches the shared techniques with independent examples and optional practice. It does **not** contain a question sheet, a PDF, or solutions to the assigned problems. Get the question sheet for **your own section** from the official course source; problem names, input layouts, and output formats differ by section. If this guide and your official question sheet differ, follow the question sheet.

You may assume inputs satisfy each problem's stated constraints. The lab does not require `if`/`else` or defensive handling of invalid input. “Extreme cases” below means **valid boundary and stress cases**, not arbitrary invalid data.

## What you should be able to do

- Define a function, call it with the required arguments, and use its return value.
- Add basic parameter and return type annotations, and understand what they do and do not do.
- Write a docstring for **every** function, including a nested helper.
- Use a default parameter while still reading exactly the input specified by the problem.
- Define and call a helper inside an outer function when a problem requires one.
- Import a standard module such as `math` when you need one of its functions or constants.
- Read one value, mixed values on one line, and values spread across multiple lines.
- Return one number or one formatted string when required, then print only the specified output.
- Design normal, boundary, precision, and exact-format tests for your own section.

## 1. Read the problem as a program contract

Before coding, write down five things from **your section's** question sheet:

| Question to answer | Why it matters |
| --- | --- |
| What lines are in the input, and what type is each value? | `input()` reads one line of text, not “all remaining numbers.” |
| What exact function names, parameters, and parameter types are required? | A correct calculation can still violate an implementation requirement. An annotation documents the type you expect to receive. |
| What should each function **return**, and what type is that value? | A function that only prints may not meet a “must return” requirement. Its return annotation should describe the actual returned value. |
| What exactly must the program print? | Labels, spaces, punctuation, units, line breaks, and decimal places matter. |
| What are the valid limits? | Test endpoints and avoid dividing by a value that is allowed to be zero. |

In particular, a default parameter in a function does **not** automatically mean an extra input value may appear. Follow the stated input layout. For each assigned problem, create a separate `.py` file.

## 2. Defining and calling a function

The `def` statement creates a function. Its body runs only when you call it. Parameter names belong to the definition; argument values are supplied by the caller. The `: int` and `-> int` in this first example describe expected types; Section 3 explains that syntax in detail.

```python
def multiply_by_scale(value: int, scale: int) -> int:
    """Multiply a measurement by a scale factor.

    Args:
        value: The original measurement.
        scale: The factor to apply.

    Returns:
        The scaled measurement.
    """
    scaled_value = value * scale
    return scaled_value


result = multiply_by_scale(7, 2)
print(result)  # 14
```

The `return` statement sends one value back to the caller and ends that call. `result` is a single variable holding that value. You can call the same function again with different arguments without rewriting its body.

Common mistakes:

```python
# These are examples of mistakes, not code to submit.
# def multiply_by_scale(value: int, scale: int) -> int  # Missing colon.
# result = multiply_by_scale(7)        # Missing one required argument.
# print(scaled_value)                  # Local variable is not visible here.
```

Indentation is part of Python syntax. Keep the statements inside a function consistently indented, and place the top-level input and output statements outside it.

### `return` is different from `print`

```python
def double_and_print(number: int) -> None:
    """Print twice the number without returning a useful value.

    Args:
        number: The number to double.

    Returns:
        None.
    """
    print(number * 2)


def double_and_return(number: int) -> int:
    """Return twice the number.

    Args:
        number: The number to double.

    Returns:
        The doubled number.
    """
    result = number * 2
    return result
```

If the question says “the function must return,” use the second pattern. Printing inside `double_and_print` does not make the function return its printed number; its return value is `None`, which is why its return annotation is `-> None`. In a lab program, the usual flow is **read input → call function → store one return value → print the required output**. If a problem asks for two output lines, two separately called functions may each return one value, which you print on separate lines.

## 3. Parameter and return type annotations

An **annotation** states the type a function expects or returns. Put `: type` after each parameter name and `-> type` after the closing parenthesis, before the colon that starts the function body:

```python
def scale_measurement(reading: float, factor: float = 2.0) -> float:
    """Multiply a measurement by a scale factor.

    Args:
        reading: The original measurement.
        factor: The factor to apply; defaults to 2.0.

    Returns:
        The scaled measurement.
    """
    result = reading * factor
    return result
```

Read the first line as: `reading` should be a `float`; `factor` should be a `float` and defaults to `2.0`; the function should return a `float`. The default value goes **after** the annotation: `factor: float = 2.0`. A required parameter such as `reading` has no `= ...` part. The final colon after `-> float` is still required by `def`.

For this lab, the most useful types are:

| Annotation | Meaning | Typical use |
| --- | --- | --- |
| `int` | A whole-number value. | Counts and integer scores. |
| `float` | A numeric value that may have a fractional part. | Measurements, rates, and calculated decimal values. |
| `str` | Text. | A label or a formatted string returned by a function. |
| `None` | No useful value is returned. | A function that only prints or performs an action. |

Choose the **return** annotation from what the function actually returns, not from what it reads or prints. Here the input parameter is numeric, but the returned f-string is text:

```python
def describe_length(length_cm: float) -> str:
    """Format a length as a short text label.

    Args:
        length_cm: A length in centimeters.

    Returns:
        A label containing the length to one decimal place.
    """
    label = f"Length: {length_cm:.1f} cm"
    return label


result = describe_length(12.5)
print(result)  # Length: 12.5 cm
```

By contrast, a function that returns the unformatted numeric result should use `-> float`; formatting that result later in `print(f"{result:.2f}")` does **not** change the function's return type. A function that only calls `print()` and has no value-bearing `return` uses `-> None`, as shown in Section 2. Do not use `-> str` just because the final program output is text: look at the specific value returned by **that function**.

Type annotations **do not convert or enforce values at runtime**. `input()` still returns `str`, even when the receiving function says `value: int` or `value: float`. Convert input explicitly with `int(...)` or `float(...)` before passing it to the function. Python can run a call with an incorrectly typed argument; an editor such as PyCharm may flag the mismatch, and the code may fail later when it tries to use that value. An annotation is a readable contract, not a replacement for input conversion, a docstring, or the function's actual `return` statement.

For example, if the input line is `12`, then `text_value = input()` stores the string `"12"`. `number = int(text_value)` creates the integer `12`, which can be passed to a parameter annotated `: int`. Passing `text_value` directly to that parameter would **not** convert it. This guide uses annotations to make function contracts clear; check your official question sheet for which annotations, if any, are explicitly required in your submission.

Avoid adding type syntax you have not learned, such as `tuple[int, int]`, to these lab examples. A function can return one `int`, one `float`, or one formatted `str`; if the question requires several printed values, you can also call separate functions and print each returned value. Follow the return behavior required by your own section's question sheet.

## 4. Docstrings: required for every function

A docstring is a string placed immediately after the function header. It should explain purpose, parameters, and return value. A comment beginning with `#` elsewhere in the function is not a replacement for its docstring.

```python
def grams_to_kilograms(grams: float) -> float:
    """Convert grams to kilograms.

    Args:
        grams: A mass measured in grams.

    Returns:
        The same mass measured in kilograms.
    """
    kilograms = grams / 1000
    return kilograms
```

For Lab 03, also document any **inner helper function** you define. Type annotations and docstrings do different jobs: `grams: float` and `-> float` identify expected types, while the docstring explains that the units change from grams to kilograms. Give names that explain what the values mean. Comments are useful for non-obvious steps, but avoid a comment on every line.

## 5. Default arguments are part of the call, not the input format

A default value is used when the caller omits that argument. Required parameters must come before parameters with defaults.

```python
def add_tip(amount: float, tip_rate: float = 0.05) -> float:
    """Return an amount after adding a tip.

    Args:
        amount: The original amount.
        tip_rate: A decimal tip rate; defaults to 0.05.

    Returns:
        The amount including the tip.
    """
    total = amount * (1 + tip_rate)
    return total


standard_total = add_tip(100.0)       # Uses 0.05.
custom_total = add_tip(100.0, 0.10)  # Overrides the default.
print(f"{standard_total:.2f}")     # 105.00
print(f"{custom_total:.2f}")       # 110.00
```

This example demonstrates both kinds of call. The annotation on `tip_rate` comes **before** `= 0.05`, and `-> float` describes the numeric amount returned by `return total`. In an assigned problem that specifically says to use the default, call the function with **only** the required argument(s). Do not invent a third input value or an `if` to decide whether one was supplied. A percentage written as 5% is the decimal `0.05` in a calculation, not `5`.

Arguments can also be named, as in `add_tip(amount=100.0, tip_rate=0.10)`. Whether you use positional or named arguments, the values must match the function's parameters. For the assigned problems, keep the required function names and parameter order from the question sheet.

## 6. Nested helper functions

A helper may live inside a function when it is only needed there. The outer function can call the helper; code outside the outer function cannot directly call that inner name.

```python
def report_height(height_meters: float) -> str:
    """Return a formatted height in centimeters.

    Args:
        height_meters: A height measured in meters.

    Returns:
        A string showing the height in centimeters.
    """
    def to_centimeters(value_meters: float) -> float:
        """Convert a height from meters to centimeters.

        Args:
            value_meters: A height measured in meters.

        Returns:
            The height measured in centimeters.
        """
        result = value_meters * 100
        return result

    height_cm = to_centimeters(height_meters)
    message = f"Height: {height_cm:.2f} cm"
    return message


height = 1.75
result = report_height(height)
print(result)  # Height: 175.00 cm
```

Notice that **both** functions have parameter and return annotations as well as docstrings. The helper returns a single number (`-> float`), while the outer function returns a single formatted string (`-> str`). Do not return a tuple when the question asks for one string or one number. Do not replace the required helper with an unrelated top-level function if the question explicitly says “inside.”

## 7. Importing a module and using its names

A module provides names such as functions and constants. Import it before using them:

```python
import math

root = math.sqrt(81)
circle_factor = math.pi
print(root)           # 9.0
print(f"{circle_factor:.2f}")  # 3.14
```

Use `**` for exponentiation (`3 ** 2` is `9`); `^` is **not** Python's power operator. `math.sqrt(value)` needs a nonnegative `value` for real-number output. In the assigned vector task, the input excludes the zero vector, so its magnitude is positive and division by the magnitude is defined. In other assigned division tasks, check the stated constraints: positive fuel, positive wavelength, positive frequency, and positive default speed make those divisions valid. You do not need to add invalid-input branches when the handout guarantees valid data.

You may also see `from math import sqrt`, after which you call `sqrt(81)` without the `math.` prefix. Pick one style and use its matching call syntax. If you use `math.sqrt(...)`, you need `import math` first. Avoid naming your own program `math.py`, because Python may import that file instead of the standard module. The current Lab 03 questions ask for one `.py` file per problem; do not invent a second module file unless your official sheet specifically requests one.

## 8. Read exactly the input that exists

`input()` returns a string representing **one line**. A function annotation does not change that. Convert each input value to the type its parameter expects before calling the function.

### One value on one line

```python
count = int(input())
measurement = float(input())  # Use this as a separate example, not an extra lab line.
```

The two statements above illustrate two alternatives. Do **not** put both in a program whose question specifies only one input line.

### Two values on the same line

For input such as `12.5 4`, where the first value is a float and the second is an integer:

```python
parts = input().split()
first = float(parts[0])
second = int(parts[1])
```

`input()` reads `"12.5 4"`; `.split()` gives the two text pieces; conversion then gives numbers. Whitespace-only splitting also handles extra spaces and tabs between values. `float(input())` on the whole line would fail because `"12.5 4"` is not one number. For several same-type values on a line, convert each element after splitting; do not assume `.split()` itself converts text to numbers.

### Values on separate lines

For an input layout with one number per line:

```python
mass = float(input())
speed = float(input())
height = float(input())
```

For **two lines with three values each**, read each line separately:

```python
first_line = input().split()
second_line = input().split()

first_x = float(first_line[0])
first_y = float(first_line[1])
first_z = float(first_line[2])
second_x = float(second_line[0])
second_y = float(second_line[1])
second_z = float(second_line[2])
```

This is a reading pattern, not a solution to any assigned calculation. If the question has only one line, do not call `input()` a second time: the program will wait for input that never arrives. In Codeforces, do not use prompts such as `input("Enter a number: ")`; prompts create unwanted output.

## 9. Format output exactly

An f-string formats a value at the point where you build the output. A number's **value** and its displayed **format** are different things.

| Code | Output | When useful |
| --- | --- | --- |
| `f"{7:.2f}"` | `7.00` | Exactly two digits after the decimal point. |
| `f"{0.5:.4f}"` | `0.5000` | Exactly four digits after the decimal point. |
| `f"{3:02d}"` | `03` | A two-digit integer field with a leading zero. |
| `f"{12.345:.2f}"` | `12.35` | Display rounding, not truncation. |

For a **single returned string**, format inside the function, annotate its return as `-> str`, and print that one return value. If a function returns a numeric value, annotate that numeric return type and format it later when printing. For **two output lines**, print two values on separate lines:

```python
first_result = 7.0
second_result = 0.5
print(f"{first_result:.2f}")
print(f"{second_result:.2f}")
```

The code prints `7.00` and `0.50` on separate lines. Add labels or units only if the question includes them. Preserve capitalization, the space before a unit, commas, parentheses, colons, the percent sign, and the number of spaces between values. For example, `"3.00Hz"` and `"3.00 Hz"` are different output strings.

Floating-point values cannot represent every decimal exactly. Small representation differences can affect a value very close to a rounding boundary. Use the precision requested by the question (`.2f`, `.4f`, etc.), test nearby values, and do not manually cut digits off a string. If a formatted positive value is very small, `0.00` can be the correct two-decimal display even though the underlying number is not zero.

## 10. A complete independent example

This small program reads a decimal parcel weight and an integer number of parcels from **one line**. It returns one formatted label. It is not one of the Lab 03 questions.

```python
def make_parcel_label(weight_kg: float, parcels: int) -> str:
    """Build a label for a parcel shipment.

    Args:
        weight_kg: Total shipment weight in kilograms.
        parcels: Number of parcels in the shipment.

    Returns:
        A formatted shipment label.
    """
    label = f"Shipment: {weight_kg:.2f} kg, {parcels} parcels"
    return label


parts = input().split()
weight_kg = float(parts[0])
parcels = int(parts[1])
result = make_parcel_label(weight_kg, parcels)
print(result)
```

If the input is `2.5 3`, the output is `Shipment: 2.50 kg, 3 parcels`. Trace the four stages: read text, convert to `float` and `int` to match the parameters, call the function, and print its single returned `str`. To test the display edge, try a valid input with `0` weight if your **own** exercise permits zero; never assume a lab problem permits it without reading its constraints.

## 11. Extreme-case testing for the current four sections

Start with the samples on your official question sheet, then try these **additional valid cases**. These are test ideas, not answer keys. Run only the row for the section and problem you were assigned. Do not add handling for out-of-range values unless an instructor changes the specification.

| Section | Problem | Valid edge or stress inputs to consider | What to inspect |
| --- | --- | --- | --- |
| 261 | 1 | `0`, `59`, `60`, `3599`, `3600`, `86399` seconds | Unit boundaries and leading zeroes in all three fields. |
| 261 | 2 | Zero hours; maximum stated hours and rate; fractional hours | Default bonus rate is used, and money has two decimal places. |
| 261 | 3 | An axis-aligned nonzero vector; negative components; small nonzero components such as `0.001` | Division is valid; signs, magnitude, and four-place display are correct. Never test `(0, 0)` as a valid input. |
| 261 | 4 | `-273.15`, `0`, and another valid non-integer Celsius value | Boundary temperature, two distinct output lines, two decimal places. |
| 262 | 1 | Score/attendance at `0 0` and `100 30` | Exact label, separator, spaces, and pluralization required by the sheet. |
| 262 | 2 | Minimum principal, maximum principal, and the maximum stated rate | Function uses the **default** compounding frequency; display has two decimals. |
| 262 | 3 | Equal points; mixed negative and positive coordinates; coordinates near the stated endpoints | Input is **two** lines; signs and three formatted values are correct. |
| 262 | 4 | Valid positive mass with zero velocity and/or zero height | Zero energies are displayed correctly on two separate lines. |
| 263 | 1 | Minimum price with one item; upper price and quantity; fractional prices | Mixed float/int input, label, and two-decimal total. |
| 263 | 2 | Minimum and maximum stated distance | Required argument only; default handling fee and precision. |
| 263 | 3 | All scores `0`, all scores `100`, and mixed fractional scores | Inner helper has its own docstring; percent symbol and two decimals. |
| 263 | 4 | A very small **positive** amount; amounts near multiples of the stated conversion factors | Two different lines and units; small positive amounts can display as `0.00`. |
| 264 | 1 | Minimum valid distance and fuel; very large distance with small positive fuel | Division stays defined, label and `km/L` unit match exactly. |
| 264 | 2 | Smallest and largest stated file size | The function call uses the default speed; MB and Mb are not the same unit. |
| 264 | 3 | Negative coordinates, symmetric coordinates, and fractional coordinates | Four inputs on **one** line; parentheses, comma, spaces, and two decimals. |
| 264 | 4 | Small **positive** wavelength and frequency such as `0.001`; larger positive values | No zero denominator; two lines use the correct, different units. |

An “extreme” test must still satisfy the specific problem's constraints. For example, `0` is a valid number of seconds for section 261 problem 1, but zero is **not** a valid vector for section 261 problem 3. Zero velocity is permitted in section 262 problem 4, while zero fuel is not permitted in section 264 problem 1.

### A reliable test routine

1. Copy one sample input exactly and verify the output character by character.
2. Pick the smallest valid value for each parameter, then the largest valid value.
3. Test zero **only where allowed**; otherwise use the smallest positive valid value.
4. Test signs and fractional values where the constraints allow them.
5. Test a value near an output-rounding boundary; verify decimal places and units.
6. Rerun after the final edit, then submit the **same** `.py` file on both platforms.

## 12. Debugging checklist

| Symptom | Likely cause | Check |
| --- | --- | --- |
| The program waits forever or gets `EOFError` online | Too many `input()` calls | Count the input lines in the question sheet. |
| `ValueError` while reading a line with several values | Tried to convert the whole line at once | Split the line, then convert each element. |
| `TypeError` during arithmetic | Forgot `int()`/`float()`, or used the wrong number of arguments | Inspect input conversions and the function signature. |
| An annotated parameter still receives text from `input()` | Assumed `: int` or `: float` converts the argument | Convert with `int(...)` or `float(...)` before the call. |
| The return annotation disagrees with the result | Used `-> float` for a formatted string, or `-> str` for an unformatted number | Check the expression following `return`; an f-string returns `str`. |
| `NameError` for a helper | Called it outside its enclosing function, or misspelled its name | Check scope, indentation, and spelling. |
| A correct-looking result is marked wrong | Output has a prompt, missing decimal zeroes, wrong units/spaces, or an extra line | Compare the output with the required format exactly. |
| A function call produces `None` | Function printed instead of returning | Ensure the required value reaches a `return` statement. |
| A power calculation behaves strangely | Used `^` instead of `**` | Use Python's exponentiation operator `**`. |
| Division by zero occurs on a supposedly valid test | Wrong variable was used or a stated positive constraint was overlooked | Trace the denominator; check that the test is actually valid. |

Use temporary `print()` calls in PyCharm to inspect intermediate values while debugging, but remove them before submitting. They become extra output in an online judge. Do not add `if`/`else`, exception handling, or input prompts to compensate for inputs the sheet says cannot occur.

## 13. Optional extra practice (not part of the assigned lab)

These four **new** exercises are for students who want more practice. They are not section problems and are **not required submissions**. Write your own solution with parameter and return annotations **and** a docstring for every function; no solutions are provided here. Assume the stated inputs are valid.

### A. Bonus points — default argument

Define `final_points(base_points: int, bonus: int = 5) -> int` to **return** the sum of a nonnegative integer `base_points` and the default bonus. Read only `base_points` from one line; call the function without passing a bonus. Print `Points: X`.

| Input | Expected output |
| --- | --- |
| `12` | `Points: 17` |
| `0` | `Points: 5` |

Hint: The missing argument is supplied by the function definition, not by an extra input line.

### B. Study log — mixed input and one formatted return value

Define `study_summary(minutes: int, sessions: int) -> str` to return one string in the format `Study: X minutes over Y sessions`. Read two nonnegative integers from **one** line. Print only the returned string.

| Input | Expected output |
| --- | --- |
| `45 3` | `Study: 45 minutes over 3 sessions` |
| `0 0` | `Study: 0 minutes over 0 sessions` |

Hint: `.split()` gives strings; convert both values to integers before passing them to the function.

### C. Stage rental — nested helper

Define `rental_cost(hours: float, rate_per_hour: float) -> float` with an inner helper `usage_cost(hours: float, rate_per_hour: float) -> float` that returns `hours * rate_per_hour`. The outer function returns the helper's result plus a fixed setup charge of `2.50`. Read two nonnegative floats on one line. Print `Cost: X.XX`. Both functions need docstrings.

| Input | Expected output |
| --- | --- |
| `1.5 12.0` | `Cost: 20.50` |
| `0 12.0` | `Cost: 2.50` |

Hint: The inner function should return one number; the outer function should return one number; format only when printing.

### D. Circle measurements — module and two output lines

Import `math`. Define `circumference(radius: float) -> float` to return `2 * math.pi * radius` and `circle_area(radius: float) -> float` to return `math.pi * radius ** 2`. Read one nonnegative float. Print `Circumference: X.XX` on the first line and `Area: X.XX` on the second. Give each function a docstring.

| Input | Expected output |
| --- | --- |
| `1` | `Circumference: 6.28`<br>`Area: 3.14` |
| `0` | `Circumference: 0.00`<br>`Area: 0.00` |

Hint: `math.pi` is a number from the imported module; it is not a function call.

## Before you submit the assigned Lab 03

- Use the **current Fall 2026 sheet for your own section**, not another section's questions and not an older year's sheet.
- Match every required function name, parameter, return behavior, docstring, and requested nested helper. Practice basic parameter and return annotations, but remember that annotations do not convert input or replace a docstring.
- Use only the input lines stated in each problem; no prompts or invented optional inputs.
- Check valid edge cases and exact output formatting after the final code change.
- Use one `.py` file per assigned problem, named as required by the handout, such as `Lab3_P1_<YourStudentID>.py` for Problem 1.
- Submit the same code to the assigned Codeforces contest and Canvas before the lab deadline. The optional exercises above do not replace the assigned problems.

For submission steps, see the [Codeforces and Canvas guide](../lab-tutorial.md). For two values on one line, the [Lab 02 input tutorial](../lab-02/README.md#reading-two-numbers-on-the-same-line) has another worked explanation.
