---
title: 'C Primer Plus: Chapter 3 Write-up'
date: 2026-11-13 01:11:42
tags: [C Primer Plus, Learning Notes, Keywords, Data Types, Escape Sequences]
categories: [C Programming]
katex: true
---

# Data and C

## Review Questions

1. Which data type would you use for each of the following kinds of data (sometimes more than one type could be appropriate)?
   a. The population of East Simpleton.
   b. The cost of a movie on DVD.
   c. The most common letter in this chapter.
   d. The number of times that the letter occurs in this chapter.
   **Answer:** 
   a. I would use type unsigned int for the population of East Simpleton.
   b. I would use type float for the cost of a movie on DVD.
   c. I would use type char for the most common letter in this chapter.
   d. I would use type unsigned short for the number of times that the letter occurs in this chapter.
   <!-- more -->
   
2. Why would you use a type long variable instead of type int ?
   **Answer:** Because the value may exceed the range of `int`. The C standard only guarantees that `int` is **at least 16 bits**; on most modern systems it is 32 bits, with a range of -2,147,483,648 to 2,147,483,647. `long` is guaranteed to be **at least as wide as `int`** (and at least 32 bits), so when a computation may produce values outside the `int` range, `long` (or `long long`) is the safer choice. Note that the exact widths are implementation-defined, so portable code should use `<limits.h>` (`INT_MAX`, `LONG_MAX`) or the fixed-width types from `<stdint.h>`.

3. What portable types might you use to get a 32-bit signed integer,  and what would the rationale be for each choice?
   **Answer:** First, I may use `int32_t`. `C99` provides an optional exact-width integer type called `int32_t`. It is a signed integer type that is exactly 32 bits wide — no less and no more. But the underlying system might not support these exact-width integer types. Second, I may use `int_least32_t`. `C99` and `C11` provide a required category of names that promise the type is at least big enough to meet the specification and that no other type that can do the job is smaller. These types are called minimum width types. Third, I may use `int_fast32_t`. `C99` and `C11` define a set of necessary types that allow the fastest computations. These are called the fastest minimum width types. The `int_fast32_t` is one of them that allows the fastest calculations for 32-bit signed values. Finally, `intmax_t` is also a good choice, which stands for the largest available signed integer type.

4. Identify the type and meaning, if any, of each of the following constants:
   a. `'\b'`
   b. `1066`
   c. `99.44`
   d. `0XAA`
   e. `2.0e30`
   **Answer:** 
   a: `'\b'`is an int constant, and it is one of the escape sequences, which is called backspace character, whose meaning is to move the cursor back one position (backspace). Usually, backspacing does not erase the characters that are backed over, but it depends on implementations.
   b: `1066` is an int constant. 
   c: `99.44` is a double constant.
   d: `0XAA` is an integer constant in hexadecimal notation, whose decimal value is 170.
   e. `2.0e30` is a double constant.

