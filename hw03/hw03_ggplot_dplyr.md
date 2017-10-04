hw03\_ggplot\_dplyr
================
Aisha Uduman
October 2, 2017

Homework 3: Using dplyr and ggplot2 to manipulate and explore data
==================================================================

I will be exploring the Gapminder dataset and completing some tasks in order to manipulate (using dplyr) and visualize (using ggplot2) this data.

First I will load the Gapminder dataset, tidyverse package:

``` r
suppressPackageStartupMessages(library(tidyverse))
suppressPackageStartupMessages(library(gapminder))
suppressPackageStartupMessages(library(knitr))
```

Task 1: Get the maximum and minimum of GDP per capita for all continents
------------------------------------------------------------------------

To do this, I first grouped the gapminder data by continent using the `group_by` function. Then, I summarized the data based on what we're interested in finding: the minimum and maximum GDP per capita. Below is the code and the table of results:

``` r
task1 <- gapminder %>% 
  group_by(continent) %>% 
  summarize(min_gdpPercap = min(gdpPercap), max_gdpPercap = max(gdpPercap))
knitr::kable(task1)
```

| continent |  min\_gdpPercap|  max\_gdpPercap|
|:----------|---------------:|---------------:|
| Africa    |        241.1659|        21951.21|
| Americas  |       1201.6372|        42951.65|
| Asia      |        331.0000|       113523.13|
| Europe    |        973.5332|        49357.19|
| Oceania   |      10039.5956|        34435.37|

I'm not sure what the knitr::kable did, as it didn't make the table any more visually pleasing to look at.

``` r
knitr::kable(task1, digits=2)
```

| continent |  min\_gdpPercap|  max\_gdpPercap|
|:----------|---------------:|---------------:|
| Africa    |          241.17|        21951.21|
| Americas  |         1201.64|        42951.65|
| Asia      |          331.00|       113523.13|
| Europe    |          973.53|        49357.19|
| Oceania   |        10039.60|        34435.37|

Something I need to investigate further. I didn't notice a difference in the output visually, however I read in the kable documentation that there is an argument for "digits". I tried using this and was able to make the output a table to 2 decimal places.

#### Visualization

I thought using a boxplot would be a good option to visualise these results, as a bar graph wouldn't be able to distinguish the data appropriately (i.e. it would just be one bar per continent, minimum GDP per capita to maximum GDP per capita). I wanted a plot that would visualise the range of GDP per capita for each continent.

``` r
ggplot(gapminder, aes(continent, gdpPercap, fill=continent)) +
  geom_boxplot() +
  scale_fill_manual(values=c("darkslateblue", "olivedrab", "tan3", "orangered3", "maroon4")) + 
  labs(x = "Continent", y = "GDP per capita", title = "Boxplot of GDP per capita for Continents") + 
  theme(legend.position = "none",
        axis.title = element_text(size=14),
        axis.text.x = element_text(size=12),
        axis.text.y = element_text(size=12),
        plot.title = element_text(size=16, hjust = 0.5))
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-4-1.png)

The boxplot is decent, however I don't think the take-home information is as clear as it could be. This is likely due to the huge range of GDP per capita in the continent of Asia. In terms of visualization, I found some colour codes online (link is below) and I decided to leave the legend out as it's self-explanatory that each continent is coloured separately.

I then thought it would be good to try and use ggplot to plot both the minimum and the maximum GDP per capita points for each continent. The minimum could be one colour and the maximum could be a different colour so we can distinguish more easily between the continents. This took me a while but I think I figured it out:

``` r
ggplot(task1, aes(x=continent)) +
  geom_point(aes(y=min_gdpPercap), colour = "tomato3", size = 5) +
  geom_point(aes(y=max_gdpPercap), colour = "palegreen4", size = 5) +
  labs(y = "GDP per capita", x = "Continent", title = "Maximum and Minimum GDP per capita for the Continents") + 
  theme(axis.title = element_text(size=14),
        axis.text.x = element_text(size=12),
        axis.text.y = element_text(size=12),
        plot.title = element_text(size=16, hjust = 0.5))
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-5-1.png)

I think this is a much better way to visualize the minimum and maximum GDP per capita between each continent. I used the `labs` function we went over in class to label the y-axis to "GDP per capita", the x-axis to "Continent" and to make a title for the plot. However, it might be more informative to display this information in the form of a bar plot, where each continent has a minimum and maximum...I wasn't able to figure out how to do this (i.e. graphing 2 variables per continent).

The output shows us that Asia has the highest GDP per capita out of the continents. I was interested in finding out which country specifically had the highest GDP corresponding to the green maximum. From the initial table, I thought it would be interesting to see which countries in Asia have a higher GDP per capita than the maximum GDP per capita of Europe (which is ~50000).

``` r
gapminder %>% filter(continent=="Asia", gdpPercap>50000)
```

    ## # A tibble: 6 x 6
    ##   country continent  year lifeExp     pop gdpPercap
    ##    <fctr>    <fctr> <int>   <dbl>   <int>     <dbl>
    ## 1  Kuwait      Asia  1952  55.565  160000 108382.35
    ## 2  Kuwait      Asia  1957  58.033  212846 113523.13
    ## 3  Kuwait      Asia  1962  60.470  358266  95458.11
    ## 4  Kuwait      Asia  1967  64.624  575003  80894.88
    ## 5  Kuwait      Asia  1972  67.712  841934 109347.87
    ## 6  Kuwait      Asia  1977  69.343 1140357  59265.48

From this we can see that [Kuwait](https://en.wikipedia.org/wiki/Economy_of_Kuwait) has a very high GDP per capita and is likely a reason why the distribution of Asian GDP per capitas is so large. Kuwait has nearly 10% of the global oil reserves which accounts for nearly half of the country's GDP, and about 95% of export revenue and government income. With a fairly low population, this means its GDP per capita is consistently high.

Task 2: Look at the spread of GDP per capita within the continents
------------------------------------------------------------------

To look at 'spread', I decided to look at the mean, minimum, maximum and standard deviation of GDP per capita within the continents.

``` r
task2 <- gapminder %>%
  group_by(continent) %>% 
  summarize(mean_gdp = mean(gdpPercap), 
            min_gdp = min(gdpPercap), 
            max_gdp = max(gdpPercap), 
            sd_gdp = sd(gdpPercap))  
knitr::kable(task2, digits=2)
```

| continent |  mean\_gdp|  min\_gdp|   max\_gdp|   sd\_gdp|
|:----------|----------:|---------:|----------:|---------:|
| Africa    |    2193.75|    241.17|   21951.21|   2827.93|
| Americas  |    7136.11|   1201.64|   42951.65|   6396.76|
| Asia      |    7902.15|    331.00|  113523.13|  14045.37|
| Europe    |   14469.48|    973.53|   49357.19|   9355.21|
| Oceania   |   18621.61|  10039.60|   34435.37|   6358.98|

This gives us our information which we will now plot.

#### Visualization

``` r
gapminder %>% 
  ggplot(aes(x=continent, y=gdpPercap)) + 
  geom_boxplot(aes(fill = continent), show.legend = FALSE) + 
  scale_fill_manual(values=c("darkslateblue", "olivedrab", "tan3", "orangered3", "maroon4")) +
  labs(x = "Country", y = "GDP per capita", title = "Spread of GDP per capita Within Continents") + 
  theme(axis.title = element_text(size=14),
        axis.text.x = element_text(size=12),
        axis.text.y = element_text(size=12),
        plot.title = element_text(size=16, hjust = 0.5))
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-8-1.png)

This is essentially the same as the initial boxplot I made in Task 1. We can see that Asia has the largest spread, however I think there is a better way to visualize the information made in the initial dataframe for this task: Instead of including the mean, minimum, maximum and standard deviation, I think it would be more useful to show the mean, median and standard deviation:

``` r
gapminder %>%
  group_by(continent) %>% 
  summarize(mean_gdp = mean(gdpPercap), sd_gdp = sd(gdpPercap)) %>% 
  ggplot(aes(x = continent)) +
  geom_point(aes(y = mean_gdp), colour = "turquoise", size = 4) + 
  geom_point(aes(y = sd_gdp), colour = "purple", shape = 8, size = 3) + 
  labs(x = "Continent", y = "GDP per cap", title = "Spread of GDP per capita within Continents") +
  theme(axis.title = element_text(size=14),
        axis.text.x = element_text(size=12),
        axis.text.y = element_text(size=12),
        plot.title = element_text(size=16, hjust = 0.5))
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-9-1.png)

I played around with this a bit. I tried plotting the minimum GDP per capita, maximum GDP per capita, mean GDP per capita and standard deviation, however because of the huge range of GDP per capitas in Asia, I found this made it hard to determine trends using the other variables like standard deviation, as the symbols seemed to just overlap. Hence I decided to stick with the mean GDP per capita and standard deviation to determine spread. I decided to just look at standard deviation in relation to the mean as this is a widely used measure of dispersion which takes into account every variable in the dataset (unlike median, interquartile range, maximum and minimums). A high standard deviation indicates values are spread apart, a low standard deviation indicates values are similar and dispersed closely around the mean.

Though this also isn't super aesthetically pleasing, I think this tells us the information we want to know. We can see that Asia has the highest standard deviation of all the continents, likely due to the presence of extremely wealthy nations such as Kuwait and Saudi Arabia, as well as very poor nations. Africa has the lowest standard deviation.

What I found interesting was Oceania, which has the second-lowest standard deviation and the highest average GDP per capita of all the continents. I'm not sure why this is. I know Australia and New Zealand will have relatively high GDPs per capita, however I'm not sure how much the smaller nations such as Tonga, Samoa, Vanuatu etc would have a high GDP per capita - perhaps it's due to their small populations? Something to look into further.

Task 3: Find out how life expectancy is changing with time on different continents
----------------------------------------------------------------------------------

For this task, first I will select the relevant variables (continent, year and life expectancy) from the gapminder dataset, and then group the results by continent to see how life expectancy is changing over time.

``` r
task3 <- gapminder %>%
    select(continent, year, lifeExp) %>%
    group_by(continent, year)
knitr::kable(task3, digits=2)
```

