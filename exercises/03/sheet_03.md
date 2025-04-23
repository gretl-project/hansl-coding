# Exercises: Variables, Control Flow, and Data Structures

## Exercise 1: Floating-Point Comparison

Write a script that takes two floating-point numbers as input and outputs `1` if both values are between 0 and 1 (inclusive), otherwise `0`.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># range_check.inp
scalar a = 0.5  # Example value
scalar b = 1.2  # Example value
</code></pre>
<pre><code class="language-hansl">scalar result = (a >= 0 && a <= 1) && (b >= 0 && b <= 1)
printf "Result: %d\n", result
</code></pre>
</details>

## Exercise 2: Improved Present Value Calculation

Enhance your present value calculation script by adding input validation. The input values should be greater than zero, and the interest rate should be less than 0.1 (10%). Print an error message if conditions are violated.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># improved_present_value.inp
scalar D = 100000   # Future value
scalar T = 10       # Years
scalar r = 0.025    # Interest rate
</code></pre>
<pre><code class="language-hansl">if D <= 0 || T <= 0 || r <= 0 || r >= 0.1
    printf "Error: Invalid input values\n"
    printf "D and T must be > 0, r must be 0 < r < 0.1\n"
else
    scalar PV = D / (1 + r)^T
    printf "Present Value: %.2f\n", PV
endif
</code></pre>
</details>

## Exercise 3: Repeated Greetings

Write a script that takes an integer `n` and prints the message 'Hello, world.' `n` times, each on a new line. **Hint:** Use a loop.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># repeated_hello.inp
scalar N = 3  # Example value
</code></pre>
<pre><code class="language-hansl">loop for i=1..N
    printf "Hello, world.\n"
endloop
</code></pre>
</details>

## Exercise 4: Monthly Costs Report

Create a script that:
1. Defines a vector called `monthly_costs` with these values (in thousands of euros): [12.5, 14.2, 11.8, 13.5, 15.0, 16.2]
2. Uses a loop to print each month's costs in the format "Month X: Costs are Y thousand euros" (starting with January as Month 1)

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># monthly_costs.inp
matrix monthly_costs = {12.5, 14.2, 11.8, 13.5, 15.0, 16.2}
strings month_names = defarray("January", "February", "March",
                              "April", "May", "June")

loop i=1..nelem(monthly_costs)
    printf "Month %d (%s): Costs are %.1f thousand euros\n", \
        i, month_names[i], monthly_costs[i]
endloop
</code></pre>
</details>

## Exercise 5: Number Grid

Write a script that prints the integers between 1000 (inclusive) and 2000 (exclusive) with five numbers per line.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># number_grid.inp
scalar count = 0
loop for (i=1000; i<2000; i++)
    printf "%d ", i
    count += 1
    if count % 5 == 0
        printf "\n"
    endif
endloop
</code></pre>
</details>


## Exercise 6: Program Analysis

Analyze the following program. What are the values of `a` and `b` after execution? Explain the result. On the `floor()` function, [click for details](https://gretl.sourceforge.net/gretl-help/funcref.html#floor).

```hansl
scalar a = 123456789
scalar b = 0
loop while a != 0
    b = (10 * b) + (a % 10)
    a = floor(a / 10)
endloop
printf "a = %d\n", a
printf "b = %d", b
```

<details>
<summary>Solution</summary>

This program reverses the digits of `a` and stores them in `b`.

Final values:
- `a` = 0 (the loop continues until a becomes 0)
- `b` = 987654321 (the reversed digits of the original `a`)

The program works by:
1. Taking the last digit of `a` using modulo 10
2. Adding it to `b` (which is multiplied by 10 to shift digits left)
3. Removing the last digit from `a` using integer division
4. Repeating until `a` becomes 0
</details>

## Exercise 7: List Generation
Write a script that creates a vector `a` with exactly 1000 random integers. Access the 1000th entry.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># list_generation.inp
matrix a = mnormal(1000, 1)  # 1000 standard normal variates
a = floor(a * 100)           # Convert to integers
</code></pre>
<pre><code class="language-hansl"># Alternatively: discrete integers between -100 and 100
#matrix a = mrandgen(i, -100, 100, 1000, 1)
</code></pre>
# This will work:
scalar element = a[1000]
printf "Element 1000: %g\n", element
</code></pre>
<pre><code class="language-hansl"># This would fail (index out of bounds):
# scalar invalid = a[1001]
</code></pre>
```

In Gretl, matrix indices are 1-based, so a 1000-element matrix has valid indices from 1 to 1000. Accessing index 1001 would produce an error.
</details>

## Exercise 8: Euclidean Distance

Write a script that:
1. Creates two vectors with 10 random values each from a normal distribution (mean 0, variance 1)
2. Calculates the Euclidean distance between them using the formula:
   d = sqrt(Σ(a_i - b_i)^2)

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># euclidean_distance.inp
matrix a = mnormal(10, 1)
matrix b = mnormal(10, 1)
</code></pre>
<pre><code class="language-hansl">matrix delta = a - b
matrix squared_diff = delta.^2     # Element-wise multiplication
scalar sum_sq = sumc(squared_diff) # sum over column dimension
scalar dist = sqrt(sum_sq)
</code></pre>
<pre><code class="language-hansl">printf "Vector a ~ b:\n %.3f   %.3f\n", a, b
printf "Vector delta:\n %.3f\n", delta
printf "Euclidean distance: %.4f\n", dist
</code></pre>
</details>

## Exercise 9: Error Detection

Analyze the following program. Why does it produce an error when executed?

```hansl
matrix a = {}
loop for (scalar v=0; v<10; v++)
    a[v] = v * v
end loop
```

<details>
<summary>Solution</summary>

The error occurs because:
1. `a` is initialized as an empty matrix
2. Gretl matrices use 1-based indexing, but the loop tries to write to `a[1]` (*Ambiguous matrix index*)
3. The matrix needs to be pre-sized before assignment

Correct version:
<pre><code class="language-hansl"># append_to_vector.inp
matrix a = zeros(10, 1)  # Initialize with 10 zeros
loop v=1..rows(a)
    a[v] = v * v
endloop
print a
</code></pre>
</details>