5. Dottie Cawm has concocted an error-laden program. Help her find mistakes.
   ```c
   include <stdio.h>
   main
   {
       float g; h;
       float tax, rate;
       g = e21;
       tax = rate * g;
   }
   ```

   **Answer:**
   The program contains **five syntax errors** plus **two semantic/logical problems**. They are listed separately below, because only the first group prevents compilation.

   **Syntax errors (the program will not compile)**

   1. **Missing `#` before `include`.** It must be `#include <stdio.h>`; without the `#`, the preprocessor directive is not recognized.
   2. **`main` lacks a return type and a parameter list.** The standard form is `int main(void)` (or `int main(int argc, char *argv[])`). Writing bare `main` is not a valid function definition in C99 and later. (Note: `void main` is *not* standard either — the return type must be `int`.)
   3. **Missing braces/parentheses around the function body header.** The definition should be `int main(void) { ... }`; as written, `main` is followed directly by `{`, with no parameter list.
   4. **`float g; h;` uses a semicolon where a comma is required.** It should be `float g, h;`. As written, `h;` is a separate, malformed declaration.
   5. **`e21` is not a valid floating constant.** C requires a digit (integer or fractional part) before the exponent: `1e21` is valid, `e21` is not. This is a syntax error.
   
   **Semantic / logical problems (compile, but are wrong or unreliable)**
   
   1. **`h` is declared but never used.** Not an error, but superfluous; it should be removed (or used).
   2. **`rate` is used uninitialized in `tax = rate * g;`.** Reading an uninitialized automatic variable yields an **indeterminate value**; the result of `tax` is therefore unpredictable. This is a logic error, not a syntax error.
   3. **`g = 1e21;` loses precision.** `1e21` is representable in `float` only approximately (see analysis below); this is a precision issue, not an error. It does not prevent compilation.
   
   > Note on counting: many textbook answer keys count only the **syntax errors** (items 1–5, sometimes also treating the missing `#include` as one), giving "5 or 6 errors." My list separates syntax from semantics so the count is unambiguous.
   
   **Why `1e21` cannot be stored exactly in a `float`**
   
   A normal IEEE-754 `float` is stored as
   $$
   N = (-1)^{S} \times 1.M \times 2^{E-127}
   $$
   with 1 sign bit, 8 exponent bits (bias 127), and 23 fraction bits (24 significant bits including the implicit leading 1). Write
   $$
   10^{21}=2^{21}×5^{21}.
   $$
   The factor $2^{21}$ is absorbed into the exponent, so the significand that must be stored exactly is $5^{21}$. Its binary length is
   $$
   \lfloor \log_25^{21} \rfloor+1=⌊21\log_25⌋+1=⌊48.7604⌋+1=49 \text{bits}.
   $$
   A `float` provides only **24** significant bits, so $5^{21}$ (hence $10^{21}$) cannot be represented exactly; the stored value is the nearest `float`, with a relative error on the order of $2^{-24}$. (For reference, $10^{21}$ itself has 70 binary digits, but the 21 trailing zeros from $2^{21}$ go into the exponent, leaving 49 significant bits — this is the number that matters.)
   
   **Fixed version**
   
   ```c
   #include <stdio.h>
   
   int main(void)
   {
       float g;
       float tax, rate = 0.45f;
       g = 1e21f;          /* still inexact, but syntactically valid */
       tax = rate * g;
       return 0;
   }
   ```
   
   Changes made: added `#`, gave `main` the standard `int main(void)` form, replaced `;` with `,` in the declaration, wrote `1e21` (with a leading digit), initialized `rate`, removed the unused `h`, and added `return 0;`.
   
6. Identify the data type (as used in declaration statements) and the `printf()` format specifier for each of the following constants:
   **Answer:**
   In `printf()`, `%f` expects a `double` argument. A `float` argument is promoted to double because of the default argument promotion in function call. So `%f` works for both. C99 accepts the specifier `%lf`(the `l` has no effect). In `scanf()`, however, `%f` corresponds to `float` and `%lf` to `double`, and this distinction is necessary.
   
   | Constant         | Type     | Specifier |
   | :--------------- | :------- | :-------- |
   | **a.** `12`      | `int`    | `%d`      |
   | **b.** `0X3`     | `int`    | `%#X` |
   | **c.** `'C'`     | `char`   | `%c`      |
   | **d.** `2.34E07` | `double` | `%e`      |
   | **e.** `'\040'`  | `char`   | `%c`      |
   | **f.** `7.0`     | `double` | `%f`      |
   | **g.** `6L`      | `long`   | `%ld`     |
   | **h.** `6.0f`    | `float`  | `%f`      |

7. Identify the data type (as used in declaration statements) and the `printf()` format specifier for each of the following constants (assume a 16-bit int):
   **Answer:**

   | Constant         | Type          | Specifier |
   | ---------------- | ------------- | --------- |
   | **a.** `012`     | `int`         | `%#o`     |
   | **b.** `2.9e05L` | `long double` | `%Lf`     |
   | **c.** `'s'`     | `char`        | `%c`      |
   | **d.** `100000`  | `long`        | `%ld`     |
   | **e.** `'\n'`    | `char`        | `%c`      |
   | **f.** `20.0f`   | `float`       | `%f`      |
   | **g.** `0x44`    | `int`         | `%#x`     |
   | **h.** `-40`     | `int`         | `%d`      |

