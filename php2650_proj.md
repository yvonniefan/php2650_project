**Reference:**
<https://bmcmedresmethodol.biomedcentral.com/articles/10.1186/s12874-017-0383-8>

# Survival Analysis

## Survival Data

-   Goal: Assess the effect of risk factors on survival
-   Outcome: Y\_i: time to event; U\_i Time to censoring

## Censoring

Types of Censoring:

-   Right censoring: *T* ≥ *T*<sub>*E*</sub>
-   Left censoring: *T* ≤ *T*<sub>0</sub>
-   Interval censoring: *T*<sub>*A*</sub> ≤ *T* ≤ *T*<sub>*B*</sub>

T: actual survival time  
*T*<sub>0</sub>: Start  
*T*<sub>*E*</sub>: End of followup

## Hazard Function

$$h(t)=\\frac{P(t\\leq T&lt;t+\\Delta t|T\\geq t)}{\\Delta(t)}$$
Cumulative Hazard Function:
*H*(*t*) = ∫<sub>0</sub><sup>*t*</sup>*h*(*u*) *d**u*

## Cox porportional hazards model

-   Y: \[T, C\]

    -   T: Observed survival time
    -   C: Censoring (or event) status

-   Hazard rate:

    $$h(t)=h\_0(t)exp(\\sum\_{j=1}^p\\beta\_jX\_j)$$
    assume *X* − *j* time-independent.

## PH assumption

Hazard Ratio: Assuming theta independent of time (constant over time)

$$HR=\\frac{h(t,X^\*)}{h(t,X)}=exp(\\sum\_{j=1}^p\\beta\_j(X\_j^\*-X\_j)}) = \\theta$$

## RSF vs CIF (conditional inference forest)

Survival trees and Random survival forest (RSF) models have been
identified as alternative methods to the Cox proportional hazards model
in analysing time-to-event data. (BMC Medical paper)

-   As alternative when PH assumption violated
-   survival trees: fully *non-parametric* - flexible, easily handle
    high dimensional covariate data
    -   flexibility and can automatically detect certain types of
        **interactions** without the need to specify them beforehand
    -   drawback: bias towards inclusion of variables with many split
        points

CIF: correct bias in RSF “by separating the algorithm for selecting the
best covariate to split on from that of the best split point search
\[15, 17, 18\].”

*Cox*-proportional hazards model is usually used for right censored
time-to-event data. Mode is convenient for flexibility and simplicity,
but it’s restricted to *proportional hazards (PH) assumptions*.

# Survival Random Forest / Survival Tree

Idea: partitioning the covariate space recursively to form groups of
subjects who are similar according to the time-to-event outcome.
Minimizing a given impurity measure. Goal: To identify prognostic
factors that are predictive of the time-to-event outcome.

## Split Rules:

### The log-rank split-rule …

… The best split at a node h, on a covariate x at a split point s ∗ is
the one that gives the largest log-rank statistic between the two
daughter nodes ..

### The log-rank score split-rule …

… Trees are generally unstable and hence researchers have recommended
the growing of a collection of trees \[10, 27\], commonly referred to as
random survival forests \[20, 26\].

# CIF conditional inference forest

The random survival forests algorithm, has been criticised for having a
bias towards selecting variables with many split points and the
conditional inference forest algorithm has been identified as a method
to reduce this selection bias.

# Application

## R

GBSG2, pbc data from survival package

## Python
