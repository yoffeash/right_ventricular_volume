Right Ventricular Mortality Analysis
================
Samuel Ash
2018-07-25

-   [Overview](#overview)
-   [Load Libraries and Data](#load-libraries-and-data)
    -   [Libraries](#libraries)
    -   [Data](#data)
-   [Analysis](#analysis)

Overview
========

Kaplan Meier analyes of mortality.

Load Libraries and Data
=======================

Libraries
---------

``` r
library(tidyverse)
library(survival)
library(survminer)
library(cowplot)
library(readxl)
library(naniar)
```

Data
----

Abbreviated data from GRW dated July 25, 2018.

``` r
RV_raw = read_excel("../data/RV size by arterial pruning and survival.xlsx")
```

Note that in this dataset: - those who are pruned i.e., those with aBV5<median, have arterial_prune=1, and those that are not pruned (aBV5>median) have arterial\_prune=0 - those that have RV\_vol=1 are those with large right ventricles (highest quartile) and those with with RV\_vol=0 have small right ventricles

Data cleaning: - remove all rows with NA

``` r
# change . to NA and remove those rows
RV_proc <- RV_raw %>% na_if(".") %>% na.omit()
# change days followed and vital status to numeric
RV_proc$days_followed <- as.numeric(RV_proc$days_followed)
RV_proc$vital_status <- as.numeric(RV_proc$vital_status)
```

Analysis
========

1.  Survival analysis by RV size only of entire dataset (ie those in this dataset which are those with GOLD &gt; 0)

``` r
KMRV <- survfit(Surv(days_followed, vital_status) ~ RV_vol, data=RV_proc)
surv.km.RV <- ggsurvplot(KMRV, data=RV_proc, risk.table = TRUE, pval = TRUE,
                            conf.int = TRUE, 
                            risk.table.y.text.col = T, risk.table.y.text = TRUE, 
                            legend = c(0.8,0.3), legend.title="Right Ventricular Size",
                            legend.labs=c("Lowest Three Quartiles","Highest Quartile"),
                            risk.table.height = 0.2,
                            palette=c("#E7B800", "#2E9FDF"),
                            title="Survival by Right Ventricular Size",
                            font.legend=c(14), font.title=c(20, "bold"),
                            font.x=c(14), font.y=c(14), font.tickslab=c(14),
                            xlab="Years")
surv.km.RV
```

![](RV_survival_with_pruning_files/figure-markdown_github/rv%20survival-1.png) Note that p value for RV volume alone using this definition (by highest quartile) is &gt;0.05

1.  Survival analysis just looking at those with big right ventricles (highest quartile)
