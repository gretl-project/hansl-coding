# Linear Regression with Gretl

## Exercise 1: Linear Model

In this exercise, you will estimate a linear model using Ordinary Least Squares (OLS) with `median_house_value` as the dependent variable. Use all variables except `longitude`, `latitude`, and `ocean_proximity` as independent variables.

1. Download the 'California Housing Prices CSV' file from the course materials:

https://github.com/gretl-project/hansl-coding/tree/main/data

2. Load the dataset into Gretl and examine the first few rows.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Load California housing dataset
open "housing.csv"
varlist
# View first few rows
print dataset --byobs --range=1:10
# Print variable names of the features list
</code></pre>
</details>

3. Create the list of exogenous variables (features) to use in the regression, excluding the target variable `median_house_value` and the variables `longitude`, `latitude`, and `ocean_proximity`. Call the list `features`. Print the initial 5 rows of the list to verify its contents.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl">#Define list of features
list features = dataset  # Start with all variables
# Remove target variable and unwanted variables
features -= median_house_value longitude latitude ocean_proximity
print features --byobs --range=1:5
print varnames(features)
</code></pre>
</details>

4. Handle the categorical variable `ocean_proximity` by creating dummy variables. Make use of the built-in function `dummify()`, and remove the first dummy to avoid the dummy variable trap (perfect multicollinearity). Return the new dummies to a list, print the variable names, and compute the basic summary statistics for the dummies.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Create dummy variables
list dum = dummify(ocean_proximity, 1)
print varnames(dum)
summary dum --simple
</code></pre>
</details>

5. Add the new dummies to the features list and run the OLS estimation using the prepared features and target variable. Use the built-in `ols` command.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Run OLS regression
list features += dum  # Add dummies to features list
# Run OLS regression
ols median_house_value features
</code></pre>
</details>

6. Store the coefficient vectors in a `matrix` called `bhat`, also store the R² value as a `scalar` variable called `r2_score`. Print the coefficient vector and the R² value, and compare with the estimation summary.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Store some model information
scalar r2_score = $rsq
matrix bhat = $coeff
print r2_score
print bhat
</code></pre>
</details>

7. Save the fitted values (in-sample predictions) and compare them with the actual values in a scatter plot. Set the range of the x and y axes to [-20000; 600000] and add appropriate labels and a title.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Store fitted values
series yhat = $yhat
#
# Create scatter plot of predicted vs actual values
gnuplot yhat median_house_value --output=display \
  { set title "Predicted vs Actual House Values";\
    set xlabel "Predicted Value";\
    set ylabel "Actual Value";\
    set grid;\
    set xrange[-200000:600000];\
    set yrange[-200000:600000];}
</code></pre>
</details>

8. Calculate the residuals and compute error measures: Mean Error (ME), Root Mean Squared Error (RMSE), and Mean Absolute Error (MAE). Write a function which takes the series of the actual and the predicted series as input and print the error measures.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Function to calculate error metrics
#
function void calculate_errors(series actual, series predicted)
    # Calculate residuals
    series error = actual - predicted
    #
    # Mean Error (ME)
    scalar me = mean(error)
    #
    # Root Mean Squared Error (RMSE)
    scalar rmse = sqrt(mean(error^2))
    #
    # Mean Absolute Error (MAE)
    scalar mae = mean(abs(error))
    #
    printf "Error Metrics:\n"
    printf "Mean Error (ME): %.2f\n", me
    printf "Root Mean Squared Error (RMSE): %.2f\n", rmse
    printf "Mean Absolute Error (MAE): %.2f\n", mae
end function
#
# Calculate and print error metrics
calculate_errors(median_house_value, predicted)
</code></pre>
</details>

9. Create a histogram of residuals and analyze their distribution.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Plot histogram of residuals
series residuals = median_house_value - yhat
freq residuals --normal --plot=display
</code></pre>
</details>

## Exercise 2: Linear Model with Polynomials

1. Add polynomial terms (second-order) for the variables `median_income` and `housing_median_age` to the model.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Add squared terms
series median_income_squared = median_income^2
series housing_median_age_squared = housing_median_age^2

# Print sample of original and squared variables
print median_income median_income_squared housing_median_age housing_median_age_squared --byobs --range=1:10

# Add new features to the feature list
features += median_income_squared housing_median_age_squared
</code></pre>
</details>

2. Run the regression with the polynomial terms and compare results with the previous model.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Run OLS with polynomial terms
printf "\nRunning regression with polynomial terms:\n"
ols $TARGET features

