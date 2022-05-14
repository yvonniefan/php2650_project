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

### Conditional Inference Forest (CIF)\*:

“The random survival forests algorithm, has been criticised for having a
bias towards selecting variables with many split points and the
conditional inference forest algorithm has been identified as a method
to reduce this selection bias.”. As a further alternative to survival
trees, CIF is able to correct bias in RSF “by separating the algorithm
for selecting the best covariate to split on from that of the best split
point search” (BMC paper)

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

# Application in R

## Veteran ranger package

In this demo we’re using ‘veteran’ data from survival package, which
records data of randomized trial of two treatment regimens for lung
cancer.

trt: 1=standard 2=test celltype: 1=squamous, 2=smallcell, 3=adeno,
4=large time: survival time status: censoring status karno: Karnofsky
performance score (100=good) diagtime: months from diagnosis to
randomisation age: in years prior: prior therapy 0=no, 10=yes

    library(survival)
    library(data.table)
    library(ranger)

    ## Warning: package 'ranger' was built under R version 3.6.2

    library(caret)

    ## Loading required package: lattice

    ## Loading required package: ggplot2

    ## Warning: package 'ggplot2' was built under R version 3.6.2

    ## 
    ## Attaching package: 'caret'

    ## The following object is masked from 'package:survival':
    ## 
    ##     cluster

    set.seed(19880303)


    data(veteran)

    ## Warning in data(veteran): data set 'veteran' not found

    vet = veteran
    head(vet)

    ##   trt celltype time status karno diagtime age prior
    ## 1   1 squamous   72      1    60        7  69     0
    ## 2   1 squamous  411      1    70        5  64    10
    ## 3   1 squamous  228      1    60        3  38     0
    ## 4   1 squamous  126      1    60        9  63    10
    ## 5   1 squamous  118      1    70       11  65    10
    ## 6   1 squamous   10      1    20        5  49     0

    vet$T = Surv(time=vet$time, event=vet$status)

    dim(dt)

    ## NULL

    dt <- data.table(veteran)
    # dt[,9] = Surv(time=vet$time, event=vet$status)

    rm(veteran)

    ## Warning in rm(veteran): object 'veteran' not found

    # plot survival curve:
    dim(dt)

    ## [1] 137   8

    kmvet = survfit(T~trt, data=vet)
    # km curve
    print(kmvet)

    ## Call: survfit(formula = T ~ trt, data = vet)
    ## 
    ##        n events median 0.95LCL 0.95UCL
    ## trt=1 69     64  103.0      59     132
    ## trt=2 68     64   52.5      44      95

    plot(kmvet, col=c('blue','red'), xlab='Time', ylab='Survival Probability', main='Kaplan Meier Curves')
    legend("topright", lwd = 1, col = c('blue','red'), cex=0.7, y.intersp = 0.5,
           legend = c('trt=1', 'trt=2'))
    abline(h=0.5,lty=3)

![](php2650_proj_files/figure-markdown_strict/unnamed-chunk-1-1.png)

    # Next, we split the data in a training and test set.
    ind <- sample(1:nrow(dt),round(nrow(dt) * 0.7,0))
    dt_train <- dt[ind,]
    dt_test <- dt[!ind,]
    dim(dt_train)

    ## [1] 96  8

    dim(dt_test)

    ## [1] 41  8

    vet.tr = vet[ind,]
    vet.te = vet[-ind,]

## Python