| continent |  year|  lifeExp|
|:----------|-----:|--------:|
| Asia      |  1952|    28.80|
| Asia      |  1957|    30.33|
| Asia      |  1962|    32.00|
| Asia      |  1967|    34.02|
| Asia      |  1972|    36.09|
| Asia      |  1977|    38.44|
| Asia      |  1982|    39.85|
| Asia      |  1987|    40.82|
| Asia      |  1992|    41.67|
| Asia      |  1997|    41.76|
| Asia      |  2002|    42.13|
| Asia      |  2007|    43.83|
| Europe    |  1952|    55.23|
| Europe    |  1957|    59.28|
| Europe    |  1962|    64.82|
| Europe    |  1967|    66.22|
| Europe    |  1972|    67.69|
| Europe    |  1977|    68.93|
| Europe    |  1982|    70.42|
| Europe    |  1987|    72.00|
| Europe    |  1992|    71.58|
| Europe    |  1997|    72.95|
| Europe    |  2002|    75.65|
| Europe    |  2007|    76.42|
| Africa    |  1952|    43.08|
| Africa    |  1957|    45.69|
| Africa    |  1962|    48.30|
| Africa    |  1967|    51.41|
| Africa    |  1972|    54.52|
| Africa    |  1977|    58.01|
| Africa    |  1982|    61.37|
| Africa    |  1987|    65.80|
| Africa    |  1992|    67.74|
| Africa    |  1997|    69.15|
| Africa    |  2002|    70.99|
| Africa    |  2007|    72.30|
| Africa    |  1952|    30.02|
| Africa    |  1957|    32.00|
| Africa    |  1962|    34.00|
| Africa    |  1967|    35.98|
| Africa    |  1972|    37.93|
| Africa    |  1977|    39.48|
| Africa    |  1982|    39.94|
| Africa    |  1987|    39.91|
| Africa    |  1992|    40.65|
| Africa    |  1997|    40.96|
| Africa    |  2002|    41.00|
| Africa    |  2007|    42.73|
| Americas  |  1952|    62.48|
| Americas  |  1957|    64.40|
| Americas  |  1962|    65.14|
| Americas  |  1967|    65.63|
| Americas  |  1972|    67.06|
| Americas  |  1977|    68.48|
| Americas  |  1982|    69.94|
| Americas  |  1987|    70.77|
| Americas  |  1992|    71.87|
| Americas  |  1997|    73.28|
| Americas  |  2002|    74.34|
| Americas  |  2007|    75.32|
| Oceania   |  1952|    69.12|
| Oceania   |  1957|    70.33|
| Oceania   |  1962|    70.93|
| Oceania   |  1967|    71.10|
| Oceania   |  1972|    71.93|
| Oceania   |  1977|    73.49|
| Oceania   |  1982|    74.74|
| Oceania   |  1987|    76.32|
| Oceania   |  1992|    77.56|
| Oceania   |  1997|    78.83|
| Oceania   |  2002|    80.37|
| Oceania   |  2007|    81.23|
| Europe    |  1952|    66.80|
| Europe    |  1957|    67.48|
| Europe    |  1962|    69.54|
| Europe    |  1967|    70.14|
| Europe    |  1972|    70.63|
| Europe    |  1977|    72.17|
| Europe    |  1982|    73.18|
| Europe    |  1987|    74.94|
| Europe    |  1992|    76.04|
| Europe    |  1997|    77.51|
| Europe    |  2002|    78.98|
| Europe    |  2007|    79.83|
| Asia      |  1952|    50.94|
| Asia      |  1957|    53.83|
| Asia      |  1962|    56.92|
| Asia      |  1967|    59.92|
| Asia      |  1972|    63.30|
| Asia      |  1977|    65.59|
| Asia      |  1982|    69.05|
| Asia      |  1987|    70.75|
| Asia      |  1992|    72.60|
| Asia      |  1997|    73.92|
| Asia      |  2002|    74.80|
| Asia      |  2007|    75.64|
| Asia      |  1952|    37.48|
| Asia      |  1957|    39.35|
| Asia      |  1962|    41.22|
| Asia      |  1967|    43.45|
| Asia      |  1972|    45.25|
| Asia      |  1977|    46.92|
| Asia      |  1982|    50.01|
| Asia      |  1987|    52.82|
| Asia      |  1992|    56.02|
| Asia      |  1997|    59.41|
| Asia      |  2002|    62.01|
| Asia      |  2007|    64.06|
| Europe    |  1952|    68.00|
| Europe    |  1957|    69.24|
| Europe    |  1962|    70.25|
| Europe    |  1967|    70.94|
| Europe    |  1972|    71.44|
| Europe    |  1977|    72.80|
| Europe    |  1982|    73.93|
| Europe    |  1987|    75.35|
| Europe    |  1992|    76.46|
| Europe    |  1997|    77.53|
| Europe    |  2002|    78.32|
| Europe    |  2007|    79.44|
| Africa    |  1952|    38.22|
| Africa    |  1957|    40.36|
| Africa    |  1962|    42.62|
| Africa    |  1967|    44.88|
| Africa    |  1972|    47.01|
| Africa    |  1977|    49.19|
| Africa    |  1982|    50.90|
| Africa    |  1987|    52.34|
| Africa    |  1992|    53.92|
| Africa    |  1997|    54.78|
| Africa    |  2002|    54.41|
| Africa    |  2007|    56.73|
| Americas  |  1952|    40.41|
| Americas  |  1957|    41.89|
| Americas  |  1962|    43.43|
| Americas  |  1967|    45.03|
| Americas  |  1972|    46.71|
| Americas  |  1977|    50.02|
| Americas  |  1982|    53.86|
| Americas  |  1987|    57.25|
| Americas  |  1992|    59.96|
| Americas  |  1997|    62.05|
| Americas  |  2002|    63.88|
| Americas  |  2007|    65.55|
| Europe    |  1952|    53.82|
| Europe    |  1957|    58.45|
| Europe    |  1962|    61.93|
| Europe    |  1967|    64.79|
| Europe    |  1972|    67.45|
| Europe    |  1977|    69.86|
| Europe    |  1982|    70.69|
| Europe    |  1987|    71.14|
| Europe    |  1992|    72.18|
| Europe    |  1997|    73.24|
| Europe    |  2002|    74.09|
| Europe    |  2007|    74.85|
| Africa    |  1952|    47.62|
| Africa    |  1957|    49.62|
| Africa    |  1962|    51.52|
| Africa    |  1967|    53.30|
| Africa    |  1972|    56.02|
| Africa    |  1977|    59.32|
| Africa    |  1982|    61.48|
| Africa    |  1987|    63.62|
| Africa    |  1992|    62.74|
| Africa    |  1997|    52.56|
| Africa    |  2002|    46.63|
| Africa    |  2007|    50.73|
| Americas  |  1952|    50.92|
| Americas  |  1957|    53.28|
| Americas  |  1962|    55.66|
| Americas  |  1967|    57.63|
| Americas  |  1972|    59.50|
| Americas  |  1977|    61.49|
| Americas  |  1982|    63.34|
| Americas  |  1987|    65.20|
| Americas  |  1992|    67.06|
| Americas  |  1997|    69.39|
| Americas  |  2002|    71.01|
| Americas  |  2007|    72.39|
| Europe    |  1952|    59.60|
| Europe    |  1957|    66.61|
| Europe    |  1962|    69.51|
| Europe    |  1967|    70.42|
| Europe    |  1972|    70.90|
| Europe    |  1977|    70.81|
| Europe    |  1982|    71.08|
| Europe    |  1987|    71.34|
| Europe    |  1992|    71.19|
| Europe    |  1997|    70.32|
| Europe    |  2002|    72.14|
| Europe    |  2007|    73.00|
| Africa    |  1952|    31.98|
| Africa    |  1957|    34.91|
| Africa    |  1962|    37.81|
| Africa    |  1967|    40.70|
| Africa    |  1972|    43.59|
| Africa    |  1977|    46.14|
| Africa    |  1982|    48.12|
| Africa    |  1987|    49.56|
| Africa    |  1992|    50.26|
| Africa    |  1997|    50.32|
| Africa    |  2002|    50.65|
| Africa    |  2007|    52.30|
| Africa    |  1952|    39.03|
| Africa    |  1957|    40.53|
| Africa    |  1962|    42.05|
| Africa    |  1967|    43.55|
| Africa    |  1972|    44.06|
| Africa    |  1977|    45.91|
| Africa    |  1982|    47.47|
| Africa    |  1987|    48.21|
| Africa    |  1992|    44.74|
| Africa    |  1997|    45.33|
| Africa    |  2002|    47.36|
| Africa    |  2007|    49.58|
| Asia      |  1952|    39.42|
| Asia      |  1957|    41.37|
| Asia      |  1962|    43.41|
| Asia      |  1967|    45.41|
| Asia      |  1972|    40.32|
| Asia      |  1977|    31.22|
| Asia      |  1982|    50.96|
| Asia      |  1987|    53.91|
| Asia      |  1992|    55.80|
| Asia      |  1997|    56.53|
| Asia      |  2002|    56.75|
| Asia      |  2007|    59.72|
| Africa    |  1952|    38.52|
| Africa    |  1957|    40.43|
| Africa    |  1962|    42.64|
| Africa    |  1967|    44.80|
| Africa    |  1972|    47.05|
| Africa    |  1977|    49.35|
| Africa    |  1982|    52.96|
| Africa    |  1987|    54.98|
| Africa    |  1992|    54.31|
| Africa    |  1997|    52.20|
| Africa    |  2002|    49.86|
| Africa    |  2007|    50.43|
| Americas  |  1952|    68.75|
| Americas  |  1957|    69.96|
| Americas  |  1962|    71.30|
| Americas  |  1967|    72.13|
| Americas  |  1972|    72.88|
| Americas  |  1977|    74.21|
| Americas  |  1982|    75.76|
| Americas  |  1987|    76.86|
| Americas  |  1992|    77.95|
| Americas  |  1997|    78.61|
| Americas  |  2002|    79.77|
| Americas  |  2007|    80.65|
| Africa    |  1952|    35.46|
| Africa    |  1957|    37.46|
| Africa    |  1962|    39.48|
| Africa    |  1967|    41.48|
| Africa    |  1972|    43.46|
| Africa    |  1977|    46.77|
| Africa    |  1982|    48.30|
| Africa    |  1987|    50.48|
| Africa    |  1992|    49.40|
| Africa    |  1997|    46.07|
| Africa    |  2002|    43.31|
| Africa    |  2007|    44.74|
| Africa    |  1952|    38.09|
| Africa    |  1957|    39.88|
| Africa    |  1962|    41.72|
| Africa    |  1967|    43.60|
| Africa    |  1972|    45.57|
| Africa    |  1977|    47.38|
| Africa    |  1982|    49.52|
| Africa    |  1987|    51.05|
| Africa    |  1992|    51.72|
| Africa    |  1997|    51.57|
| Africa    |  2002|    50.52|
| Africa    |  2007|    50.65|
| Americas  |  1952|    54.74|
| Americas  |  1957|    56.07|
| Americas  |  1962|    57.92|
| Americas  |  1967|    60.52|
| Americas  |  1972|    63.44|
| Americas  |  1977|    67.05|
| Americas  |  1982|    70.56|
| Americas  |  1987|    72.49|
| Americas  |  1992|    74.13|
| Americas  |  1997|    75.82|
| Americas  |  2002|    77.86|
| Americas  |  2007|    78.55|
| Asia      |  1952|    44.00|
| Asia      |  1957|    50.55|
| Asia      |  1962|    44.50|
| Asia      |  1967|    58.38|
| Asia      |  1972|    63.12|
| Asia      |  1977|    63.97|
| Asia      |  1982|    65.53|
| Asia      |  1987|    67.27|
| Asia      |  1992|    68.69|
| Asia      |  1997|    70.43|
| Asia      |  2002|    72.03|
| Asia      |  2007|    72.96|
| Americas  |  1952|    50.64|
| Americas  |  1957|    55.12|
| Americas  |  1962|    57.86|
| Americas  |  1967|    59.96|
| Americas  |  1972|    61.62|
| Americas  |  1977|    63.84|
| Americas  |  1982|    66.65|
| Americas  |  1987|    67.77|
| Americas  |  1992|    68.42|
| Americas  |  1997|    70.31|
| Americas  |  2002|    71.68|
| Americas  |  2007|    72.89|
| Africa    |  1952|    40.72|
| Africa    |  1957|    42.46|
| Africa    |  1962|    44.47|
| Africa    |  1967|    46.47|
| Africa    |  1972|    48.94|
| Africa    |  1977|    50.94|
| Africa    |  1982|    52.93|
| Africa    |  1987|    54.93|
| Africa    |  1992|    57.94|
| Africa    |  1997|    60.66|
| Africa    |  2002|    62.97|
| Africa    |  2007|    65.15|
| Africa    |  1952|    39.14|
| Africa    |  1957|    40.65|
| Africa    |  1962|    42.12|
| Africa    |  1967|    44.06|
| Africa    |  1972|    45.99|
| Africa    |  1977|    47.80|
| Africa    |  1982|    47.78|
| Africa    |  1987|    47.41|
| Africa    |  1992|    45.55|
| Africa    |  1997|    42.59|
| Africa    |  2002|    44.97|
| Africa    |  2007|    46.46|
| Africa    |  1952|    42.11|
| Africa    |  1957|    45.05|
| Africa    |  1962|    48.44|
| Africa    |  1967|    52.04|
| Africa    |  1972|    54.91|
| Africa    |  1977|    55.62|
| Africa    |  1982|    56.70|
| Africa    |  1987|    57.47|
| Africa    |  1992|    56.43|
| Africa    |  1997|    52.96|
| Africa    |  2002|    52.97|
| Africa    |  2007|    55.32|
| Americas  |  1952|    57.21|
| Americas  |  1957|    60.03|
| Americas  |  1962|    62.84|
| Americas  |  1967|    65.42|
| Americas  |  1972|    67.85|
| Americas  |  1977|    70.75|
| Americas  |  1982|    73.45|
| Americas  |  1987|    74.75|
| Americas  |  1992|    75.71|
| Americas  |  1997|    77.26|
| Americas  |  2002|    78.12|
| Americas  |  2007|    78.78|
| Africa    |  1952|    40.48|
| Africa    |  1957|    42.47|
| Africa    |  1962|    44.93|
| Africa    |  1967|    47.35|
| Africa    |  1972|    49.80|
| Africa    |  1977|    52.37|
| Africa    |  1982|    53.98|
| Africa    |  1987|    54.66|
| Africa    |  1992|    52.04|
| Africa    |  1997|    47.99|
| Africa    |  2002|    46.83|
| Africa    |  2007|    48.33|
| Europe    |  1952|    61.21|
| Europe    |  1957|    64.77|
| Europe    |  1962|    67.13|
| Europe    |  1967|    68.50|
| Europe    |  1972|    69.61|
| Europe    |  1977|    70.64|
| Europe    |  1982|    70.46|
| Europe    |  1987|    71.52|
| Europe    |  1992|    72.53|
| Europe    |  1997|    73.68|
| Europe    |  2002|    74.88|
| Europe    |  2007|    75.75|
| Americas  |  1952|    59.42|
| Americas  |  1957|    62.33|
| Americas  |  1962|    65.25|
| Americas  |  1967|    68.29|
| Americas  |  1972|    70.72|
| Americas  |  1977|    72.65|
| Americas  |  1982|    73.72|
| Americas  |  1987|    74.17|
| Americas  |  1992|    74.41|
| Americas  |  1997|    76.15|
| Americas  |  2002|    77.16|
| Americas  |  2007|    78.27|
| Europe    |  1952|    66.87|
| Europe    |  1957|    69.03|
| Europe    |  1962|    69.90|
| Europe    |  1967|    70.38|
| Europe    |  1972|    70.29|
| Europe    |  1977|    70.71|
| Europe    |  1982|    70.96|
| Europe    |  1987|    71.58|
| Europe    |  1992|    72.40|
| Europe    |  1997|    74.01|
| Europe    |  2002|    75.51|
| Europe    |  2007|    76.49|
| Europe    |  1952|    70.78|
| Europe    |  1957|    71.81|
| Europe    |  1962|    72.35|
| Europe    |  1967|    72.96|
| Europe    |  1972|    73.47|
| Europe    |  1977|    74.69|
| Europe    |  1982|    74.63|
| Europe    |  1987|    74.80|
| Europe    |  1992|    75.33|
| Europe    |  1997|    76.11|
| Europe    |  2002|    77.18|
| Europe    |  2007|    78.33|
| Africa    |  1952|    34.81|
| Africa    |  1957|    37.33|
| Africa    |  1962|    39.69|
| Africa    |  1967|    42.07|
| Africa    |  1972|    44.37|
| Africa    |  1977|    46.52|
| Africa    |  1982|    48.81|
| Africa    |  1987|    50.04|
| Africa    |  1992|    51.60|
| Africa    |  1997|    53.16|
| Africa    |  2002|    53.37|
| Africa    |  2007|    54.79|
| Americas  |  1952|    45.93|
| Americas  |  1957|    49.83|
| Americas  |  1962|    53.46|
| Americas  |  1967|    56.75|
| Americas  |  1972|    59.63|
| Americas  |  1977|    61.79|
| Americas  |  1982|    63.73|
| Americas  |  1987|    66.05|
| Americas  |  1992|    68.46|
| Americas  |  1997|    69.96|
| Americas  |  2002|    70.85|
| Americas  |  2007|    72.23|
| Americas  |  1952|    48.36|
| Americas  |  1957|    51.36|
| Americas  |  1962|    54.64|
| Americas  |  1967|    56.68|
| Americas  |  1972|    58.80|
| Americas  |  1977|    61.31|
| Americas  |  1982|    64.34|
| Americas  |  1987|    67.23|
| Americas  |  1992|    69.61|
| Americas  |  1997|    72.31|
| Americas  |  2002|    74.17|
| Americas  |  2007|    74.99|
| Africa    |  1952|    41.89|
| Africa    |  1957|    44.44|
| Africa    |  1962|    46.99|
| Africa    |  1967|    49.29|
| Africa    |  1972|    51.14|
| Africa    |  1977|    53.32|
| Africa    |  1982|    56.01|
| Africa    |  1987|    59.80|
| Africa    |  1992|    63.67|
| Africa    |  1997|    67.22|
| Africa    |  2002|    69.81|
| Africa    |  2007|    71.34|
| Americas  |  1952|    45.26|
| Americas  |  1957|    48.57|
| Americas  |  1962|    52.31|
| Americas  |  1967|    55.85|
| Americas  |  1972|    58.21|
| Americas  |  1977|    56.70|
| Americas  |  1982|    56.60|
| Americas  |  1987|    63.15|
| Americas  |  1992|    66.80|
| Americas  |  1997|    69.53|
| Americas  |  2002|    70.73|
| Americas  |  2007|    71.88|
| Africa    |  1952|    34.48|
| Africa    |  1957|    35.98|
| Africa    |  1962|    37.48|
| Africa    |  1967|    38.99|
| Africa    |  1972|    40.52|
| Africa    |  1977|    42.02|
| Africa    |  1982|    43.66|
| Africa    |  1987|    45.66|
| Africa    |  1992|    47.55|
| Africa    |  1997|    48.24|
| Africa    |  2002|    49.35|
| Africa    |  2007|    51.58|
| Africa    |  1952|    35.93|
| Africa    |  1957|    38.05|
| Africa    |  1962|    40.16|
| Africa    |  1967|    42.19|
| Africa    |  1972|    44.14|
| Africa    |  1977|    44.53|
| Africa    |  1982|    43.89|
| Africa    |  1987|    46.45|
| Africa    |  1992|    49.99|
| Africa    |  1997|    53.38|
| Africa    |  2002|    55.24|
| Africa    |  2007|    58.04|
| Africa    |  1952|    34.08|
| Africa    |  1957|    36.67|
| Africa    |  1962|    40.06|
| Africa    |  1967|    42.12|
| Africa    |  1972|    43.52|
| Africa    |  1977|    44.51|
| Africa    |  1982|    44.92|
| Africa    |  1987|    46.68|
| Africa    |  1992|    48.09|
| Africa    |  1997|    49.40|
| Africa    |  2002|    50.73|
| Africa    |  2007|    52.95|
| Europe    |  1952|    66.55|
| Europe    |  1957|    67.49|
| Europe    |  1962|    68.75|
| Europe    |  1967|    69.83|
| Europe    |  1972|    70.87|
| Europe    |  1977|    72.52|
| Europe    |  1982|    74.55|
| Europe    |  1987|    74.83|
| Europe    |  1992|    75.70|
| Europe    |  1997|    77.13|
| Europe    |  2002|    78.37|
| Europe    |  2007|    79.31|
| Europe    |  1952|    67.41|
| Europe    |  1957|    68.93|
| Europe    |  1962|    70.51|
| Europe    |  1967|    71.55|
| Europe    |  1972|    72.38|
| Europe    |  1977|    73.83|
| Europe    |  1982|    74.89|
| Europe    |  1987|    76.34|
| Europe    |  1992|    77.46|
| Europe    |  1997|    78.64|
| Europe    |  2002|    79.59|
| Europe    |  2007|    80.66|
| Africa    |  1952|    37.00|
| Africa    |  1957|    39.00|
| Africa    |  1962|    40.49|
| Africa    |  1967|    44.60|
| Africa    |  1972|    48.69|
| Africa    |  1977|    52.79|
| Africa    |  1982|    56.56|
| Africa    |  1987|    60.19|
| Africa    |  1992|    61.37|
| Africa    |  1997|    60.46|
| Africa    |  2002|    56.76|
| Africa    |  2007|    56.73|
| Africa    |  1952|    30.00|
| Africa    |  1957|    32.06|
| Africa    |  1962|    33.90|
| Africa    |  1967|    35.86|
| Africa    |  1972|    38.31|
| Africa    |  1977|    41.84|
| Africa    |  1982|    45.58|
| Africa    |  1987|    49.27|
| Africa    |  1992|    52.64|
| Africa    |  1997|    55.86|
| Africa    |  2002|    58.04|
| Africa    |  2007|    59.45|
| Europe    |  1952|    67.50|
| Europe    |  1957|    69.10|
| Europe    |  1962|    70.30|
| Europe    |  1967|    70.80|
| Europe    |  1972|    71.00|
| Europe    |  1977|    72.50|
| Europe    |  1982|    73.80|
| Europe    |  1987|    74.85|
| Europe    |  1992|    76.07|
| Europe    |  1997|    77.34|
| Europe    |  2002|    78.67|
| Europe    |  2007|    79.41|
| Africa    |  1952|    43.15|
| Africa    |  1957|    44.78|
| Africa    |  1962|    46.45|
| Africa    |  1967|    48.07|
| Africa    |  1972|    49.88|
| Africa    |  1977|    51.76|
| Africa    |  1982|    53.74|
| Africa    |  1987|    55.73|
| Africa    |  1992|    57.50|
| Africa    |  1997|    58.56|
| Africa    |  2002|    58.45|
| Africa    |  2007|    60.02|
| Europe    |  1952|    65.86|
| Europe    |  1957|    67.86|
| Europe    |  1962|    69.51|
| Europe    |  1967|    71.00|
| Europe    |  1972|    72.34|
| Europe    |  1977|    73.68|
| Europe    |  1982|    75.24|
| Europe    |  1987|    76.67|
| Europe    |  1992|    77.03|
| Europe    |  1997|    77.87|
| Europe    |  2002|    78.26|
| Europe    |  2007|    79.48|
| Americas  |  1952|    42.02|
| Americas  |  1957|    44.14|
| Americas  |  1962|    46.95|
| Americas  |  1967|    50.02|
| Americas  |  1972|    53.74|
| Americas  |  1977|    56.03|
| Americas  |  1982|    58.14|
| Americas  |  1987|    60.78|
| Americas  |  1992|    63.37|
| Americas  |  1997|    66.32|
| Americas  |  2002|    68.98|
| Americas  |  2007|    70.26|
| Africa    |  1952|    33.61|
| Africa    |  1957|    34.56|
| Africa    |  1962|    35.75|
| Africa    |  1967|    37.20|
| Africa    |  1972|    38.84|
| Africa    |  1977|    40.76|
| Africa    |  1982|    42.89|
| Africa    |  1987|    45.55|
| Africa    |  1992|    48.58|
| Africa    |  1997|    51.45|
| Africa    |  2002|    53.68|
| Africa    |  2007|    56.01|
| Africa    |  1952|    32.50|
| Africa    |  1957|    33.49|
| Africa    |  1962|    34.49|
| Africa    |  1967|    35.49|
| Africa    |  1972|    36.49|
| Africa    |  1977|    37.47|
| Africa    |  1982|    39.33|
| Africa    |  1987|    41.24|
| Africa    |  1992|    43.27|
| Africa    |  1997|    44.87|
| Africa    |  2002|    45.50|
| Africa    |  2007|    46.39|
| Americas  |  1952|    37.58|
| Americas  |  1957|    40.70|
| Americas  |  1962|    43.59|
| Americas  |  1967|    46.24|
| Americas  |  1972|    48.04|
| Americas  |  1977|    49.92|
| Americas  |  1982|    51.46|
| Americas  |  1987|    53.64|
| Americas  |  1992|    55.09|
| Americas  |  1997|    56.67|
| Americas  |  2002|    58.14|
| Americas  |  2007|    60.92|
| Americas  |  1952|    41.91|
| Americas  |  1957|    44.66|
| Americas  |  1962|    48.04|
| Americas  |  1967|    50.92|
| Americas  |  1972|    53.88|
| Americas  |  1977|    57.40|
| Americas  |  1982|    60.91|
| Americas  |  1987|    64.49|
| Americas  |  1992|    66.40|
| Americas  |  1997|    67.66|
| Americas  |  2002|    68.56|
| Americas  |  2007|    70.20|
| Asia      |  1952|    60.96|
| Asia      |  1957|    64.75|
| Asia      |  1962|    67.65|
| Asia      |  1967|    70.00|
| Asia      |  1972|    72.00|
| Asia      |  1977|    73.60|
| Asia      |  1982|    75.45|
| Asia      |  1987|    76.20|
| Asia      |  1992|    77.60|
| Asia      |  1997|    80.00|
| Asia      |  2002|    81.50|
| Asia      |  2007|    82.21|
| Europe    |  1952|    64.03|
| Europe    |  1957|    66.41|
| Europe    |  1962|    67.96|
| Europe    |  1967|    69.50|
| Europe    |  1972|    69.76|
| Europe    |  1977|    69.95|
| Europe    |  1982|    69.39|
| Europe    |  1987|    69.58|
| Europe    |  1992|    69.17|
| Europe    |  1997|    71.04|
| Europe    |  2002|    72.59|
| Europe    |  2007|    73.34|
| Europe    |  1952|    72.49|
| Europe    |  1957|    73.47|
| Europe    |  1962|    73.68|
| Europe    |  1967|    73.73|
| Europe    |  1972|    74.46|
| Europe    |  1977|    76.11|
| Europe    |  1982|    76.99|
| Europe    |  1987|    77.23|
| Europe    |  1992|    78.77|
| Europe    |  1997|    78.95|
| Europe    |  2002|    80.50|
| Europe    |  2007|    81.76|
| Asia      |  1952|    37.37|
| Asia      |  1957|    40.25|
| Asia      |  1962|    43.60|
| Asia      |  1967|    47.19|
| Asia      |  1972|    50.65|
| Asia      |  1977|    54.21|
| Asia      |  1982|    56.60|
| Asia      |  1987|    58.55|
| Asia      |  1992|    60.22|
| Asia      |  1997|    61.77|
| Asia      |  2002|    62.88|
| Asia      |  2007|    64.70|
| Asia      |  1952|    37.47|
| Asia      |  1957|    39.92|
| Asia      |  1962|    42.52|
| Asia      |  1967|    45.96|
| Asia      |  1972|    49.20|
| Asia      |  1977|    52.70|
| Asia      |  1982|    56.16|
| Asia      |  1987|    60.14|
| Asia      |  1992|    62.68|
| Asia      |  1997|    66.04|
| Asia      |  2002|    68.59|
| Asia      |  2007|    70.65|
| Asia      |  1952|    44.87|
| Asia      |  1957|    47.18|
| Asia      |  1962|    49.33|
| Asia      |  1967|    52.47|
| Asia      |  1972|    55.23|
| Asia      |  1977|    57.70|
| Asia      |  1982|    59.62|
| Asia      |  1987|    63.04|
| Asia      |  1992|    65.74|
| Asia      |  1997|    68.04|
| Asia      |  2002|    69.45|
| Asia      |  2007|    70.96|
| Asia      |  1952|    45.32|
| Asia      |  1957|    48.44|
| Asia      |  1962|    51.46|
| Asia      |  1967|    54.46|
| Asia      |  1972|    56.95|
| Asia      |  1977|    60.41|
| Asia      |  1982|    62.04|
| Asia      |  1987|    65.04|
| Asia      |  1992|    59.46|
| Asia      |  1997|    58.81|
| Asia      |  2002|    57.05|
| Asia      |  2007|    59.55|
| Europe    |  1952|    66.91|
| Europe    |  1957|    68.90|
| Europe    |  1962|    70.29|
| Europe    |  1967|    71.08|
| Europe    |  1972|    71.28|
| Europe    |  1977|    72.03|
| Europe    |  1982|    73.10|
| Europe    |  1987|    74.36|
| Europe    |  1992|    75.47|
| Europe    |  1997|    76.12|
| Europe    |  2002|    77.78|
| Europe    |  2007|    78.89|
| Asia      |  1952|    65.39|
| Asia      |  1957|    67.84|
| Asia      |  1962|    69.39|
| Asia      |  1967|    70.75|
| Asia      |  1972|    71.63|
| Asia      |  1977|    73.06|
| Asia      |  1982|    74.45|
| Asia      |  1987|    75.60|
| Asia      |  1992|    76.93|
| Asia      |  1997|    78.27|
| Asia      |  2002|    79.70|
| Asia      |  2007|    80.75|
| Europe    |  1952|    65.94|
| Europe    |  1957|    67.81|
| Europe    |  1962|    69.24|
| Europe    |  1967|    71.06|
| Europe    |  1972|    72.19|
| Europe    |  1977|    73.48|
| Europe    |  1982|    74.98|
| Europe    |  1987|    76.42|
| Europe    |  1992|    77.44|
| Europe    |  1997|    78.82|
| Europe    |  2002|    80.24|
| Europe    |  2007|    80.55|
| Americas  |  1952|    58.53|
| Americas  |  1957|    62.61|
| Americas  |  1962|    65.61|
| Americas  |  1967|    67.51|
| Americas  |  1972|    69.00|
| Americas  |  1977|    70.11|
| Americas  |  1982|    71.21|
| Americas  |  1987|    71.77|
| Americas  |  1992|    71.77|
| Americas  |  1997|    72.26|
| Americas  |  2002|    72.05|
| Americas  |  2007|    72.57|
| Asia      |  1952|    63.03|
| Asia      |  1957|    65.50|
| Asia      |  1962|    68.73|
| Asia      |  1967|    71.43|
| Asia      |  1972|    73.42|
| Asia      |  1977|    75.38|
| Asia      |  1982|    77.11|
| Asia      |  1987|    78.67|
| Asia      |  1992|    79.36|
| Asia      |  1997|    80.69|
| Asia      |  2002|    82.00|
| Asia      |  2007|    82.60|
| Asia      |  1952|    43.16|
| Asia      |  1957|    45.67|
| Asia      |  1962|    48.13|
| Asia      |  1967|    51.63|
| Asia      |  1972|    56.53|
| Asia      |  1977|    61.13|
| Asia      |  1982|    63.74|
| Asia      |  1987|    65.87|
| Asia      |  1992|    68.02|
| Asia      |  1997|    69.77|
| Asia      |  2002|    71.26|
| Asia      |  2007|    72.53|
| Africa    |  1952|    42.27|
| Africa    |  1957|    44.69|
| Africa    |  1962|    47.95|
| Africa    |  1967|    50.65|
| Africa    |  1972|    53.56|
| Africa    |  1977|    56.16|
| Africa    |  1982|    58.77|
| Africa    |  1987|    59.34|
| Africa    |  1992|    59.28|
| Africa    |  1997|    54.41|
| Africa    |  2002|    50.99|
| Africa    |  2007|    54.11|
| Asia      |  1952|    50.06|
| Asia      |  1957|    54.08|
| Asia      |  1962|    56.66|
| Asia      |  1967|    59.94|
| Asia      |  1972|    63.98|
| Asia      |  1977|    67.16|
| Asia      |  1982|    69.10|
| Asia      |  1987|    70.65|
| Asia      |  1992|    69.98|
| Asia      |  1997|    67.73|
| Asia      |  2002|    66.66|
| Asia      |  2007|    67.30|
| Asia      |  1952|    47.45|
| Asia      |  1957|    52.68|
| Asia      |  1962|    55.29|
| Asia      |  1967|    57.72|
| Asia      |  1972|    62.61|
| Asia      |  1977|    64.77|
| Asia      |  1982|    67.12|
| Asia      |  1987|    69.81|
| Asia      |  1992|    72.24|
| Asia      |  1997|    74.65|
| Asia      |  2002|    77.05|
| Asia      |  2007|    78.62|
| Asia      |  1952|    55.56|
| Asia      |  1957|    58.03|
| Asia      |  1962|    60.47|
| Asia      |  1967|    64.62|
| Asia      |  1972|    67.71|
| Asia      |  1977|    69.34|
| Asia      |  1982|    71.31|
| Asia      |  1987|    74.17|
| Asia      |  1992|    75.19|
| Asia      |  1997|    76.16|
| Asia      |  2002|    76.90|
| Asia      |  2007|    77.59|
| Asia      |  1952|    55.93|
| Asia      |  1957|    59.49|
| Asia      |  1962|    62.09|
| Asia      |  1967|    63.87|
| Asia      |  1972|    65.42|
| Asia      |  1977|    66.10|
| Asia      |  1982|    66.98|
| Asia      |  1987|    67.93|
| Asia      |  1992|    69.29|
| Asia      |  1997|    70.27|
| Asia      |  2002|    71.03|
| Asia      |  2007|    71.99|
| Africa    |  1952|    42.14|
| Africa    |  1957|    45.05|
| Africa    |  1962|    47.75|
| Africa    |  1967|    48.49|
| Africa    |  1972|    49.77|
| Africa    |  1977|    52.21|
| Africa    |  1982|    55.08|
| Africa    |  1987|    57.18|
| Africa    |  1992|    59.69|
| Africa    |  1997|    55.56|
| Africa    |  2002|    44.59|
| Africa    |  2007|    42.59|
| Africa    |  1952|    38.48|
| Africa    |  1957|    39.49|
| Africa    |  1962|    40.50|
| Africa    |  1967|    41.54|
| Africa    |  1972|    42.61|
| Africa    |  1977|    43.76|
| Africa    |  1982|    44.85|
| Africa    |  1987|    46.03|
| Africa    |  1992|    40.80|
| Africa    |  1997|    42.22|
| Africa    |  2002|    43.75|
| Africa    |  2007|    45.68|
| Africa    |  1952|    42.72|
| Africa    |  1957|    45.29|
| Africa    |  1962|    47.81|
| Africa    |  1967|    50.23|
| Africa    |  1972|    52.77|
| Africa    |  1977|    57.44|
| Africa    |  1982|    62.16|
| Africa    |  1987|    66.23|
| Africa    |  1992|    68.75|
| Africa    |  1997|    71.56|
| Africa    |  2002|    72.74|
| Africa    |  2007|    73.95|
| Africa    |  1952|    36.68|
| Africa    |  1957|    38.87|
| Africa    |  1962|    40.85|
| Africa    |  1967|    42.88|
| Africa    |  1972|    44.85|
| Africa    |  1977|    46.88|
| Africa    |  1982|    48.97|
| Africa    |  1987|    49.35|
| Africa    |  1992|    52.21|
| Africa    |  1997|    54.98|
| Africa    |  2002|    57.29|
| Africa    |  2007|    59.44|
| Africa    |  1952|    36.26|
| Africa    |  1957|    37.21|
| Africa    |  1962|    38.41|
| Africa    |  1967|    39.49|
| Africa    |  1972|    41.77|
| Africa    |  1977|    43.77|
| Africa    |  1982|    45.64|
| Africa    |  1987|    47.46|
| Africa    |  1992|    49.42|
| Africa    |  1997|    47.49|
| Africa    |  2002|    45.01|
| Africa    |  2007|    48.30|
| Asia      |  1952|    48.46|
| Asia      |  1957|    52.10|
| Asia      |  1962|    55.74|
| Asia      |  1967|    59.37|
| Asia      |  1972|    63.01|
| Asia      |  1977|    65.26|
| Asia      |  1982|    68.00|
| Asia      |  1987|    69.50|
| Asia      |  1992|    70.69|
| Asia      |  1997|    71.94|
| Asia      |  2002|    73.04|
| Asia      |  2007|    74.24|
| Africa    |  1952|    33.69|
| Africa    |  1957|    35.31|
| Africa    |  1962|    36.94|
| Africa    |  1967|    38.49|
| Africa    |  1972|    39.98|
| Africa    |  1977|    41.71|
| Africa    |  1982|    43.92|
| Africa    |  1987|    46.36|
| Africa    |  1992|    48.39|
| Africa    |  1997|    49.90|
| Africa    |  2002|    51.82|
| Africa    |  2007|    54.47|
| Africa    |  1952|    40.54|
| Africa    |  1957|    42.34|
| Africa    |  1962|    44.25|
| Africa    |  1967|    46.29|
| Africa    |  1972|    48.44|
| Africa    |  1977|    50.85|
| Africa    |  1982|    53.60|
| Africa    |  1987|    56.15|
| Africa    |  1992|    58.33|
| Africa    |  1997|    60.43|
| Africa    |  2002|    62.25|
| Africa    |  2007|    64.16|
| Africa    |  1952|    50.99|
| Africa    |  1957|    58.09|
| Africa    |  1962|    60.25|
| Africa    |  1967|    61.56|
| Africa    |  1972|    62.94|
| Africa    |  1977|    64.93|
| Africa    |  1982|    66.71|
| Africa    |  1987|    68.74|
| Africa    |  1992|    69.75|
| Africa    |  1997|    70.74|
| Africa    |  2002|    71.95|
| Africa    |  2007|    72.80|
| Americas  |  1952|    50.79|
| Americas  |  1957|    55.19|
| Americas  |  1962|    58.30|
| Americas  |  1967|    60.11|
| Americas  |  1972|    62.36|
| Americas  |  1977|    65.03|
| Americas  |  1982|    67.41|
| Americas  |  1987|    69.50|
| Americas  |  1992|    71.45|
| Americas  |  1997|    73.67|
| Americas  |  2002|    74.90|
| Americas  |  2007|    76.19|
| Asia      |  1952|    42.24|
| Asia      |  1957|    45.25|
| Asia      |  1962|    48.25|
| Asia      |  1967|    51.25|
| Asia      |  1972|    53.75|
| Asia      |  1977|    55.49|
| Asia      |  1982|    57.49|
| Asia      |  1987|    60.22|
| Asia      |  1992|    61.27|
| Asia      |  1997|    63.62|
| Asia      |  2002|    65.03|
| Asia      |  2007|    66.80|
| Europe    |  1952|    59.16|
| Europe    |  1957|    61.45|
| Europe    |  1962|    63.73|
| Europe    |  1967|    67.18|
| Europe    |  1972|    70.64|
| Europe    |  1977|    73.07|
| Europe    |  1982|    74.10|
| Europe    |  1987|    74.86|
| Europe    |  1992|    75.44|
| Europe    |  1997|    75.44|
| Europe    |  2002|    73.98|
| Europe    |  2007|    74.54|
| Africa    |  1952|    42.87|
| Africa    |  1957|    45.42|
| Africa    |  1962|    47.92|
| Africa    |  1967|    50.34|
| Africa    |  1972|    52.86|
| Africa    |  1977|    55.73|
| Africa    |  1982|    59.65|
| Africa    |  1987|    62.68|
| Africa    |  1992|    65.39|
| Africa    |  1997|    67.66|
| Africa    |  2002|    69.61|
| Africa    |  2007|    71.16|
| Africa    |  1952|    31.29|
| Africa    |  1957|    33.78|
| Africa    |  1962|    36.16|
| Africa    |  1967|    38.11|
| Africa    |  1972|    40.33|
| Africa    |  1977|    42.49|
| Africa    |  1982|    42.80|
| Africa    |  1987|    42.86|
| Africa    |  1992|    44.28|
| Africa    |  1997|    46.34|
| Africa    |  2002|    44.03|
| Africa    |  2007|    42.08|
| Asia      |  1952|    36.32|
| Asia      |  1957|    41.91|
| Asia      |  1962|    45.11|
| Asia      |  1967|    49.38|
| Asia      |  1972|    53.07|
| Asia      |  1977|    56.06|
| Asia      |  1982|    58.06|
| Asia      |  1987|    58.34|
| Asia      |  1992|    59.32|
| Asia      |  1997|    60.33|
| Asia      |  2002|    59.91|
| Asia      |  2007|    62.07|
| Africa    |  1952|    41.73|
| Africa    |  1957|    45.23|
| Africa    |  1962|    48.39|
| Africa    |  1967|    51.16|
| Africa    |  1972|    53.87|
| Africa    |  1977|    56.44|
| Africa    |  1982|    58.97|
| Africa    |  1987|    60.84|
| Africa    |  1992|    62.00|
| Africa    |  1997|    58.91|
| Africa    |  2002|    51.48|
| Africa    |  2007|    52.91|
| Asia      |  1952|    36.16|
| Asia      |  1957|    37.69|
| Asia      |  1962|    39.39|
| Asia      |  1967|    41.47|
| Asia      |  1972|    43.97|
| Asia      |  1977|    46.75|
| Asia      |  1982|    49.59|
| Asia      |  1987|    52.54|
| Asia      |  1992|    55.73|
| Asia      |  1997|    59.43|
| Asia      |  2002|    61.34|
| Asia      |  2007|    63.78|
| Europe    |  1952|    72.13|
| Europe    |  1957|    72.99|
| Europe    |  1962|    73.23|
| Europe    |  1967|    73.82|
| Europe    |  1972|    73.75|
| Europe    |  1977|    75.24|
| Europe    |  1982|    76.05|
| Europe    |  1987|    76.83|
| Europe    |  1992|    77.42|
| Europe    |  1997|    78.03|
| Europe    |  2002|    78.53|
| Europe    |  2007|    79.76|
| Oceania   |  1952|    69.39|
| Oceania   |  1957|    70.26|
| Oceania   |  1962|    71.24|
| Oceania   |  1967|    71.52|
| Oceania   |  1972|    71.89|
| Oceania   |  1977|    72.22|
| Oceania   |  1982|    73.84|
| Oceania   |  1987|    74.32|
| Oceania   |  1992|    76.33|
| Oceania   |  1997|    77.55|
| Oceania   |  2002|    79.11|
| Oceania   |  2007|    80.20|
| Americas  |  1952|    42.31|
| Americas  |  1957|    45.43|
| Americas  |  1962|    48.63|
| Americas  |  1967|    51.88|
| Americas  |  1972|    55.15|
| Americas  |  1977|    57.47|
| Americas  |  1982|    59.30|
| Americas  |  1987|    62.01|
| Americas  |  1992|    65.84|
| Americas  |  1997|    68.43|
| Americas  |  2002|    70.84|
| Americas  |  2007|    72.90|
| Africa    |  1952|    37.44|
| Africa    |  1957|    38.60|
| Africa    |  1962|    39.49|
| Africa    |  1967|    40.12|
| Africa    |  1972|    40.55|
| Africa    |  1977|    41.29|
| Africa    |  1982|    42.60|
| Africa    |  1987|    44.55|
| Africa    |  1992|    47.39|
| Africa    |  1997|    51.31|
| Africa    |  2002|    54.50|
| Africa    |  2007|    56.87|
| Africa    |  1952|    36.32|
| Africa    |  1957|    37.80|
| Africa    |  1962|    39.36|
| Africa    |  1967|    41.04|
| Africa    |  1972|    42.82|
| Africa    |  1977|    44.51|
| Africa    |  1982|    45.83|
| Africa    |  1987|    46.89|
| Africa    |  1992|    47.47|
| Africa    |  1997|    47.46|
| Africa    |  2002|    46.61|
| Africa    |  2007|    46.86|
| Europe    |  1952|    72.67|
| Europe    |  1957|    73.44|
| Europe    |  1962|    73.47|
| Europe    |  1967|    74.08|
| Europe    |  1972|    74.34|
| Europe    |  1977|    75.37|
| Europe    |  1982|    75.97|
| Europe    |  1987|    75.89|
| Europe    |  1992|    77.32|
| Europe    |  1997|    78.32|
| Europe    |  2002|    79.05|
| Europe    |  2007|    80.20|
| Asia      |  1952|    37.58|
| Asia      |  1957|    40.08|
| Asia      |  1962|    43.16|
| Asia      |  1967|    46.99|
| Asia      |  1972|    52.14|
| Asia      |  1977|    57.37|
| Asia      |  1982|    62.73|
| Asia      |  1987|    67.73|
| Asia      |  1992|    71.20|
| Asia      |  1997|    72.50|
| Asia      |  2002|    74.19|
| Asia      |  2007|    75.64|
| Asia      |  1952|    43.44|
| Asia      |  1957|    45.56|
| Asia      |  1962|    47.67|
| Asia      |  1967|    49.80|
| Asia      |  1972|    51.93|
| Asia      |  1977|    54.04|
| Asia      |  1982|    56.16|
| Asia      |  1987|    58.24|
| Asia      |  1992|    60.84|
| Asia      |  1997|    61.82|
| Asia      |  2002|    63.61|
| Asia      |  2007|    65.48|
| Americas  |  1952|    55.19|
| Americas  |  1957|    59.20|
| Americas  |  1962|    61.82|
| Americas  |  1967|    64.07|
| Americas  |  1972|    66.22|
| Americas  |  1977|    68.68|
| Americas  |  1982|    70.47|
| Americas  |  1987|    71.52|
| Americas  |  1992|    72.46|
| Americas  |  1997|    73.74|
| Americas  |  2002|    74.71|
| Americas  |  2007|    75.54|
| Americas  |  1952|    62.65|
| Americas  |  1957|    63.20|
| Americas  |  1962|    64.36|
| Americas  |  1967|    64.95|
| Americas  |  1972|    65.81|
| Americas  |  1977|    66.35|
| Americas  |  1982|    66.87|
| Americas  |  1987|    67.38|
| Americas  |  1992|    68.22|
| Americas  |  1997|    69.40|
| Americas  |  2002|    70.75|
| Americas  |  2007|    71.75|
| Americas  |  1952|    43.90|
| Americas  |  1957|    46.26|
| Americas  |  1962|    49.10|
| Americas  |  1967|    51.45|
| Americas  |  1972|    55.45|
| Americas  |  1977|    58.45|
| Americas  |  1982|    61.41|
| Americas  |  1987|    64.13|
| Americas  |  1992|    66.46|
| Americas  |  1997|    68.39|
| Americas  |  2002|    69.91|
| Americas  |  2007|    71.42|
| Asia      |  1952|    47.75|
| Asia      |  1957|    51.33|
| Asia      |  1962|    54.76|
| Asia      |  1967|    56.39|
| Asia      |  1972|    58.06|
| Asia      |  1977|    60.06|
| Asia      |  1982|    62.08|
| Asia      |  1987|    64.15|
| Asia      |  1992|    66.46|
| Asia      |  1997|    68.56|
| Asia      |  2002|    70.30|
| Asia      |  2007|    71.69|
| Europe    |  1952|    61.31|
| Europe    |  1957|    65.77|
| Europe    |  1962|    67.64|
| Europe    |  1967|    69.61|
| Europe    |  1972|    70.85|
| Europe    |  1977|    70.67|
| Europe    |  1982|    71.32|
| Europe    |  1987|    70.98|
| Europe    |  1992|    70.99|
| Europe    |  1997|    72.75|
| Europe    |  2002|    74.67|
| Europe    |  2007|    75.56|
| Europe    |  1952|    59.82|
| Europe    |  1957|    61.51|
| Europe    |  1962|    64.39|
| Europe    |  1967|    66.60|
| Europe    |  1972|    69.26|
| Europe    |  1977|    70.41|
| Europe    |  1982|    72.77|
| Europe    |  1987|    74.06|
| Europe    |  1992|    74.86|
| Europe    |  1997|    75.97|
| Europe    |  2002|    77.29|
| Europe    |  2007|    78.10|
| Americas  |  1952|    64.28|
| Americas  |  1957|    68.54|
| Americas  |  1962|    69.62|
| Americas  |  1967|    71.10|
| Americas  |  1972|    72.16|
| Americas  |  1977|    73.44|
| Americas  |  1982|    73.75|
| Americas  |  1987|    74.63|
| Americas  |  1992|    73.91|
| Americas  |  1997|    74.92|
| Americas  |  2002|    77.78|
| Americas  |  2007|    78.75|
| Africa    |  1952|    52.72|
| Africa    |  1957|    55.09|
| Africa    |  1962|    57.67|
| Africa    |  1967|    60.54|
| Africa    |  1972|    64.27|
| Africa    |  1977|    67.06|
| Africa    |  1982|    69.89|
| Africa    |  1987|    71.91|
| Africa    |  1992|    73.61|
| Africa    |  1997|    74.77|
| Africa    |  2002|    75.74|
| Africa    |  2007|    76.44|
| Europe    |  1952|    61.05|
| Europe    |  1957|    64.10|
| Europe    |  1962|    66.80|
| Europe    |  1967|    66.80|
| Europe    |  1972|    69.21|
| Europe    |  1977|    69.46|
| Europe    |  1982|    69.66|
| Europe    |  1987|    69.53|
| Europe    |  1992|    69.36|
| Europe    |  1997|    69.72|
| Europe    |  2002|    71.32|
| Europe    |  2007|    72.48|
| Africa    |  1952|    40.00|
| Africa    |  1957|    41.50|
| Africa    |  1962|    43.00|
| Africa    |  1967|    44.10|
| Africa    |  1972|    44.60|
| Africa    |  1977|    45.00|
| Africa    |  1982|    46.22|
| Africa    |  1987|    44.02|
| Africa    |  1992|    23.60|
| Africa    |  1997|    36.09|
| Africa    |  2002|    43.41|
| Africa    |  2007|    46.24|
| Africa    |  1952|    46.47|
| Africa    |  1957|    48.95|
| Africa    |  1962|    51.89|
| Africa    |  1967|    54.42|
| Africa    |  1972|    56.48|
| Africa    |  1977|    58.55|
| Africa    |  1982|    60.35|
| Africa    |  1987|    61.73|
| Africa    |  1992|    62.74|
| Africa    |  1997|    63.31|
| Africa    |  2002|    64.34|
| Africa    |  2007|    65.53|
| Asia      |  1952|    39.88|
| Asia      |  1957|    42.87|
| Asia      |  1962|    45.91|
| Asia      |  1967|    49.90|
| Asia      |  1972|    53.89|
| Asia      |  1977|    58.69|
| Asia      |  1982|    63.01|
| Asia      |  1987|    66.30|
| Asia      |  1992|    68.77|
| Asia      |  1997|    70.53|
| Asia      |  2002|    71.63|
| Asia      |  2007|    72.78|
| Africa    |  1952|    37.28|
| Africa    |  1957|    39.33|
| Africa    |  1962|    41.45|
| Africa    |  1967|    43.56|
| Africa    |  1972|    45.81|
| Africa    |  1977|    48.88|
| Africa    |  1982|    52.38|
| Africa    |  1987|    55.77|
| Africa    |  1992|    58.20|
| Africa    |  1997|    60.19|
| Africa    |  2002|    61.60|
| Africa    |  2007|    63.06|
| Europe    |  1952|    58.00|
| Europe    |  1957|    61.69|
| Europe    |  1962|    64.53|
| Europe    |  1967|    66.91|
| Europe    |  1972|    68.70|
| Europe    |  1977|    70.30|
| Europe    |  1982|    70.16|
| Europe    |  1987|    71.22|
| Europe    |  1992|    71.66|
| Europe    |  1997|    72.23|
| Europe    |  2002|    73.21|
| Europe    |  2007|    74.00|
| Africa    |  1952|    30.33|
| Africa    |  1957|    31.57|
| Africa    |  1962|    32.77|
| Africa    |  1967|    34.11|
| Africa    |  1972|    35.40|
| Africa    |  1977|    36.79|
| Africa    |  1982|    38.45|
| Africa    |  1987|    40.01|
| Africa    |  1992|    38.33|
| Africa    |  1997|    39.90|
| Africa    |  2002|    41.01|
| Africa    |  2007|    42.57|
| Asia      |  1952|    60.40|
| Asia      |  1957|    63.18|
| Asia      |  1962|    65.80|
| Asia      |  1967|    67.95|
| Asia      |  1972|    69.52|
| Asia      |  1977|    70.80|
| Asia      |  1982|    71.76|
| Asia      |  1987|    73.56|
| Asia      |  1992|    75.79|
| Asia      |  1997|    77.16|
| Asia      |  2002|    78.77|
| Asia      |  2007|    79.97|
| Europe    |  1952|    64.36|
| Europe    |  1957|    67.45|
| Europe    |  1962|    70.33|
| Europe    |  1967|    70.98|
| Europe    |  1972|    70.35|
| Europe    |  1977|    70.45|
| Europe    |  1982|    70.80|
| Europe    |  1987|    71.08|
| Europe    |  1992|    71.38|
| Europe    |  1997|    72.71|
| Europe    |  2002|    73.80|
| Europe    |  2007|    74.66|
| Europe    |  1952|    65.57|
| Europe    |  1957|    67.85|
| Europe    |  1962|    69.15|
| Europe    |  1967|    69.18|
| Europe    |  1972|    69.82|
| Europe    |  1977|    70.97|
| Europe    |  1982|    71.06|
| Europe    |  1987|    72.25|
| Europe    |  1992|    73.64|
| Europe    |  1997|    75.13|
| Europe    |  2002|    76.66|
| Europe    |  2007|    77.93|
| Africa    |  1952|    32.98|
| Africa    |  1957|    34.98|
| Africa    |  1962|    36.98|
| Africa    |  1967|    38.98|
| Africa    |  1972|    40.97|
| Africa    |  1977|    41.97|
| Africa    |  1982|    42.95|
| Africa    |  1987|    44.50|
| Africa    |  1992|    39.66|
| Africa    |  1997|    43.80|
| Africa    |  2002|    45.94|
| Africa    |  2007|    48.16|
| Africa    |  1952|    45.01|
| Africa    |  1957|    47.98|
| Africa    |  1962|    49.95|
| Africa    |  1967|    51.93|
| Africa    |  1972|    53.70|
| Africa    |  1977|    55.53|
| Africa    |  1982|    58.16|
| Africa    |  1987|    60.83|
| Africa    |  1992|    61.89|
| Africa    |  1997|    60.24|
| Africa    |  2002|    53.37|
| Africa    |  2007|    49.34|
| Europe    |  1952|    64.94|
| Europe    |  1957|    66.66|
| Europe    |  1962|    69.69|
| Europe    |  1967|    71.44|
| Europe    |  1972|    73.06|
| Europe    |  1977|    74.39|
| Europe    |  1982|    76.30|
| Europe    |  1987|    76.90|
| Europe    |  1992|    77.57|
| Europe    |  1997|    78.77|
| Europe    |  2002|    79.78|
| Europe    |  2007|    80.94|
| Asia      |  1952|    57.59|
| Asia      |  1957|    61.46|
| Asia      |  1962|    62.19|
| Asia      |  1967|    64.27|
| Asia      |  1972|    65.04|
| Asia      |  1977|    65.95|
| Asia      |  1982|    68.76|
| Asia      |  1987|    69.01|
| Asia      |  1992|    70.38|
| Asia      |  1997|    70.46|
| Asia      |  2002|    70.81|
| Asia      |  2007|    72.40|
| Africa    |  1952|    38.63|
| Africa    |  1957|    39.62|
| Africa    |  1962|    40.87|
| Africa    |  1967|    42.86|
| Africa    |  1972|    45.08|
| Africa    |  1977|    47.80|
| Africa    |  1982|    50.34|
| Africa    |  1987|    51.74|
| Africa    |  1992|    53.56|
| Africa    |  1997|    55.37|
| Africa    |  2002|    56.37|
| Africa    |  2007|    58.56|
| Africa    |  1952|    41.41|
| Africa    |  1957|    43.42|
| Africa    |  1962|    44.99|
| Africa    |  1967|    46.63|
| Africa    |  1972|    49.55|
| Africa    |  1977|    52.54|
| Africa    |  1982|    55.56|
| Africa    |  1987|    57.68|
| Africa    |  1992|    58.47|
| Africa    |  1997|    54.29|
| Africa    |  2002|    43.87|
| Africa    |  2007|    39.61|
| Europe    |  1952|    71.86|
| Europe    |  1957|    72.49|
| Europe    |  1962|    73.37|
| Europe    |  1967|    74.16|
| Europe    |  1972|    74.72|
| Europe    |  1977|    75.44|
| Europe    |  1982|    76.42|
| Europe    |  1987|    77.19|
| Europe    |  1992|    78.16|
| Europe    |  1997|    79.39|
| Europe    |  2002|    80.04|
| Europe    |  2007|    80.88|
| Europe    |  1952|    69.62|
| Europe    |  1957|    70.56|
| Europe    |  1962|    71.32|
| Europe    |  1967|    72.77|
| Europe    |  1972|    73.78|
| Europe    |  1977|    75.39|
| Europe    |  1982|    76.21|
| Europe    |  1987|    77.41|
| Europe    |  1992|    78.03|
| Europe    |  1997|    79.37|
| Europe    |  2002|    80.62|
| Europe    |  2007|    81.70|
| Asia      |  1952|    45.88|
| Asia      |  1957|    48.28|
| Asia      |  1962|    50.30|
| Asia      |  1967|    53.66|
| Asia      |  1972|    57.30|
| Asia      |  1977|    61.20|
| Asia      |  1982|    64.59|
| Asia      |  1987|    66.97|
| Asia      |  1992|    69.25|
| Asia      |  1997|    71.53|
| Asia      |  2002|    73.05|
| Asia      |  2007|    74.14|
| Asia      |  1952|    58.50|
| Asia      |  1957|    62.40|
| Asia      |  1962|    65.20|
| Asia      |  1967|    67.50|
| Asia      |  1972|    69.39|
| Asia      |  1977|    70.59|
| Asia      |  1982|    72.16|
| Asia      |  1987|    73.40|
| Asia      |  1992|    74.26|
| Asia      |  1997|    75.25|
| Asia      |  2002|    76.99|
| Asia      |  2007|    78.40|
| Africa    |  1952|    41.22|
| Africa    |  1957|    42.97|
| Africa    |  1962|    44.25|
| Africa    |  1967|    45.76|
| Africa    |  1972|    47.62|
| Africa    |  1977|    49.92|
| Africa    |  1982|    50.61|
| Africa    |  1987|    51.53|
| Africa    |  1992|    50.44|
| Africa    |  1997|    48.47|
| Africa    |  2002|    49.65|
| Africa    |  2007|    52.52|
| Asia      |  1952|    50.85|
| Asia      |  1957|    53.63|
| Asia      |  1962|    56.06|
| Asia      |  1967|    58.28|
| Asia      |  1972|    60.41|
| Asia      |  1977|    62.49|
| Asia      |  1982|    64.60|
| Asia      |  1987|    66.08|
| Asia      |  1992|    67.30|
| Asia      |  1997|    67.52|
| Asia      |  2002|    68.56|
| Asia      |  2007|    70.62|
| Africa    |  1952|    38.60|
| Africa    |  1957|    41.21|
| Africa    |  1962|    43.92|
| Africa    |  1967|    46.77|
| Africa    |  1972|    49.76|
| Africa    |  1977|    52.89|
| Africa    |  1982|    55.47|
| Africa    |  1987|    56.94|
| Africa    |  1992|    58.06|
| Africa    |  1997|    58.39|
| Africa    |  2002|    57.56|
| Africa    |  2007|    58.42|
| Americas  |  1952|    59.10|
| Americas  |  1957|    61.80|
| Americas  |  1962|    64.90|
| Americas  |  1967|    65.40|
| Americas  |  1972|    65.90|
| Americas  |  1977|    68.30|
| Americas  |  1982|    68.83|
| Americas  |  1987|    69.58|
| Americas  |  1992|    69.86|
| Americas  |  1997|    69.47|
| Americas  |  2002|    68.98|
| Americas  |  2007|    69.82|
| Africa    |  1952|    44.60|
| Africa    |  1957|    47.10|
| Africa    |  1962|    49.58|
| Africa    |  1967|    52.05|
| Africa    |  1972|    55.60|
| Africa    |  1977|    59.84|
| Africa    |  1982|    64.05|
| Africa    |  1987|    66.89|
| Africa    |  1992|    70.00|
| Africa    |  1997|    71.97|
| Africa    |  2002|    73.04|
| Africa    |  2007|    73.92|
| Europe    |  1952|    43.59|
| Europe    |  1957|    48.08|
| Europe    |  1962|    52.10|
| Europe    |  1967|    54.34|
| Europe    |  1972|    57.01|
| Europe    |  1977|    59.51|
| Europe    |  1982|    61.04|
| Europe    |  1987|    63.11|
| Europe    |  1992|    66.15|
| Europe    |  1997|    68.83|
| Europe    |  2002|    70.84|
| Europe    |  2007|    71.78|
| Africa    |  1952|    39.98|
| Africa    |  1957|    42.57|
| Africa    |  1962|    45.34|
| Africa    |  1967|    48.05|
| Africa    |  1972|    51.02|
| Africa    |  1977|    50.35|
| Africa    |  1982|    49.85|
| Africa    |  1987|    51.51|
| Africa    |  1992|    48.83|
| Africa    |  1997|    44.58|
| Africa    |  2002|    47.81|
| Africa    |  2007|    51.54|
| Europe    |  1952|    69.18|
| Europe    |  1957|    70.42|
| Europe    |  1962|    70.76|
| Europe    |  1967|    71.36|
| Europe    |  1972|    72.01|
| Europe    |  1977|    72.76|
| Europe    |  1982|    74.04|
| Europe    |  1987|    75.01|
| Europe    |  1992|    76.42|
| Europe    |  1997|    77.22|
| Europe    |  2002|    78.47|
| Europe    |  2007|    79.42|
| Americas  |  1952|    68.44|
| Americas  |  1957|    69.49|
| Americas  |  1962|    70.21|
| Americas  |  1967|    70.76|
| Americas  |  1972|    71.34|
| Americas  |  1977|    73.38|
| Americas  |  1982|    74.65|
| Americas  |  1987|    75.02|
| Americas  |  1992|    76.09|
| Americas  |  1997|    76.81|
| Americas  |  2002|    77.31|
| Americas  |  2007|    78.24|
| Americas  |  1952|    66.07|
| Americas  |  1957|    67.04|
| Americas  |  1962|    68.25|
| Americas  |  1967|    68.47|
| Americas  |  1972|    68.67|
| Americas  |  1977|    69.48|
| Americas  |  1982|    70.81|
| Americas  |  1987|    71.92|
| Americas  |  1992|    72.75|
| Americas  |  1997|    74.22|
| Americas  |  2002|    75.31|
| Americas  |  2007|    76.38|
| Americas  |  1952|    55.09|
| Americas  |  1957|    57.91|
| Americas  |  1962|    60.77|
| Americas  |  1967|    63.48|
| Americas  |  1972|    65.71|
| Americas  |  1977|    67.46|
| Americas  |  1982|    68.56|
| Americas  |  1987|    70.19|
| Americas  |  1992|    71.15|
| Americas  |  1997|    72.15|
| Americas  |  2002|    72.77|
| Americas  |  2007|    73.75|
| Asia      |  1952|    40.41|
| Asia      |  1957|    42.89|
| Asia      |  1962|    45.36|
| Asia      |  1967|    47.84|
| Asia      |  1972|    50.25|
| Asia      |  1977|    55.76|
| Asia      |  1982|    58.82|
| Asia      |  1987|    62.82|
| Asia      |  1992|    67.66|
| Asia      |  1997|    70.67|
| Asia      |  2002|    73.02|
| Asia      |  2007|    74.25|
| Asia      |  1952|    43.16|
| Asia      |  1957|    45.67|
| Asia      |  1962|    48.13|
| Asia      |  1967|    51.63|
| Asia      |  1972|    56.53|
| Asia      |  1977|    60.77|
| Asia      |  1982|    64.41|
| Asia      |  1987|    67.05|
| Asia      |  1992|    69.72|
| Asia      |  1997|    71.10|
| Asia      |  2002|    72.37|
| Asia      |  2007|    73.42|
| Asia      |  1952|    32.55|
| Asia      |  1957|    33.97|
| Asia      |  1962|    35.18|
| Asia      |  1967|    36.98|
| Asia      |  1972|    39.85|
| Asia      |  1977|    44.17|
| Asia      |  1982|    49.11|
| Asia      |  1987|    52.92|
| Asia      |  1992|    55.60|
| Asia      |  1997|    58.02|
| Asia      |  2002|    60.31|
| Asia      |  2007|    62.70|
| Africa    |  1952|    42.04|
| Africa    |  1957|    44.08|
| Africa    |  1962|    46.02|
| Africa    |  1967|    47.77|
| Africa    |  1972|    50.11|
| Africa    |  1977|    51.39|
| Africa    |  1982|    51.82|
| Africa    |  1987|    50.82|
| Africa    |  1992|    46.10|
| Africa    |  1997|    40.24|
| Africa    |  2002|    39.19|
| Africa    |  2007|    42.38|
| Africa    |  1952|    48.45|
| Africa    |  1957|    50.47|
| Africa    |  1962|    52.36|
| Africa    |  1967|    53.99|
| Africa    |  1972|    55.63|
| Africa    |  1977|    57.67|
| Africa    |  1982|    60.36|
| Africa    |  1987|    62.35|
| Africa    |  1992|    60.38|
| Africa    |  1997|    46.81|
| Africa    |  2002|    39.99|
| Africa    |  2007|    43.49|

