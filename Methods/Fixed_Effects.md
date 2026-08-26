# Fixed-Effects (FE)

## Model

$$
Y_{it} = \beta_1 X_{it} + \beta'Z_{it} + \alpha_i + \gamma_t + \epsilon_{it}
$$

Where:
- $Y_{it}$ = dependent variable for individual $i$ at time $t$
- $X_{it}$ = explanatory variable of interest
- $Z_{it}$ = vector of control variables
- $\beta_1$ = coefficient on the explanatory variable
- $\beta'$ = vector of coefficients on the control variables
- $\alpha_i$ = individual fixed effects (denoted with subscript $i$)
- $\gamma_t$ = time fixed effects (denoted with subscript $t$)
- $\epsilon_{it}$ = error term for individual $i$ at time $t$

## Uses

- Used to estimate the relationship between a dependent variable and one or more explanatory variables while controlling for unobserved, time-invariant characteristics. 
- Looks at changes over time within individual units in the dataset.   

## Data

- Used with panel data with repeated observations of the same individuals over time.  
- Requires variation within indivdual units over time.    

## Assumptions

- **Within-unit variation:** The explanatory variables have variation over time within individual units.

- **No serial correlation:** Errors are not correlated over time within individual units.
- **Linearity:** The relationship between the dependent and explanatory variables is linear.

- **Exogeneity/Zero Conditional Mean:** The expected value of the error term, conditional on the explanatory variables and individual fixed effects, is zero.

$$
E[\epsilon_{it} \mid X_{it}, \alpha_i] = 0
$$

- **Independence:** Observations/errors are independent of each other.

- **No perfect multicollinearity:** The explanatory variables are not perfectly linearly related.

- **Normality:** Errors are normally distributed.

- **Homoskedasticity:** The error term has constant variance across observations.

*Note: Normality and homoskedasticity are relevant for inference. Serial correlation can also affect inference and may require appropriate standard errors.

---
<br><br>

## R

```r
# Specify Two-way FE Model
model_name <- plm(
                    Y ~ X1 + X2, 
                    data = dataset_name, 
                    model = "within", 
                    effect = "twoways"
                    )

# View Results
summary(model_name)
```

### Definitions

- `plm()` = function to fit panel data models
- `Y ~ X1 + X2` = regress Y on X1 and X2
- `data = dataset_name` = specifies the dataset
- `model = "within"`* = specifies the fixed effects model
- `effect = "twoways"`** = specifies both individual and time fixed effects
- `model_name <-` = saves the model under the specified name

***Note:** `model = "within"` specifies the fixed effects model. Other options include `model = "random"` and `model = "pooling"`.

****Note:** `effect = "twoways"` for both individual and time fixed effects. Other options include `effect = "individual"` and `effect = "time"`.

---
<br><br>

## Stata
```stata
* Specify Panel Structure
xtset unit_variable time_variable

* Estimate Two-way FE Model
xtreg Y X1 X2 i.time_variable, fe
```
### Definitions

- `xtset unit_variable time_variable` = specifies the unit and time variables
- `xtreg` = command to fit panel-data regression models
- `Y` = dependent variable
- `X1 X2` = explanatory variables
- `i.time_variable` = uses the specified time variable to include time fixed effects
- `fe` = specifies the individual fixed effects model

---
<br><br>

## Python
```python
from linearmodels.panel import PanelOLS

# Specify Panel Structure
dataset_name = dataset_name.set_index(["unit_variable", "time_variable"])

# Specify Two-way FE Model
model_name = PanelOLS(
    dataset_name["Y"],
    dataset_name[["X1", "X2"]],
    entity_effects = True,
    time_effects = True
).fit()

# View Results
print(model_name)
```
### Definitions

- `PanelOLS()` = function to fit panel-data regression models
- `dataset_name.set_index(["unit_variable", "time_variable"])` = specifies the unit and time variables as the panel index
- `dataset_name["Y"]` = specifies the dependent variable
- `dataset_name[["X1", "X2"]]` = specifies the explanatory variables
- `entity_effects = True` = includes individual/unit fixed effects
- `time_effects = True` = includes time fixed effects
- `.fit()` = fits the model to the data and estimates the coefficients
- `print(model_name)` = displays the regression results

---