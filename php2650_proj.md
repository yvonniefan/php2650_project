**Reference:**
<https://bmcmedresmethodol.biomedcentral.com/articles/10.1186/s12874-017-0383-8>

# Survival Analysis

## Survival Data

-   Goal:
    -   Assess the effect of risk factors on survival time - time until
        an event occurs, for example, death, diagnosis recovery, etc.
    -   Estimate and interpret survival
    -   Compare survival time / curve between groups
-   Outcome:
    -   The outcome of interest is a combination of continuous variable
        represents time to event and a binary variable represents
        censoring status: Y = \[T, C\]  
    -   T: Observed survival time
    -   C: Censoring (or event) status
-   Data snapshot:

<img src="figures/f1_data.png" width="1140" />

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

<img src="figures/f2_censor.jpg" width="1323" /> *Image credit to
PHP2514 course material, Dr. Chrysanthopoulou*

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

<img src="https://render.githubusercontent.com/render/math?math=h(t) = lim \Delta t \rightarrow 0 : \frac {P(t\leq T<t+\Delta t|T\geq t)} {\Delta(t)}">

$$h(t) = lim \\Delta t \\rightarrow 0 : \\frac {P(t\\leq T&lt;t+\\Delta t|T\\geq t)} {\\Delta(t)}$$

<img src="figures/f3_weibull.jpg" width="1335" /> *Image credit to
PHP2514 course material, Dr. Chrysanthopoulou*

Cumulative Hazard Function: integrate hazard function h(t) from time 0
to time t

*H*(*t*) = ∫<sub>0</sub><sup>*t*</sup>*h*(*u*) *d**u*

# Regression Models for Survival Data

## Cox Porportional Hazards Model (semi-parametric)

-   Y: \[T, C\]
    -   T: Observed survival time
    -   C: Censoring (or event) status
-   Hazard rate:

Assume *h*<sub>0</sub>(*t*) as fixed and *X*<sub>*j*</sub> are
**time-independent**:

$$h(t)=h\_0(t)e^{\\sum\_{j=1}^p\\beta\_jX\_j}$$

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

Graphically, this is represented as roughly parallel survival curves
between different categories of a covariate. For example, the graph
below shows parallel pattern between high and low WBC, but non-parallel
between medium and the others.

<img src="figures/f4.png" width="1170" />

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

# Veteran Data

In this demo we’re using ‘veteran’ data from survival package, which
records data of randomized trial of two treatment regimens for lung
cancer.

    read.csv('figures/veteran.csv')  %>% kable(caption = 'Veteran Data')

<table>
<caption>
Veteran Data
</caption>
<thead>
<tr>
<th style="text-align:left;">
Variable
</th>
<th style="text-align:left;">
Explanation
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
trt:
</td>
<td style="text-align:left;">
1=standard 2=test
</td>
</tr>
<tr>
<td style="text-align:left;">
celltype:
</td>
<td style="text-align:left;">
1=squamous, 2=smallcell, 3=adeno, 4=large
</td>
</tr>
<tr>
<td style="text-align:left;">
time:
</td>
<td style="text-align:left;">
survival time
</td>
</tr>
<tr>
<td style="text-align:left;">
status:
</td>
<td style="text-align:left;">
censoring status
</td>
</tr>
<tr>
<td style="text-align:left;">
karno:
</td>
<td style="text-align:left;">
Karnofsky performance score (100=good)
</td>
</tr>
<tr>
<td style="text-align:left;">
diagtime:
</td>
<td style="text-align:left;">
months from diagnosis to randomisation
</td>
</tr>
<tr>
<td style="text-align:left;">
age:
</td>
<td style="text-align:left;">
in years
</td>
</tr>
<tr>
<td style="text-align:left;">
prior:
</td>
<td style="text-align:left;">
prior therapy 0=no, 10=yes
</td>
</tr>
</tbody>
</table>

First, we split data into training set and test set:

    library(survival)
    library(data.table)

    ## 
    ## Attaching package: 'data.table'

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     between, first, last

    ## The following object is masked from 'package:purrr':
    ## 
    ##     transpose

    library(ranger)

    ## Warning: package 'ranger' was built under R version 3.6.2

    library(caret)

    ## Loading required package: lattice

    ## 
    ## Attaching package: 'caret'

    ## The following object is masked from 'package:survival':
    ## 
    ##     cluster

    ## The following object is masked from 'package:purrr':
    ## 
    ##     lift

    set.seed(123456)

    data(veteran)

    ## Warning in data(veteran): data set 'veteran' not found

    vet = veteran
    vet$T = Surv(time=vet$time, event=vet$status)

    dt <- data.table(veteran)

    # Next, we split the data in a training and test set.
    ind <- sample(1:nrow(dt),round(nrow(dt) * 0.7,0))
    dt_train <- dt[ind,]
    dt_test <- dt[!ind,]
    vet.tr = vet[ind,]
    vet.te = vet[-ind,]