The results are here, however it is very hard to visualize this data, especially if we're looking at longer time spans. For this information to be meaningful, we need to visualize it using ggplot:

#### Visualization

``` r
ggplot(task3, aes(year, lifeExp, colour=continent)) + geom_smooth(method="loess", se=FALSE) + 
  scale_colour_manual(values=c("darkslateblue", "olivedrab", "tan3", "orangered3", "maroon4")) +
  labs(x = "Year", y = "Life Expectancy", title = "Life Expectancy Over Time for the Continents")
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-11-1.png)

This is much more informative. We can see how life expectancy is changing over time for each continent, which is coloured inidividually. It appears tha Africa's life expectancy was increasing from 1950 to around 1986-7, after which it started to plateau. Asia seemed to have had the steepest rise in life expectancy, though it slowed down towards 1990. The Americas and Europe both have shown increases in life expectancy, though at difference rates, but the trend is upward. Oceania as well shows an upward trend, despite going through a slow patch between 1960-1980.

It might be interesting to see the individual points composing these lines, and to be able to identify any outliers. For this we should use facetting:

``` r
plot3 <- ggplot(task3, aes(year, lifeExp, colour = continent)) +
    facet_wrap(~ continent) +
    geom_point(alpha=0.4) + 
  geom_smooth(method='loess', colour = "black", se=FALSE, span = 5, linetype = "dashed") +
  scale_colour_manual(values=c("darkslateblue", "olivedrab", "tan3", "orangered3", "maroon4")) +
  labs(x = "Year", y = "Life Expectancy", title = "Life Expectancy of Continents over Time")
