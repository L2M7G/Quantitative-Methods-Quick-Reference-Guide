# Regression Discontinuity (RD)

## Model
$$
Y_i = \beta_0 + \beta_1 Treatment_i + \beta_2g(x_i)+ \beta_3 g (x_i ∗ Treatment_i) + \beta_4 Control_i+ u_i
$$

Where:
- $Y_i$ = outcome for individual $i$
- $\beta_0$ = estiamted value for those under the threshold
- $\beta_1$ = difference in the estimated values for those just above and below the threshold/the jump at the cutoff (RD treatment effect)
- $\beta_2$ = relationship below the threshold
- $\beta_3$ = diference in the slopes/relationships above and below
- $\beta_4$ = coeffient on the control variable
- $Treatment_i$ = 1 if individual $i$ recieved the treatment, 0 otherwise
- $g()$ = functional forms (linear, quadratic, or cubic fits)
- $x_i$ = running variable 

## Uses
- Uses variation resulting from sharp cutoffs assigning treatment.
- Compare outcomes just above and just below treattment cutoff as units will be very simialr in characteristics.
- Common with policies with eligibility based on scores, age, income, etc. 

## Data
- Cross-sectional data
- Running variable with a predetermined cutoff. 
- Requires a binary variable for treated and untreated observations.

## Assumptions
- **Continuity:** Outcomes would change smoothly through the cutoff without the treatment.
- **No Manipulation:** Units are unable to manipulate which side of the cutoff they fall.
- **Balance:** Units just above and just below are balanced in characterics.  

---
<br><br>

## R

### Estimate the Model
```r
# Linear
model_linear <- lm(Y ~ Treatment * x + Control,
  data = dataset_name)

# Quadratic
model_quadratic <- lm(Y ~ Treatment * (x + I(x^2)) + Control,
  data = dataset_name)

# Cubic
model_cubic <- lm(Y ~ Treatment * (x + I(x^2) + I(x^3)) + Control,
  data = dataset_name)

# Display results
summary(model_linear)
summary(model_quadratic)
summary(model_cubic)
```

### Definitions

- `model_linear` = estimates the RD model using a linear function of the running variable
- `model_quadratic` = estimates the RD model using a quadratic function of the running variable
- `model_cubic` = estimates the RD model using a cubic function of the running variable
- `Treatment * x` = allows the relationship between the running variable and outcome to differ above and below the cutoff
- `I(x^2)` = includes the squared running variable
- `I(x^3)` = includes the cubed running variable
- `Control` = includes the control variable(s)
- `lm()` = estimates the linear regression model

### Visualization
```r
# Visualize RD
library(ggplot2)

ggplot(dataset_name, aes(x = x, y = Y)) +
  geom_point(alpha = 0.3) +
  geom_smooth(
    data = subset(dataset_name, x < cutoff),
    method = "lm",
    formula = y ~ x,
    # formula = y ~ x + I(x^2),
    # formula = y ~ x + I(x^2) + I(x^3),
    se = FALSE) +
  geom_smooth(
    data = subset(dataset_name, x >= cutoff),
    method = "lm",
    formula = y ~ x,
    # formula = y ~ x + I(x^2),
    # formula = y ~ x + I(x^2) + I(x^3),
    se = FALSE) +
  geom_vline(
    xintercept = cutoff,
    linetype = "dashed") +
  labs(
    x = "Running variable",
    y = "Outcome")
```
### Definitions

- `aes()` = specifies the running variable and outcome for the plot
- `geom_point()` = plots the individual observations
- `geom_smooth()` = adds a fitted regression line/curve
- `subset(dataset_name, x < cutoff)` = fits the relationship using observations below the cutoff
- `formula = y ~ x` = specifies a linear functional form
- `formula = y ~ x + I(x^2)` = specifies a quadratic functional form
- `formula = y ~ x + I(x^2) + I(x^3)` = specifies a cubic functional form
- `se = FALSE` = does not display the confidence interval around the fitted line/curve
- `geom_vline()` = adds a vertical line to the plot
- `xintercept = cutoff` = places the vertical line at the treatment cutoff

### Continuity
```r
install.packages("stargazer")
library(stargazer)

# Continuity / Balance Check
balance_model_1 <- lm(
  Control1 ~ Treatment * x,
  data = dataset_name)

balance_model_2 <- lm(
  Control2 ~ Treatment * x,
  data = dataset_name)

# Balance Table
stargazer(
  balance_model_1,
  balance_model_2,
  type = "text"
)
```

### Definitions
- `balance_model_1` / `balance_model_2` = estimates whether the predetermined characteristic changes discontinuously at the cutoff
- `Control1` / `Control2` = predetermined characteristics that should not be affected by treatment
- `Treatment` = 1 if the observation receives treatment, 0 otherwise
- `x` = running variable
- `Treatment * x` = allows the relationship between the running variable and the characteristic to differ on either side of the cutoff
- `lm()` = estimates the regression model

---
<br><br>

## Stata

### Estimate the Model
```stata
* Linear
reg Y i.Treatment##c.x Control

* Quadratic
reg Y i.Treatment##(c.x c.x#c.x) Control

* Cubic
reg Y i.Treatment##(c.x c.x#c.x c.x#c.x#c.x) Control
```

