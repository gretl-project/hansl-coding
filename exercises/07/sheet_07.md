# Data Handling with Gretl

## Exercise 1: Working with Data in Gretl: HR Dataset

Complete the following tasks:

1. Download the 'Kaggle HR Dataset CSV' file (`HRDataset_v14.csv`) from https://github.com/gretl-project/hansl-coding/tree/main/data

2. Write a script to load this dataset and display it in the data viewer.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Load HR dataset
open "HRDataset_v14.csv"
varlist
</code></pre>
</details>

3. Calculate a new column `SalaryThousands` from the `Salary` column, containing the salary in units of thousands.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Calculate salary in thousands
series SalaryThousands = Salary / 1000
print Salary SalaryThousands --byobs --range=1:10
</code></pre>
</details>

4. Normalize the `EmpSatisfaction` column so the minimum is 0 and the maximum is 1. Display the resulting series, show descriptive statistics, and calculate the correlation coefficient between the two columns.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Normalize EmpSatisfaction to [0,1] range
scalar min_sat = min(EmpSatisfaction)
scalar max_sat = max(EmpSatisfaction)
series NormalizedSatisfaction = (EmpSatisfaction - min_sat) / (max_sat - min_sat)
# Display results
print EmpSatisfaction NormalizedSatisfaction --byobs --range=1:10
# Descriptive statistics
summary EmpSatisfaction NormalizedSatisfaction --simple
# Calculate correlation
corr EmpSatisfaction NormalizedSatisfaction
</code></pre>
</details>

5. Delete the `Position` and `PositionID` columns. Then display the dataframe.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Delete columns
list DROP = Position PositionID
delete DROP
# Show dataframe (columns)
varlist
</code></pre>
</details>

6. Try to delete the column `Foo` which is not in the dataset. What error message do you receive?
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Try to delete non-existent column
delete Foo
# This would show an error message like:
# "Unknown variable name in command"
</code></pre>
</details>

7. Delete rows with `EmpID` equal to 10066, 10226 and 10181. How many rows remain in the dataframe?
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Delete specific rows
printf "Number of rows before: %d\n", $nobs
series to_keep = (EmpID != 10066) && (EmpID != 10226) && (EmpID != 10181)
# Activate the "--permanent" flag to delete rows in place
smpl to_keep --restrict #--permanent
printf "Number of rows now: %d\n", $nobs
</code></pre>
</details>


## Exercise 2: Working with Gretl: Zurich Dogs Dataset

Complete the following tasks:

1. Download the 'Zurich Dogs CSV' files ('20151001hundehalter.csv' and 'zuordnungstabellehunderassehundetyp.csv') from https://github.com/gretl-project/hansl-coding/tree/main/data/dogsofzurich

2. Write a script to load both files and display them in the data viewer.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Load the dogs dataset
open "20151001hundehalter.csv"
print "Dogs dataset loaded"
varlist

# Save the dogs dataset to a temporary file for later use
store "@dogs.gdt"

# Load the dog breed type mapping
open "zuordnungstabellehunderassehundetyp.csv"
print "Dog breed types dataset loaded"
varlist
</code></pre>
</details>

3. What column can you use to join the two loaded dataframes? Perform a merge for both dataframes and display the result.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># We can merge on the RASSE column
# First, store the breed types dataset
store "@breedtypes.gdt"

# Then load the dogs dataset again
open "@dogs.gdt"

# Join using the dog breed column
join RASSE --data=HUNDERASSENTYP "@breedtypes.gdt"

# Display the result
print HUNDENAME RASSE HUNDERASSENTYP --byobs --range=1:10
</code></pre>
</details>

4. Are there any dogs for which there is no assigned dog breed type (`HUNDERASSENTYP`) in the dataset? Are there any incomplete entries in the breed type mapping?
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Check for dogs without breed type
series missing_breed_type = ok(HUNDERASSENTYP) ? 0 : 1
printf "Number of dogs without breed type: %d\n", sum(missing_breed_type)

# Count occurrences
freq missing_breed_type

# Display some examples of missing breed types
smpl missing_breed_type == 1 --restrict
print HUNDENAME RASSE HUNDERASSENTYP --byobs --range=1:10
smpl full

# Check for incomplete entries in the breed mapping (load mapping dataset)
open "@breedtypes.gdt"
series incomplete_mapping = (ok(HUNDERASSENTYP) && strlen(HUNDERASSENTYP) > 0) ? 0 : 1
printf "Number of incomplete mappings: %d\n", sum(incomplete_mapping)

# Show incomplete mappings if any
if sum(incomplete_mapping) > 0
    smpl incomplete_mapping == 1 --restrict
    print RASSE HUNDERASSENTYP --byobs
    smpl full
endif
</code></pre>
</details>

5. Replace the missing entries in the `HUNDERASSENTYP` column with the value 'Unknown'.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Load the merged dataset
open "@dogs.gdt"
join RASSE --data=HUNDERASSENTYP "@breedtypes.gdt"

# Replace missing values with 'Unknown'
series missing_type = ok(HUNDERASSENTYP) ? 0 : 1
smpl missing_type == 1 --restrict
series string HUNDERASSENTYP = "Unknown"
smpl full

# Verify the change
freq HUNDERASSENTYP
</code></pre>
</details>

6. Count the dogs in Zurich by breed type. Use the `HUNDERASSENTYP` column.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Count dogs by breed type
freq HUNDERASSENTYP

# Alternative approach with more control over output
strings breed_types = strvals(HUNDERASSENTYP)
printf "Count of dogs by breed type:\n"
printf "-------------------------\n"
loop foreach i breed_types
    series count = (HUNDERASSENTYP == "$i")
    printf "%-15s: %5d\n", "$i", sum(count)
endloop
</code></pre>
</details>

7. Which dog breeds belong to Type II?
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Find breeds in Type II
series is_type2 = (HUNDERASSENTYP == "II")
smpl is_type2 == 1 --restrict

# Get unique breeds in Type II
strings type2_breeds = strvals(RASSE)
printf "Breeds in Type II:\n"
printf "----------------\n"
loop foreach i type2_breeds
    printf "%s\n", "$i"
endloop
smpl full
</code></pre>
</details>

8. Visualize the age structure of Type II dog owners compared to the age distribution of all dog owners.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Visualize age structure comparison
# First for all dog owners
gnuplot ALTER --histogram --output=display \
  {set title "Age Distribution of All Dog Owners";}

# Then for Type II dog owners
smpl HUNDERASSENTYP == "II" --restrict
gnuplot ALTER --histogram --output=display \
  {set title "Age Distribution of Type II Dog Owners";}
smpl full

# Create a density comparison plot
series is_type2 = (HUNDERASSENTYP == "II")
gnuplot ALTER --dummy-axis=is_type2 --boxplot --output=display \
  {set title "Age Comparison: All Owners vs. Type II Dog Owners";}
</code></pre>
</details>

The exercises have been formatted following the same style as the previous exercise sheets, with detailed solutions enclosed in collapsible blocks. Each solution includes appropriate Gretl/hansl commands for:

1. Loading data files
2. Calculating new columns and normalizing data
3. Deleting columns and rows
4. Joining datasets
5. Handling missing values
6. Counting and grouping data by categories
7. Filtering specific breeds and types
8. Creating visualizations for comparing distributions

These exercises help students develop skills in data manipulation, transformation, and visualization using Gretl's hansl scripting language.