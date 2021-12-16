# Overview
This repository contains the R package I wrote as part of my research project for my M.Sc. in Data and Computational Science.

The main function, `TopLevelOptimise`, takes a dataset as input and returns the parameter estimates of the best-fitting Johnson SU distribution for the given dataset.

# Installation
```
library(devtools)
install_github("cjclarke98/JSUestimation")
```

# Usage
```
library(JSUestimation)
# access the accompanying sample data (the weekly log returns of the Irish Stock Exchange Quotient)
data(ISEQ)
TopLevelOptimise(ISEQ)
res = TopLevelOptimise(ISEQ)
res
```
