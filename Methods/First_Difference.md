# First-Difference (FD)

## Model

$$
\Delta Y_{it} = \beta_1 \Delta X_{it} + \beta' \Delta Z_{it} + \Delta \epsilon_{it}
$$

Where:
- $\Delta Y_{it}$ = change in the dependent variable for individual $i$ between the previous and the current period
- $\Delta X_{it}$ = change in the explanatory variable of interest
- $\Delta Z_{it}$ = change in the vector of control variables
- $\beta_1$ = coefficient on the explanatory variable
- $\beta'$ = vector of coefficients on the control variables
- $\Delta \epsilon_{it}$ = change in the error term

## Uses

- Used to estimate the relationship between changes in a dependent variable and changes in one or more explanatory variables. 
- Removes unobserved, time-invariant characteristics of individual units by differencing.   

## Data

- Used with panel data with repeated observations of the same individual units over time.  
- Requires observations for the same units over multiple periods.  
- Requires variation within individual units over time.    

## Assumptions

- **\*\*Within-unit variation:\*\*** The explanatory variables have variation over time within individual units.

- **\*\*No serial correlation:\*\*** Errors are not correlated over time within individual units.

- **\*\*Linearity:\*\*** The relationship between the changes in the dependent and explanatory variables is linear.

- **\*\*Exogeneity/Zero Conditional Mean:\*\*** The expected value of the change in the error term, conditional on the changes in the explanatory variables, is zero.

$$
E[\Delta\epsilon_{it} \mid \Delta X_{it}, \Delta Z_{it}] = 0
$$

- **\*\*Independence:\*\*** Observations/errors are independent of each other.

- **\*\*No perfect multicollinearity:\*\*** The explanatory variables are not perfectly linearly related.

- **\*\*Normality:\*\*** Errors are normally distributed.

- **\*\*Homoskedasticity:\*\*** The error term has constant variance across observations.

*Note: Normality and homoskedasticity are relevant for inference. Serial correlation can also affect inference and may require appropriate standard errors.

---
<br><br>

## R

```R
# Specify FD Model
model_name <- plm(
                    Y ~ X1 + X2, 
                    data = dataset_name, 
                    model = "fd")

# View Results
summary(model_name)

```
### Definitions

- `plm()` = function to fit panel data models
- `Y ~ X1 + X2` = regress Y on X1 and X2
- `data = dataset_name` = specifies the dataset
- `model = "fd"` = specifies the first difference model
- `model_name <-` = saves the model under the specified name

---
<br><br>

## Stata

```stata
* Specify Panel Structure
xtset unit_variable time_variable

* Estimate First-Difference Model
xtreg Y X1 X2, fd
```

### Definitions

- `xtset unit_variable time_variable` = specifies the unit and time variables
- `xtreg` = command to fit panel-data regression models
- `Y` = dependent variable
- `X1 X2` = explanatory variables
- `fd` = specifies the first-difference model

---
<br><br>

## Python

```python
from linearmodels.panel import FirstDifferenceOLS

# Specify panel structure
dataset_name = dataset_name.set_index(["unit_variable", "time_variable"])

# Specify First-Difference Model
model_name = FirstDifferenceOLS(
    dataset_name["Y"],
    dataset_name[["X1", "X2"]]
).fit()

# View Results
print(model_name)
```

### Definitions

- `FirstDifferenceOLS()` = function for fitting first-difference panel-data models
- `dataset_name.set_index(["unit_variable", "time_variable"])` = sets the unit and time variables as the panel index
- `dataset_name["Y"]` = specifies the dependent variable
- `dataset_name[["X1", "X2"]]` = specifies the explanatory variables
- `.fit()` = fits the model to the data and estimates the coefficients
- `print(model_name)` = displays the regression results

---