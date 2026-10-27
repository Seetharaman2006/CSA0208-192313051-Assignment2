# C PROGRAMMING ASSIGNMENT 2

## Grama Jyothi Rural Electricity Co-operative Billing Program

**Department:** Electrical and Electronics Engineering (EEE)
**Assignment:** 2
**Programming Language:** C
**Compiler:** GCC

---

# TASK 1: UNDERSTAND HEADER FILES AND CONDITIONAL COMPILATION

## 1. Problem Description

The Grama Jyothi electricity billing program requires a structured approach to organize its declarations, functions, and constants. Header files help separate function declarations from their implementations, while conditional compilation allows selected sections of code to be included or excluded during compilation.

The program must avoid multiple header inclusions, support debugging, and make tariff-related declarations reusable.

## 2. Inputs and Outputs

### Inputs

* Previous meter reading.
* Current meter reading.
* Debug configuration selected during compilation.

### Outputs

* Electricity units consumed.
* Energy charge and fixed charge.
* Total electricity bill.
* Debug messages when debugging is enabled.
* Error messages for invalid input.

## 3. Header File Structure

A header file named `grama_jyothi.h` can contain the function prototypes, constants, and declarations shared by the program.

Example:

```c
#ifndef GRAMA_JYOTHI_H
#define GRAMA_JYOTHI_H

#include <stdint.h>

#define MAX_READING 65535U
#define FIXED_CHARGE_PAISE 5000LL

uint32_t calculate_units(uint32_t previous, uint32_t current);
int64_t calculate_bill(uint32_t units);

#endif
```

The `#ifndef`, `#define`, and `#endif` directives form an **include guard**. They prevent the contents of the header from being processed repeatedly within the same translation unit.

## 4. Conditional Compilation

Conditional compilation allows code to be included only when a specified preprocessor condition is satisfied.

Example:

```c
#ifndef DEBUG
#define DEBUG 0
#endif

#if DEBUG
#define DEBUG_PRINT(...) do { \
    printf("[DEBUG] ");       \
    printf(__VA_ARGS__);      \
} while (0)
#else
#define DEBUG_PRINT(...) ((void)0)
#endif
```

When `DEBUG` is set to `1`, debug messages are enabled. When it is set to `0`, calls to `DEBUG_PRINT` do not produce debug output.

The program can be compiled with debugging enabled using:

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic -DDEBUG=1 main.c billing.c -o billing
```

## 5. Difference Between Compile-Time and Runtime Debugging

| Compile-time debugging                                      | Runtime debugging                                                                          |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Controlled by preprocessor directives or compiler options.  | Controlled while the program executes.                                                     |
| Code can be excluded during preprocessing.                  | Code is already compiled and executes conditionally.                                       |
| Useful for removing debug-only code from production builds. | Useful for controlling diagnostic output without rebuilding, if designed for that purpose. |
| Example: `-DDEBUG=1`.                                       | Example: a runtime variable such as `debug_enabled`.                                       |

## 6. Difference Between Header Inclusion Methods

| Syntax                      | Purpose                                                                                        |
| --------------------------- | ---------------------------------------------------------------------------------------------- |
| `#include <stdio.h>`        | Typically searches standard or implementation-defined system include locations.                |
| `#include "grama_jyothi.h"` | Typically searches the source file's directory first, then other configured include locations. |

The exact search behavior is determined by the compiler implementation and its configuration.

## 7. Alternative Approach

Instead of using a shared header file, all declarations and functions could be placed in one source file.

**Advantages:**

* Simple structure for small programs.
* Fewer files to manage.

**Disadvantages:**

* Difficult to maintain as the program grows.
* Code reuse becomes less convenient.
* Mixing declarations, calculations, and user interaction can reduce readability.

A separate header file and implementation file provide better organization for a larger billing system.

## 8. Time and Space Complexity

Header inclusion and conditional compilation occur before the program executes.

* Runtime complexity of header processing: Not applicable to the executed program.
* Runtime complexity of calculating one bill: O(1).
* Auxiliary space for the calculation: O(1).

## Conclusion

Header files improve code organization and reuse, while include guards prevent repeated declarations. Conditional compilation supports configurable debugging and helps maintain a clear separation between development and production builds.

---

# TASK 2: READ AND PRINT DATA CORRECTLY

## 1. Problem Description

The electricity billing program must read meter readings correctly, validate user input, calculate electricity consumption, and display the result using appropriate format specifiers.

It must also handle meter rollover, format monetary amounts correctly, and prevent invalid readings from producing incorrect bills.

## 2. Inputs and Outputs

### Inputs

* Previous meter reading.
* Current meter reading.
* Household or meter identification, if required.

### Outputs

* Validated meter readings.
* Electricity units consumed.
* Total electricity bill.
* Error message if input is invalid.

## 3. Input and Output Format Specifiers

C requires format specifiers that match the types of the supplied arguments.

| Data type        | `scanf()` example           | `printf()` example |
| ---------------- | --------------------------- | ------------------ |
| `int`            | `%d`                        | `%d`               |
| `unsigned int`   | `%u`                        | `%u`               |
| `long`           | `%ld`                       | `%ld`              |
| `long long`      | `%lld`                      | `%lld`             |
| `double`         | `%lf`                       | `%f`               |
| Character string | `%31s` for a 32-byte buffer | `%s`               |

The required format specifier depends on the exact data type. In particular, `%lf` is used to read a `double` with `scanf()`, while `%f` is used to print a `double` with `printf()`.

## 4. Calculating Electricity Consumption

If the current reading is greater than or equal to the previous reading:

**Units consumed = Current reading − Previous reading**

Example:

* Previous reading = 1200
* Current reading = 1530
* Units consumed = 1530 − 1200 = **330 units**

If the current reading is lower than the previous reading, a rollover may have occurred.

For a 16-bit meter register:

**Units consumed = (65535 − Previous reading) + 1 + Current reading**

Example:

* Previous reading = 65400
* Current reading = 120
* Units consumed = (65535 − 65400) + 1 + 120
* Units consumed = **256 units**

This calculation assumes that no more than one rollover occurred and that the readings are valid for the specified meter.

## 5. Input Validation

The program should verify that:

1. Input values are numeric.
2. Meter readings fall within the valid range of 0–6553