8. Suppose a program begins with these declarations:
   ```c
   int imate = 2;
   long shot = 53456;
   char grade = 'A';
   float log = 2.71828;
   ```

   Fill in the proper type specifiers in the following `printf()` statements:
   ```c
   printf("The odds against %_ were %_ to 1.\n", imate, shot);
   printf("A score of %_ is not an %_ grade.\n", log, grade);
   ```

   **Answer:** `d`, `ld`, `f`, `c`.

9. Suppose that `ch` is a type `char` variable. Show how to assign the carriage-return character to `ch` by using an escape sequence, a decimal value, an octal character constant, and a hex character constant. (Assume ASCII code values).
   **Answer:**

   ```c
   char ch;
   /* 1. By using an escape sequence. */
   ch = '\r';
   
   /* 2. By using a decimal value. */
   ch = 13;
   
   /* 3. By using an octal character constant. */
   ch = '\015';
   
   /* 4. By using an hex character constant. */
   ch = '\x0D';
   ```

10. Correct this silly program. (The / in C means division.)
   ```c
   void main(int) / this program is perfect /
   {
       cows, legs integer;
       printf("How many cow legs did you count?\n);
       scanf("%c", legs);
       cows = legs / 4;
       printf("That implies there are %f cows.\n", cows)
   }
   ```

   **Answer:** 

   1. The program has to begin with`#include<stdio.h>`，because it uses the `printf()` function;

   2. There is no need for `main()` adding an argument.
      
   3. The comment using `/.../` is wrong. It should start with `"//"` or be enclosed by `"/*"` and `"*/"`;

   4. The declaration of multiple integer variables should obey the standard like the following:
      ```c
      int variable_a, variable_b, variable_c;
      ```

   5. The information argument in `printf()` should be enclosed by double quotes.

   6. `scanf()` should be used like the following:

      ```c
      scanf("specifier(starts with '%')", &variable_name);
      ```

   7. The specifier for type int is `%d`, not `%c` or `%f`;

   8. Each statement should end with a semicolon.

   Therefore, the fixed version of this program should be like the following:
   ```c
   #include <stdio.h>
   
   int main(void) /* this program is perfect */
   {
   	int cows, legs;
   	printf("How many cow legs did you count?\n");
   	scanf("%d", &legs);
   	cows = legs / 4;
   	printf("That implies there are %d cows.\n", cows);
   	return 0;
   }
   ```

11. Identify what each of the following escape sequences represents:
    **a.** `\n`
    **b.** `\\`
    **c.** `\"`
    **d.** `\t`
    **Answer:** 
    a: `\n`represents a newline character, which sets the active position to the beginning of the next line.
    b: `\\` represents a backslash.
    c: `\"` represents a double quote.
    d: `\t` represents a horizontal tab character, which moves the active position to the next horizontal tab stop (typically, they are found at character positions 1, 9, 17, 25 and so on).

## Programming Exercises