# Store R-squared for comparison
scalar r2_score_poly = $rsq

# Compare with previous model
printf "\nModel Comparison:\n"
printf "Basic model R-squared: %.4f\n", r2_score
printf "Polynomial model R-squared: %.4f\n", r2_score_poly
printf "Improvement: %.4f\n", r2_score_poly - r2_score

# Store fitted values
series predicted_poly = $yhat

# Calculate residuals for the polynomial model
series residuals_poly = $TARGET - predicted_poly

# Calculate error metrics for the polynomial model
calculate_errors(residuals_poly)

# Plot histogram of residuals for polynomial model
plot_residuals_histogram(residuals_poly)

# Compare scatter plots of predictions
gnuplot predicted $TARGET predicted_poly $TARGET --output=display \
  {
    set title "Predicted vs Actual Values - Model Comparison";
    set xlabel "Predicted Value";
    set ylabel "Actual Value";
    set key top left;
    plot $1 using 1:2 title "Basic Model" pt 7 ps 0.5, \
         $1 using 3:4 title "Polynomial Model" pt 7 ps 0.5;
  }
</code></pre>
</details>

## Exercise 3: Model Regularization

1. Apply regularization to the model and plot the R² value as a function of the regularization parameter λ.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Create a sequence of lambda values
scalar min_lambda = 0.0001
scalar max_lambda = 10
scalar steps = 20
matrix lambda_values = seq(log(min_lambda), log(max_lambda), steps)
lambda_values = exp(lambda_values)

# Initialize matrices to store results
matrix r2_values = zeros(steps, 1)
matrix coeffs_matrix = zeros(steps, nelem(features)+1)  # +1 for intercept

# Loop through lambda values
loop i=1..steps
    scalar lambda = lambda_values[i]

    # Ridge regression (L2 regularization)
    ridge $TARGET features lambda

    # Store R-squared
    r2_values[i] = $rsq

    # Store coefficients
    coeffs_matrix[i, 1] = $coeff  # Intercept
    loop j=1..nelem(features)
        coeffs_matrix[i, j+1] = $coeff[j+1]
    endloop

    printf "Lambda = %.6f, R² = %.4f\n", lambda, $rsq
endloop

# Plot R² vs lambda
gnuplot lambda_values r2_values --output=display \
  {
    set title "R-squared vs Regularization Parameter λ";
    set xlabel "Lambda (log scale)";
    set ylabel "R-squared";
    set logscale x;
    set grid;
    set key top right;
  }
</code></pre>
</details>

2. Analyze how the model coefficients change as a function of the regularization parameter λ.
<details>
<summary>Solution</summary>
<pre><code class="language-hansl"># Prepare data for coefficient plot
matrix coeff_data = zeros(steps, nelem(features)+2)
coeff_data[,1] = lambda_values
coeff_data[,2:] = coeffs_matrix

# Plot first 5 coefficients vs lambda
gnuplot lambda_values coeffs_matrix[,1:5] --output=display \
  {
    set title "Coefficient Values vs Regularization Parameter λ";
    set xlabel "Lambda (log scale)";
    set ylabel "Coefficient Value";
    set logscale x;
    set grid;
    set key top right;
    plot $1 using 1:2 title "Intercept", \
         $1 using 1:3 title "Coef 1", \
         $1 using 1:4 title "Coef 2", \
         $1 using 1:5 title "Coef 3", \
         $1 using 1:6 title "Coef 4";
  }

# Create a correlation plot to show relationship between lambda and R²
gnuplot lambda_values r2_values --output=display \
  {
    set title "Trade-off: Lambda vs R-squared";
    set xlabel "Lambda (log scale)";
    set ylabel "R-squared";
    set logscale x;
    set grid;
    set key top right;
  }

# Find optimal lambda value
scalar max_r2_idx = imax(r2_values)
scalar optimal_lambda = lambda_values[max_r2_idx]
printf "\nOptimal lambda = %.6f with R² = %.4f\n", optimal_lambda, r2_values[max_r2_idx]

# Run final model with optimal lambda
ridge $TARGET features optimal_lambda
printf "Final model with optimal regularization:\n"
printf "R-squared: %.4f\n", $rsq
</code></pre>
</details>

These exercises provide a comprehensive introduction to linear regression in Gretl, covering basic OLS estimation, polynomial features for nonlinear relationships, and regularization techniques. The solutions include code for data preparation, model estimation, performance evaluation, and visualization of results.