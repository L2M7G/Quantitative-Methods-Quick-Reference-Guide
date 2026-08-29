# Bootstrap 

## Process

1. **Resample the data:** Draw a new sample from the original data **with replacement**, using the same number of observations as the original sample.
2. **Estimate the model:** Estimate the model using the new bootstrap sample.
3. **Record the estimate:** Record the coefficient or statistic of interest from the model.
4. **Repeat:** Repeat the resampling and estimation process many times (e.g., $B = 10,000$).
5. **Construct the bootstrap distribution:** Use the collection of bootstrap estimates to approximate the sampling distribution of the statistic.
5. **Calculate the standard error:** Calculate the standard deviation of the bootstrap estimates to obtain the bootstrap standard error.
7. **Calculate the confidence interval:** Use the bootstrap distribution to construct a confidence interval for the statistic.

## Uses

- Used to estimate standard errors when necessary parametric assumptions do not hold.
- Can be useful with small samples when conventional standard-error estimates may be unreliable.
- Used to construct confidence intervals for estimated coefficients or statistics.

---
<br><br>

## R
```r
library(boot)

# Function to calculate the statistic
boot_stat <- function(data, indices) {
  sample_data <- data[indices, ]
  model <- lm(Y ~ X1 + X2, data = sample_data)
  coef(model)["X1"]
}

# Bootstrap
boot_results <- boot(
  data = dataset_name,
  statistic = boot_stat,
  R = 10000
)

# Bootstrap estimate
boot_results$t0

# Bootstrap standard error
sd(boot_results$t)

# Bootstrap confidence interval
boot.ci(
  boot_results,
  type = "perc"
)
```

### Definitions

- `library(boot)` = loads the `boot` package for bootstrap resampling
- `boot_stat()` = function that calculates the statistic of interest for each bootstrap sample
- `data` = dataset being resampled
- `indices` = row indices selected for each bootstrap sample
- `sample_data` = bootstrap sample created by resampling the original data with replacement
- `data[indices, ]` = selects the observations specified by the bootstrap sample indices
- `lm()` = estimates the OLS regression model
- `coef(model)["X1"]` = extracts the estimated coefficient on `X1` from the regression model
- `boot()` = repeatedly resamples the data and calculates the specified statistic
- `R = 10000` = specifies 10,000 bootstrap replications
- `boot_results$t0` = returns the original sample estimate of the statistic
- `boot_results$t` = contains the statistic calculated for each bootstrap sample
- `sd(boot_results$t)` = calculates the bootstrap standard error from the distribution of bootstrap estimates
- `boot.ci()` = calculates a bootstrap confidence interval
- `type = "perc"` = specifies the percentile method for calculating the bootstrap confidence interval

---
<br><br>

## Stata

```stata
* Bootstrap Coefficient on X1
bootstrap _b[X1], reps(10000): regress Y X1 X2

* Bootstrap Results for Whole Model
bootstrap, reps(10000): regress Y X1 X2
```

### Definitions

- `bootstrap` = repeatedly estimates the specified model using bootstrap samples
- `_b[X1]` = specifies coefficient on `X1` as the statistic of interest to bootstrap
- `reps(10000)` = specifies 10,000 bootstrap replications
- `regress Y X1 X2` = estimates the model (replace with relevant model - ivregress, etc.)

---
<br><br>

## Python

```python
import numpy as np
import statsmodels.formula.api as smf

# Number of bootstrap replications
B = 10000

# Store bootstrap estimates
bootstrap_estimates = []

# Bootstrap
for i in range(B):

    # Draw a bootstrap sample with replacement
    sample = dataset_name.sample(n=len(dataset_name), replace=True)

    # Estimate regression
    model = smf.ols("Y ~ X1 + X2", data=sample).fit()

    # Store coefficient of interest
    bootstrap_estimates.append(model.params["X1"])

# Bootstrap standard error
bootstrap_se = np.std(bootstrap_estimates, ddof=1)

# 95% percentile confidence interval
ci_lower = np.percentile(bootstrap_estimates, 2.5)

ci_upper = np.percentile(bootstrap_estimates, 97.5)

# Display results
print("Bootstrap SE:", bootstrap_se)
print("95% CI:", ci_lower, ci_upper)
```

### Definitions

- `B = 10000` = specifies 10,000 bootstrap replications
- `bootstrap_estimates = []` = creates an empty list to store the coefficient estimates from each bootstrap sample
- `for i in range(B)` = repeats the bootstrap procedure the specified number of times
- `dataset_name.sample()` = randomly draws observations from the specified dataset
- `n = len(dataset_name)` = specifies that each bootstrap sample contains the same number of observations as the original dataset
- `replace = True` = samples observations with replacement
- `sample` = stores the bootstrap sample
- `smf.ols()` = estimates an OLS regression model
- `"Y ~ X1 + X2"` = specifies the regression of `Y` on `X1` and `X2`
- `data = sample` = specifies the bootstrap sample used to estimate the regression
- `.fit()` = estimates the regression model
- `model.params["X1"]` = extracts the estimated coefficient on `X1`
- `bootstrap_estimates.append()` = adds each new current bootstrap sample to the list of bootstrap estimates
- `np.std()` = calculates the standard deviation of the bootstrap estimates
- `ddof = 1` = uses the sample standard deviation when calculating the bootstrap standard error
- `bootstrap_se` = stores the bootstrap standard error of the coefficient on `X1`
- `np.percentile()` = calculates a specified percentile from the bootstrap distribution

---