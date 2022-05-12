Survival analysis Censoring Characteristic functions: survival, hazard,
cumulative hazard KM curve: survival probability v.s. time Random
Survival Forest Formula Algorithm

# Survival Analysis

## Survival Data

-   Goal: Assess the effect of risk factors on survival
-   Outcome: Y\_i: time to event; U\_i Time to censoring

## Censoring

Types of Censoring: \* Right censoring: *T* ≥ *T*<sub>*E*</sub> \* Left
censoring: *T* ≤ *T*<sub>0</sub> \* Interval censoring:
*T*<sub>*A*</sub> ≤ *T* ≤ *T*<sub>*B*</sub>

T: actual survival time *T*<sub>0</sub>: Start *T*<sub>*E*</sub>: End of
followup

## Cox porportional hazards model

## PH assumption

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

# Survival Tree

Idea: partitioning the covariate space recursively to form groups of
subjects who are similar according to the time-to-event outcome.
Minimizing a given impurity measure. Goal: To identify prognostic
factors that are predictive of the time-to-event outcome.
