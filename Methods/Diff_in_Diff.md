# Difference-in-Differences (DiD)

## Model

$$
Y_{g,t} = \alpha + \beta Treat_g + \gamma Post_t + \delta_{DD} Treat_g * Post_t + u_{g,t}
$$

Where:
- $Y_{g,t}$ = outcome for group $g$ at time $t$
- $\alpha$ = outcome for the control group before treatment 
- $\beta$ = difference between the control and treatment groups before treatment
- $\gamma$ = change in the control group (what the change for the treatment group may have looked like without the treatment/the counterfactual)
- $\delta_{DD}$ = DiD treatment effect
- $Treat_g$ = equal to 1 if group $g$ is the treatment group, 0 otherwise
- $Post_t$ = equal to 1 if time $t$ is the period after treatment, 0 otherwise
- $Treat_g * Post_t$ = interaction term between the treatment group and post-treatment periods
- $u_{g,t}$ = error term for group $g$ at time $t$


### Difference-in-Differences Estimator
Difference in the changes of the treatment and control groups
$$
\tau_{DD} = (Y_{T,After} - Y_{T,Before}) - (Y_{C,After} - Y_{C,Before})
$$

Where:
- $(Y_{T,After}-Y_{T,Before})$ = change in the treatment group
- $(Y_{C,After}-Y_{C,Before})$ = change in the control group
- $\tau_{DD}$ = change in the treatment group relative to the change in the control group


### Fixed Effects 

- Time fixed effects to control for things that affected both groups over a period
- Group fixed effects to control for group specific differences that did not change over time 

$$
Y_{g,t} = \alpha_g + \gamma_t + \delta_{DD} Treat_g * Post_t + u_{g,t}
$$

Where:
- $Y_{g,t}$ = outcome for group $g$ at time $t$
- $\alpha_g$ = group fixed effects
- $\gamma_t$ = time fixed effects
- $\delta_{DD}$ = DiD treatment effect
- $Treat_g * Post_t$ = interaction term between the treatment group and post-treatment periods
- $u_{g,t}$ = error term for group $g$ at time $t$

## Uses

- Compare the change in a treated group to the change in a control group.
- Common design for policy analysis, such as when a policy is enacted in one city and the outcome is compared to a similar city that did not receive the same treatment.

## Data

- Requires a treatment and control group.
- Requires observations before and after the treatment.
- Used with panel data or repeated cross-sectional data.
- Might need to create a treatment period dummy - 1 for post, 0 for pre.

## Assumptions
- **Parallel Trends:** Assumes that what would have happened to the treatment group is represented by the change in the control group.  Assume that the two groups would have followed parallel trends in the absence of the treatment.

    - Control group is similar to the treatment group in the initial period.
    - The two groups have similar trends before the treatment. 

---
<br><br>

## R 

### Estimate Model
```r
# Basic  Model
model_name <- lm(
  Y ~ Treat * Post + X1 + X2,
  data = dataset_name
)

# Display results
summary(model_name)


# With Fixed Effects
install.packages("plm")
library(plm)

model_name_fe <- plm(
  Y ~ Treat:Post + X1 + X2,
  data = dataset_name,
  index = c("unit_variable", "time_variable"),
  model = "within",
  effect = "twoways"
)

# Display results
summary(model_name_fe)
```
### Definitions
- `lm()` = estimates the basic DiD regression model
- `Treat * Post` = includes `Treat`, `Post`, and their interaction
- `plm()` = estimates the panel-data model with fixed effects
- `Treat:Post` = estimates the DiD treatment effect
- `model = "within"` = estimates the model using fixed effects
- `effect = "twoways"` = includes unit and time fixed effects
- `index` = specifies the unit and time variables
- `X1 + X2` = adds controls

### Check Parallel Trends
```r
# Visual
library(ggplot2)

ggplot(dataset_name, aes(x = time_variable, y = Y, color = Treat)) +
  stat_summary(fun = mean, geom = "line") +
  geom_vline(xintercept = treat_time, linetype = "dashed") +
  labs(
    x = "Time",
    y = "Outcome",
    color = "Group"
  )
```
### Definitions
- `ggplot()` = creates a plot to visually assess parallel trends
- `aes()` = specifies the variables used for the plot
- `stat_summary(fun = mean, geom = "line")` = plots the average outcome for each group over time
- `geom_vline())` = creates a dashed evrtical line at the time of the treatment to show pre/post


