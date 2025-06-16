# Data Handling with Gretl

## Exercise 1: Getting Familiar with Gretl

Get familiar with the Gretl software environment by completing the following tasks:

1. Visit the website [https://gretl.sourceforge.net/](https://gretl.sourceforge.net/) and explore the resources available.
2. **If you haven't installed Gretl yet:** Install Gretl on your computer. Download it from the official website and follow the installation instructions for your operating system.
3. Complete the tutorial on basic data handling available at:
   [https://github.com/atecon/gretl_tutorials/blob/main/basics_datahandling/basics_datahandling.md](https://github.com/atecon/gretl_tutorials/blob/main/basics_datahandling/basics_datahandling.md)

This tutorial will teach you essential skills for working with data in Gretl including:
- Opening different types of data files
- Exploring dataset properties
- Viewing and summarizing data
- Basic data manipulation techniques
- Creating new variables
- Handling missing values
- Subsetting data

```hansl
# This is a simple script to verify your Gretl installation
print "Gretl is working correctly!"
printf "Gretl version: %d\n", $version

# Show information about the system
print $sysinfo
```

## Exercise 2: Working with Data in Gretl: HR Dataset

Work through the following tasks, using the Gretl functions reference and examples from the User Guide as needed.

1. Download the 'Kaggle HR Dataset CSV' file (`HRDataset_v14.csv`)  from https://github.com/gretl-project/hansl-coding/tree/main/data

2. Write a Gretl script to load this dataset and display it in the data viewer.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># exercise_2.inp
open "<PATH_TO_YOUR_FILE>/HRDataset_v14.csv"
varlist
</code></pre>
</details>

3. Write a script to display the first 11 rows of the dataset.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Show first 11 rows
print dataset --byobs --range=1:11
</code></pre>
</details>

4. Write a script to display the last 6 rows of the dataset.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Show last 6 rows
print dataset --byobs --range=-6:
</code></pre>
</details>

5. Display the column names of the loaded dataset.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Display column names
varlist
</code></pre>
</details>

6. Display the data types of each column in the dataset.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Display data types
list L = dataset
loop foreach i L
    printf "Variable: %s, Type: %s\n", "$i", typename($i)
endloop
</code></pre>
</details>

7. Calculate the minimum, maximum, mean, standard deviation, and quantiles for the columns `Salary` and `EmpSatisfaction`. Interpret the results.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Calculate statistics
summary Salary   #--simple
summary EmpSatisfaction   #--simple
</code></pre>
</details>

8. Select and display the unique employee names from the `Employee_Name` column.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Select and display employee names
eval strvals(Employee_Name)  # initial 10 only
# print all distinct names
strings Names = strvals(Employee_Name)
loop foreach i Names
    print "$i"
endloop
</code></pre>
</details>

9. Select and display the names of all employees who are not US citizens (from the `CitizenDesc` column).
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Select and display non-US citizens
series nonUS = (CitizenDesc != "US Citizen")
smpl nonUS  == TRUE --restrict
print Employee_Name CitizenDesc --byobs
smpl full  # reset to full sample
</code></pre>
</details>

10. Sort the dataset in descending order by the `Salary` column and display the names of the 15 highest-paid employees.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Sort by salary and show highest-paid employees
dataset dsortby Salary
print Employee_Name Salary --byobs --range=1:15
</code></pre>
</details>

11. Select all rows where `EmpID` is between 10100 and 10150.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Select rows where EmpID is between 10100 and 10150
series range_emp = (EmpID >= 10100 && EmpID <= 10150)
smpl range_emp --restrict
print Employee_Name EmpID
smpl full
</code></pre>
</details>

12. Sort the dataset in ascending order by `EmpID` and display rows 10 to 20 from the sorted dataset.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Sort by EmpID and display rows 10-20
dataset sortby EmpID
print Employee_Name EmpID --byobs --range=10:20
</code></pre>
</details>

13. Create appropriate visualizations for the `EmpSatisfaction` and `Absences` columns.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Create visualizations
freq EmpSatisfaction --plot=display
freq Absences --normal --plot=display
gnuplot Absences EmpSatisfaction --output=display \
  { set jitter over 0.5 spread 0.5;}
</code></pre>
</details>

## Exercise 3: Working with Gretl: Zurich Dogs Dataset

Complete the following tasks:

1. Open the 'Zurich Dogs CSV' dataset (`20151001hundehalter.csv`) directly from the following URL and store it as `20151001hundehalter.csv`:

URL: https://raw.githubusercontent.com/gretl-project/hansl-coding/refs/heads/main/data/dogsofzurich/20151001hundehalter.csv

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># exercise_3.inp
string URL = "https://raw.githubusercontent.com/gretl-project/hansl-coding/refs/heads/main/data/dogsofzurich/20151001hundehalter.csv"
string FILENAME = "20151001hundehalter.csv"

open "@URL"  # One can directly open a URL in gretl
store "@FILENAME"
varlist
</code></pre>
Alternatively, you can download the file manually and then open it by ```open "@FILENAME"``
</details>


2. Display the column names of the dataset and show the first 10 rows.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl">
varlist
print dataset --byobs --range=1:10  # Show first 10 rows
</code></pre>
</details>

3. Create appropriate visualizations for:
- `ALTER` (age of dog) by means of a frequency plot, and
- `GEBURTSJAHR_HUND` (dog birth year) by means of a kernel density plot.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Create histograms
freq ALTER --plot=display
kdplot GEBURTSJAHR_HUND --output=display
</code></pre>
</details>

4. Restrict the dataset to the `GEBURTSJAHR_HUND` between 1900 and 2020, and create again a kernel density plot for `GEBURTSJAHR_HUND`. Store the plot as `kdplot_GEBURTSJAHR_HUND.png` and look at the produced plot.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Restrict the dataset
smpl 1900 2020   # restrict years
kdplot GEBURTSJAHR_HUND --output="kdplot_GEBURTSJAHR_HUND_1990_to_2020.png"
smpl full  # reset to full sample
</code></pre>
</details>

5. Display the statistical summary for the entire dataset. For which columns is the statistical analysis meaningfully interpretable?
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Show statistical summary
summary dataset #--simple
# The statistical analysis is most meaningful for numeric columns like:
# - GEBURTSJAHR_HUND (dog birth year)
# Most other columns are categorical and/ or are string series
</code></pre>
</details>

6. Display the statistical summary for `GEBURTSJAHR_HUND` grouped by `GESCHLECHT` (breed 1). Which kind has the highest variance in dog birth year?
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Grouped statistics
summary GEBURTSJAHR_HUND --by=GESCHLECHT --simple
</code></pre>
</details>

7. Sort the dataset in descending order by `GEBURTSJAHR_HUND` (dog birth year) and display the first 10 rows of the variables:
```
GEBURTSJAHR_HUND RASSE1 RASSE1_MISCHLING GESCHLECHT_HUND
```
When were the oldest dogs born?
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Sort by dog birth year in descending order
dataset sortby GEBURTSJAHR_HUND
# Show the ten dog breeds with the most dogs
print GEBURTSJAHR_HUND RASSE1 RASSE1_MISCHLING GESCHLECHT_HUND \
  --byobs --range=1:10
</code></pre>
</details>


8. Install a new user-contributed package named `PandasPort` from the Gretl package repository. This package provides additional data handling functions similar to those in Python's Pandas library.

```hansl
# Download the PandasPort package
pkg install PandasPort

# Load into the package into memory
include PandasPort.gfn

# Read the documentation for the PandasPort package
help PandasPort
```

- Read the documentation for the `PandasPort` package at:
  [https://gretl.sourceforge.net/current_fnfiles/PandasPort.gfn](https://gretl.sourceforge.net/current_fnfiles/PandasPort.gfn)

9. Count the number of dogs in each breed using the `PandasPort` package and its function `value_counts()`. Store the result in a matrix named `table` and print it.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Count the number of dogs by birth year
include PandasPort.gfn
matrix table = value_counts(RASSE1)
print table
</code></pre>
</details>

10. You've seen that the table is not sorted by the number of occurrences. Sort the table by the number of dogs (2nd column) in descending order and display the top 10 breeds.

<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Count the number of dogs by birth year
matrix table_sorted = mreverse(msortby(table, 2))
print table_sorted[1:10,]
</code></pre>
</details>