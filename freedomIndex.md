Freedom Index
================
Waqqas Manzoor
2023-11-23

# Freedom Index Global With respect to India

#### About the Data

From `Wikipedia`, the free encyclopedia This article contains a list of
freedom indices produced by several non-governmental organizations that
publish and maintain assessments of the state of freedom in the world,
according to their own various definitions of the term, and rank
countries as being free, partly free, or using various measures of
freedom, including civil liberties, political rights and economic
rights. Some of the indices measure only some aspects of freedom, such
as democracy or corruption.

### Read more about it here.

[Click Here](https://en.wikipedia.org/wiki/List_of_freedom_indices)

# R Environment

Preparing R Environment

- Installing libraries `tidyverse` , `dplyr` , `ggplot2` and `rvest`
- Loading libraries `tidyverse` , `dplyr` , `ggplot2` and `rvest`

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(ggplot2)
library(dplyr)
```

# Scraping Data using `rvest`

Web scraping is the process of extracting data from websites. The
`rvest` package in R facilitates this task by providing tools to
navigate and extract information from HTML documents.

### Step 1

Go to the target website, in this case

[click here](https://en.wikipedia.org/wiki/List_of_freedom_indices)

### Step 2

Search for the data/table

<figure>
<img
src="C:\Users\shahw\OneDrive\Documents\code_playground\project_R_Freedom_index\images\img0.png"
alt="this" />
<figcaption aria-hidden="true">this</figcaption>
</figure>

### Step 3

- Once on table, left click and press inspect.
- On the html part, search for table format.(**as highlighted in
  green**)
- Click on it, and see type of table.(**as highlighted in yellow**)
- Copy the type table.

<figure>
<img
src="C:\Users\shahw\OneDrive\Documents\code_playground\project_R_Freedom_index\images\img1.png"
alt="see" />
<figcaption aria-hidden="true">see</figcaption>
</figure>

### Step 4

Open R studio(**or the platform used for analysis**)

#### Saving url in variable

``` r
url <- "https://en.wikipedia.org/wiki/List_of_freedom_indices"
```

#### Using `read_html()`to read html data

``` r
data <- read_html(url)
```

#### Using `html_element()` and `html_table()`

- `html_element()`used to read type of data element for scrapping.
- `html_table()` used to convert html-type into readable DataFrame.

``` r
data_r <-data %>% html_element("table.wikitable.sortable") %>% html_table()
```

**Scrapping data is done let us see what data we have**

# Pre-cleaning/ viewing data

### Column names

``` r
colnames(data_r)
```

    ## [1] "Country"                            "Freedom in the World 2023[13]"     
    ## [3] "Score"                              "Index of Economic Freedom 2023[14]"
    ## [5] "Score"                              "Press Freedom Index 2023[3]"       
    ## [7] "Score"                              "Democracy Index 2023[9]"           
    ## [9] "Score"

**As we can see column names are repeating, having duplicates, having
spaces, and ending with \[\]**

### Viewing data

``` r
head(data_r,20)
```

    ## # A tibble: 20 × 9
    ##    Country        Freedom in the World 2023…¹ Score Index of Economic Fr…² Score
    ##    <chr>          <chr>                       <chr> <chr>                  <chr>
    ##  1 Norway         1 free                      100   2 mostly free          76.9 
    ##  2 Ireland        1 free                      97    1 free                 82   
    ##  3 Sweden         1 free                      100   2 mostly free          77.5 
    ##  4 Finland        1 free                      100   2 mostly free          77.1 
    ##  5 Denmark        1 free                      97    2 mostly free          77.6 
    ##  6 Switzerland    1 free                      96    1 free                 83.8 
    ##  7 New Zealand    1 free                      99    2 mostly free          78.9 
    ##  8 Netherlands    1 free                      97    2 mostly free          78   
    ##  9 Luxembourg     1 free                      97    2 mostly free          78.4 
    ## 10 Estonia        1 free                      94    2 mostly free          78.6 
    ## 11 Canada         1 free                      98    2 mostly free          73.7 
    ## 12 Taiwan         1 free                      94    1 free                 80.7 
    ## 13 Iceland        1 free                      94    2 mostly free          72.2 
    ## 14 Germany        1 free                      94    2 mostly free          73.7 
    ## 15 Portugal       1 free                      96    3 moderately free      69.5 
    ## 16 Australia      1 free                      95    2 mostly free          74.8 
    ## 17 Czech Republic 1 free                      92    2 mostly free          71.9 
    ## 18 Lithuania      1 free                      89    2 mostly free          72.2 
    ## 19 Latvia         1 free                      88    2 mostly free          72.8 
    ## 20 United Kingdom 1 free                      93    3 moderately free      69.9 
    ## # ℹ abbreviated names: ¹​`Freedom in the World 2023[13]`,
    ## #   ²​`Index of Economic Freedom 2023[14]`
    ## # ℹ 4 more variables: `Press Freedom Index 2023[3]` <chr>, Score <chr>,
    ## #   `Democracy Index 2023[9]` <chr>, Score <chr>

\*\*As we can see columns are not clean, forexample columns with `chr`
should have `dbl`, columns have numbers, spaces and character parts ie;
1 free,5 not free etc, nil, n/a showing as “\_” . let us fix all of
it.\*\*

# Cleaning the data

## Fixing the columns

using `vector` to replace the column names

``` r
new_colnames <- c("country", "freedom_in_the_world", "score_freedom", "economic_freedom", "score_economic", "press_freedom", "score_press", "democracy_index", "score_democracy")

colnames(data_r) <- new_colnames
```

### Viewing new updated columns

``` r
colnames(data_r)
```

    ## [1] "country"              "freedom_in_the_world" "score_freedom"       
    ## [4] "economic_freedom"     "score_economic"       "press_freedom"       
    ## [7] "score_press"          "democracy_index"      "score_democracy"

**AS we can see column names have been cleaned**

## Fixing the columns

Using `mutate()`, `gsub()` and `redix` to update column values

``` r
columns_to_clean <- c("freedom_in_the_world", "economic_freedom", "press_freedom", "democracy_index")

data_r <- data_r %>%
  mutate(across(all_of(columns_to_clean),
                ~ ifelse(is.na(.) | . == "n/a", NA, gsub("\\d+", "", .))))
```

## Fixing unwanted spaces

using`trimws`

``` r
data_r[] <- lapply(data_r, trimws)
```

## Fixing the Variable Type

Again using `mutate()`

``` r
columns_to_convert <- c("score_freedom", "score_economic", "score_press", "score_democracy")


data_r <- suppressWarnings({
  data_r %>%
    mutate(across(all_of(columns_to_convert),
                  ~ ifelse(. %in% c("—", "NA", "N/A", ""), NA, as.numeric(.))))
})
```

## Viewing Cleaned Data

``` r
head(data_r,20)
```

    ## # A tibble: 20 × 9
    ##    country    freedom_in_the_world score_freedom economic_freedom score_economic
    ##    <chr>      <chr>                        <dbl> <chr>                     <dbl>
    ##  1 Norway     free                           100 mostly free                76.9
    ##  2 Ireland    free                            97 free                       82  
    ##  3 Sweden     free                           100 mostly free                77.5
    ##  4 Finland    free                           100 mostly free                77.1
    ##  5 Denmark    free                            97 mostly free                77.6
    ##  6 Switzerla… free                            96 free                       83.8
    ##  7 New Zeala… free                            99 mostly free                78.9
    ##  8 Netherlan… free                            97 mostly free                78  
    ##  9 Luxembourg free                            97 mostly free                78.4
    ## 10 Estonia    free                            94 mostly free                78.6
    ## 11 Canada     free                            98 mostly free                73.7
    ## 12 Taiwan     free                            94 free                       80.7
    ## 13 Iceland    free                            94 mostly free                72.2
    ## 14 Germany    free                            94 mostly free                73.7
    ## 15 Portugal   free                            96 moderately free            69.5
    ## 16 Australia  free                            95 mostly free                74.8
    ## 17 Czech Rep… free                            92 mostly free                71.9
    ## 18 Lithuania  free                            89 mostly free                72.2
    ## 19 Latvia     free                            88 mostly free                72.8
    ## 20 United Ki… free                            93 moderately free            69.9
    ## # ℹ 4 more variables: press_freedom <chr>, score_press <dbl>,
    ## #   democracy_index <chr>, score_democracy <dbl>

**Now our Data is clean and ready for analyzing and Visualization**

# Analyzing the data

## India VS World(average)

- Lets see what is the global mean for score of freedom, economy etc.
- Then we will compare it with how India compares with the Rest.

``` r
mean_countries<-data_r %>% drop_na() %>%
   summarize( mean_freedom = mean(score_freedom), mean_economy = mean(score_economic), mean_press=mean(score_press),mean_democracy=mean(score_democracy)) 
      
head(mean_countries)
```

    ## # A tibble: 1 × 4
    ##   mean_freedom mean_economy mean_press mean_democracy
    ##          <dbl>        <dbl>      <dbl>          <dbl>
    ## 1         55.4         59.1       58.7           5.46

Right we can see global mean, now lets find out where India stands

``` r
Indian_r <- data_r[107,] %>% select(score_freedom, score_economic, score_press, score_democracy, country)
head(Indian_r)
```

    ## # A tibble: 1 × 5
    ##   score_freedom score_economic score_press score_democracy country
    ##           <dbl>          <dbl>       <dbl>           <dbl> <chr>  
    ## 1            66           52.9        36.6            6.61 India

#### It seems India is above global average in score_freedom and score_democracy but just above global average .Also there are many autocratic countries.

##### India is also below global average in score_economics and does very poorly in score_press.

##### lets dig a little deeper to see with data why it is the way it is. But first let us see top performing countries in each index.

## Top countries in each index

### Top countries with absolute freedom

``` r
top_freedom <- data_r %>% arrange(desc(score_freedom))
head(top_freedom) %>% select(country) 
```

    ## # A tibble: 6 × 1
    ##   country    
    ##   <chr>      
    ## 1 Norway     
    ## 2 Sweden     
    ## 3 Finland    
    ## 4 New Zealand
    ## 5 Canada     
    ## 6 Ireland

### Countries with best performing economies

``` r
top_economics <- data_r %>% arrange(desc(score_economic))
head(top_economics) %>% select(country)
```

    ## # A tibble: 6 × 1
    ##   country    
    ##   <chr>      
    ## 1 Singapore  
    ## 2 Switzerland
    ## 3 Ireland    
    ## 4 Taiwan     
    ## 5 New Zealand
    ## 6 Estonia

### Countries where Press is Absolutely free

``` r
top_press <- data_r %>% arrange(desc(score_press))
head(top_press) %>% select(country)
```

    ## # A tibble: 6 × 1
    ##   country    
    ##   <chr>      
    ## 1 Norway     
    ## 2 Ireland    
    ## 3 Denmark    
    ## 4 Sweden     
    ## 5 Finland    
    ## 6 Netherlands

### Countries with best Democracies

``` r
top_democracy <- data_r %>% arrange(desc(score_democracy))
head(top_democracy) %>% select(country)
```

    ## # A tibble: 6 × 1
    ##   country    
    ##   <chr>      
    ## 1 Norway     
    ## 2 Iceland    
    ## 3 Sweden     
    ## 4 New Zealand
    ## 5 Canada     
    ## 6 Finland

# Data Visualization

In this segment we:

- Will see how different indexes changes countries performance.
- Will see different parameters and analyze using visualization.
- Will see why India performs low in Economic index and Press index.

### Economy VS. Freedom

Lets see if Free Markets/Economies mean More Freedom

``` r
data_r %>% drop_na() %>%
  ggplot(aes(x = score_economic, y = score_freedom)) +
geom_point(color = "darkgreen") + geom_smooth() +
      labs(title = "Relationship Between Economic Score and Freedom Score",x = "Economic Score",y = "Freedom Score") +
    theme_minimal()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](freedomIndex_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

**WE can conclude**

- There is a positive relation till Economic score = 70 which means if
  one is developing nation it is better for them to have free markets.
- There is a negative relation from Economic score \> 70 which means if
  one is a developed nation, it is better to have mixed market.

### Democracy VS Freedom

Lets see if Democracies are inheretly Free

``` r
data_r %>% drop_na() %>%
  ggplot(aes(x = score_freedom, y = score_democracy)) +
    geom_point(color = "darkgreen") + geom_smooth(method='lm')+
      labs(title = "Relationship Between Freedom Score and Democracy Score",x = "Freedom Score",y = "Democracy Score") +
    theme_minimal()
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](freedomIndex_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

**As we can see there is a positive relation between Democracy and
Freedom, therefore, Stronger the Democracy more the Freedom**

**Now since India is a Democracy that is why India scores better than
global average in Freedom and Democracy**

#### Why India scores less in Press Freedom ?

To answer this question lets first see how the distribution of press
freedom looks like

### Distribution of press freedom.

``` r
data_r %>% drop_na() %>%
ggplot( aes(x = score_press)) +
  geom_histogram(binwidth = 5, fill = "orange", color = "black", alpha = 0.7) +
  labs(title = "Distribution of Press Freedom Scores",
       x = "Freedom Score",
       y = "Count") +
  theme_minimal()
```

![](freedomIndex_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

**It seems like most of the countries score better than 50 which means
in most countries there is fair Journalism** **To answer our original
question we need to see what is global Distribution of world Governments
and whether economy effects freedom of press**

### Distribution of Democracy categories.

``` r
ggplot(data_r, aes(x = democracy_index)) +
  geom_bar(fill = "skyblue", color = "black") + 
  labs(title = "Distribution of Democracy Categories",
       x = "Democracy Categories",
       y = "Count") +
  theme_minimal()
```

![](freedomIndex_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

**Since most of the countries are not Full Democracies and are not free
but the global average for Press Freedom is higher than 50 therefore we
can conclude that it is the Policies of Government which affects Press
Freedom than the kind of Government**

**Let us put our analysis to test**

### Freedom vs. Democracy with Press Freedom

``` r
data_r %>% drop_na()%>%
  ggplot(aes(x = score_freedom, y = score_democracy, size = score_press, color = democracy_index)) +
    geom_point(alpha = 0.7) +
    scale_size_continuous(range = c(3, 15)) +
    labs(title = "Bubble Chart: Freedom vs. Democracy with Press Freedom Bubble Size",
         x = "Freedom Score",
         y = "Democracy Score") +
    theme_minimal()
```

![](freedomIndex_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

**As we can from the above graph that some Authoritarian regimes have
higher press freedom than some Hybrid regimes and some flawed
democracies, therefore some Flawed Democracies might have some policies
that affect Press freedom**

### Classification of India As per the index.

``` r
data_r[107,] %>%
  select(freedom_in_the_world,economic_freedom,press_freedom,democracy_index)
```

    ## # A tibble: 1 × 4
    ##   freedom_in_the_world economic_freedom press_freedom democracy_index 
    ##   <chr>                <chr>            <chr>         <chr>           
    ## 1 partly free          mostly unfree    very serious  flawed democracy

### Definition of terms and policies of India

#### Flawed Democracy

‘Flawed democracies’ are nations where elections are fair and free and
basic civil liberties are honored but may have issues (e.g. media
freedom infringement and minor suppression of political opposition and
critics). These nations have significant faults in other democratic
aspects, including underdeveloped political culture, low levels of
participation in politics, and issues in the functioning of governance.

[Read more
here](https://www.populismstudies.org/Vocabulary/flawed-defective-democracy/)

#### Policies affecting/affected Press Freedom in India

- News Ads sponsored by ruling Government [Read
  Here](https://www.reuters.com/article/india-media-idINKCN1TT1R6/)

- Covid-19 and Irregular GDP Growth

  [Read
  Here](https://iju.org.in/media-room/issues/244-role-of-media-during-the-covid-19-pandemic-and-its-impact-on-media)

  [Read
  Here](https://www.macrotrends.net/countries/IND/india/gdp-gross-domestic-product)

# Conclusion

- **Developing economies should adopt Free Market and Developed
  economies should adopt Mixed Market**

- **Full Democracies give their Citizens Full Freedom**

- **Most of the countries are not Full Democracies**

- **Some Authoritarian regimes have better Press Freedom than some
  Democracies**

- **Press freedom Depends on the Policies of the government**

## How India can improve its numbers?

- **Having a Free economy**

- **Adopting New AD policy for NEWS Agencies**

- **Adopting New Strategies to become Full Democracy**

- **Right now India stands on 107th position Globally but scores better
  in Global Average in Freedom and Democracy**
