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
<pre><code class="language-hansl"># compound_interest_calculation.inp
# Method 1: Using compound interest formula
scalar initial = 100000
scalar rate = 0.035
scalar years = 5
scalar final_compound = initial * (1 + rate)^years
printf "Compound formula result: %.2f\n", final_compound
</code></pre>
<pre><code class="language-hansl"># Method 2: Step-by-step calculation
scalar capital = initial
loop i=1..5
    scalar capital = capital * (1 + rate)
endloop
printf "Step-by-step result: %.2f\n", capital
</code></pre>
<pre><code class="language-hansl"># Explanation for the difference
printf "Difference: %.10f\n", final_compound - capital
</code></pre>

The results differ slightly due to floating-point arithmetic precision limitations.
With the compound formula, we perform one exponentiation and one multiplication.
In the step-by-step approach, we perform 5 multiplications sequentially.
Each operation introduces a tiny rounding error, and these errors accumulate differently.
</details>

## Exercise 3: Logical Simplification

Given integer variables `a` and `b`, simplify the following expression:

```
(not (a < b) and not (a > b))
```

<details>
<summary>Solution</summary>
The expression simplifies to:
`(a == b)`

**Explanation**
"not (a < b) and not (a > b)" means "a is not less than b and a is not greater than b" which is equivalent to "a equals b"
</details>

## Exercise 4: Type Conversion

Explain the output for each line in the following program:

```hansl
printf "%g\n", 2 + 3
printf "%g\n", 2.2 + 3.3
printf "%s\n", "2" ~ "3"
printf "%s\n", "2.2" ~ "3.3"
printf "%g\n", atof("2") + atof("3")
printf "%g\n", atof("2" ~ "3")
printf "%g\n", atof("2.2") + atof("3.3")
printf "%g\n", atof("2.2" ~ "3.3")
printf "%g\n", int(2.6 + 2.6)
printf "%g\n", int(2.6) + int(2.6)
```

<details>
<summary>Solution</summary>
The explanations for each line:

<pre><code class="language-hansl">Line 1: 5       # Integer addition of 2 + 3
Line 2: 5.5     # Float addition of 2.2 + 3.3
Line 3: 23      # String concatenation of "2" and "3"
Line 4: 2.23.3  # String concatenation of "2.2" and "3.3"
Line 5: 5       # Converting strings to floats (atof) then adding
Line 6: 23      # Concatenating strings first, then converting to float
Line 7: 5.5     # Converting strings to floats then adding
Line 8: 0       # Invalid conversion - "2.23.3" is not a valid number, returns 0
Line 9: 5       # Adding floats (5.2) then converting to integer
Line 10: 4      # Converting each float to integer (truncating) then adding
</code></pre>

This illustrates type conversions between numeric and string types, and the difference between operation order in expressions.
</details>

## Exercise 5: Present Value Calculation

Calculate the present value (PV) of a payment (D) of €100,000 in T=10 years, assuming an annual real interest rate (r) of 2.5%. Store the result in a variable named PV and print it.

Formula: PV = D / (1 + r)^T

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># present_value.inp
scalar D = 100000
scalar T = 10
scalar r = 0.025
</code></pre>
<pre><code class="language-hansl">
scalar PV = D / (1 + r)^T
printf "Present value: %.2f\n", PV
</code></pre>
</details>

## Exercise 6: Percentage Change Approximation

Verify the approximation that the percentage difference between two numbers near 1 can be approximated by the difference of their logarithms. Use x=1.05 and y=1.02.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># percentage_change_approximation.inp
set verbose off
scalar x = 1.05
scalar y = 1.02
</code></pre>
<pre><code class="language-hansl"># Exact percentage difference
scalar exact = (y - x)/x
# Logarithmic approximation
scalar approx = log(y) - log(x)
</code></pre>
<pre><code class="language-hansl">printf "Exact: %.4f\nApproximation: %.4f\n", exact, approx
</code></pre>
</details>

## Exercise 7: Data Types
## Exercise 7: Data Types

Determine the data type of each of the following literals. Write a hansl program that stores these values in variables and prints their types using the `typeof()` function.

```hansl
scalar a = "hello, world"
scalar b = 3
scalar c = 3.14
matrix d = {1, 2, 3}
string e = "gretl doesn't have tuples"
string f = "gretl doesn't have sets"
bundle g = defbundle("1", "one", "2", "two", "three", 3)
scalar h = $true  # or 1
scalar i = NA
```

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># data_types.inp
scalar a = "hello, world"
scalar b = 3
scalar c = 3.14
matrix d = {1, 2, 3}
bundle e = defbundle("1", "one", "2", "two", "three", 3)
scalar f = $true
scalar g = NA
</code></pre>
<pre><code class="language-hansl">printf "a: %s\n", typename(a)
printf "b: %s\n", typename(b)
printf "c: %s\n", typename(c)
printf "d: %s\n", typename(d)
printf "e: %s\n", typename(e)
printf "f: %s\n", typename(f)
printf "g: %s\n", typename(g)
printf "h: %s\n", typename(h)
printf "i: %s\n", typename(i)
</code></pre>

The data types in hansl/gretl are:
- a: string (text values)
- b: scalar (integer)
- c: scalar (float)
- d: matrix (row vector here)
- e: bundle (key-value collection)
- f: scalar (boolean)
- g: scalar (missing value)
</details>


## Exercise 8: Divisibility Check

Write a program that takes two positive integers as arguments, compares them, and outputs `TRUE` if one number is a divisor of the other number, and `FALSE` otherwise. If either number is zero, print an error message. **Hint**: Use the modulus operator `%` to check for divisibility.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># divisibility_check.inp
function void check_divisibility(int a, int b)
    if a == 0 || b == 0
        printf "Error: Zero cannot be used in divisibility check\n"
        return
    endif
    if a % b == 0 || b % a == 0
        printf "TRUE\n"
    else
        printf "FALSE\n"
    endif
end function
</code></pre>
<pre><code class="language-hansl"># Examples
check_divisibility(10, 5)    # True: 5 divides 10
check_divisibility(7, 14)    # True: 7 divides 14
check_divisibility(7, 9)     # False: neither divides the other
check_divisibility(0,1)   # Error: Zero cannot be used
</code></pre>
</details>