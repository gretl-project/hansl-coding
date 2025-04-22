# Gretl/hansl Exercises: Variables, Types, and Errors

## Exercise 1: Variable Swapping Trace

Assume `a` and `b` are integer variables. What does the following program do? Trace the object-level changes step by step.

```hansl
scalar t = a
scalar b = t
scalar a = b
```

<details>
<summary>Solution</summary>
# Initial state:
#   a contains an integer value
#   b contains an integer value

# After line 1 (t = a):
#   t points to the same value as a

# After line 2 (b = t):
#   b now points to the same value as t (and thus a)
#   The original value of b is overwritten

# After line 3 (a = b):
#   a continues to point to the same value since b already points to a's value

# Conclusion:
# This code does not actually swap values but makes all three variables (a, b, t)
# reference the original value of a. The original value of b is lost.
</details>

## Exercise 2: Compound Interest Calculation

**Background**: In finance, precise calculation of interest and returns is crucial. However, floating-point precision limitations can lead to small but significant deviations.

**Task**: Write a hansl script that calculates the growth of an initial capital of €100,000 over 5 years at an annual interest rate of 3.5% using two methods:
1. Compound interest formula for the entire period: `final = initial * (1 + rate)^years`
2. Step-by-step annual calculation: Multiply each year's capital by the interest factor

Why are the results not identical?

<details>
<summary>Solution</summary>
```hansl
# Method 1: Using compound interest formula
scalar initial = 100000
scalar rate = 0.035
scalar years = 5
scalar final_compound = initial * (1 + rate)^years
printf "Compound formula result: %.2f\n", final_compound

# Method 2: Step-by-step calculation
scalar capital = initial
loop i=1..5
    scalar capital = capital * (1 + rate)
endloop
printf "Step-by-step result: %.2f\n", capital

# Explanation for the difference
printf "Difference: %.10f\n", final_compound - capital

# The results differ slightly due to floating-point arithmetic precision limitations.
# With the compound formula, we perform one exponentiation and one multiplication.
# In the step-by-step approach, we perform 5 multiplications sequentially.
# Each operation introduces a tiny rounding error, and these errors accumulate differently.
```
</details>

## Exercise 3: Logical Simplification

Given integer variables `a` and `b`, simplify the following expression:

```hansl
(not (a < b) and not (a > b))
```

<details>
<summary>Solution</summary>

```hansl
# The expression simplifies to:
(a == b)

# Explanation:
# "not (a < b) and not (a > b)" means "a is not less than b and a is not greater than b"
# which is equivalent to "a equals b"
```
</details>

## Exercise 4: Type Conversion

Explain the output for each line in the following program:

```hansl
printf "%g\n", 2 + 3
printf "%g\n", 2.2 + 3.3
printf "%s\n", "2" + "3"
printf "%s\n", "2.2" + "3.3"
printf "%s\n", str(2) + str(3)
printf "%s\n", str(2.2) + str(3.3)
printf "%g\n", atof("2") + atof("3")
printf "%g\n", atof("2" + "3")
printf "%g\n", atof("2.2") + atof("3.3")
printf "%g\n", atof("2.2" + "3.3")
printf "%g\n", int(2.6 + 2.6)
printf "%g\n", int(2.6) + int(2.6)
```

<details>
<summary>Solution</summary>

```hansl
# 1: 5 (integer addition)
# 2: 5.5 (float addition)
# 3: "23" (string concatenation)
# 4: "2.23.3" (string concatenation)
# 5: "23" (string conversion then concatenation)
# 6: "2.23.3" (string conversion then concatenation)
# 7: 5 (string to float then addition)
# 8: 23 (string concatenation then conversion)
# 9: 5.5 (string to float then addition)
# 10: 2.23.3 (invalid - will return NA)
# 11: 5 (float addition then integer conversion)
# 12: 4 (integer conversion then addition)
```
</details>

## Exercise 5: Present Value Calculation

Calculate the present value (PV) of a payment (D) of €100,000 in T=10 years, assuming an annual real interest rate (r) of 2.5%. Store the result in a variable named PV and print it.

Formula: PV = D / (1 + r)^T

<details>
<summary>Solution</summary>

```hansl
scalar D = 100000
scalar T = 10
scalar r = 0.025

scalar PV = D / (1 + r)^T
printf "Present value: %.2f\n", PV
```
</details>

## Exercise 6: Percentage Change Approximation

Verify the approximation that the percentage difference between two numbers near 1 can be approximated by the difference of their logarithms. Use x=1.05 and y=1.02.

<details>
<summary>Solution</summary>

```hansl
scalar x = 1.05
scalar y = 1.02

# Exact percentage difference
scalar exact = (y - x)/x

# Logarithmic approximation
scalar approx = log(y) - log(x)

printf "Exact: %.4f\nApproximation: %.4f\n", exact, approx
```
</details>
