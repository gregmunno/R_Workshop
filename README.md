# R Workshop
Nov. 8, 2019, Workshop at the Newhouse School 

## Installing R 
Download and install R at https://cran.case.edu/

Download and install RStudio https://www.rstudio.com/products/rstudio/download/ . Not to make sure you install R first, then R Studio 

## What is R?
R is a programing language that specializes in statistical analysis and the plotting of data. Written mostly in C and in R itself, it first appeared in 1993. It is free and open source, and is a top choice for data scientists and academic researchers, and also popular in newsrooms. Developers have created a rich repository of "packages" for R, which create new funtionality and even a different way of writing code than Base R. These packages have expanded R's usability to include things like data scrapping, textual analysis, and interactive graphics. 

## What is RStudio?
RStudio is an interactive development environment for R. 

## Why R?
Capable of handling larger data than Excel

Can interact in real time with APIs for constantly update data (think stock market charts on a digital news site) 

Free and open source with a large community contributing to the development of new tools all the time. 

Gold standard in terms of creating a reproducible transcript of your data work with all kinds of benefits for collaboration, documentation, transparency, and crowd-sourcing. 

Powerful markdown options for publishing to the web. 

## RStudio -- A closer look
In RStudio you generally have four panes -- one to write your scripts (Source), one where your scripts are excuted (Console), one where your data is stored (Environment) and one to navigate to files, visualize data, etc.) You can customize the layout and appearance under "Tools > Global Settings"

Useful keyboard shortcuts can be found at https://support.rstudio.com/hc/en-us/articles/200711853-Keyboard-Shortcuts . Three that get used a lot are:

RUN Code - Ctrl+Enter
Insert PIPE opperator (%) - Ctrl+Shift+M
Insert ASSIGNMENT operator (<-) -	Alt+- (Windows)	Option+- (Mac) 
Clear Console - CTRL+L

At its base, R works like a calculator. So you can type 2+2 in the console and hit return and get 4, or you type 2-2 in scripting pane, click run (or control click), and the result will again appear in the console. 

You can assign values to a variable and then have R priont that value 
```
x <- 2
x' 
```
And you can assign a series of values to a variable to create a vector 
```
y <- c(1,2,3,4)
y 
```

# Packages and visualizing city water main breaks 

Install each package individually by pressing "Run" or command/control enter when on that line.
```
install.packages("dplyr")

install.packages("jsonlite")

install.packages("leaflet")

install.packages("ggplot2")
```
Now we load the packages to activite them
```
library(dplyr)
library(jsonlite)
library(leaflet)
library(ggplot2)
```
Now we connect to the API
```
url <- "https://services6.arcgis.com/bdPqSfflsdgFRVVM/arcgis/rest/services/Water_Main_Breaks/FeatureServer/0/query?where=1%3D1&outFields=*&outSR=4326&f=json"
jsondata <- fromJSON(url)
```
We are renaming the data as simply "data2", reducing the amount of information in the file, and asking it to display the variable names and first rows of data.
```
data2 <- jsondata$features$attributes
head(data2)
```
Creating the background map
```
breaks_map <- leaflet() %>% addTiles() %>% setView(lng = -76.1455, lat = 43.0493, zoom = 12)
breaks_map
```
Next, we add all of the incidents as points to the map. We’ll also change the basemap, so you can see some of other map styles offered. Try playing around with the "color," “radius”, “fillcolor”, and “fillopacity” parameters to get the markers that you think look best.
```
data2$popup <- paste("Date: ", data2$weekday, ", ", data2$month, "/", data2$date, " Location: ", data$location ,sep = "")

breaks_map <- leaflet() %>%
  addProviderTiles("Stamen.Toner") %>%
  addCircles(lng=data2$lon, lat=data2$lat, popup = data2$popup, radius = 50, color = "red")

breaks_map
```