Use Kaplan Meier Curve as a visual summary of the survival probability
between treatment groups:

    # plot survival curve:
    kmvet = survfit(T~trt, data=vet)
    # km curve
    plot(kmvet, col=c('blue','red'), xlab='Time', ylab='Survival Probability', main='Kaplan Meier Curves')
    legend("topright", lwd = 1, col = c('blue','red'), cex=0.7, y.intersp = 0.5,
           legend = c('trt=1', 'trt=2'))
    abline(h=0.5,lty=3)

![](php2650_proj_files/figure-markdown_strict/unnamed-chunk-7-1.png)

## Cox PH Model

We first fit a COX PH Model. Using backward selection, we found the
‘best’ variable to fit the data. However, the log-log survival curves
show non-parallel curves between variable groups. So the PH assumption
might be violated.

    ########################################  COX PH ####################################
    coxm0 = coxph(T~(celltype+trt+karno+diagtime+age+prior)^2, data=vet.tr, ties='breslow')
    # coxm1 = step(coxm0, direction = "backward")

    # model selected from backward selection
    coxm1 = coxph(formula = T ~ celltype + trt + karno + diagtime + age +
        prior + celltype:diagtime + trt:karno + trt:prior + karno:diagtime +
        karno:age, data = vet.tr, ties = "breslow")
    # summary(coxm1)

    # PH assumption: non-parallel, violated
    par(mfrow=c(2,2))
    sapply(list(vet.tr$trt, vet.tr$celltype, vet.tr$karno, vet.tr$prior), 
           function(var) plot(survfit(T ~ var, data=vet.tr),
                              col=1:10,
                              fun="cloglog",
                              ylab='log(-log(S(t)))', 
                              xlab='log(time)',
                              xlim=c(20,250),
                              ylim=c(-2,0.5),
                              main='Log-Log Survival Curves')
    )

![](php2650_proj_files/figure-markdown_strict/unnamed-chunk-8-1.png)

The goodness of fit test based on Schoenfeld Residuals is another way to
test the PH assumption, which is the null hypothesis. However, we can
see a lot of p values of less than 0.05, this validated our concern that
the proportional hazard assumption is violated.

    cox.zph(coxm1) 

    ##                     chisq df      p
    ## celltype           8.0306  3 0.0454
    ## trt                0.0141  1 0.9055
    ## karno              9.4653  1 0.0021
    ## diagtime           0.0400  1 0.8415
    ## age                0.2297  1 0.6317
    ## prior              3.5195  1 0.0607
    ## celltype:diagtime  3.5644  3 0.3125
    ## trt:karno          2.6757  1 0.1019
    ## trt:prior          4.8684  1 0.0274
    ## karno:diagtime     2.2032  1 0.1377
    ## karno:age          9.4110  1 0.0022
    ## GLOBAL            32.7959 15 0.0050

## Random Survival Models

### Use ranger package to train the random survival models.

Recall that in random forest, we need to find number of variables **mtry
** to randomly select from at each node. First, we find the optimal mtry
parameter that gives the smallest OOB error. Over 1000 iterations, m = 3
has the highest frequency of giving the smallest OOB error.

    ########################################  Ranger #############################################
    par(mfrow=c(1,1))

    # I created a simple function to find best m:
    functune = function(m){
      sapply(1:6, function(m) ranger(Surv(time, status) ~ .,
                    data = dt_train,
                    mtry = m,
                    verbose = TRUE,
                    write.forest=TRUE,
                    num.trees= 1000,
                    importance = 'permutation')$prediction.error)
    }
    # I commented out this line of code to save running time: 
    # findM = apply(replicate(100,functune()), 2, which.min)
    # save(findM, file='findM')
    load(file='findM')
    # m = 3 is the optimal m
    # table(findM) 

So we fit random survival tree with m = 3.

    # fit random survival tree
    r_fit <- ranger(Surv(time, status) ~ .,
                    data = dt_train,
                    mtry = 3,
                    verbose = TRUE,
                    write.forest=TRUE,
                    num.trees= 1000,
                    importance = 'permutation')

