---
title       : MPG Prediction
subtitle    : Developing Data Products Course Project
author      : Mohd Fauzi Mohamad
job         : 
framework   : revealjs      # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : default       # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## Fuel Efficiency

Customers are very concscious with fuel efficiency, for various reasons:
- rising cost of fuel
- increasing commute times
- increasing concern for the environment

It's important to be able to predict the fuel efficiency of new cars during the
design stage. Many factors can effect the fuel efficiency, including:
- number of cylinders
- gross horsepower
- weight
- engine displacement
- rear axle ratio
- transmission type

---

## Using the mtcars dataset

The ```mtcars``` dataset can be used to build a model of the effects of car 
characteristics on fuel efficiency.  An example of the data is below:

```r
data(mtcars); library(xtable)
print(xtable(mtcars[1:8,]), type = "html")
```

<!-- html table generated in R 3.2.2 by xtable 1.7-4 package -->
<!-- Sun Sep 27 05:32:13 2015 -->
<table border=1>
<tr> <th>  </th> <th> mpg </th> <th> cyl </th> <th> disp </th> <th> hp </th> <th> drat </th> <th> wt </th> <th> qsec </th> <th> vs </th> <th> am </th> <th> gear </th> <th> carb </th>  </tr>
  <tr> <td align="right"> Mazda RX4 </td> <td align="right"> 21.00 </td> <td align="right"> 6.00 </td> <td align="right"> 160.00 </td> <td align="right"> 110.00 </td> <td align="right"> 3.90 </td> <td align="right"> 2.62 </td> <td align="right"> 16.46 </td> <td align="right"> 0.00 </td> <td align="right"> 1.00 </td> <td align="right"> 4.00 </td> <td align="right"> 4.00 </td> </tr>
  <tr> <td align="right"> Mazda RX4 Wag </td> <td align="right"> 21.00 </td> <td align="right"> 6.00 </td> <td align="right"> 160.00 </td> <td align="right"> 110.00 </td> <td align="right"> 3.90 </td> <td align="right"> 2.88 </td> <td align="right"> 17.02 </td> <td align="right"> 0.00 </td> <td align="right"> 1.00 </td> <td align="right"> 4.00 </td> <td align="right"> 4.00 </td> </tr>
  <tr> <td align="right"> Datsun 710 </td> <td align="right"> 22.80 </td> <td align="right"> 4.00 </td> <td align="right"> 108.00 </td> <td align="right"> 93.00 </td> <td align="right"> 3.85 </td> <td align="right"> 2.32 </td> <td align="right"> 18.61 </td> <td align="right"> 1.00 </td> <td align="right"> 1.00 </td> <td align="right"> 4.00 </td> <td align="right"> 1.00 </td> </tr>
  <tr> <td align="right"> Hornet 4 Drive </td> <td align="right"> 21.40 </td> <td align="right"> 6.00 </td> <td align="right"> 258.00 </td> <td align="right"> 110.00 </td> <td align="right"> 3.08 </td> <td align="right"> 3.21 </td> <td align="right"> 19.44 </td> <td align="right"> 1.00 </td> <td align="right"> 0.00 </td> <td align="right"> 3.00 </td> <td align="right"> 1.00 </td> </tr>
  <tr> <td align="right"> Hornet Sportabout </td> <td align="right"> 18.70 </td> <td align="right"> 8.00 </td> <td align="right"> 360.00 </td> <td align="right"> 175.00 </td> <td align="right"> 3.15 </td> <td align="right"> 3.44 </td> <td align="right"> 17.02 </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> <td align="right"> 3.00 </td> <td align="right"> 2.00 </td> </tr>
  <tr> <td align="right"> Valiant </td> <td align="right"> 18.10 </td> <td align="right"> 6.00 </td> <td align="right"> 225.00 </td> <td align="right"> 105.00 </td> <td align="right"> 2.76 </td> <td align="right"> 3.46 </td> <td align="right"> 20.22 </td> <td align="right"> 1.00 </td> <td align="right"> 0.00 </td> <td align="right"> 3.00 </td> <td align="right"> 1.00 </td> </tr>
  <tr> <td align="right"> Duster 360 </td> <td align="right"> 14.30 </td> <td align="right"> 8.00 </td> <td align="right"> 360.00 </td> <td align="right"> 245.00 </td> <td align="right"> 3.21 </td> <td align="right"> 3.57 </td> <td align="right"> 15.84 </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> <td align="right"> 3.00 </td> <td align="right"> 4.00 </td> </tr>
  <tr> <td align="right"> Merc 240D </td> <td align="right"> 24.40 </td> <td align="right"> 4.00 </td> <td align="right"> 146.70 </td> <td align="right"> 62.00 </td> <td align="right"> 3.69 </td> <td align="right"> 3.19 </td> <td align="right"> 20.00 </td> <td align="right"> 1.00 </td> <td align="right"> 0.00 </td> <td align="right"> 4.00 </td> <td align="right"> 2.00 </td> </tr>
   </table>

Henderson and Velleman (1981), Building multiple regression models interactively. *Biometrics*, 37, 391–411.

---

## Modelling predictor contribution

Using a linear model, I determined the effect of gross horsepower, number of 
cylinders, and weight on fuel efficiency (mpg), and used this model to create a
function to predict fuel efficiency based on these factors.

```r
modelFit <- lm(mpg ~ hp + cyl + wt, data=mtcars)
modelFit$coefficients
```

```
## (Intercept)          hp         cyl          wt 
##  38.7517874  -0.0180381  -0.9416168  -3.1669731
```

```r
mpg <- function(hp, cyl, wt) {
    modelFit$coefficients[1] + modelFit$coefficients[2] * hp + 
        modelFit$coefficients[3] * cyl + modelFit$coefficients[4] * wt
}
```

---

## MPG Prediction Shiny App

![MPG Prediction App](./assets/img/shinyapp.png)

The user inputs predictor values in the left sidebar, fuel effiency predictions
are output to the main panel, and plots below the prediction show how the mpg
compares with data from the mtcars dataset.