plot3 + theme_bw() +
    theme(strip.background = element_rect(fill="grey"), 
          strip.text = element_text(size=12, face="bold"), 
          plot.title = element_text(size=14,face="bold"),
          axis.title = element_text(size=12, face="bold"))
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-12-1.png)

This I found was more informative than the first graph for this task, as it shows the concentration of data points that are influencing the overall trend line. I used `se=FALSE` as I wasn't concerned with confidence intervals at this point. I realised I could colour each continent separately by adding the 'colour = continent' argument in the first line!

I adjusted the figure heights and widths and the alpha values of the data points to allow me to see any interesting outliers that might exist. I also experimented with multiple `span` values to see which one seemed to fit the data best.

A couple interesting ones was an outlier in Asia in the late 1970's, an outlier in Africa in the early 1990's. From my limited history knowledge, I would hazard a guess that the Asian outlier corresponds to the horrific Cambodian civil war and the African one is likely Rwanda as a result of the Rwandan genocide. I will try to see if I'm correct!

**Late 1970's Asian outlier**

``` r
gapminder %>%
  filter(continent == "Asia", year > 1970 & year < 1980) %>%
  filter(lifeExp == min(lifeExp))
```

    ## # A tibble: 1 x 6
    ##    country continent  year lifeExp     pop gdpPercap
    ##     <fctr>    <fctr> <int>   <dbl>   <int>     <dbl>
    ## 1 Cambodia      Asia  1977   31.22 6978607  524.9722