The model gives the fitted survival probability in a table format, where
rows represent individual patient and columns represent event time
points, from beginning to the end.

    # distinct survival time in training data
    # r_fit$unique.death.times

    # fitted survival: survival probability
    # rows represent individual patient; columns represent event time points
    fit.surv = r_fit$survival
    fit.surv %>% data.frame() %>% head(5)

    ##          X1        X2        X3        X4        X5        X6        X7
    ## 1 0.9803422 0.9615677 0.8959521 0.8084944 0.8072767 0.8072767 0.8064427
    ## 2 0.9903623 0.9903623 0.9877519 0.9212304 0.9201504 0.9014983 0.8864864
    ## 3 0.9552650 0.8765003 0.8644494 0.7720116 0.7649667 0.7594641 0.7502806
    ## 4 0.9934633 0.9868441 0.9661402 0.9604876 0.9604876 0.9553862 0.9250581
    ## 5 0.9600375 0.9600375 0.9297039 0.8834538 0.8834538 0.8828354 0.8631281
    ##          X8        X9       X10       X11       X12       X13       X14
    ## 1 0.7854943 0.7606922 0.6631539 0.6273708 0.6174266 0.5657040 0.5519285
    ## 2 0.8859667 0.8854472 0.8584663 0.8304848 0.8093666 0.7898850 0.7116501
    ## 3 0.7297835 0.7269596 0.7122085 0.6867723 0.6867723 0.6738877 0.6726276
    ## 4 0.9059823 0.9046430 0.9033058 0.8432768 0.8432768 0.8422795 0.8346309
    ## 5 0.8495754 0.8495754 0.8285358 0.8210273 0.8183486 0.7994301 0.7886219
    ##         X15       X16       X17       X18       X19       X20       X21
    ## 1 0.5481384 0.5397754 0.5133618 0.5120370 0.5120370 0.5113759 0.4775195
    ## 2 0.6902397 0.6767440 0.6347409 0.6221174 0.6221174 0.6221174 0.5909795
    ## 3 0.6171515 0.6171515 0.6152685 0.6085333 0.5974626 0.5974626 0.5931120
    ## 4 0.7484001 0.7484001 0.7484001 0.7405078 0.7157217 0.7157217 0.7141353
    ## 5 0.7724073 0.7218619 0.6949445 0.6801409 0.6801409 0.6340991 0.6340991
    ##         X22       X23       X24       X25       X26       X27       X28
    ## 1 0.4756722 0.4596421 0.4470115 0.4424150 0.4329902 0.4304060 0.4273584
    ## 2 0.5664597 0.5664597 0.5550818 0.5414164 0.5414164 0.5120746 0.5105751
    ## 3 0.5931120 0.5034251 0.4789155 0.4784888 0.4780625 0.4548015 0.3943352
    ## 4 0.7099221 0.6143286 0.6143286 0.6125138 0.5954791 0.5538429 0.4303773
    ## 5 0.5832749 0.5832749 0.5322936 0.5156608 0.5113457 0.4869296 0.4839382
    ##         X29       X30       X31       X32       X33       X34       X35
    ## 1 0.3965729 0.3950388 0.3931041 0.3898166 0.3898166 0.3809377 0.3734654
    ## 2 0.5012778 0.4962318 0.4958681 0.4890087 0.4615135 0.4323404 0.3928839
    ## 3 0.3668704 0.3650765 0.3581770 0.3581770 0.3581770 0.3391180 0.3388159
    ## 4 0.4077757 0.4071730 0.4053700 0.4053700 0.4053700 0.3912306 0.3912306
    ## 5 0.4825395 0.4825395 0.4825395 0.4764952 0.4575082 0.4569958 0.4057051
    ##         X36       X37       X38       X39       X40       X41       X42
    ## 1 0.3708210 0.3558791 0.3558791 0.3558791 0.3522197 0.3522197 0.3522197
    ## 2 0.3723371 0.2952248 0.2952248 0.2952248 0.2952248 0.2952248 0.2924244
    ## 3 0.3352862 0.3181108 0.3181108 0.3060819 0.3060819 0.3056730 0.3048567
    ## 4 0.3776217 0.3776217 0.3776217 0.3598608 0.3440702 0.3411379 0.3402979
    ## 5 0.3720489 0.3435984 0.3435984 0.3319308 0.3319308 0.3314662 0.3314662
    ##         X43       X44       X45       X46       X47       X48       X49
    ## 1 0.3280880 0.3276644 0.3232076 0.3115364 0.3115364 0.3042177 0.3031714
    ## 2 0.2922101 0.2922101 0.2890849 0.2858534 0.2858534 0.2854346 0.2812798
    ## 3 0.2750357 0.2750357 0.2715042 0.2661138 0.2657582 0.2639407 0.2517852
    ## 4 0.3195227 0.3142879 0.3142879 0.2552783 0.2552783 0.2552783 0.2552783
    ## 5 0.3314662 0.3314662 0.3138465 0.3009048 0.2993635 0.2932768 0.2932768
    ##         X50       X51       X52       X53       X54       X55       X56
    ## 1 0.3031714 0.2980577 0.2980577 0.2968791 0.2968791 0.2968791 0.2932954
    ## 2 0.2812798 0.2763739 0.2751609 0.2751609 0.2738862 0.2738862 0.2730842
    ## 3 0.2517852 0.2497180 0.2477229 0.2477229 0.2477229 0.2470614 0.2470614
    ## 4 0.2552783 0.2552783 0.2541479 0.2475798 0.2475798 0.2457553 0.2445224
    ## 5 0.2932768 0.2851225 0.2851225 0.2827365 0.2824727 0.2824727 0.2824727
    ##         X57       X58       X59       X60       X61       X62       X63
    ## 1 0.2920350 0.2760154 0.2700192 0.2655104 0.2583367 0.2583367 0.2583367
    ## 2 0.2708908 0.2619331 0.2614215 0.2592837 0.2592837 0.2592837 0.2592837
    ## 3 0.2470614 0.2196009 0.2196009 0.2196009 0.2172647 0.2172647 0.2172647
    ## 4 0.2445224 0.2445224 0.2433728 0.2415198 0.2411322 0.2377086 0.2377086
    ## 5 0.2695255 0.2695255 0.2665225 0.2665225 0.2665225 0.2605434 0.2605434
    ##         X64       X65       X66       X67       X68       X69       X70
    ## 1 0.2564523 0.2477778 0.2460764 0.2423428 0.2351967 0.2351967 0.2315784
    ## 2 0.2467951 0.2460724 0.2460724 0.2414853 0.2407781 0.2407781 0.2407781
    ## 3 0.2172647 0.2138070 0.2138070 0.2104043 0.2077027 0.2065949 0.2065949
    ## 4 0.2370063 0.2310635 0.2201523 0.2182069 0.2171336 0.2171336 0.2073398
    ## 5 0.2605434 0.2605434 0.2605434 0.2562011 0.2562011 0.2544133 0.2544133
    ##         X71       X72       X73       X74       X75       X76       X77
    ## 1 0.2315784 0.1998629 0.1983196 0.1962803 0.1962803 0.1962803 0.1962803
    ## 2 0.2407781 0.1876557 0.1865583 0.1865583 0.1865583 0.1865583 0.1865583
    ## 3 0.2065949 0.2065949 0.2065949 0.2063189 0.2063189 0.1998932 0.1951403
    ## 4 0.2073398 0.2067273 0.2067273 0.1887501 0.1870823 0.1865296 0.1859785
    ## 5 0.2426956 0.2294730 0.2256485 0.2256485 0.2069776 0.2046715 0.2046715