1. Find out what your system does with integer overflow, floating-point overflow, and floating-point underflow by using the experimental approach; that is, write programs having these problems. (You can check the discussion in **Chapter 4** of `limits.h` and `float.h` to get guidance on the largest and smallest values).
   **Answer:** 
   **Program**
   
   ```c
   #include <stdio.h>
   #include <limits.h>
   #include <float.h>
   #include <math.h>
   
   int main(void)
   {
       /* Integer overflow: unsigned wrap-around is well defined. */
       unsigned int u_max = UINT_MAX;
       unsigned int u_wrap = u_max + 1u;
   
       /* Signed overflow is undefined behavior; observe it only as an
          implementation detail, never rely on it. */
       int i_max = INT_MAX;
       int i_min = INT_MIN;
   
       /* Floating-point overflow: exceed FLT_MAX. */
       float f_over = FLT_MAX * 2.0f;
   
       /* Floating-point underflow: shrink below FLT_MIN (normal). */
       float f_under = FLT_MIN / 2.0f;
   
       printf("UINT_MAX + 1        = %u\n", u_wrap);
       printf("INT_MAX             = %d\n", i_max);
       printf("INT_MIN             = %d\n", i_min);
       printf("FLT_MAX * 2.0f      = %f\n", f_over);
       printf("FLT_MIN / 2.0f      = %e (subnormal)\n", f_under);
       printf("isinf(f_over)       = %d\n", isinf(f_over));
       printf("f_under != 0.0f     = %d\n", f_under != 0.0f);
   
       return 0;
   }
   ```
   
   **Sample output (x86-64, gcc, IEEE-754 binary32)**
   
   ```text
   UINT_MAX + 1        = 0
   INT_MAX             = 2147483647
   INT_MIN             = -2147483648
   FLT_MAX * 2.0f      = inf
   FLT_MIN / 2.0f      = 5.877472e-39 (subnormal)
   isinf(f_over)       = 1
   f_under != 0.0f     = 1
   ```
   
   **Integer overflow**
   
   - **Unsigned integers** wrap around modulo 2^N. `UINT_MAX + 1u` is **defined** to be `0`; this is the portable way to demonstrate wrap-around.
   - **Signed integers**: `INT_MAX + 1` is **undefined behavior (UB)** in C. The value you observe (e.g. wrapping to `INT_MIN` on two's-complement hardware) is an **implementation detail**, not a language guarantee. The standard permits anything, including a trap or an optimized-away result. My program therefore only *prints* `INT_MAX`/`INT_MIN` and does not compute `INT_MAX + 1`.
   - In two's-complement implementations, `INT_MAX` is `0111...1111` and adding 1 produces `1000...0000`, i.e. `INT_MIN`; this explains the observed wrap, but do not cite it as standard behavior.
   
   **Floating-point representation (IEEE-754 binary32)**
   
   A normal `float` is stored as
   $$
   (-1)^S \times 1.M \times 2^{E-127},
   $$
   where $S$ is the sign bit, $E$ is the 8-bit biased exponent ($1 \leq E \leq 254$ for normals), and $M$ is the 23-bit fraction. The largest finite value is
   $$
   (2-2^{-23})×2^{127}=2^{128}-2^{104}≈3.4028235×10^{38}=\text{FLT\_MAX}.
   $$
   **Why `FLT_MAX * 2.0f` overflows**
   
   Floating-point addition/subtraction proceeds in six steps:
   
   1. **Negate** (subtraction only): flip the sign bit of the subtrahend.
   2. **Align exponents**: shift the significand of the smaller-exponent operand right by the exponent difference; keep guard/round/sticky bits.
   3. **Add significands** with signs (same sign → add magnitudes; opposite → subtract and keep the larger magnitude's sign). Call the result $R$, exponent $E$.
   4. **Normalize**: if $R \geq 2$, shift right and $E += 1$; if $R < 1$, shift left and $E -= 1$ until $R \in [1,2)$.
   5. **Round** $R$ to 23 fraction bits (round-to-nearest, ties-to-even); a carry may require one more right-normalization.
   6. **Boundary check**: after normalization, $E ≥ 255$ → **overflow** → $±inf$; $E ≤ 0$ → **underflow** → subnormal or zero.
   
   `FLT_MAX * 2.0f` doubles the significand while keeping the exponent, so $E$ exceeds 254 and the result rounds to $+inf$. Likewise `-FLT_MAX * 2.0f` gives $-\inf$.
   
   **Underflow**
   
   `FLT_MIN` (the smallest **normal** positive `float`) is $2^{-126} ≈ 1.175494e-38$. Dividing by 2 gives $2^{-127}$, which is below the normal range; it is representable only as a **subnormal** (`≈ 5.877472e-39`), with reduced precision. Repeated division eventually rounds to `±0`. Note the terminology: "floating-point underflow" here means *gradual underflow into the subnormal range*, not an integer-style wrap.
   
2. Write a program that asks you to enter an ASCII code value, such as 66, and then prints the character having that ASCII code.
   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	int acv;
   	printf("Please enter an ASCII code value: ");
   	scanf("%d", &acv);
   	printf("The character having that ASCII code is \"%c\".", acv);
   	
   	return 0;
   }
   ```

   The output result is following.

   ```text
   Please enter an ASCII code value: 66
   The character having that ASCII code is "B".
   ```

3. Write a program that sounds an alert and then prints the following text.
   ```text
   Startled by the sudden sound, Sally shouted, "By the Great Pumpkin, what was that!"
   ```

   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	printf("\a");
   	printf("Startled by the sudden sound, Sally shouted,"
   		"\"By the Great Pumpkin, what was that!\"\n");
   
   	return 0;
   }
   ```

4. Write a program that reads in a floating-point number and prints it first in decimal-point notation, then in exponential notation, and then, if your system supports it, p notation. Have the out put use the following format (the actual number of digits displayed for the exponent depends on the system):
   ```text
   Enter a floating-point value: 64.25
   fixed-point notation: 64.250000
   exponential notation: 6.425000e+01
   p notation: 0x1.01p+6
   ```

   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	float fpv;
   	printf("Enter a floating-point value: ");
   	scanf("%f", &fpv);
   	printf("fixed-point notation: %f\n", fpv);
   	printf("exponential notation: %e\n", fpv);
   	printf("p notation: %a\n", fpv);
   
   	return 0;
   }
   ```

5. There are approximately $3.156 \times 10^7$ seconds in a year. Write a program that requests your age in years and then displays the equivalent number of seconds.
   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	unsigned int age;
   	printf("Enter your age in years: ");
   	scanf("%u", &age);
   	printf("The equivalent number of seconds is: %u.\n", age * 365 * 24 * 60 * 60);
   
   	return 0;
   }
   ```

   The output result is following.
   ```text
   Enter your age in years: 29
   The equivalent number of seconds is: 914544000.
   ```

   