```r
# Compare pre-treatment trends 
pre_treat <- subset(
  dataset_name,
  Post == 0
)

pre_trends_model <- lm(
  Y ~ Treat * time_variable,
  data = pre_treat
)

summary(pre_trends_model)
```
### Definitions
- `pre_treat` = create a data set with only pre-treatment data
- `Post == 0` = keeps only pre-treatment observations
- `pre_trends_model` = estiamte the model with only pre-treatment data 
- `lm()` = estimates the pre-treatment trend model
- `Treat * time_variable` = includes treatment status, time, and their interaction
- `Treat:time_variable` = estimates the difference in pre-treatment trends between the treatment and control groups (this should not be statistically significant)

---
<br><br>
## Stata

### Estimate Model
```stata
* Basic model
regress Y i.Treat##i.Post X1 X2

* With Fixed Effects
xtset unit_variable time_variable

xtreg Y i.Treat#i.Post X1 X2 i.time_variable, fe
```

### Definitions
- `regress` = estimates the basic DiD regression model
- `i.Treat##i.Post`* = includes Treat, Post, and their interaction (`##` is the factorial operator)
- `xtreg` = estimates a panel-data model
- `i.Treat#i.Post`* = includes the interaction between treatment status and the post-treatment period
- `i.time_variable`* = includes time fixed effects
- `fe` = includes unit fixed effects
- `xtset` = specifies the unit and time variables
- `X1 X2` = adds controls

***Note:** `i.variable_name` indicates that it is a categorical variable. 

### Check Parallel Trends
```stata
* Visual
preserve

collapse (mean) Y, by(time_variable Treat)

twoway ///
    (line Y time_variable if Treat == 0) ///
    (line Y time_variable if Treat == 1), ///
    xline(treat_time, lpattern(dash)) ///
    xtitle("Time") ///
    ytitle("Outcome") ///
    legend(order(1 "Control" 2 "Treatment"))

restore
```

### Definitions
- `preserve` = temporarily preserves the original dataset
- `restore` = returns the dataset to its original form
- `collapse (mean) Y `= calculates the average outcome for each group and time period
- `by(time_variable Treat) `= calculates the averages separately by time and treatment group
- `twoway `= creates a graph
- `line` = plots average outcome over time
- `if Treat == 0` = plots the control group
- `if Treat == 1` = plots the treatment group
- `xline()` = adds a dashed vertical line at the treatment time

```stata
* Compare pre-treatment trends
regress Y c.time_variable##i.Treat if Post == 0
```

### Definitions
- `regress` = estimates the pre-treatment trend model
- `c.time_variable##i.Treat` = includes time, treatment status, and their interaction
- `c.time_variable` = treats time as a continuous variable
- `if Post == 0` = keeps only pre-treatment observations
- `Treat#time_variable` = estimates the difference in pre-treatment trends between the treatment and control groups

---
<br><br>

## Python

### Estimate Model
```python
import statsmodels.formula.api as smf

# Model
model_name = smf.ols(
    "Y ~ Treat * Post + X1 + X2 + C(unit_variable) + C(time_variable)",
    data=dataset_name
).fit()

# Display results
print(model_name.summary())
```
### Definitions
- `smf.ols()` = estimates the model
- `Treat * Post` = includes Treat, Post, and their interaction
- `X1 + X2` = adds controls
- `.fit()` = fits the model and estimates the coefficients
- `C(unit_variable)` = includes unit fixed effects
- `C(time_variable)` = includes time fixed effects

### Check Parallel Trends
```python
# Visual
import seaborn as sns
import matplotlib.pyplot as plt

sns.lineplot(
    data = dataset_name,
    x = "time_variable",
    y = "Y",
    hue = "Treat",
    estimator = "mean"
)

plt.axvline(treat_time, linestyle="--")
plt.xlabel("Time")
plt.ylabel("Outcome")
plt.show()
```
### Definitions
- `sns.lineplot()` = plots the mean outcome for each group over time
- `hue="Treat"` = creates separate lines for the treatment and control groups
- `estimator = "mean"` = calculates the mean for each group and time period
- `plt.axvline()` = adds a dashed vertical line at the treatment time
- `plt.show()` = displays the plot

```python
# Compare pre-treatment trends
pre_treat = dataset_name[dataset_name["Post"] == 0]

pre_trends_model = smf.ols(
    "Y ~ Treat * time_variable",
    data=pre_treat
).fit()

print(pre_trends_model.summary())
```
### Definitions
- `pre_treat` = dataset with only pre-treatment observations
- `smf.ols()` = estimates the pre-treatment trend model
- `Treat * time_variable` = includes treatment status, time, and their interaction
- `.fit()` = fits the model
- `Treat:time_variable` = estimates the difference in pre-treatment trends between the treatment and control groups

---
