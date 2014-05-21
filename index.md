---
title       : Presenting Shiny and rCharts application
subtitle    : Interactive map 
author      : Marika3
job         : Coursera Test Team
logo        : MtCook.jpg
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : sunburst   # 
widgets     : [bootstrap, quiz]   # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
---

## How this application works?

Application runs as hosted interactive HTML page. [The address is here](https://marika3.shinyapps.io/ShinyTest/)  
Users can select decile of schools in Auckland, New Zealand, and see their location on map, along with school names.  
They can change decile setting with slidebar, and map will display all schools for selected decile.  
Map can be easily zoomed in and out.


```r
AucklandSchools <- read.csv("AuckSch.csv", header = TRUE, strip.white = TRUE)
head(AucklandSchools)
```

```
##                Name Longitude Latitude Decile
## 1    Rodney College     174.5   -36.29      4
## 2 Mahurangi College     174.7   -36.41      8
## 3     Orewa College     174.7   -36.59      9
## 4   Kaipara College     174.5   -36.68      7
## 5  Long Bay College     174.7   -36.69     10
## 6 Rangitoto College     174.7   -36.74     10
```


--- &radio

## What is "decile" in regards with New Zealand education system? Do you know?  

1. Some random statistical term.

2. _Name of the method to allocate funding to schools_ 

3. School rating 

4. Tenth part of something

*** .explanation
Decile is term used by New Zealand Ministry of Education to allocate funding to schools.  
Each decile represents about 10% of country schools.  
Decile 1 schools are schools with the highest proportion of students from low socio-economic communities, whereas decile 10 schools are schools with the lowest proportion of these students.  
The lower a school's decile rating, the more funding it gets to provide additional resources to support their students' learning needs.

*** .

## How this application made  
This interactive application is written in R using packages rCharts and Shiny.  
rCharts allows to produce map with zooming and place markers on it using dataset from Ministry of Education. Shiny allows to arrange user input and operate the page interactively.  
You can see code on next pages. Thanks for your attention!  

---

## server.R:

```r
library(shiny)
library(rCharts)
AucklandSchools <- read.csv("AuckSch.csv", header = TRUE, strip.white = TRUE)
shinyServer(
function(input, output){
    output$myMap <- renderMap ({
    mapT <- Leaflet$new()
    mapT$set(width = 800, height = 400)
    mapT$setView(c(AucklandSchools$Latitude[507],AucklandSchools$Longitude[507]), 
            zoom = 12)
    Selected <- subset(AucklandSchools, AucklandSchools$Decile == input$decile, 
            rownames=FALSE)
    for (i in 1:nrow(Selected))
        mapT$marker(c(Selected$Latitude[i], Selected$Longitude[i]), 
                        bindPopup = Selected$Name[i])
    return(mapT)
    })
})
```

---

## ui.R:

```r
require(shiny)
require(rCharts)
shinyUI(
    fluidPage(
    titlePanel(h1("Interactive mapping with rCharts and Shiny", align = "center")),
    sidebarPanel(
    sliderInput("decile", h5(p("Select decile to see schools on map."), 
                            align = "center"), 1, 10, 1),
    helpText("Deciles are a way in which New Zealand Ministry of Education allocates 
        funding to schools.
        The lower a school's decile rating, the more funding it gets to provide 
        additional resources to support their students' learning needs." )
          ),
    mainPanel(
        mapOutput('myMap'),
        helpText(h5(p("Zoom map to suit."),
            p("Click on popups to see school names"), align="center")
            ))
         )
```
