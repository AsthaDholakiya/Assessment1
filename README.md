---
title: "Assignment1.1"
author: "Astha Dholakiya s4644484"
date: "8/20/2021"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Part 1
# R Markdown editing

**_This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents [RStudio](https://rmarkdown.rstudio.com/lesson-8.html)._**

```{r}
library(tidyverse)
library(ggplot2)
library(knitr)
library(skimr)
```

Creating the variable "fastfood" that contains the dataset

```{r}
fastfood <-read.csv(url("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2018/2018-09-04/fastfood_calories.csv"))
```


The first 10 rows and the first 5 variables

```{r}
fastfood_kable<-kable((fastfood[, 1:5]),"pipe")
head(fastfood_kable,10)
```

The observations that has more than 1000 calories

```{r}
fastfood %>% filter(calories > 1000)
```

Save observations with more than 40 in total_fat and more than 80 in total_carb in a new variable (dataset) called `dont_eat_this`

```{r}
dont_eat_this <- fastfood%>% filter(total_fat>40 & total_carb>80)
head(dont_eat_this)
```

# Part 2

Display the types of variables in the dataset using `skimr` package

```{r}
skim(fastfood)
```


Show the count of observations for each restaurant
```{r}
table(fastfood["restaurant"])
```

summarise and display the average number of calories for each restaurant

```{r}
fastfood %>% group_by(restaurant)%>%
  summarise(
    avg_calories = mean(calories)
)
```

Add a variable to the dataset, which calculates the average calories per type of restaurant and call it `average_calories` 

```{r}
fastfood %>% 
  mutate(average_calories = mean(calories))
```

Display the data vis of total fat per each type of restaurant

**_Comment_** The visualisation is a bar chart showing the total fat for each restuarant. The bar chart demonstrates that Burger King restaurant have the highest total fat followed by Taco Bell. On the contrary, the restaurant with the lowest total fat is Chick Fil-A. 


```{r}
total_fat <-fastfood %>% group_by(restaurant)%>%
  summarise(
    Total_fat= sum(total_fat)
)
data.frame(total_fat)

ggplot(data = total_fat) +
geom_bar(mapping = aes(x=restaurant, y = Total_fat),stat = "identity")
```

Add a variable to the dataset, which calculates the maximum total fat per type of restaurant and call it `max_total_fat` 

```{r}
fastfood %>% 
  mutate(max_total_fat = max(total_fat))

```


Use observations for Mcdonalds to plot sugar variable against protein with `geom_point()` 

```{r}
#Filtering the observations for Mcdonalds
Mcdonalds <-fastfood%>% filter(restaurant=="Mcdonalds")

#plotting
ggplot(data = Mcdonalds)+
  geom_point(mapping = aes(x = protein, y = sugar))
``` 


# Part 3
Identify variable(s) which should be factors and transform their type into a factor variable.

```{r}
as.factor(fastfood$restaurant)
```

Read about `cut_number()` function using Help and add a new variable to the dataset `calories_type`. Use `calories` variable for `cut_number()` function to split it into 3 categories `n=3`, add labels `labels=c("low", "med", "high")` and make the dataset ordered. 

```{r}
calories_type <- cut_number(fastfood$calories,n=3,
    labels = c("low","med","high"))
fastfood %>% 
  mutate(calories_type = calories_type)
```

Create a dataviz that shows the distribution of `calories_type` in food items for each type of restaurant.


```{r}
calories_type <- cut_number(fastfood$calories,n=3,
    labels = c("low","med","high"))
fastfood<-fastfood %>% 
  mutate(calories_type = calories_type)
ggplot(data = fastfood) +
geom_bar(mapping = aes(x=restaurant, y = item),stat = "identity")+
  facet_wrap(~ calories_type, nrow = 3)
```


The second variable should show the percentage of `trans_fat` in `total_fat`. Add the variable to the dataset and call it `trans_fat_percent`. Do not forget to save the updated dataset. 
 
```{r}
fastfood<-fastfood %>% 
  mutate(trans_fat_percent = (trans_fat/total_fat)*100)
```

Create a dataviz that shows the distribution of `trans_fat` in food items for each type of restaurant. Think carefully about the choice of data viz. Use facets, coordinates and theme layers to make your data viz visually appealing and meaningful.

```{r}
ggplot(data = fastfood) +
geom_bar(mapping = aes(x=restaurant, y = trans_fat_percent),stat = "identity")+
  facet_wrap(~ calories_type, nrow = 3)
```

 Calculate and show the average (mean)  `total_fat` for each type of restaurant. No need to save it as a variable.
 
```{r}
avg_total_fat<-fastfood %>% group_by(restaurant)%>%
  summarise(
    avg_total_fat = mean(total_fat)
)
avg_total_fat
```
 And create a dataviz that allow to compare different restaurants on this variable (`total_fat`). You can present it on one dataviz (= no facets). 
 
```{r}
ggplot(data = avg_total_fat) +
geom_bar(mapping = aes(x=restaurant, y = avg_total_fat),stat = "identity")
```