Yes, this outlier correponds to Cambodia in 1977 specifically. After a bit of reading this makes sense as the Cambodian Civil War ended in 1975 and was followed by the [Khmer Rouge regime](http://www.cnn.com/2010/WORLD/asiapcf/07/25/cambodia.khmer.rouge.timeline/index.html) and a period knowns as the 'Cambodian genocide', a 4-year period where nearly 2 million people died of forced labour, disease, political executions and starvation. This corresponded to a loss of almost a quarter of Cambodia's population.

**Early 1990's African outlier**

``` r
gapminder %>%
  filter(continent == "Africa", year > 1988 & year < 2000) %>%
  filter(lifeExp == min(lifeExp))
```

    ## # A tibble: 1 x 6
    ##   country continent  year lifeExp     pop gdpPercap
    ##    <fctr>    <fctr> <int>   <dbl>   <int>     <dbl>
    ## 1  Rwanda    Africa  1992  23.599 7290203  737.0686

Yes, this outlier does correspond to Rwanda, shortly after the RPF invasion sparks a civil war. Over 800000 Tutsi and Hutu sympathizers were killed during this period, the ['Rwandan Genocide'](http://www.history.com/topics/rwandan-genocide). From the brief reading I have done, it seems like most of these killings occured a bit later than this outlier - around 1994-1996, however there were tensions and clashes in the years leading up to the genocide. The Gapminder dataset doesn't have information for years between 1992-2000, so this result makes sense.

Task 4: Investigating out of interest: Impact of HIV/AIDS epidemic in Sub-Saharan Africa vs Northern Africa
-----------------------------------------------------------------------------------------------------------

I'm interested in comparing the GDP per capita and life expectancies of two regions of Africa, differently affected by HIV/AIDS. Sub-Saharan Africa is now the worst-affected region in the world, and I will look specifically at Swaziland, Lesotho, Botswana, South Africa and Zimbabwe. North Africa, in contrast, has very low HIV/AIDS rates - I will look specifically at Algeria, Tunisia, Egypt, Morocco and Sudan.

What I hope to do is graph the Northern African countries separately to the Sub-Saharan African countries so we can evaluate general trends. I will need to use `facetting` for this.

``` r
task4a <- gapminder %>% 
    select(country, year, lifeExp) %>% 
  filter(country %in% c("", "Swaziland", "Lesotho", "Botswana", "South Africa", "Zimbabwe"), year <=2015, year >= 1960)
task4a
```

    ## # A tibble: 50 x 3
    ##     country  year lifeExp
    ##      <fctr> <int>   <dbl>
    ##  1 Botswana  1962  51.520
    ##  2 Botswana  1967  53.298
    ##  3 Botswana  1972  56.024
    ##  4 Botswana  1977  59.319
    ##  5 Botswana  1982  61.484
    ##  6 Botswana  1987  63.622
    ##  7 Botswana  1992  62.745
    ##  8 Botswana  1997  52.556
    ##  9 Botswana  2002  46.634
    ## 10 Botswana  2007  50.728
    ## # ... with 40 more rows

``` r
knitr::kable(task4a, digits=2)
```

| country      |  year|  lifeExp|
|:-------------|-----:|--------:|
| Botswana     |  1962|    51.52|
| Botswana     |  1967|    53.30|
| Botswana     |  1972|    56.02|
| Botswana     |  1977|    59.32|
| Botswana     |  1982|    61.48|
| Botswana     |  1987|    63.62|
| Botswana     |  1992|    62.74|
| Botswana     |  1997|    52.56|
| Botswana     |  2002|    46.63|
| Botswana     |  2007|    50.73|
| Lesotho      |  1962|    47.75|
| Lesotho      |  1967|    48.49|
| Lesotho      |  1972|    49.77|
| Lesotho      |  1977|    52.21|
| Lesotho      |  1982|    55.08|
| Lesotho      |  1987|    57.18|
| Lesotho      |  1992|    59.69|
| Lesotho      |  1997|    55.56|
| Lesotho      |  2002|    44.59|
| Lesotho      |  2007|    42.59|
| South Africa |  1962|    49.95|
| South Africa |  1967|    51.93|
| South Africa |  1972|    53.70|
| South Africa |  1977|    55.53|
| South Africa |  1982|    58.16|
| South Africa |  1987|    60.83|
| South Africa |  1992|    61.89|
| South Africa |  1997|    60.24|
| South Africa |  2002|    53.37|
| South Africa |  2007|    49.34|
| Swaziland    |  1962|    44.99|
| Swaziland    |  1967|    46.63|
| Swaziland    |  1972|    49.55|
| Swaziland    |  1977|    52.54|
| Swaziland    |  1982|    55.56|
| Swaziland    |  1987|    57.68|
| Swaziland    |  1992|    58.47|
| Swaziland    |  1997|    54.29|
| Swaziland    |  2002|    43.87|
| Swaziland    |  2007|    39.61|
| Zimbabwe     |  1962|    52.36|
| Zimbabwe     |  1967|    53.99|
| Zimbabwe     |  1972|    55.63|
| Zimbabwe     |  1977|    57.67|
| Zimbabwe     |  1982|    60.36|
| Zimbabwe     |  1987|    62.35|
| Zimbabwe     |  1992|    60.38|
| Zimbabwe     |  1997|    46.81|
| Zimbabwe     |  2002|    39.99|
| Zimbabwe     |  2007|    43.49|

This table is not very informative on its own, and needs to be visualized using ggplot. Next I used ggplot and facet wrapping to visualize each of these countries and their individual life expectancy trends over time:

#### Visualization

``` r
plot4a <- ggplot(task4a, aes(year, lifeExp, colour=country)) +
    facet_wrap(~ country) +
    geom_point() + 
  geom_line() +
  scale_colour_manual(values=c("darkslateblue", "dodgerblue4", "cyan4", "olivedrab", "seagreen3")) +
  labs(x = "Year", y = "Life Expectancy", title = "Life Expectancy of Sub-Saharan Africa Worst Affected by HIV/AIDS")
plot4a + theme_bw() +
    theme(strip.background = element_rect(fill="palegoldenrod"))
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-16-1.png)

This is very interesting - for each country worst affected by HIV/AIDS we can see a sharp decline in life expectancies all occuring between 1985-1990. \*\*MOre info about epidemic with links). Something promising is the upwards trend seen in Botswana and Zimbabwe.

Now to look at Northern Africa:

``` r
task4b <- gapminder %>% 
    select(country, year, lifeExp) %>% 
  filter(country %in% c("", "Algeria", "Tunisia", "Egypt", "Morocco", "Sudan"), year <=2015, year >= 1960)
task4b
```

    ## # A tibble: 50 x 3
    ##    country  year lifeExp
    ##     <fctr> <int>   <dbl>
    ##  1 Algeria  1962  48.303
    ##  2 Algeria  1967  51.407
    ##  3 Algeria  1972  54.518
    ##  4 Algeria  1977  58.014
    ##  5 Algeria  1982  61.368
    ##  6 Algeria  1987  65.799
    ##  7 Algeria  1992  67.744
    ##  8 Algeria  1997  69.152
    ##  9 Algeria  2002  70.994
    ## 10 Algeria  2007  72.301
    ## # ... with 40 more rows

``` r
knitr::kable(task4b, digits=2)
```

| country |  year|  lifeExp|
|:--------|-----:|--------:|
| Algeria |  1962|    48.30|
| Algeria |  1967|    51.41|
| Algeria |  1972|    54.52|
| Algeria |  1977|    58.01|
| Algeria |  1982|    61.37|
| Algeria |  1987|    65.80|
| Algeria |  1992|    67.74|
| Algeria |  1997|    69.15|
| Algeria |  2002|    70.99|
| Algeria |  2007|    72.30|
| Egypt   |  1962|    46.99|
| Egypt   |  1967|    49.29|
| Egypt   |  1972|    51.14|
| Egypt   |  1977|    53.32|
| Egypt   |  1982|    56.01|
| Egypt   |  1987|    59.80|
| Egypt   |  1992|    63.67|
| Egypt   |  1997|    67.22|
| Egypt   |  2002|    69.81|
| Egypt   |  2007|    71.34|
| Morocco |  1962|    47.92|
| Morocco |  1967|    50.34|
| Morocco |  1972|    52.86|
| Morocco |  1977|    55.73|
| Morocco |  1982|    59.65|
| Morocco |  1987|    62.68|
| Morocco |  1992|    65.39|
| Morocco |  1997|    67.66|
| Morocco |  2002|    69.61|
| Morocco |  2007|    71.16|
| Sudan   |  1962|    40.87|
| Sudan   |  1967|    42.86|
| Sudan   |  1972|    45.08|
| Sudan   |  1977|    47.80|
| Sudan   |  1982|    50.34|
| Sudan   |  1987|    51.74|
| Sudan   |  1992|    53.56|
| Sudan   |  1997|    55.37|
| Sudan   |  2002|    56.37|
| Sudan   |  2007|    58.56|
| Tunisia |  1962|    49.58|
| Tunisia |  1967|    52.05|
| Tunisia |  1972|    55.60|
| Tunisia |  1977|    59.84|
| Tunisia |  1982|    64.05|
| Tunisia |  1987|    66.89|
| Tunisia |  1992|    70.00|
| Tunisia |  1997|    71.97|
| Tunisia |  2002|    73.04|
| Tunisia |  2007|    73.92|

Again, this table is not very informative on its own, and needs to be visualized using ggplot. I used basically the same code as earlier but with the dataset made for Northern African countries:

``` r
plot4b <- ggplot(task4b, aes(year, lifeExp, colour=country)) +
    facet_wrap(~ country) +
    geom_point() + 
  geom_line() +
  scale_colour_manual(values=c("orange2", "sienna2", "orangered2", "firebrick", "mediumvioletred")) +
  labs(x = "Year", y = "Life Expectancy", title = "Life Expectancy of Northern Africa Least Affected by HIV/AIDS")
plot4b + theme_bw() +
    theme(strip.background = element_rect(fill="honeydew2"))
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-18-1.png)

This is much better to visualize: we can see the huge contrast in life expectancies between Sub-Saharan Africa and Northern Africa (least affected by HIV/AIDS) during the same time period. Life expectancies for all countries included seem to be on an upwards trend.

Of course, this is just one variable and there are a huge number of other variables to factor in when comparing life expetancies between nations. This is just an interesting side study to see the potential affects HIV/AIDS had directly on life expectancies in a region worst-hit compared to a region relatively unaffected.

Task 5: Report the absolute and/or relative abundance of countries with low life expectancy over time by continent
------------------------------------------------------------------------------------------------------------------

For this, I first thought it made sense to compare these countries with the global mean life expectancy of the gapminder dataset.

``` r
summary(gapminder$lifeExp)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   23.60   48.20   60.71   59.47   70.85   82.60

We can see that the mean life expectancy of this dataset is 59.47. I will just use 60 for the sake of ease (and also because my mom just turned 60 and it's interesting to think about our own lives in different contexts). Now I will try to determine how many countries within each continent has an average life expectancy lower than the global average of 60.

``` r
task5 <- gapminder %>% 
  group_by(continent, year) %>% 
  filter(lifeExp <= 60) %>% 
  summarize(number = length(country))
task5
```

    ## # A tibble: 41 x 3
    ## # Groups:   continent [?]
    ##    continent  year number
    ##       <fctr> <int>  <int>
    ##  1    Africa  1952     52
    ##  2    Africa  1957     52
    ##  3    Africa  1962     51
    ##  4    Africa  1967     50
    ##  5    Africa  1972     50
    ##  6    Africa  1977     50
    ##  7    Africa  1982     44
    ##  8    Africa  1987     40
    ##  9    Africa  1992     39
    ## 10    Africa  1997     39
    ## # ... with 31 more rows

It took me a while to figure out I could either use `length` or `n_distinct` to determine the number of countries within each continent where the life expectancy was below 60 for each year. I think n\_distinct is a more logical thing to use, so I used it below:

``` r
task5b <- gapminder %>% 
  group_by(continent, year) %>% 
  filter(lifeExp <= 60) %>% 
  summarize(number = n_distinct(country))
task5b
```

    ## # A tibble: 41 x 3
    ## # Groups:   continent [?]
    ##    continent  year number
    ##       <fctr> <int>  <int>
    ##  1    Africa  1952     52
    ##  2    Africa  1957     52
    ##  3    Africa  1962     51
    ##  4    Africa  1967     50
    ##  5    Africa  1972     50
    ##  6    Africa  1977     50
    ##  7    Africa  1982     44
    ##  8    Africa  1987     40
    ##  9    Africa  1992     39
    ## 10    Africa  1997     39
    ## # ... with 31 more rows

This tells us for each year, how many countries in each continent have an average life expectancy below 60.

#### Visualization

``` r
ggplot(task5, aes(year, number, colour = continent)) + 
  facet_wrap(~ continent) + 
  geom_point() + 
  scale_colour_manual(values=c("darkslateblue", "olivedrab", "tan3", "orangered3", "maroon4")) +
  labs (x = "Year", y = "Number of Countries with Life Expectancy < 60", title = "Absolute Abundance of Countries with Low Life Expectancy over Time") + 
  theme(strip.background = element_rect(fill="rosybrown3"))
```

![](hw03_ggplot_dplyr_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-22-1.png)

What's most interesting is that Oceania was never in this range, meaning the life expectancy on average has always been greater than 60. We can see that in the Americas and Asia, life expectancy is rising (illustrated by the number of countries whose life expectancy is below 60 decreasing), maybe due to advanced in healthcare and medicine?

Reporting on my process
-----------------------

I found this homework fairly challenging, as I'm still grasping a handle on R and still need to refer back to class notes to remember the functions and order of code sometimes. Overall it was good to reinforce the concepts we've gone over in class and to understand them at a deeper level, as sometimes the classes move a bit too fast for me!

**Some issues I encountered:**

-   Limiting the Task 1 table to 2 decimal places, which would be appropriate for the audience. I figured out later that there is an argument in the 'kable' function that allows you to set digits, so I set it to 2 and was glad that the results are now displayed to 2 decimal places.

-   I'm not sure what the knitr::kable does to my tables, as I can't notice a difference. I will look into this further. I would like to get familiar with this and be able to customize my table outputs to make them more informative and aesthetic. I think the difference can be seen in the github output, just maybe not in the console?

-   Understanding the difference between colour= and fill=, both of which I used. For example, I tried using fill=continent for the visualization of Task 3, however the manual colours I chose using `scale_fill_manual` seemed to be overrided by something, as all continents came out blue. When I use colour=continent in the aes, R automatically distinguishes each continent by colour, so I then defined the colour by using `scale_colour_manual` followed by a vector of my colour choices. This seemed to work (I wanted my continent colours to be consistent throughout this entire exercise), but I need to more clearly understand the difference between colour= and fill= !

-   I'm sure there is a more efficient way to compare the minimum and maximum GDP per capita on the same plot than the method I used - hopefully that will become clear soon!

-   My facetted plots for Task 3 look okay, however I wanted the smooth line to be black but with some transparency. I found changing the alpha value didn't do anything here, and I would like to learn how to adjust the transparency of this.

-   Determining how many countries had life expectancy below 60 for Task 5 took me a long time...there is no reason for that other than I just wasn't thinking straight. I realised the number of countries under this category would correspond to the length of rows of output, and once I realized that it made sense.

**Some things I enjoyed:**

-   I enjoy the visualizations more than anything else, and though it takes a while I find it interesting to play with colours, scales, axis titles and different plotting styles. As a beginner myself I hope I ended up choosing appropriate styles that were both informative as well as visually easy to understand and pleasing.

-   I tried out a few trends I thought would be interesting to visualize (e.g. Effect of South Asian tsunami on GDP per capita for the worst-affected countries) however the dataset wasn't appropriate for such visualizations and didn't have enough recent data to be able to really deduce a trend. It was a good experience nonetheless, and I used much of the same code to do Task 4, so it wasn't a complete waste of time!

**Useful Links**

-   ggplot [quick reference for colour and fill](http://sape.inf.usi.ch/quick-reference/ggplot2/colour)

-   Customizing [boxplot axis labels](http://t-redactyl.io/blog/2016/04/creating-plots-in-r-using-ggplot2-part-10-boxplots.html) and [fill colour](http://www.r-graph-gallery.com/264-control-ggplot2-boxplot-colors/)

-   [Different line styles](http://www.sthda.com/english/wiki/ggplot2-line-types-how-to-change-line-types-of-a-graph-in-r-software)
