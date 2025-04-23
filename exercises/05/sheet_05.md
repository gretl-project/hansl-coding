# Packages and Functions

## Exercise 1: Working with Modules

Write a script that:
1) Installs and loads the `calendar_utils` 3rd-party package,
2) prints its documentation, and
3) prints details of the package, e.g., the version number

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># load_calendar_utils.inp
pkg install calendar_utils
include calendar_utils.gfn
# Print documentation
help calendar_utils
# List package information
pkg query calendar_utils
</code></pre>
</details>

## Exercise 2: Product Data Implementation

1. Write a function named `init_product()` that initializes a product bundle with fields: `name`, `price`, and `stock`
2. Initialize two product bundles with meaningful values, and print the values of one product
3. Create a function `value_stock()` for computing the total inventory value (price * stock) of some product bundle, and print the value of some product
4. Reduce stock by a specified amount (with validation)
5. Recalculate inventory value after stock reduction

<details>
<summary>Solution Ex. 1</summary>
<pre><code class="language-hansl"># product.inp
function bundle init_product (string name,
                              scalar price,
                              int stock[0::])
    bundle B = _(name, price, stock)
    return B
end function
</code></pre>
</details>

<details>
<summary>Solution Ex. 2</summary>
<pre><code class="language-hansl"># product.inp
bundle prod1 = init_product("Laptop", 1200, 5)
bundle prod2 = init_product("Smartphone", 800, 10)
print prod1 prod2
</code></pre>
</details>

<details>
<summary>Solution Ex. 3</summary>
<pre><code class="language-hansl"># product.inp
function scalar value_stock (bundle self)
    scalar value = self.price * self.stock
    return value
end function
printf "Value of product 1: %d\n", value_stock(prod1)
printf "Value of product 2: %d\n", value_stock(prod2)
</code></pre>
</details>

<details>
<summary>Solution Ex. 4</summary>
<pre><code class="language-hansl"># product.inp
function bundle reduce_stock(bundle self, int amount[0::])
    if amount > self.stock
        print "Not enough stock. No change made."
    else
        self.stock -= amount
        printf "Reduced stock by %d. New stock: %d\n", amount, self.stock
    endif
    return self
end function
# Example usage
prod1 = reduce_stock(prod1, 2)
prod2 = reduce_stock(prod2, 12)
printf "Product 1 after reduction:\n"
print prod1
printf "Product 2 after reduction:\n"
print prod2
</code></pre>
</details>


## Exercise 3: Mathematical Functions
1. Implement the power function x^n with proper documentation
2. Create functions to calculate circle properties (diameter, circumference, area) given radius

<details>
<summary>Solution Ex. 1</summary>
<pre><code class="language-hansl"># power.inp
function scalar power(scalar x, scalar n)
    /*Calculates x raised to power n
      Parameters:
      x: base value
      n: exponent
      Returns:
      x^n   */
    return x^n
end function
# Example usage
print power(2,3)
</code></pre>
</details>

<details>
<summary>Solution Ex. 2</summary>
<pre><code class="language-hansl"># circle.inp
function scalar circle_diameter(scalar radius)
    return 2 * radius
end function
</code></pre>
<pre><code class="language-hansl">function scalar circle_circumference(scalar radius)
    return 2 * $pi * radius
end function
</code></pre>
<pre><code class="language-hansl">function scalar circle_area(scalar radius)
    return $pi * radius^2
end function
</code></pre>
<pre><code class="language-hansl"># Example usage
scalar r = 5
printf "Circle with radius %.2f:\n", r
printf "Diameter: %.2f\n", circle_diameter(r)
printf "Circumference: %.2f\n", circle_circumference(r)
printf "Area: %.2f\n", circle_area(r)
</code></pre>
</details>

<!-- ## Exercise 4: Employee Data

Implement employee data handling with:
1. A matrix to store employee data (name, ID, salary, department)
2. Functions to:
    - Print the data in table format
    - Find an employee by name
    - Change an employee's department
    - Calculate overtime pay (for hours > 40)
    - Find the highest paid employee

| emp_name | emp_id | emp_salary | emp_department |
|----------|--------|------------|----------------|
| Mueller  | E7976  | 1250       | Accounting     |
| Johann   | E7999  | 1000       | Research       |
| Martin   | E7900  | 1500       | Sales          |
| Schmidt  | E3998  | 1600       | Operations     |

<details>
<summary>Solution</summary>

```hansl
# Initialize employee data matrix
matrix emp_data = {"Mueller", "E7976", 1250, "Accounting";
                   "Johann", "E7999", 1000, "Research";
                   "Martin", "E7900", 1500, "Sales";
                   "Schmidt", "E3998", 1600, "Operations"}

# Function to print employee table
function void print_employees(matrix data)
    printf "%-10s %-6s %6s %-12s\n", "Name", "ID", "Salary", "Dept"
    loop i=1..rows(data)
        printf "%-10s %-6s %6g %-12s\n", data[i,1], data[i,2], data[i,3], data[i,4]
    endloop
end function

# Function to calculate overtime pay
function scalar overtime_pay(scalar salary, scalar hours)
    scalar ot_hours = max(0, hours - 40)
    return ot_hours * (salary / 40)
end function

# Example usage
print_employees(emp_data)
scalar ot = overtime_pay(emp_data[1,3], 45)
printf "\nOvertime pay for %s: %.2f\n", emp_data[1,1], ot
```
</details> -->
