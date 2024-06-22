---
title: "Mini-Project 02"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_float: true
---


```{r}
library(tidyverse)
library(gapminder)
library(plotly)
library(sf)
```
## Interactive Visualization of West Roxbury Data

The first visualization will be an **interactive visualization** about houses in the West Roxbury neighborhood. First, the data is read below.

```{r}
westroxbury_data <- read_csv("https://raw.githubusercontent.com/reisanar/datasets/master/WestRoxbury.csv")

```
Then we check the content of the data.
```{r}
westroxbury_data
```


Here a summary is done of the data, which shows the number of years and counts for each years. There is a year 0 which is a major outlier that will mess with the data, and the year 0 does not exist as well.

```{r}
westroxbury_data %>% group_by(`YR BUILT`) %>% summarise(count_year = n())
```

A new dataset is created to filter out the 1 record with the year "0"
```{r}
westroxbury <- westroxbury_data %>% filter(`YR BUILT` != 0)
westroxbury
```



Afterwards, an **interactive plot** is created with the "westroxbury" dataframe. This interactive plot shows the tax amount over time based on the remodel types: None, Old, and Recent. Based on this graph, houses that have through remodels recently tend to be taxed more. In regards to data visualization principles, I considered the following when making this chart:

* Chart Junk: I avoided too much cluttering in the graph by considering only three categories with their corresponding line graphs. In addition, the grid in the background was not removed because it may be helpful for estimation. 

*Layout: The legend was placed on the side for readability. Facetting the graph based on remodel was not needed since placing them all graphs for the three remodel types facilitates comparison.

*Text: Text was not included on the lines since a legend and the different colors differentiate the line graphs for Remodel.

*Color: No specific palette was used, but the colors for Remodel are entirely different to prevent confusion since these are unordered categories.
```{r}
westroxbury_plot <- ggplot(data = westroxbury, mapping = aes(x = `YR BUILT`, y = TAX, 
                 color = REMODEL)) + 
  geom_point(alpha = 0.5) + geom_smooth(method = "loess") + scale_y_log10() +  labs(x = "Tax Amount", y = "Year Built",
       title = "Tax On Houses Over Time") 

interactive_plot <- ggplotly(westroxbury_plot)
interactive_plot
```

The plot is saved in an HTML file.
```{r}
htmlwidgets::saveWidget(interactive_plot, "Tax_Plot.html")
```



## Spatial visualization for Florida Lakes:


The summary below concerns the lakes in two counties: Osceola and Polk. The number of lakes in both counties are shown as well.

```{r}
loc_lakes <- "./Florida_Lakes/Florida_Lakes/Florida_Lakes.shp"
fla_lakes <- st_read(loc_lakes, quiet = TRUE)
fla_lakes %>% filter(COUNTY %in% c("POLK", "OSCEOLA")) %>% group_by(COUNTY) %>%
  summarise(Number_Lakes = n())
```

The following spatial visualization was created to demonstrate how many lakes there are visually in both counties. Based on the visualization, it seems apparent that Polk County contains more lakes than Osceola County, which confirms what was intially expected from the data summary. In regards to data visualization principles, I considered the following when making this chart:

* Chart Junk: I avoided too much cluttering in the graph by considering only two categories with their corresponding line graphs. In addition, the grid in the background was not removed because it may be helpful for estimation. A minimal theme was employed to place emphasis on the lakes and minimize the chart as much as possible.  

*Layout: The legend is placed on the right side for readability. Faceting the graph based on County was not needed since placing the spatial visualizations of the lakes of both counties side by side makes the comparison easier, especially since these two counties are placed side by side.

*Text: Text was not included on the visual representation of the lakes since the legend and color differentiate the lakes based on county. A title was given for this graph.

*Color: No specific palette was used since there are only two counties being compared.

```{r}
fla_lakes %>% filter(COUNTY %in% c("POLK", "OSCEOLA")) %>%
  ggplot() +
  geom_sf(mapping = aes(color = COUNTY, fill = COUNTY),  
          alpha=1, col="white") + theme_minimal() +
  labs(title = "Lakes in Osceola and Polk Counties")
```


## Model Visualization of West Roxbury Data:


This is a **model visualization** of the West Roxbury Data. Based on the geom_smooth line below, it is apparent that the total value of homes decreased in the first half of the 20th century, but then started to increase steadily in the second half. The value of this graph matters because it shows a trend.  Contrary to my expectations, there was actually a dip at some point in the past. In regards to data visualization principles, I considered the following when making this chart:

* Chart Junk: I avoided too much cluttering in the graph by considering only the geom_line graph and the geom_smooth line that demonstrates the average change. In addition, the grid in the background was not removed because it may be helpful for estimation of values. 

*Layout: No legend is included and the blue line is a line estimating the average change based on the line that geom_line generated

*Text: Text was not included on the lines, but a title and subtitle were placed on the graph.

*Color: There was no emphasis on color for this graph
```{r}
ggplot(data = westroxbury, mapping = aes(x = `YR BUILT`, y = `TOTAL VALUE`)) +
  geom_line() +
  geom_smooth() +
  labs(x = "Year", y = "Total Value in USD",
       title = "Total Value of West Roxbury Houses",
       subtitle = "1798-2011") +
  theme_minimal() +
  theme(plot.title = element_text(face = "bold"))
```

This graph shows the correlation that some attributes have towards the total value of a house. The number of rooms appears to have the most correlation, which makes sense since bigger houses tend to cost more.
```{r}
library(GGally)
westroxbury_subset <- westroxbury %>% 
  select(`TOTAL VALUE`, FLOORS, ROOMS, BEDROOMS)
ggpairs(westroxbury_subset)
```