### Definitions
- `reg` = estimates the linear regression model
- `i.Treatment` = treats `Treatment` as a categorical/binary variable
- `c.x` = treats `x` as a continuous variable
- `i.Treatment##c.x` = includes `Treatment`, `x`, and their interaction
- `i.Treatment##(c.x c.x#c.x)` = includes treatment interactions with the linear and quadratic terms
- `i.Treatment##(c.x c.x#c.x c.x#c.x#c.x)` = includes treatment interactions with the linear, quadratic, and cubic terms
- `Control` = includes the control variable(s)

### Visualization
```stata
* Linear RD visualization
twoway ///
    (scatter Y x) ///
    (lfit Y x if x < cutoff) ///
    (lfit Y x if x >= cutoff) ///
    , xline(cutoff, lpattern(dash)) ///
        xtitle("Running variable") ///
        ytitle("Outcome")

* For a quadratic fit: replace both 'lfit' commands with 'qfit'.
/* For a cubit fit: reg Y c.x##c.x##c.x if x < cutoff
                    predict yhat_left if e(sample)
                    reg Y c.x##c.x##c.x if x >= cutoff
                    predict yhat_right if e(sample)
                    Then replace Y with yhats */
```

### Definitions
- `lfit Y x` = adds a linear fitted line
- `qfit Y x` = adds a quadratic fitted curve
- `if x < cutoff` = fits the relationship using observations below the cutoff
- `if x >= cutoff` = fits the relationship using observations at or above the cutoff
- `xline(cutoff)` = adds a vertical line at the treatment cutoff

### Continuity
```stata
* Continuity / Balance Check
reg Control1 i.Treatment##c.x
estimates store balance1

reg Control2 i.Treatment##c.x
estimates store balance2

* Balance Table
estimates table balance1 balance2
```

### Definitions
- `Control1` / `Control2` = predetermined characteristics that should not be affected by treatment
- `balance1` / `balance2` = stored results from the continuity/balance regressions
- `i.Treatment##c.x` = includes treatment, the running variable, and their interaction
- `reg` = estimates the regression model
- `estimates store` = stores regression results for later comparison
- `estimates table` = combines the stored regression results into one table

---
<br><br>

## Python

### Estimate the Model
```python
import statsmodels.formula.api as smf

# Linear
model_linear = smf.ols("Y ~ Treatment * x + Control",
    data=dataset_name).fit()

# Quadratic
model_quadratic = smf.ols("Y ~ Treatment * (x + I(x**2)) + Control",
    data=dataset_name).fit()

# Cubic
model_cubic = smf.ols("Y ~ Treatment * (x + I(x**2) + I(x**3)) + Control",
    data=dataset_name).fit()

# Display results
print(model_linear.summary())
print(model_quadratic.summary())
print(model_cubic.summary())
```

### Definitions
- `smf.ols()` = estimates an ordinary least squares regression model
- `.fit()` = estimates the model using the specified data
- `Treatment * x` = includes `Treatment`, `x`, and their interaction
- `I(x**2)` = includes the squared running variable
- `I(x**3)` = includes the cubed running variable


### Visualization
```python
import matplotlib.pyplot as plt
import statsmodels.formula.api as smf

# Split data at the cutoff
below = dataset_name[dataset_name["x"] < cutoff]
above = dataset_name[dataset_name["x"] >= cutoff]

# Plot observations
plt.scatter(dataset_name["x"], dataset_name["Y"], alpha=0.3)

# Fit models on each side 
# ('+ I(x**2)' or '+ I(x**2) + I(x**3)' for quadratic and cubic)
model_below = smf.ols("Y ~ x", data=below).fit()
model_above = smf.ols("Y ~ x", data=above).fit()

# Plot fitted lines
plt.plot(below["x"], model_below.predict(below))
plt.plot(above["x"], model_above.predict(above))

# Add cutoff
plt.axvline(cutoff, linestyle="--")

plt.xlabel("Running variable")
plt.ylabel("Outcome")
plt.show()
```

### Definitions
- `dataset_name[dataset_name["x"] < cutoff]` = selects observations below the cutoff
- `dataset_name[dataset_name["x"] >= cutoff]` = selects observations at or above the cutoff
- `smf.ols()` = estimates the fitted regression relationship on each side of the cutoff
- `.predict()` = generates predicted outcome values from the fitted model
- `plt.axvline()` = adds a vertical line at the cutoff

### Continuity / Balance
```python
# Continuity / Balance Check
balance_model_1 = smf.ols("Control1 ~ Treatment * x",
    data=dataset_name).fit()

balance_model_2 = smf.ols("Control2 ~ Treatment * x",
    data=dataset_name).fit()

# Display results
print(balance_model_1.summary())
print(balance_model_2.summary())
```

### Definitions
- `Control1` / `Control2` = predetermined characteristics that should not be affected by treatment
- `balance_model_1` / `balance_model_2` = estimate whether the predetermined characteristic changes discontinuously at the cutoff
- `Treatment * x` = includes treatment, the running variable, and their interaction
- `smf.ols()` = estimates the regression model
- `.fit()` = fits the regression model to the data

---