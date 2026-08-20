# Ordinary Least Squares (OLS)

## Model

$$
Y_i = \beta_0 + \beta_1 X_i + \cdots + \epsilon_i
$$

Where:
- $Y_i$ = dependent/response variable
- $X_i$ = independent/explanatory variable
- $\beta_0$ = intercept
- $\beta_1$ = coefficient
- $\epsilon_i$ = error term

## Uses

- Used to estimate the relationship between a dependent variable and one or more explanatory variables.  

## Data

- Can be used with cross-sections, time-series, or panel data.  

## Assumptions

- **Linearity:** The relationship between the dependent and explanatory variables is linear.
- **Exogeneity/Zero Conditional Mean:** The expected value of the error term, conditional on the explanatory variables, is zero. 
$$E[\epsilon_i | X_i] = 0$$
- **Independence:** Observations/errors are independent of each other.  
- **No perfect multicollinearity:** The explanatory variables are not perfectly linearly related.  
- **Normality:*** Errors are normally distributed.
- **Homoskedasticity:*** The error term has constant variance across observations.  

*Note: Normality and homoskedasticity are relevant for inference. 


## R

```r
# Estimate OLS model
model_name <- lm(Y ~ X1 + X2, data = dataset_name)

# View Results
summary(model_name)

```
### Definitions

- `lm()` = function for fitting linear model
- `Y` = dependent variable
- `~` = regress Y on X
- `X` = explanatory variable
- `data = dataset_name` = specify the dataset name
- `model_name <-` = saves the model under the specified name
- `summary(model_name)` = displays regression results
  -   coefficients
  - standard errors
  - t-statistics
  - p-values
  - $R^2$
  - adjusted $R^2$


## Stata

```stata
* Estimate OLS Model
regress Y X1 X2

* View Results
** Results are displayed automatically after the `regress` command
```
### Definitions
- `regress` = command for linear model
- `Y` = dependent variable
- `X` = explanatory variable

**Notes:** Any variables listed after `Y` are explanatory variables.  No dataset is specified as the dataset is loaded into Stata.    


## Python
```python
import statsmodels.formula.api as smf

# Estimate OLS Model
model_name = smf.ols("Y ~ X1 + X2", data = dataset_name).fit()

# View Results
print(model_name.summary())
```
### Definitions
- `statsmodels.formula.api` = module for specifying statistical models using formulas
- `smf.ols()` = function for fitting an OLS model
- `"Y ~ X"` = regress Y on X
- `data = dataset_name` = specify dataset
- `.fit()` = fits the model to the data and estimates the coefficients
- `model_name` = assigns the model the specified name
- `model_name.summary()` = display regression results