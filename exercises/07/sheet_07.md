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


