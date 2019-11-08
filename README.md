# R Workshop
Nov. 8, 2019, Workshop at the Newhouse School 

## Installing R 
Download and install R at https://cran.case.edu/

Download and install RStudio https://www.rstudio.com/products/rstudio/download/ . Not to make sure you install R first, then R Studio 

## What is R?
R is a programing language that specializes in statistical analysis and the plotting of data. Written mostly in C and in R itself, it first appeared in 1993. It is free and open source, and is a top choice for data scientists and academic researchers, and also popular in newsrooms. Developers have created a rich repository of "packages" for R, which create new funtionality and even a different vocabulary for writing code than Base R. These packages have expanded R's usability to include things like data scrapping, textual analysis, and interactive graphics. 

## What is RStudio?
RStudio is an interactive development environment for R. 

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
x 
```
And you can assign a series of values to a variable to create a vector 
```
y <- c(1,2,3,4)
y 
```

# Packages and visualizing city water main breaks 

Install each package individually by pressing "Run" or command/control enter when on that line.
```
install.packages("tidyverse")

install.packages("jsonlite")

install.packages("leaflet")

install.packages("ggplot2")
```
## Why R?
While our packages are loading we can talk about the advantages of R.

Capable of handling larger data than Excel

Can interact in real time with APIs for constantly update data (think stock market charts on a digital news site) 

Free and open source with a large community contributing to the development of new tools all the time. 

Gold standard in terms of creating a reproducible transcript of your data work with all kinds of benefits for collaboration, documentation, transparency, and crowd-sourcing. 

Powerful markdown options for publishing to the web. 

Now that our packages have installed, we load them to activite
```
library(dplyr)
library(jsonlite)
library(leaflet)
library(ggplot2)
```
Note that "dplyr" is a sub-library of the tidyverse and that together with ggplot are the most commonly used packages in the data journalism space. 

There is extensive documentation on all these packages that teach you how to manipulate data within those packages' ecosystems. For instance: https://dplyr.tidyverse.org/ 

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
We can generate any  background map simply by changing the longitude and latitude. For instance:
```
breaks_map <- leaflet() %>% addTiles() %>% setView(lng = -73.9712, lat = 40.7831, zoom = 12)
breaks_map
```
Now back to Syracuse:
```
breaks_map <- leaflet() %>% addTiles() %>% setView(lng = -76.1455, lat = 43.0493, zoom = 12)
breaks_map
```
Next, we add all of the incidents as points to the map and change the basemap style to toner. We’ll also change the basemap, so you can see some of other map styles offered. Try playing around with the "color," “radius”, “fillcolor”, and “fillopacity” parameters to get the markers that you think look best.
```
data2$popup <- paste("Date: ", data2$weekday, ", ", data2$month, "/", data2$date, " Location: ", data$location ,sep = "")

breaks_map <- leaflet() %>%
  addProviderTiles("Stamen.Toner") %>%
  addCircles(lng=data2$lon, lat=data2$lat, popup = data2$popup, radius = 50, color = "red")

breaks_map
```
Now we can play with different basemap styles. For instance:
```
breaks_map <- leaflet() %>%
  addProviderTiles(providers$Esri.NatGeoWorldMap) %>% 
  addCircles(lng=data2$lon, lat=data2$lat, popup = data2$popup, radius = 50, color = "red")

breaks_map
```
This one is kind of neat, and let's us play with color too: 
```
breaks_map <- leaflet() %>%
  addProviderTiles("Stamen.Watercolor") %>%
  addCircles(lng=data2$lon, lat=data2$lat, popup = data2$popup, radius = 50, color = "blue")

breaks_map
```
# Working with existing data 
Start a new RStudio session

Under FILE, create new project

Browse to convenient place to create (and leave) your project

Name project, which creates new folder inside your directory

Create folder inside the new project folder called "data"

Download https://drive.google.com/file/d/1QaOHeFYLzT8uEIJmlSSA9o6_e7eN_VmL/view?usp=sharing and move the CSV into the data folder 

Import data and assign it the name "voters"
```
library(readr)
texasvoters <- read_csv("data/texasvoters.csv")
head(texasvoters)
```
Let's plot the propotion of Spanish named voters with the propotion of the Democratic vote

Install packages associated libraries for modeling and load those libraries (tidyverse packages already installed)
```
library(tidyverse)
library(ggplot2)
library(modelr)
library(broom)
```
Now let's do a basic plot 
```
ggplot(texasvoters, aes(x=SpanishVotersProp, y=DEMprop)) +
  geom_point(size=2, color="blue") +
  geom_smooth(method = "lm", color="red") +
  scale_x_continuous(limits=c(0,1), breaks=seq(0,.5,1)) +
  scale_y_continuous(limits=c(0,1), breaks=seq(0,.5,1))
```
Now we can estimate the parameters using a simple linear model
```
votermodel <- lm(DEMprop ~ SpanishVotersProp, data = texasvoters)
summary(votermodel)
```
It is showing use that the relationship between the number of Spanish surnamed voters and the Democratic vote is significant (the p value). 

R-squared (R2) is a statistical measure that represents the proportion of the variance for a dependent variable that's explained by an independent variable or variables in a regression model. In this case, about a third of the variance. 

The estimate is the beta, or slope, of the line, in the formula Y= a + bX, where Y is the dependent variable (that's the variable that goes on the Y axis), X is the independent variable (i.e. it is plotted on the X axis), b is the slope of the line and a is the y-intercept.

So in this example Y (Democratic vote) = .48X (Spanish voters) + .15. So by knowing either X or Y, we can estimate the other. 
