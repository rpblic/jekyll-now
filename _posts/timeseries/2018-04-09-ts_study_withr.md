---
layout: post
title: R과 시계열 분석 스터디
category: Timeseries
tags: ['R', 'Arima', 'HoltWinters', 'STL', 'TBATS']
---

# R과 시계열 분석 스터디

@(심규민)[R, Arima, Holtwinters, STL, TBATS]


![Arima](/public/img/ts_study_withr/arimaexample.jpg)

```r
library('ggplot2')
library('forecast')
library('tseries')
library('dplyr')

dt= read.csv('C:\\Studying\\nextop_engine\\_element\\data\\rawdata.csv', header= TRUE)
dt$ds= as.Date(dt$ds)

#Deleting Sat/Sun data
dt$wkds= weekdays(dt$ds)
dt= dt %>% filter(wkds != "토요일" & wkds!= "일요일")
ggplot(dt, aes(ds, y_sum)) + geom_line()

#Using Data Cleansing
count_ts= ts(dt[, c('y_sum')])
count_ts= tsclean(count_ts)
dt$cleansed_y_sum= count_ts
ggplot(dt, aes(ds, cleansed_y_sum)) + geom_line()

adf.test(dt$cleansed_y_sum, alternative = "stationary")
# Already stationary enough: p-value= 0.01

#plot Acf/Pacf
Acf(dt$cleansed_y_sum, main= '')
Pacf(dt$cleansed_y_sum, main='')

#Take test data(as `30 days)
compare_hold= window(dt$cleansed_y_sum, start= 2036)
compare_log_hold= window(log(dt$cleansed_y_sum), start= 2036)

#Use Arima/Seasonal Arima
arima_fit= auto.arima(dt$cleansed_y_sum[0:-30], seasonal= FALSE)
arima_log_fit= auto.arima(log(dt$cleansed_y_sum[0:-30]), seasonal = FALSE)
sarima_fit= auto.arima(dt$cleansed_y_sum[0:-30], seasonal= TRUE)
sarima_log_fit= auto.arima(log(dt$cleansed_y_sum[0:-30]), seasonal = TRUE)
sarima2_fit= arima(dt$cleansed_y_sum[0:-30], order = c(4,1,2), seasonal = list(order = c(1,1,1), period = 364))

fcst_arima= forecast(arima_fit, h=30)
png(filename=".\\Arima,auto,4,1,2.png", height=600, width=900, bg="white")
plot(fcst_arima)
lines(compare_hold, col= 'red')
dev.off()

#Draw forecast images
fcst_log_arima= forecast(arima_log_fit, h=30)
png(filename=".\\Arima,logscale,auto,1,1,5.png", height=600, width=900, bg="white")
plot(fcst_log_arima)
lines(compare_log_hold, col= 'red')
dev.off()

fcst_sarima= forecast(sarima_fit, h=30)
png(filename=".\\SeasonalArima,auto,4,1,2.png", height=600, width=900, bg="white")
plot(fcst_sarima)
lines(compare_hold, col= 'red')
dev.off()

fcst_log_sarima= forecast(sarima_log_fit, h=30)
png(filename=".\\SeasonalArima,logscale,auto,1,1,5.png", height=600, width=900, bg="white")
plot(fcst_log_sarima)
lines(compare_log_hold, col= 'red')
dev.off()

#Check accuracy
accuracy(fcst_arima, compare_hold)
accuracy(fcst_sarima, compare_hold)
```

```r
library(forecast)
library(zoo)

#Use sine+error func as data
x<-c(1:120)
y <- sin(x/20) + rnorm(120,sd=.1)
data <- cbind(x,y)
tsdata <- ts(data,start=c(2001,1),frequency=12)
set.seed(123)
plot(tsdata,plot.type="m")


