# Carseats-EDA-with-R
The following video is a sample of how to use R to perform an Exploratory Data Analysis (EDA). We will use the tidyverse library package together with dlookr, as well as using the explore library to easily implement analysis techniques.

To carry out this small project we will rely on the article by Choonghyun Ryu [Exploratory Data Analysis](https://cran.r-project.org/web/packages/dlookr/vignettes/EDA.html), and we will also implement some of the methods used in the exploratory data analysis. Finally, to show the usefulness of one of the most famous R libraries, we will use dplyr to extract some insights from this dataset.

Regarding the dataset, carseats is a simulated data set containing sales of child car seats at 400 different stores. We can find this dataset in ISLR library.

# Table of Contents

- [Libraries](#libraries)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Normality](#normality)
- [Correlation](#correlation)
- [EDA when target variable is categorical variable](#eda-when-target-variable-is-categorical-variable)
- [EDA when target variable is numerical variable](#eda-when-target-variable-is-numerical-variable)
- [References](#references)

# Libraries

```{r}
library(tidyverse)
library(dlookr)
library(explore)
library(ISLR)
```

# Exploratory Data Analysis

Describe() provides descriptive statistics for numerical data
```{r}
dlookr::describe(Carseats)

# A tibble: 8 x 26
  described_variables     n    na   mean     sd se_mean    IQR skewness kurtosis   p00    p01   p05    p10    p20
  <fct>               <int> <int>  <dbl>  <dbl>   <dbl>  <dbl>    <dbl>    <dbl> <dbl>  <dbl> <dbl>  <dbl>  <dbl>
1 Sales                 400     0   7.50   2.82   0.141   3.93   0.186   -0.0809     0  0.906  3.15   4.12   5.07
2 CompPrice             400     0 125.    15.3    0.767  20     -0.0428   0.0417    77 89.0   98    106    113.  
3 Income                400     0  68.7   28.0    1.40   48.2    0.0494  -1.09      21 22.0   26     30     39   
4 Advertising           400     0   6.64   6.65   0.333  12      0.640   -0.545      0  0      0      0      0   
5 Population            400     0 265.   147.     7.37  260.    -0.0512  -1.20      10 16.0   29     58.9  110.  
6 Price                 400     0 116.    23.7    1.18   31     -0.125    0.452     24 55.0   77     87     96.8 
7 Age                   400     0  53.3   16.2    0.810  26.2   -0.0772  -1.13      25 25     27     30     36   
8 Education             400     0  13.9    2.62   0.131   4      0.0440  -1.30      10 10     10     10     11   
# ... with 12 more variables: p25 <dbl>, p30 <dbl>, p40 <dbl>, p50 <dbl>, p60 <dbl>, p70 <dbl>, p75 <dbl>,
#   p80 <dbl>, p90 <dbl>, p95 <dbl>, p99 <dbl>, p100 <dbl>


dlookr::describe(Carseats, Sales, CompPrice, Income, Price)

# A tibble: 4 x 26
  described_variables     n    na   mean    sd se_mean   IQR skewness kurtosis   p00    p01   p05    p10    p20
  <fct>               <int> <int>  <dbl> <dbl>   <dbl> <dbl>    <dbl>    <dbl> <dbl>  <dbl> <dbl>  <dbl>  <dbl>
1 Sales                 400     0   7.50  2.82   0.141  3.93   0.186   -0.0809     0  0.906  3.15   4.12   5.07
2 CompPrice             400     0 125.   15.3    0.767 20     -0.0428   0.0417    77 89.0   98    106    113.  
3 Income                400     0  68.7  28.0    1.40  48.2    0.0494  -1.09      21 22.0   26     30     39   
4 Price                 400     0 116.   23.7    1.18  31     -0.125    0.452     24 55.0   77     87     96.8 
# ... with 12 more variables: p25 <dbl>, p30 <dbl>, p40 <dbl>, p50 <dbl>, p60 <dbl>, p70 <dbl>, p75 <dbl>,
#   p80 <dbl>, p90 <dbl>, p95 <dbl>, p99 <dbl>, p100 <dbl>

head(Carseats)

 Sales CompPrice Income Advertising Population Price ShelveLoc Age Education Urban  US
1  9.50       138     73          11        276   120       Bad  42        17   Yes Yes
2 11.22       111     48          16        260    83      Good  65        10   Yes Yes
3 10.06       113     35          10        269    80    Medium  59        12   Yes Yes
4  7.40       117    100           4        466    97    Medium  55        14   Yes Yes
5  4.15       141     64           3        340   128       Bad  38        13   Yes  No
6 10.81       124    113          13        501    72       Bad  78        16    No Yes
```
The variables are as follows (we find this info in the official package of ISLR):
```{r}
A data frame with 400 observations on the following 11 variables.

Sales
Unit sales (in thousands) at each location

CompPrice
Price charged by competitor at each location

Income
Community income level (in thousands of dollars)

Advertising
Local advertising budget for company at each location (in thousands of dollars)

Population
Population size in region (in thousands)

Price
Price company charges for car seats at each site

ShelveLoc
A factor with levels Bad, Good and Medium indicating the quality of the shelving location for the car seats at each site

Age
Average age of the local population

Education
Education level at each location

Urban
A factor with levels No and Yes to indicate whether the store is in an urban or rural location

US
A factor with levels No and Yes to indicate whether the store is in the US or not

Source
Simulated data

References
James, G., Witten, D., Hastie, T., and Tibshirani, R. (2013) An Introduction to Statistical Learning with applications in R, https://www.statlearning.com, Springer-Verlag, New York
```
With dplyr we can subset the statistics we want from our numerical variables
```{r}
Carseats %>%
  dlookr::describe() %>%
  select(described_variables, mean, se_mean, sd) %>%
  arrange(desc(sd))

# A tibble: 8 x 4
  described_variables   mean se_mean     sd
  <fct>                <dbl>   <dbl>  <dbl>
1 Population          265.     7.37  147.  
2 Income               68.7    1.40   28.0 
3 Price               116.     1.18   23.7 
4 Age                  53.3    0.810  16.2 
5 CompPrice           125.     0.767  15.3 
6 Advertising           6.64   0.333   6.65
7 Sales                 7.50   0.141   2.82
8 Education            13.9    0.131   2.62
```
Now, lets group by this statistic by a factor, in this case Urban: variables have higher means when Urban is Yes except Education
```{r}
Carseats %>%
  group_by(Urban) %>%
  dlookr::describe() %>%
  select(described_variables, Urban, mean, se_mean, sd) %>%
  arrange(desc(sd))

# A tibble: 16 x 5
   described_variables Urban   mean se_mean     sd
   <fct>               <fct>  <dbl>   <dbl>  <dbl>
 1 Population          No    277.    14.2   154.  
 2 Population          Yes   260.     8.61  145.  
 3 Income              No     67.0    2.73   29.7 
 4 Income              Yes    69.3    1.62   27.3 
 5 Price               Yes   117.     1.43   23.9 
 6 Price               No    114.     2.12   23.0 
 7 Age                 No     52.6    1.54   16.8 
 8 Age                 Yes    53.6    0.952  16.0 
 9 CompPrice           Yes   126.     0.935  15.7 
10 CompPrice           No    123.     1.32   14.4 
11 Advertising         Yes     6.82   0.401   6.73
12 Advertising         No      6.20   0.595   6.47
13 Sales               Yes     7.47   0.169   2.84
14 Sales               No      7.56   0.258   2.81
15 Education           No     14.0    0.242   2.62
16 Education           Yes    13.8    0.156   2.62
```
Lets focus on income and age: if urban yes higher income and age in mean
```{r}
Carseats %>%
  select(Urban, Income, Age) %>%
  group_by(Urban) %>%
  dlookr::describe() %>%
  select(described_variables, Urban, mean, se_mean, sd) %>%
  arrange(desc(sd))

# A tibble: 4 x 5
  described_variables Urban  mean se_mean    sd
  <fct>               <fct> <dbl>   <dbl> <dbl>
1 Income              No     67.0   2.73   29.7
2 Income              Yes    69.3   1.62   27.3
3 Age                 No     52.6   1.54   16.8
4 Age                 Yes    53.6   0.952  16.0
```

### Normality

Normality() performs a normality test on numerical data

Shapiro-Wilk normality test is performed (can be aslo performed for the normality of residuals)
- H0 = null hypotesis: variables are distributed normally  p-value>=alpha
- HA: we reject the null hypotesis    p-value<alpha
```{r}
normality(Carseats)

# A tibble: 8 x 4
  vars        statistic  p_value sample
  <chr>           <dbl>    <dbl>  <dbl>
1 Sales           0.995 2.54e- 1    400
2 CompPrice       0.998 9.77e- 1    400
3 Income          0.961 8.40e- 9    400
4 Advertising     0.874 1.49e-17    400
5 Population      0.952 4.08e-10    400
6 Price           0.996 3.90e- 1    400
7 Age             0.957 1.86e- 9    400
8 Education       0.924 2.43e-13    400
```
```{r}
normality(Carseats, Sales, CompPrice, Income, Price)

# A tibble: 4 x 4
  vars      statistic       p_value sample
  <chr>         <dbl>         <dbl>  <dbl>
1 Sales         0.995 0.254            400
2 CompPrice     0.998 0.977            400
3 Income        0.961 0.00000000840    400
4 Price         0.996 0.390            400
```
If we take a significance level of 0.01 lets see those NOT normal variables. Advertising is the most out of the normal distribution
```{r}
Carseats %>%
  normality() %>%
  filter(p_value <= 0.01) %>%
  arrange(desc(p_value))

# A tibble: 5 x 4
  vars        statistic  p_value sample
  <chr>           <dbl>    <dbl>  <dbl>
1 Income          0.961 8.40e- 9    400
2 Age             0.957 1.86e- 9    400
3 Population      0.952 4.08e-10    400
4 Education       0.924 2.43e-13    400
5 Advertising     0.874 1.49e-17    400
```
Income is not normalyy distributed, but when ShelveLoc is bad it follows a normal distribution
```{r}
Carseats %>%
  select(ShelveLoc, Income) %>%
  group_by(ShelveLoc) %>%
  normality() %>%
  arrange(desc(p_value))

# A tibble: 3 x 5
  variable ShelveLoc statistic    p_value sample
  <chr>    <fct>         <dbl>      <dbl>  <dbl>
1 Income   Bad           0.968 0.0180         96
2 Income   Good          0.944 0.00108        85
3 Income   Medium        0.959 0.00000587    219
```
```{r}
plot_normality(Carseats, Sales) #normal distributed
```
![Normality diagnosis Sales](https://github.com/user-attachments/assets/966b44dc-1e28-4ff0-80b6-d1c930f0c4a7)

```{r}
plot_normality(Carseats, Income) #not bormal distributed
```
![Normality diagnosis Income](https://github.com/user-attachments/assets/9211dbf5-de59-4305-bcc5-a3d73a8d371d)

See the difference of Income normality
```{r}
Carseats %>%
  filter(ShelveLoc == "Bad") %>% #when ShelveLoc is Bad (normal)
  group_by(ShelveLoc) %>%
  plot_normality(Income)
```
![Normality diagnosis Income when ShelveLoc is Bad](https://github.com/user-attachments/assets/0664fe8e-63df-4e89-b52e-40884eca643e)

```{r}
Carseats %>%
  filter(ShelveLoc == "Good") %>% #and when is Good (not normal)
  group_by(ShelveLoc) %>%
  plot_normality(Income)
```
![Normality diagnosis income when shelveloc is good](https://github.com/user-attachments/assets/88f2f65d-5c80-4759-aea6-106aae92c4ee)

### Correlation

correlate() calculates the correlation coefficient of all combinations of dataset numerical variables

correlate() produces two pairs of variables
```{r}
correlate(Carseats, Sales, CompPrice, Income, Price)

# A tibble: 28 x 3
   var1      var2        coef_corr
   <fct>     <fct>           <dbl>
 1 CompPrice Sales          0.0641
 2 Income    Sales          0.152 
 3 Price     Sales         -0.445 
 4 Sales     CompPrice      0.0641
 5 Income    CompPrice     -0.0807
 6 Price     CompPrice      0.585 
 7 Sales     Income         0.152 
 8 CompPrice Income        -0.0807
 9 Price     Income        -0.0567
10 Sales     Advertising    0.270 
# ... with 18 more rows
```
Lets see the corr of income grouped by Urban qith the rest of numerical variables
```{r}
Carseats %>%
  group_by(Urban) %>%
  correlate(Income)

# A tibble: 14 x 4
   Urban var1   var2        coef_corr
   <fct> <fct>  <fct>           <dbl>
 1 No    Income Sales          0.261 
 2 No    Income CompPrice     -0.0491
 3 No    Income Advertising    0.115 
 4 No    Income Population     0.0471
 5 No    Income Price         -0.0290
 6 No    Income Age           -0.0848
 7 No    Income Education      0.0342
 8 Yes   Income Sales          0.104 
 9 Yes   Income CompPrice     -0.0980
10 Yes   Income Advertising    0.0328
11 Yes   Income Population    -0.0314
12 Yes   Income Price         -0.0716
13 Yes   Income Age            0.0318
14 Yes   Income Education     -0.0965
```
```{r}
plot_correlate(Carseats, Sales, CompPrice, Income, Price)
```
![Corr Matrix](https://github.com/user-attachments/assets/661e4db3-cb1e-4170-8f4e-909b45a2c691)

See the correlation matrix taking into account Urban Yes or No
```{r}
Carseats %>%
  group_by(Urban) %>%
  correlate() %>%
  plot()
```
![Corr matrix urban No](https://github.com/user-attachments/assets/04c435d5-8d8f-4f0e-8ab3-212fe14c8a88)
![Corr matrix urban yes](https://github.com/user-attachments/assets/875ce26c-d977-4de9-a839-af47e0976e9d)

## EDA when target variable is categorical variable

### When predictors are numeric variable

Lets perform EDA when the target variable is a categorical variable. When the categorical variable is the target variable, we examine the relationship between the target variable and the predictor

relate() shows the relationship between the target variable and the predictor
```{r}
category <- target_by(Carseats, Urban)

relate(category, Income)
# A tibble: 3 x 27
  described_variables Urban     n    na  mean    sd se_mean   IQR skewness kurtosis   p00   p01   p05   p10   p20
  <fct>               <fct> <int> <int> <dbl> <dbl>   <dbl> <dbl>    <dbl>    <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
1 Income              No      118     0  67.0  29.7    2.73  49.8   0.144     -1.11    21  21.2  23.8  26.7    36
2 Income              Yes     282     0  69.3  27.3    1.62  47     0.0130    -1.07    21  23.6  28    32      41
3 Income              total   400     0  68.7  28.0    1.40  48.2   0.0494    -1.09    21  22.0  26    30      39
# ... with 12 more variables: p25 <dbl>, p30 <dbl>, p40 <dbl>, p50 <dbl>, p60 <dbl>, p70 <dbl>, p75 <dbl>,
#   p80 <dbl>, p90 <dbl>, p95 <dbl>, p99 <dbl>, p100 <dbl>
```

plot() to visualizes the relationship between the target variable and the predictor variable. This is a Density Plot: we can see two peaks where there are more Income (around 30 and around 75) wheter urban is Yes or No
```{r}
plot(relate(category, Income))
```
![Urbans density plot by Income](https://github.com/user-attachments/assets/74498ceb-3900-4c70-b047-7f11877527d6)

```{r}
Carseats %>% explore(Income, target = Urban) #same output but with explore package
```
![Urbarns density plot with explore](https://github.com/user-attachments/assets/e48fbbdf-bf59-4333-b6d9-b31450c99bae)

```{r}
Carseats %>%
  select(Income, Advertising, Urban) %>%
  explore_all(target = Urban) #use explore to explore the target Urban by Income and Advertising
```
![Use explore to explore the target Urban by Income and Advertising](https://github.com/user-attachments/assets/4315d7e6-cce3-4d0e-be42-1cf6c54a49d3)

We can create a decission tree with explore. In this case, the first node shows that 70% of observations are Urban yes. If the population is higher than 456, still 54% of observations are Urban yes. And from here, those observations with income higher than 104 are Urban yes
```{r}
Carseats %>%
  explain_tree(target = Urban)
```
![decission tree](https://github.com/user-attachments/assets/6c96bf7c-bd45-406a-be42-1abc08e4a8ab)

### When predictors are categorical variable

In this case, it shows the contingency table of two variables. The summary() function performs independence test on the contingency table4
```{r}
relate(category, ShelveLoc) #contingency table

     ShelveLoc
Urban Bad Good Medium
  No   22   28     68
  Yes  74   57    151
```

- H0: categ. variables are independent
- HA: categ. variables are dependent

In this case alpha=0.01 so we dont reject null hypotesis and variables are independent
```{r}
summary(relate(category, ShelveLoc)) #independence test of all factors

Call: xtabs(formula = formula_str, data = data, addNA = TRUE)
Number of cases in table: 400 
Number of factors: 2 
Test for independence of all factors:
	Chisq = 2.7376, df = 2, p-value = 0.2544
```

plot() visualizes the relationship between the target variable and the predictor
```{r}
plot(relate(category, ShelveLoc))
```
![urbans mosaic plot by shelveloc](https://github.com/user-attachments/assets/f0c39057-6741-4ebd-bc93-3a95ad41cc1d)

## EDA when target variable is numerical variable

### When predictors are numeric variable

It shows the result of a simple linear model of the target ~ predictor formula. The summary() function expresses the details of the model
```{r}
number <- target_by(Carseats, Income)

relate(number, Advertising)

Coefficients:
(Intercept)  Advertising  
    67.0103       0.2483


summary(relate(number, Advertising))

Call:
lm(formula = formula_str, data = data)

Residuals:
    Min      1Q  Median      3Q     Max 
-49.982 -25.259   0.131  22.809  52.990 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)  67.0103     1.9769  33.897   <2e-16 ***
Advertising   0.2483     0.2106   1.179    0.239    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 27.97 on 398 degrees of freedom
Multiple R-squared:  0.00348,	Adjusted R-squared:  0.0009766 
F-statistic:  1.39 on 1 and 398 DF,  p-value: 0.2391


plot(relate(number, Advertising))
```
![Income by advertising](https://github.com/user-attachments/assets/c7efa1ed-598d-476b-b2d8-c7f89b497464)

### When predictors are categorical variable

```{r}
relate(number, Urban)

Analysis of Variance Table

Response: Income
           Df Sum Sq Mean Sq F value Pr(>F)
Urban       1    450  450.48  0.5745 0.4489
Residuals 398 312054  784.05


summary(relate(number, Urban))

Call:
lm(formula = formula(formula_str), data = data)

Residuals:
   Min     1Q Median     3Q    Max 
-48.34 -25.34   0.32  22.66  52.98 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)   67.017      2.578  25.999   <2e-16 ***
UrbanYes       2.327      3.070   0.758    0.449    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 28 on 398 degrees of freedom
Multiple R-squared:  0.001442,	Adjusted R-squared:  -0.001067 
F-statistic: 0.5745 on 1 and 398 DF,  p-value: 0.4489


plot(relate(number, Urban))
```
![income boxplot by urban](https://github.com/user-attachments/assets/997e09f2-800a-4843-9fbd-78b450509b5f)

## Exploring with dplyr and ggplot libraries

Lets summarize in a plot the total sales by ShelveLoc and Urban
```{r}
Carseats %>%
  select(ShelveLoc, Urban, Sales) %>%
  group_by(ShelveLoc, Urban) %>%
  summarise(total_sales = sum(Sales)) %>%
  arrange(desc(total_sales)) %>%
  ggplot() + geom_col(aes(x = ShelveLoc, y = total_sales, fill = Urban))
```
![total sales by ShelveLoc and Urban](https://github.com/user-attachments/assets/4f5070f1-5948-49d7-880d-96283f16e85c)

Now lets check ShelveLoc, Urban and US
```{r}
ggplot(Carseats) + geom_jitter(aes(ShelveLoc, Urban, color = Urban, shape = US))
```
![ShelveLoc Urban and US](https://github.com/user-attachments/assets/1b412b7b-b7a8-44ce-a6eb-7f3c7de5dd62)

Now lets gather these three categorical variables with total_sales. We knew that Urban Yes and ShelveLoc Medium- had the biggest sales, and we know that the biggest sales location is US
```{r}
Carseats %>%
  select(ShelveLoc, Urban, US, Sales) %>%
  group_by(ShelveLoc, Urban, US) %>%
  summarise(total_sales = sum(Sales)) %>%
  ggplot() + geom_jitter(aes(ShelveLoc, Urban, color = Urban, shape = US, size = total_sales))
```
![three categorical variables with total_sales](https://github.com/user-attachments/assets/1ee21516-1abd-4b4b-aa79-4f0f0b70b3e5)

Now we are going to focus on Price, Advertising and CompPrice (competitor price). This graph shows that whe are positive correlated with price competitors and that the most common advertising size (at least at glance) is 10, so we are spending most of times this amount wheter the ShelveLoc is bad, good or medium
```{r}
Carseats %>%
  select(ShelveLoc, Price, CompPrice, Advertising) %>%
  ggplot() + geom_point(aes(Price, CompPrice, color = ShelveLoc, size = Advertising)) +
  geom_smooth(aes(Price, CompPrice))
```
![competitor price](https://github.com/user-attachments/assets/6cc686c2-ffbc-4542-bc99-35366c8c10c8)

Moreover, we are spending more money in advertising in US
```{r}
mean(Carseats$Advertising) #6.635

Carseats %>%
  select(Price, CompPrice, Advertising) %>%
  correlate()

# A tibble: 6 x 3
  var1        var2        coef_corr
  <fct>       <fct>           <dbl>
1 CompPrice   Price          0.585 
2 Advertising Price          0.0445
3 Price       CompPrice      0.585 
4 Advertising CompPrice     -0.0242
5 Price       Advertising    0.0445
6 CompPrice   Advertising   -0.0242

ggplot(Carseats) + 
  geom_point(aes(Price, CompPrice, color = ShelveLoc, shape = US, size = Advertising))
```
![advertising in US](https://github.com/user-attachments/assets/8ab714ec-740d-4616-99d0-81e34a0d23de)

# References

[Choonghyun Ryu (2024) Exploratory Data Analysis](https://cran.r-project.org/web/packages/dlookr/vignettes/EDA.html)

[explore library](https://www.rdocumentation.org/packages/explore/versions/1.3.0)

[Tidyverse](https://www.tidyverse.org/)

[ISLR](https://www.rdocumentation.org/packages/ISLR/versions/1.4)

[dlookr from Choonghyun Ryu repository](https://github.com/choonghyunryu/dlookr)

