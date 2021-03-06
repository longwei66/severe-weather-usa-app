---
title       : USA Severe Weather - travel risk advisor
subtitle    : A shiny app to inform about severe weather in USA
author      : Barthelemy Longueville
job         : Coursera - Developping Data Products Assignement - July 2015
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : zenburn      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft, selfcontained}
knit        : slidify::knit2slides
---


<style>

code {
  font-size: 60%;
  font-family: "Source Code Pro", monospace;
  line-height: 0.9em;
  color: darkred;
}

/* Fonts and Spacing */
article p, article li, article li.build, section p, section li{
  font-family: 'Open Sans','Helvetica', 'Crimson Text', 'Garamond',  'Palatino', sans-serif;
  text-align: justify;
  font-size:20px;
  line-height: 1.1em;
  color: #444;
}

.title-slide {
  background-color: #DDDDDD; /* #EDE0CF; #CBE7A5 ; #CA9F9D*/
  /* background-image:url(http://goo.gl/EpXln); */
}

.title-slide hgroup > h2 {
  color: #535E43 ;  /* ; #EF5150*/
}
</style>

## App objectives and principle

The objective of this shiny app is to inform the user about the most critical severe weather risks which could be encoutered while travelling to a specific USA State during a specific month.

As inputs, the user have to select the month of travelling, an USA state of destination, the type of risk. Global risk index is selected per default, but one can choose health impact or damages impact

The app returns :

1. The most important severe weather event in term of impact (as selected by the user), with a map of USA showing where this type of weather event is the most active for the month
2. The estimation of what would be the best month to travel in this state
3. The estimation of what would be the best (safest) state to travel during this month

The app is available onn shinyapps : https://longwei.shinyapps.io/weather-shiny-app

--- .class #id 

## Data Source



The data used by this app is a cleaned and tidy data set of severe weather events in the US since 1950 evaluated from both financial and health impact cumed for each month of the year, each state and event type. Initial data is from the NOAA Storm Database (see 
http://rpubs.com/longwei66/87696 for details). This dataset is stored in the data frame 'weather' which consists in 7039 of 11 variables. An extract is presented bellow. 

<!-- html table generated in R 3.2.1 by xtable 1.7-4 package -->
<!-- Tue Jul 21 23:36:27 2015 -->
<table border=1>
<tr> <th>  </th> <th> month </th> <th> EVENT_TYPE </th> <th> STATE </th> <th> DMG </th> <th> health_impact </th> <th> riskIndex </th>  </tr>
  <tr> <td align="right"> 1 </td> <td align="right">   1 </td> <td> AVALANCHE </td> <td> AK </td> <td align="right"> 536000.00 </td> <td align="right">   6 </td> <td align="right"> 36.56 </td> </tr>
  <tr> <td align="right"> 2 </td> <td align="right">   1 </td> <td> AVALANCHE </td> <td> CA </td> <td align="right"> 0.00 </td> <td align="right">   6 </td> <td align="right"> 14.39 </td> </tr>
  <tr> <td align="right"> 3 </td> <td align="right">   1 </td> <td> AVALANCHE </td> <td> CO </td> <td align="right"> 401800.00 </td> <td align="right">  14 </td> <td align="right"> 41.72 </td> </tr>
   </table>

1. health_impact represent the sum of fatalities and injuries over the period (1950 to 2011) for the month mentioned in column month
2. DMG is the total damages (crop and property) in USD over the period for the month
3. Risk index is an aggregated risk evaluation (see next slide)

--- .class #id 

## Risk Index

The risk index is a composite of log of total damages (crop and property) in USD and health impact (in number of injuries and fatalities) normalised to be between 0 and 100. The charts bellow shows the distribution for each state / month / type of event.


```r
par(mfrow = c(1,4), mar = c(6, 5, 5, 6), oma = c(1, 1, 2, 0))
with(weather, {
        hist(log(health_impact), main = "Health impact (log of fatalities+injuries)", 
             col = "grey", cex.lab = 1.5, cex.axis = 1.5, cex.main = 1.5)
        hist(log(DMG), main = "Damages in UDS (log)", cex = 0.6, 
             col = "grey", cex.lab = 1.5, cex.axis = 1.5, cex.main = 1.5)
        hist(log(DMG + 1)/1.1 + 4 * log(health_impact + 1), main = "Risk index formula", 
             xlab = "Risk Index", cex.lab = 1.5, cex.axis = 1.5, cex.main = 1.5, col = "darkgreen")
        hist((log(DMG + 1)/1.1 + 4 * log(health_impact + 1))/max(log(DMG + 1)/1.1 + 4 * log(health_impact + 1))*100, 
             main = "Risk index formula (normalised to 100)", xlab = "Risk Index Norm.", 
             cex.lab = 1.5, cex.axis = 1.5, cex.main = 1.5, col = "firebrick")
        mtext("Defining composite riskIndex", outer = TRUE, cex = 1.5)})
```

![plot of chunk riskIndex](assets/fig/riskIndex-1.png) 


--- .class #id 

## Mapping the risks


To give better understanding of the risks, we have integrated a [choropleth map](https://en.wikipedia.org/wiki/Choropleth_map) of USA states, illustrating similar type of events risks.


```r
plot_weather_map(weather, eventType = "TORNADO", monthView = 5)
```

![plot of chunk mappingrisk](assets/fig/mappingrisk-1.png) 

We have followed this excellent tutorial to design the risk mapping function 
[https://trinkerrstuff.wordpress.com](https://trinkerrstuff.wordpress.com/2013/07/05/ggplot2-chloropleth-of-supreme-court-decisions-an-tutorial/). Our app code is available on github : [https://github.com/longwei66/](https://github.com/longwei66/severe-weather-usa-app/tree/master/weather-shiny-app)
