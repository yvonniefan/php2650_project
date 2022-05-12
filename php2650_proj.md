**Reference:**
<https://bmcmedresmethodol.biomedcentral.com/articles/10.1186/s12874-017-0383-8>

# Survival Analysis

## Survival Data

-   Goal:
    -   Assess the effect of risk factors on survival time - time until
        an event occurs, for example, death, diagnosis recovery, etc.
    -   Estimate and interpret survival
    -   Compare survival time / curve between groups
-   Outcome: Y = \[T, C\]
    -   T: Observed survival time: time to event (continuous)
    -   C: Censoring (or event) status: Yes/No (binary)

    <!-- Y_i: time to event; U_i Time to censoring -->
-   Data snapshot …

## Censoring

Consider *Y*<sub>*i*</sub>, *U*<sub>*i*</sub> where *T*<sub>*i*</sub> is
the time to event and *U*<sub>*i*</sub> is time to censoring, i.e. end
of follow up period. We can only observe the minimum of the two time,
whichever comes the first,
*T*<sub>*i*</sub> = *m**i**n*(*Y*<sub>*i*</sub>,*U*<sub>*i*</sub>).
Hence the actual exact survival time (T) is usually unknown, mainly due
to that the follow-up period is limited. Some instances are like when
there is no event happened up to the end of the follow-up period,
researchers lose contact with the patient, patient withdrawal
participation, or the exact date of the event is unknown. So we would
use censoring to account for the missing data issue. There are three
types of censoring:

-   Right censoring: *T* ≥ *T*<sub>*E*</sub> Event happened after study
    period
-   Left censoring: *T* ≤ *T*<sub>0</sub> Event happened before study
    period
-   Interval censoring: *T*<sub>*A*</sub> ≤ *T* ≤ *T*<sub>*B*</sub> Only
    know event time interval but don’t know the exact time

T: actual survival time  
*T*<sub>0</sub>: Start of study period  
*T*<sub>*E*</sub>: End of study period

… image?

For the Random Survival Forest method, we mainly focus on the
right-censoring data.

## Hazard Function

Survival analysis involves three characteristic functions: survival
function, hazard function, and cumulative hazard function, of which we
can derive all three given any one of the functions. The random survival
forest algorithm uses the cumulative hazard function. Hazard function
and cumulative hazard function are written as:

Hazard function (hazard rate): instantaneous potential per unit time for
the event to occur at time t, given survival up to time t:

$$h(t) = lim \\Delta t \\rightarrow 0 : \\frac {P(t\\leq T&lt;t+\\Delta t|T\\geq t)} {\\Delta(t)}$$

…image? weibull hazard function

Cumulative Hazard Function: integrate hazard function h(t) from time 0
to time t

*H*(*t*) = ∫<sub>0</sub><sup>*t*</sup>*h*(*u*) *d**u*

# Regression Models for Survival Data

## Cox Porportional Hazards Model (semi-parametric)

-   Y: \[T, C\]

    -   T: Observed survival time
    -   C: Censoring (or event) status

-   Hazard rate:

    $$h(t)=h\_0(t)e^{\\sum\_{j=1}^p\\beta\_jX\_j}$$
    assume *h*<sub>0</sub>(*t*) as fixed and *X*<sub>*j*</sub> are
    **time-independent**

## PH assumption

Cox proportional hazards model is usually used for right censored
time-to-event data. Mode is convenient for flexibility and simplicity,
but it’s restricted to *proportional hazards (PH) assumptions*, which is
to assume hazard ratio as independent of time t (constant over time). In
other words, the hazard rate at any time point t for a set a covariate
X\* is **proportional** to the hazard rate at the same time point t for
other set of covariate X.

Hazard Ratio *θ* is defined as:

$$HR=\\frac{h(t,X^\*)}{h(t,X)}=e^{\\sum\_{j=1}^p\\beta\_j(X\_j^\*-X\_j)}=\\theta$$

Graphically, this is represented as two roughly parallel survival curves
between two sets of covariates.

…image loglog survival curves

## Survival Trees as alternative to Cox PH

When PH assumption is violated, which is most of the cases for real life
data, we turn to survival trees and Random survival forest (RSF) models
as alternative methods to the Cox PH model. (BMC Medical paper)

In contrast of the semi-parametric Cox PH model, survival trees is a
fully *non-parametric* method, which is much more flexible and can
easily handle high dimensional covariate data. And it can automatically
detect certain types of **interactions** without the need to specify
them beforehand. However, the drawback of survival trees is that it
tends to be biased towards inclusion of variables with many split
points.

Conditional Inference Forest (CIF):

As a further alternative to survival trees, CIF is able to correct bias
in RSF “by separating the algorithm for selecting the best covariate to
split on from that of the best split point search” (BMC paper)

------------------------------------------------------------------------

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
