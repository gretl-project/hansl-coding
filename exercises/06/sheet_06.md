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
<pre><code class="language-hansl"># Example script to load and examine the HR dataset
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
gnuplot EmpSatisfaction --histogram --output=display
gnuplot Absences --histogram --output=display
gnuplot Absences EmpSatisfaction --output=display \
{ set jitter over 0.5 spread 0.5;}
</code></pre>
</details>

## Exercise 3: Working with Gretl: Zurich Dogs Dataset

Complete the following tasks:

1. Download the 'Zurich Dogs CSV' dataset from the course materials and extract it.
2. Write a script to load the file '20151001hundehalter.csv' and display it in the data viewer.
3. Create appropriate visualizations for the `ALTER` (age) and `GEBURTSJAHR_HUND` (dog birth year) columns.
4. Display the statistical summary for the entire dataset. For which columns is the statistical analysis meaningfully interpretable?
5. Sort the dataset in descending order by dog age. Show the ten oldest dogs. Which breeds do they belong to?
6. Which dogs are owned by the youngest dog owners? Which dogs are owned by the oldest dog owners?

```hansl
# Script for the Zurich Dogs dataset
open 20151001hundehalter.csv

# Display dataset information
print $datainfo

# Show statistical summary
summary --simple

# Calculate and visualize dog age
series DOG_AGE = $year - GEBURTSJAHR_HUND
setinfo DOG_AGE "Dog age in years"

# Create visualizations
gnuplot ALTER --histogram --output=display
gnuplot GEBURTSJAHR_HUND --histogram --output=display
gnuplot ALTER DOG_AGE --scatter --output=display

# Sort by dog age, show oldest dogs
series SORTINDEX = DOG_AGE
sort SORTINDEX --descending
smpl 1 10
print HUNDENAME RASSE DOG_AGE
smpl full

# Find dogs owned by youngest owners
series SORTINDEX = ALTER
sort SORTINDEX
smpl 1 10
print HUNDENAME RASSE ALTER
smpl full

# Find dogs owned by oldest owners
sort SORTINDEX --descending
smpl 1 10
print HUNDENAME RASSE ALTER
smpl full
```

The exercises above will help you become familiar with Gretl's data handling, analysis, and visualization capabilities. Working through them will provide you with practical skills needed for econometric analysis using Gretl's hansl scripting language.