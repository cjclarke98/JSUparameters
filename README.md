# Overview
This repository contains the (updated) R package I wrote as part of my research project for my M.Sc. in Data and Computational Science.

The main function, `JSUparameters`, takes a dataset as input and returns the parameter estimates of the best-fitting Johnson SU distribution for the given dataset.

# Installation
```
# install directly from CRAN
install.packages("JSUparameters")

# install directly from GitHub
library(devtools)
install_github("cjclarke98/JSUparameters")
```

# Usage
```
library(JSUparameters)
# go to https://finance.yahoo.com/quote/%5EISEQ/history?period1=868838400&period2=1622419200&interval=1wk&filter=history&frequency=1wk&includeAdjustedClose=true
# download the csv file
# load in the csv file (check your own file path)
iseq = read.csv("Downloads/^ISEQ.csv")
# only store the closing price (convert from characters)
iseq = as.numeric(iseq$Close)
# interpolate the missing observation
which(is.na(iseq)) # 195
iseq[195] = (iseq[194] + iseq[196])/2
# calculate the log returns
returns = log(iseq[2:1247] / iseq[1:1246])
# find the best-fitting JSU distribution to this data
JSUparameters(returns)
```