6. The mass of a single molecule of water is about $3.0 \times 10 ^{-23}$ grams. A quart of water is about 950 grams. Write a program that requests an amount of water, in quarts, and displays the number of water molecules in that amount.
   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	float water_in_quarts;
   	printf("Enter an amount of water in quarts: ");
   	scanf("%f", &water_in_quarts);
   	double water_molecules = water_in_quarts * 950 / 3.0e-23;
   	printf("The number of water molecules in that amount: %e.\n", water_molecules);
   
   	return 0;
   }
   ```

   The output result is following.
   ```text
   Enter an amount of water in quarts: 5
   The number of water molecules in that amount: 1.583333e+26.
   ```

7. There are 2.54 centimeters to the inch. Write a program that asks you to enter your height in inches and then displays your height in centimeters. Or, if you prefer, ask for the height in centimeters and convert that to inches.
   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	float height_in_centimeters;
   	printf("Enter your height in centimeters: ");
   	scanf("%f", &height_in_centimeters);
   	float height_in_inches = height_in_centimeters / 2.54;
   	printf("The height in inches: %f.\n", height_in_inches);
   
   	return 0;
   }
   ```

   The output result is following.
   ```text
   Enter your height in centimeters: 170
   The height in inches: 66.929131.
   ```

8. In the U.S. system of volume measurements, a pint is 2 cups, a cup is 8 ounces, an ounce is 2 tablespoons, and a tablespoon is 3 teaspoons. Write a program that requests a volume in cups and that displays the equivalent volumes in pints, ounces, tablespoons, and teaspoons. Why does a floating-point type make more sense for this application than an integer type?
   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	float volume_in_cups;
   	printf("Enter a volume in cups: ");
   	scanf("%f", &volume_in_cups);
   	float volume_in_pints = volume_in_cups / 2;
   	float volume_in_ounces = volume_in_cups * 8;
   	float volume_in_tablespoons = volume_in_ounces * 2;
   	float volume_in_teaspoons = volume_in_tablespoons * 3;
   	printf("The equivalent volume in pints: %f.\n", volume_in_pints);
   	printf("The equivalent volume in ounces: %f.\n", volume_in_ounces);
   	printf("The equivalent volume in tablespoons: %f.\n", volume_in_tablespoons);
   	printf("The equivalent volume in teaspoons: %f.\n", volume_in_teaspoons);
   
   	return 0;
   }
   ```
   
   The output result is following:
   ```text
   Enter a volume in cups: 3
   The equivalent volume in pints: 1.500000.
   The equivalent volume in ounces: 24.000000.
   The equivalent volume in tablespoons: 48.000000.
   The equivalent volume in teaspoons: 144.000000.
   ```
   
   A floating-point type makes more sense for the application than an integer. Because converting cups to pints can yield half-pints (e.g., 3 cups = 1.5 pints), and integer division would truncate the fractional part.

