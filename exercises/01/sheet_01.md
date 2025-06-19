# Gretl/hansl Exercises for Introductory Course

## Exercise 1: Setting Up the Working Environment

1. Create a new subfolder named `exercises` in your course folder.
2. Create another subfolder named `exercise_1` inside `exercises`.

Your folder structure should now look like:

```
intro_to_gretl
├── lectures
├── exercises
│   ├── exercise_1
│   │
│   └── ...
└── ...
```

## Exercise 2: Hello World

Create a script that prints the message 'Hello, world!' three times, each on a new line. Make either use of the [`print`](https://gretl.sourceforge.net/gretl-help/cmdref.html#print) or [`printf`](https://gretl.sourceforge.net/gretl-help/cmdref.html#printf) command. Save the script as `hello_world.inp` in the `exercise_1` folder and run it.

You may execute either way:

1) via the Gretl editor by the key-stroke `Ctrl+R` (or `Cmd+R` on Mac) or,
2) via the command line (CLI) using the following command (assuming you are in the folder where the script is located) `run hello_world.inp`, or
3) via the terminal executing the script directly (assuming you are in the folder where the script is located): `gretlcli hello_world.inp`.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># hello_world.inp
printf "Hello, world!\n"
printf "Hello, world!\n"
printf "Hello, world!\n"
</code></pre>
</details>


## Exercise 3: Common Errors I

Create a new script `common_errors.inp` that demonstrates what happens when you make these common mistakes in hansl:

1) In gretl you can load external libraries by means of the [`include`]([`print`](https://gretl.sourceforge.net/gretl-help/cmdref.html#include)) command, e.g. `include extra`. What happens if the `include` statement is missing?
2) Trying to use a function named `foo()` which does not exist
3) Misspelling a built-in command (e.g., `prnit` instead of `print`)

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># common_errors_1.inp
extra   # correct: "include extra"
eval foo()
prnit "Hello, world!"
</code></pre>
</details>

## Exercise 4: Common Errors II

Modify the Hello World program to demonstrate these errors:

1) Missing the opening quote in a string
2) Missing the closing quote in a string
3) Using incorrect command syntax (e.g., `print Hello` without parentheses)

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># common_errors_2.inp
string name = Hello, world!"
string name = "Hello, world!
print Hello # correct: print "Hello, world!"
</code></pre>
</details>


<!-- ## Exercise 5: Common Errors III

Create a script `arguments.inp` that demonstrates handling command-line arguments in hansl (note: hansl doesn't directly support command-line arguments like Python, but you can simulate with variables):

```hansl
# arguments.inp
string name1 = "Alice"
string name2 = "Bob"
string name3 = "Charlie"

printf "Hi %s, %s and %s\n", name3, name2, name1
``` -->

## Exercise 5: Formatted Printing I
Create a script `print_names.inp`. Create a string variable and print it in a formatted way. Use the [`printf`](https://gretl.sourceforge.net/gretl-help/cmdref.html#printf) function to format the output:

```hansl
string name1 = "Alice"
printf "Hello, %s!\n", name1
```

Modify the script to include and welcome two names, e.g., "Alice" and "Bob". Use the `printf` function to format the output.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># print_names.inp
string name1 = "Alice"
string name2 = "Bob"
printf "Hello, %s and %s!\n", name1, name2
</code></pre>
</details>

## Exercise 6: Formatted Printing II

Modify the `print_names.inp` script to include the ages of the names. Use the `printf` function to format the output:

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># print_names.inp
string name1 = "Alice"
string name2 = "Bob"
scalar age1 = 25
scalar age2 = 30
printf "Hello, %s and %s! They are %d and %d years old.\n", name1, name2, age1, age2
</code></pre>
</details>

## Exercise 7: Formatted Printing III
Modify the `print_names.inp` script to hold:
1) the names in a string array -- check the [`deflist()`](https://gretl.sourceforge.net/gretl-help/funcref.html#defarray) function, and
2) the ages in a row vector (e.g. `r = {1, 2, 3}`).

Print the names and ages for each person in reverse order line by line. Use the `printf` function to format the output.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># print_names.inp
string names = deflist("Alice", "Bob", "Charlie")
matrix ages = {25, 30, 35}
printf "Hello, %s! You are %d years old.\n", names[3], ages[3]
printf "Hello, %s! You are %d years old.\n", names[2], ages[2]
printf "Hello, %s! You are %d years old.\n", names[1], ages[1]
</code></pre>
</details>