Then I samples 4 individuals to plot fitted survival curve.

    par(mfrow=c(1,1))
    set.seed(12345)
    individual = sample(1:dim(dt_train)[1], 4)
    plot(r_fit$unique.death.times, fit.surv[individual[1],], type = 'l', col = 'red',xlab='Time', ylab='Survival Probability', main='Fitted Survival Curve (4 individuals)')
    lines(r_fit$unique.death.times,fit.surv[individual[2],], type = 'l', col = 'blue',xlab='Time', ylab='Survival Probability')
    lines(r_fit$unique.death.times,fit.surv[individual[3],], type = 'l', col = 'green',xlab='Time', ylab='Survival Probability')
    lines(r_fit$unique.death.times,fit.surv[individual[4],], type = 'l', col = 'pink',xlab='Time', ylab='Survival Probability')
    abline(v=dt_train[individual,]$time, lty=3, col=c('red','blue','green','pink'))
    legend("topright", lwd = 1, col = c('red','blue','green','pink'),
           legend = c('id14', 'id51', 'id80', 'id90'))

![](php2650_proj_files/figure-markdown_strict/unnamed-chunk-13-1.png)

    dt_train[individual,]

    ##    trt  celltype time status karno diagtime age prior
    ## 1:   2 smallcell   21      1    20        4  71     0
    ## 2:   1     large  250      1    70        8  53    10
    ## 3:   2     large  378      1    80        4  65     0
    ## 4:   1  squamous  314      1    50       18  43     0

The variable importance obtained here is very similar to what Cox PH
model gave - cell type, treatment, karno, diagtime, age and prior are
all considered as important predictor of survival time, except that Cox
PH model is able to show interactions between the variables.

    r_fit$variable.importance

    ##           trt      celltype         karno      diagtime           age 
    ##  0.0051800025  0.0240913985  0.0873113429 -0.0032790188 -0.0061162517 
    ##         prior 
    ## -0.0004687483

In the following chunk of code, I calculated the accuracy of the model
when it needs to predict survival after 61 days (this is purely
arbitrary for demonstrational purposes).

    preds <- predict(r_fit, dt_test, type = 'response')$survival
    pred.surv <- data.table(preds)
    colnames(pred.surv) <- as.character(r_fit$unique.death.times)

    prediction <- pred.surv$`61` > 0.5
    real <- dt_test$time >= 61
    # confusion matrix:
    table(prediction, real)

    ##           real
    ## prediction FALSE TRUE
    ##      FALSE    12    8
    ##      TRUE      5   16
