# Instrumental Variables (IV) / Two Stage Least Squares (2SLS)

## Model

$$
Y_i = \beta_0 + \beta_1 D_i + \beta_2 X_i + u_i
$$

Where:
- $Y_i$ = outcome for individual $i$
- $\beta_0$ = intercept
- $\beta_1$ = estimated causal effect of the treatment on the outcome
- $\beta_2$ = coefficient(s) on the control variable(s)
- $D_i$ = treatment variable that may be endogenous
- $X_i$ = control variable(s)
- $u_i$ = error term

### First Stage

$$
D_i = \phi_0 + \phi_1 Z_i + \phi_2 X_i + v_i
$$

Where:
- $D_i$ = treatment for individual $i$
- $\phi_0$ = intercept
- $\phi_1$ = relationship between the instrument and the treatment
- $Z_i$  = instrument
- $\phi_2$ = coefficents on controls
- $X_i$ = control variables
- $v_i$ = first-stage error term

### Second Stage

$$
Y_i = \lambda_0 + \lambda_{2SLS} \hat{D_i} + \lambda_2 X_i + \epsilon_i
$$

Where:
- $Y_i$ = outcome for individual $i$
- $\lambda_0$ = intercept
- $\lambda_2SLS$ = IV/2SLS estimate of the causal effect of treatment 
- $\hat{D_i}$ = predicted treatment of individual $i$
- $\lambda_2$ = coefficient(s) on the control variable(s)
- $X_i$ = control variable(s)
- $\epsilon_i$ = second-stage error term

## Uses

- Addresses endogeneity when treatment is correlated with unobserved factors affecting the outcome.

- Uses an instrumental variable to provide exogenous variation in the treatment to estimate the effect of the treatment on the outcome.  

## Data

- Cross-sectional or panel data.

- Requires a treatment variable that may be endogenous.

- Requires an instrumental variable that is related to the treatment.

## Assumptions

- **Relevance:** The instrument affects the treatment variable. 

   - $D_i = \phi_0 + \phi_1 Z_i + u_i$  where $\phi_1$ $\neq$ 0

- **Exclusion Restriction:** The instrument only affects the outcome through the treatment.  The instrument does not affect other factors that can affect the outcome.  

---
<br><br>

## R
```r
install.packages("AER")
library(AER)

# Estimate Model
model_iv <- ivreg(
  Y ~ D + X1 + X2 |
    Z1 + Z2 + X1 + X2,
  data = dataset_name
)

# Display results
summary(model_iv)

# First Stage (Check Relationship Between Instrument and Treatment)
first_stage <- lm(
  D ~ Z1 +Z2 + X1 + X2,
  data = dataset_name
)

summary(first_stage)
```

### Definitions
- `ivreg()` = estimates an instrumental variables model using two-stage least squares (2SLS)
- `model_iv` = stores the estimated IV/2SLS model
- `Y` = outcome variable
- `D` = treatment variable
- `X1 + X2` = control variables
- `Z1 + Z2` = instrumental variable(s)
- `Y ~ D + X1 + X2` = specifies the outcome equation with the treatment and control variables
- `|` = separates the outcome equation from the instruments
- `Z1 + Z2 + X1 + X2` = specifies the instrument(s) and control variables used in the IV estimation
- `data = dataset_name` = specifies the dataset used for the analysis
- `summary()` = displays the regression results

---
<br><br>

## Stata
```stata
* Estimate Model
ivregress 2sls Y X1 X2 (D = Z1 Z2)

* First Stage (Check Relationship Between Instruments and Treatment)
reg D Z1 Z2 X1 X2
```

### Definitions

- `ivregress 2sls` = estimates an instrumental variables model using two-stage least squares (2SLS)
- `Y` = outcome variable
- `D` = treatment variable
- `X1 X2` = control variables
- `Z1 Z2` = instrumental variable(s)
- `(D = Z1 Z2)` = specifies that `D` is endogenous and is instrumented using `Z1` and `Z2`
- `ivregress 2sls Y X1 X2 (D = Z1 Z2)` = estimates the IV/2SLS model using the instrument(s) to isolate exogenous variation in the treatment

---
<br><br>

## Python
```python
pip install linearmodels
from linearmodels.iv import IV2SLS

# Estimate Model
model_iv = IV2SLS.from_formula(
    "Y ~ 1 + X1 + X2 + [D ~ Z1 + Z2]",
    data=dataset_name
).fit()

# Display results
print(model_iv.summary)

# First Stage (Check Relationship Between Instruments and Treatment)
import statsmodels.formula.api as smf

first_stage = smf.ols(
    "D ~ Z1 + Z2 + X1 + X2",
    data=dataset_name
).fit()

print(first_stage.summary)
```

### Definitions

- `IV2SLS.from_formula()` = specifies and estimates an IV model using two-stage least squares (2SLS)
- `model_iv` = stores the estimated IV/2SLS model
- `Y` = outcome variable
- `D` = treatment variable
- `X1 + X2` = control variables
- `Z1 + Z2` = instrumental variable(s)
- `1` = includes an intercept
- `[D ~ Z1 + Z2]` = specifies that `D` is endogenous and is instrumented using `Z1` and `Z2`
- `data=dataset_name` = specifies the dataset
- `.fit()` = estimates the model
- `print()` = displays the results
- `model_iv.summary` = displays the IV/2SLS regression results

---