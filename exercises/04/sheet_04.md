
# Variables, Types, and Functions

## Exercise 1: Philosophers I
Modify the code below to set Plato's birth year to 428 BC (428 BC is represented as -427 in the Gregorian calendar).

```hansl
bundle plato = _(\
  name = "Plato",
  born = -427,
  teacher = "Socrates",
  students = defarray("Aristotle", "Speusippus") \
)
print plato
```

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># philosophers.inp
plato["born"] = -428  # Set birth year to 428 BC
# Alternatively, you can use the following line:
# plato.born = -428   # using the dot-accessor
print plato
</code></pre>
</details>

## Exercise 2: Philosophers II
Using the dictionary from above:

1. Add a new key 'work' with value 'The Republic'
2. Print the value of 'work' using index notation
3. Print the value of 'name' using the get() method
4. Try to print the value of 'age' (what happens?)
5. Print the number of keys
6. Print all keys
7. Print all values
8. Print each key-value next to each other but each pair on a new line


<details>
<summary>Solution Ex. 1</summary>
<pre><code class="language-hansl"># philosopher.inp
# 1. Add new element
string plato["work"] = "The Republic"
</code></pre>
<pre><code class="language-hansl"># 2. Print value using index notation
printf "Work: %s\n", plato["work"]
</code></pre>
<pre><code class="language-hansl"># 3. Print name using get()
printf "Name: %s\n", plato.get("name")
</code></pre>
<pre><code class="language-hansl"># 4. Try to print age (returns NA)
printf "Age: %s\n", plato.get("age")
</code></pre>
<pre><code class="language-hansl"># 5. Number of keys
printf "Number of keys: %d\n", nelem(plato)
</code></pre>
<pre><code class="language-hansl"># 6. All keys
strings keys = getkeys(plato)
print "Keys: ", flatten(keys)
</code></pre>
<pre><code class="language-hansl"># 7. All values
print "Values: "
loop foreach i plato
    print plato["$i"]
endloop
</code></pre>
<pre><code class="language-hansl"># 8. Key-value pairs
loop foreach i plato
    printf "%s = ", "$i"
    print plato["$i"]
endloop
</code></pre>
</details>

## Exercise 3: Functions
1. Write a function that returns the maximum of three integers
2. Write a function that sums all numbers in a vector
3. Write a function that reverses a string
4. Write a function that calculates the factorial of a positive integer
5. Write a function that returns only even numbers from a vector of integers
<!-- 6. (Optional) Write a function that checks if a string is a palindrome
7. (Optional) Write a function that returns the nth row of Pascal's triangle
8. (Optional) Write a function that sorts hyphen-separated words alphabetically -->

<details>
<summary>Solution Ex. 1</summary>
<pre><code class="language-hansl"># get_maximum.inp
# 1. Maximum of three numbers
function scalar max3(int a, int b, int c)
    if a > b
        scalar m = a
    else
        scalar m = b
    endif
    if m < c
        scalar m = c
    endif
   return m
end function
print max3(2,3,1)
</code></pre>
</details>

<details>
<summary>Solution Ex. 2</summary>
<pre><code class="language-hansl"># vector_sum.inp
# 2. Sum of vector
function scalar sum_vector(matrix x)
    x = vec(x)
    scalar result = 0
    loop i=1..nelem(x)
        result += x[i]
    endloop
    return result
end function
print sum_vector({1, 2, 3})
</code></pre>
</details>

<details>
<summary>Solution Ex. 3</summary>
<pre><code class="language-hansl"># reverse_string.inp
# 3. Reverse string
function string reverse_str(string s)
    string rev = empty
    loop i=nelem(s)..1 --decr
        rev ~= s[i]
    endloop
    return rev
end function
</code></pre>
<pre><code class="language-hansl"># Test the function
string s = "AB C"
s_reversed = reverse_str(s)
print s_reversed
</code></pre>
</details>

<details>
<summary>Solution Ex. 4</summary>
<pre><code class="language-hansl"># factorial.inp
function scalar factorial(int n[1::])  # minimum value is 1
    scalar result = 1
    loop for (i=2; i<=n; i++)
        result *= i
    endloop
    return result
end function
</code></pre>
<pre><code class="language-hansl"># Test the function
scalar n = 5
scalar expected = 120
scalar fact = factorial(n)
printf "%d! = %g\n", n, fact
# Check result
assert(fact == expected)
</code></pre>
</details>

<details>
<summary>Solution Ex. 5</summary>
<pre><code class="language-hansl"># evens_only.inp
function matrix evens_only(matrix x)
    matrix evens = {}
    loop i=1..nelem(x)
        if x[i] % 2 == 0
            evens |= x[i]
        endif
    endloop
    return evens
end function
</code></pre>
<pre><code class="language-hansl"># Test the function
matrix m = {1,2,3,4}
printf "Evens in {1,2,3,4}:\n%d", evens_only(m)
```
</details>

## Exercise 4: Philosophers III
Explain the behavior of this code and modify it to be more intuitive:

```hansl
function strings addZeno(strings philoList)
  philoList += "Zeno"
  return philoList
end function

strings philosophers = defarray("Socrates", "Plato", "Aristotle")
morePhilosophers = addZeno(philosophers)

print philosophers
print morePhilosophers
```

<details>
<summary>Solution</summary>
The behavior may be counter-intuitive tp Python users. In gretl/ hansl, unlike Python, string arrays do not suffer from aliasing, but the function modifies the input array directly. We can make it more clear:

<pre><code class="language-hansl">function list addZeno(const list philoList)
  list newList = philoList  # Make a copy
  newList += "Zeno"
  return newList
end function

philosophers = ["Socrates", "Plato", "Aristotle"]
morePhilosophers = addZeno(philosophers)

print philosophers  # Now unchanged
print morePhilosophers
</code></pre>
</details>

## Exercise 5: Docstrings
Add a docstring to the max3 function from Exercise 3 explaining its purpose and usage.

<details>
<summary>Solution</summary>

<pre><code class="language-hansl">function scalar max3(scalar a, scalar b, scalar c)
/*
    Returns the maximum of three scalar values
       Parameters:
      a, b, c: Numeric values to compare
       Returns:
      The largest of the three input values
 */
    scalar m = a > b ? a : b
    return m > c ? m : c
end function
</code></pre>
</details>
