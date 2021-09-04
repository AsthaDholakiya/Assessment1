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




### Assesement 2



---
title: "Assignment2"
author: "Astha Dholakiya s4644484"
date: "9/04/2021"
output: html_document
---


```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```


## Libraries

```{r}
library(tidyverse)
library(knitr)
library(skimr)
library(ggplot2)
```



### Part 2: Data Wrangling and visualization

```{r}
#Importing data
buser_df<-read.csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2018/2018-11-20/thanksgiving_meals.csv")
```

1.  Displaying the first 10 rows of the dataset using `kable()` function. 

```{r}
kable(head(buser_df,10))
```


2. Using `skim()` to display the summary of variables

```{r}
skim(buser_df)
```


Making Predictions from the data


3.Use `fct_reorder` and `parse_number` functions to create a factor variable `family_income`

```{r}
x<-parse_number(buser_df$family_income)
f<-factor(buser_df$family_income)
xy<-fct_reorder(f,x,min)
family_income<-as.numeric(xy)
head(family_income)
```


4. What is the number of people who celebrate? 

```{r}
table(buser_df$celebrate)
```
The number of peaople who celebrate is 980. 



5. What are categories and insights for each main dish served and the method it is prepared? 


```{r}
unique(buser_df$main_dish)
```
```{r}
unique(buser_df$main_prep)
```
Response:



6. Create 3 different data viz showing insights for main dish served and the method. Provide your own legend and use themes.
Write 2-3 sentences with your explanation of each insight. (4 marks)

```{r}
ggplot(data=buser_df, aes(x=main_dish),size=6)+ 
  stat_count()
```

```{r}
ggplot(data = buser_df) +
stat_count(mapping = aes(x = main_prep))
```


```{r}
ggplot(data = buser_df) +
stat_count(mapping = aes(x = as.factor(main_dish)))+
facet_wrap(~ main_prep, nrow = 2)
```



7. How many use cranberry sauce? How many use gravy? 2marks


```{r}
table(buser_df$gravy)
```

8. What is the distribution of those who celebrate across income ranges. Create a data viz.


```{r}
ggplot(data = buser_df) +
stat_count(mapping = aes(x = family_income))+
facet_wrap(~ celebrate, nrow = 2)
```

9. Write 2-3 sentences with your explanation of each insight

10. Use the following code to create a new data set.

```{r}
new_df<-buser_df%>%
select(id, starts_with("side"),
         starts_with("pie"),
         starts_with("dessert")) %>%
  select(-side15, -pie13, -dessert12) %>%
  gather(type, value, -id) %>%
  filter(!is.na(value),
         !value %in% c("None", "Other (please specify)")) %>%
  mutate(type = str_remove(type, "\\d+"))
head(new_df)
```

Write 2-3 sentences with your explanation of what it does. (4 marks)

11. Intall package `widyr` and use `pairwise_cor()` function https://www.rdocumentation.org/packages/widyr/versions/0.1.3/topics/pairwise_cor
Write 2-3 sentences with your explanation of what it does. (2 marks)

```{r}
#install.packages("widyr")
```

Use this code for the new dataset

```{r}
library(widyr)
new_df%>%
pairwise_cor(value,id, sort = TRUE)
```

Write 1 sentence with your explanation of what insights it shows. (2 marks)

13. Use `lm()` function to build a model that predict a family income based on data in the dataset.

Compare 3 models using different set of input variables. Use different number of variables.

Explain your choice of variables (3 sentences) 

Write 2 sentences explaining which model os best.


```{r}
#Creating the regression dataset
y<-family_income
x1=as.numeric(factor(buser_df$us_region))
x2<-as.numeric(factor(buser_df$community_type))
x3<-as.numeric(factor(buser_df$main_dish))
x4<-as.numeric(factor(buser_df$age))
reg_df<-data.frame(y,x1,x2,x3,x4)
names(reg_df)<-c("family_income","region","community_type",
                 "main_dish","age")
reg_df[is.na(reg_df)]=0
```



```{r}
#Model 1: A simple Linear regression model for predicting family income based on region
model_1<-lm(family_income~region,data=reg_df)
summary(model_1)
```


```{r}
#Model 2: A multiple Linear regression for predicting family income based on region and community type
model_2<-lm(family_income~region+community_type,data=reg_df)
summary(model_2)
```


```{r}
#Model 3: A multiple Linear regression for predicting family income based on region, main dish, age, and community type
model_3<-lm(family_income~.,data=reg_df)
summary(model_3)
```

Comment:
model 3 performs better.
