# Standard Errors (SEs)

$$
SE(\hat{\beta}) = \sqrt{Var(\hat{\beta})}
$$

- Measure the uncertainty or precision of an estimated coefficient.
- Used to calculate t-statistics, p-values, and confidence intervals.

$$
t = \frac{\hat{\beta} - \beta_0}{SE(\hat{\beta})} \qquad\qquad \hat{\beta} \pm t_{\alpha/2}SE(\hat{\beta})
$$

---
<br><br>

## Conventional Standard Errors
- Assume that errors are homoskedastic and independent.

### R

```r
summary(model_name)
```
- `summary(model_name)` = displays the regression results, including the conventional SEs for the specified model

### Stata
```stata
regress Y X1 X2
```
- `regress` = estimates the OLS model and displays the regression results, including the conventional SEs for the specified model

### Python
```python
import statsmodels.formula.api as smf

print(model_name.summary())
```
- `statsmodels.formula.api` = module required to use summary() for the specified model.
- `model_name.summary()` = displays the regression results, including the conventional SEs for the specified model

---
<br><br>

## Heteroskedastic Robust Standard Errors
- Used when there is heteroskedasticity (the variance of the error term is not constant across observations).
- Still assumes that errors are independent across observations.

### R

```r
# Install and load packages
install.packages(c("sandwich", "lmtest"))
library(sandwich)
library(lmtest)

# Estimate OLS
model_name <- lm(Y ~ X1 + X2, data = dataset_name)

# Display robust standard errors
coeftest(model_name, vcov = vcovHC(model_name, type = "HC1"))
```
- `coeftest()` = displays coefficient estimates with the specified standard errors
- `vcovHC()` = calculates a heteroskedasticity-robust variance-covariance matrix
- `type = "HC1"`* = specifies the HC1 heteroskedasticity-robust estimator
- `model_name` = specifies the estimated model

*Note: `HC0`–`HC5` are heteroskedastic robust. 
|Type||
|---|---|
|`HC0`| Orignal White|
|`HC1`| Common general-purpose (degrees of freedom adjustment)
|`HC2`|	Use when there are extreme observations
|`HC3`|	Use when there are extreme observations, and small sample (default)

### Stata

```stata
* OLS with heteroskedasticity-robust standard errors (specify with the model)
regress Y X1 X2, vce(robust)
```
- `regress` = estimates the OLS model
- `Y` = dependent variable
- `X1 X2` = explanatory variables
- `vce(robust)` = specifies heteroskedasticity-robust standard errors (similar to `HC1`)

### Python

```python
# Import Package
import statsmodels.formula.api as smf

# Estimate OLS with robust standard errors
model_name = smf.ols(
    "Y ~ X1 + X2",
    data=dataset_name
).fit(cov_type="HC1")

# Display results
print(model_name.summary())
```
- `smf.ols()` = specifies the OLS model
- `.fit()` = fits the model and estimates the coefficients
- `cov_type="HC1"` = specifies HC1 heteroskedasticity-robust standard errors
- `print(model_name.summary())` = displays the regression results, including the robust standard errors

---
<br><br>

## Clustered Standard Errors
- Account for within-group/cluster corelation.
- Errors may be correlated within groups.

### R

```r
# Install and load packages
install.packages(c("sandwich", "lmtest"))
library(sandwich)
library(lmtest)

# Display Clustered SEs
coeftest(model_name, vcov = vcovHC(
    model_name,
    type = "HC1",
    cluster = "group"
  )
)
```
- `cluster = "group"` = clusters the standard errors by individual/unit
- `type = "HC1"` = specifies the HC1 variance estimator

### Stata

```stata
* OLS with clustered standard errors
regress Y X1 X2, vce(cluster unit_variable)
```
- `vce(cluster unit_variable)` = specifies cluster-robust standard errors using the specified unit variable as the cluster

### Python

```python
# Import Package
import statsmodels.formula.api as smf

# Estimate OLS with cluster-robust standard errors
model_name = smf.ols("Y ~ X1 + X2",data=dataset_name).fit(
    cov_type = "cluster",
    cov_kwds = {"groups": dataset_name["unit_variable"]}
)

# Display results
print(model_name.summary())
```
- `cov_type = "cluster"` = specifies cluster-robust standard errors
- `cov_kwds = {"groups": dataset_name["unit_variable"]}` = specifies the variable used to define the clusters

---
<br><br>

## Choosing Standard Errors

Situation	| Standard errors
--|--
Homoskedastic, independent errors|	Conventional
Heteroskedasticity	|Heteroskedasticity-robust
Correlation within groups	|Cluster-robust
Panel data with repeated observations	|Often cluster by individual/unit

---