#Plot moving average methods
k <- 5
plot(tsdata[,2])
### FILTER FUNCTION
lines(filter(tsdata[,2], rep(1/k,k), sides=2), col = "red")
### MA FUNCTION
lines(ma(tsdata[,2], order=k, centre = TRUE), col="blue")
### ROLLMEAN
lines(rollmean(tsdata[,2], k, fill=NA, align="center"),col='green')


#Plot Holtwinters Decomposed values
fit_hw <- HoltWinters(tsdata[,2], alpha = NULL, beta = NULL, gamma = NULL,  seasonal = "additive")
plot(tsdata[,2])
lines(fit_hw$fitted[,1], col="red")     # xhat
lines(fit_hw$fitted[,2], col="grey")     # level
lines(fit_hw$fitted[,3], col="green")     # trend
lines(fit_hw$fitted[,4], col="blue")     # season
#Plot Holtwinters Forecasting
plot(forecast(fit_hw, h=12))


#Plot STL Decomposed values
fit_stl <- stl(tsdata[,2],s.window="per")
plot(fit_stl)
plot(tsdata[,2])
lines(fit_stl$time.series[,1]+fit_stl$time.series[,2], col="red")
lines(fit_stl$time.series[,1], col="blue")     # season
lines(fit_stl$time.series[,2], col="green")     # trend
lines(fit_stl$time.series[,3], col="grey")     # remainder
#Plot STL Forecasting
plot(forecast(fit_stl, h=12))
```

```r
library(forecast)
library(tseries)


# Getting data
sales_s1 <- read.csv('C:\\Studying\\nextop_engine\\_element\\data\\rawdata.csv', header= TRUE)
head(sales_s1)

#Plot data

par(mfrow=c(3,1))
ts.plot(sales_s1$site1)
ts.plot(sales_s1$site2)
ts.plot(sales_s1$total)

#Divide three train/test data
ms_t1 <- sales_s1$total[1:1099]
ts_t1 <- sales_s1$total[1100:1127]

ms_t2 <- sales_s1$total[1:1069]
ts_t2 <- sales_s1$total[1070:1097]

ms_t3 <- sales_s1$total[1:1039]
ts_t3 <- sales_s1$total[1040:1067]


# Take single/multi Seasonality
ms_t1 <- ts(ms_t1, frequency = 7) # single seasonality
ms_t2 <- ts(ms_t2, frequency = 7) # single seasonality
ms_t3 <- ts(ms_t3, frequency = 7) # single seasonality

ms_mt1 <- msts(ms_t1, seasonal.periods = c(7, 365)) # multi-seasonal
ms_mt2 <- msts(ms_t2, seasonal.periods = c(7, 365)) # multi-seasonal
ms_mt3 <- msts(ms_t3, seasonal.periods = c(7, 365)) # multi-seasonal


# Using TBATS

t1_tbats <- tbats(ms_mt1)
t2_tbats <- tbats(ms_mt2)
t3_tbats <- tbats(ms_mt3)

tbats_fc1 <- forecast(t1_tbats, h=28) 
tbats_fc2 <- forecast(t2_tbats, h=28)
tbats_fc3 <- forecast(t3_tbats, h=28)


# Get negative values to 0

tbats_fc1$mean[tbats_fc1$mean<0] <- 0 
tbats_fc2$mean[tbats_fc2$mean<0] <- 0 
tbats_fc3$mean[tbats_fc3$mean<0] <- 0 

# Plot data

plot(tbats_fc1, main = "Forecasts with Trend, Seasonality & ARMA : TBATS")
plot(tbats_fc2, main = "Forecasts with Trend, Seasonality & ARMA : TBATS")
plot(tbats_fc3, main = "Forecasts with Trend, Seasonality & ARMA : TBATS")

# Get MAPE


100-100*(abs(sum(tbats_fc1$mean)-(sum(ts_t1)))/(sum(ts_t1)))
100-100*(abs(sum(tbats_fc2$mean)-(sum(ts_t2)))/(sum(ts_t2)))
100-100*(abs(sum(tbats_fc3$mean)-(sum(ts_t3)))/(sum(ts_t3)))
```