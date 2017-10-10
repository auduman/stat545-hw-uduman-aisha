hm04\_tidy\_data\_and\_joins
================
Aisha Uduman
October 7, 2017

``` r
suppressPackageStartupMessages(library(tidyverse))
suppressPackageStartupMessages(library(gapminder))
suppressPackageStartupMessages(library(knitr))
```

General Data Reshaping and Relationship to Aggregation
------------------------------------------------------

#### Activity \#2

Make a tibble with one row per year and columns for life expectancy for two or more countries. Use knitr::kable() to make this table look pretty in your rendered homework. Take advantage of this new data shape to scatterplot life expectancy for one country against that of another.

For this exercise, I just thought of the first country that came to mind for each continent (note that I considered North America and South America as separate continents, but in Gapminder I know they are classified as one). The countries chosen were Canada, Bolivia, Greece, Tanzania, Indonesia and New Zealand.

Out of the Gapminder dataset, I used `filter` to select data for only the countries chosen. Next I used `select` to only retain the columns relevant to this activity, which are country, year and life expectancy. I used the `head` argument so only the first 6 rows of this otherwise very long dataframe would be displayed:

``` r
lifeExp_1 <- gapminder %>% 
  filter(country %in% c("Canada", "Bolivia", "Greece", "Tanzania", "Indonesia", "New Zealand")) %>% 
  select(country, year, lifeExp) 
knitr::kable(head(lifeExp_1))
```

| country |  year|  lifeExp|
|:--------|-----:|--------:|
| Bolivia |  1952|   40.414|
| Bolivia |  1957|   41.890|
| Bolivia |  1962|   43.428|
| Bolivia |  1967|   45.032|
| Bolivia |  1972|   46.714|
| Bolivia |  1977|   50.023|

Now we have to convert this long dataframe into a wide dataframe. To reshape the data, I used `spread`, which converts a key-value pair across multiple columns (turns 'long' data into 'wide' data). This means the countries, which previously were in rows, will now be in columns with each country having an individual column. The new dataframe will automatically have one row per year as a result.

``` r
lifeExp_year <- lifeExp_1 %>% 
  spread(key = country, value = lifeExp)

knitr::kable(lifeExp_year, digits = 2, col.names = c("Year", "Canada Life Expectancy", "Bolivia Life Expectancy", "Greece Life Expectancy", "Tanzania Life Expectancy", "Indonesia Life Expectancy", "New Zealand Life Expectancy"))
```

|  Year|  Canada Life Expectancy|  Bolivia Life Expectancy|  Greece Life Expectancy|  Tanzania Life Expectancy|  Indonesia Life Expectancy|  New Zealand Life Expectancy|
|-----:|-----------------------:|------------------------:|-----------------------:|-------------------------:|--------------------------:|----------------------------:|
|  1952|                   40.41|                    68.75|                   65.86|                     37.47|                      69.39|                        41.22|
|  1957|                   41.89|                    69.96|                   67.86|                     39.92|                      70.26|                        42.97|
|  1962|                   43.43|                    71.30|                   69.51|                     42.52|                      71.24|                        44.25|
|  1967|                   45.03|                    72.13|                   71.00|                     45.96|                      71.52|                        45.76|
|  1972|                   46.71|                    72.88|                   72.34|                     49.20|                      71.89|                        47.62|
|  1977|                   50.02|                    74.21|                   73.68|                     52.70|                      72.22|                        49.92|
|  1982|                   53.86|                    75.76|                   75.24|                     56.16|                      73.84|                        50.61|
|  1987|                   57.25|                    76.86|                   76.67|                     60.14|                      74.32|                        51.53|
|  1992|                   59.96|                    77.95|                   77.03|                     62.68|                      76.33|                        50.44|
|  1997|                   62.05|                    78.61|                   77.87|                     66.04|                      77.55|                        48.47|
|  2002|                   63.88|                    79.77|                   78.26|                     68.59|                      79.11|                        49.65|
|  2007|                   65.55|                    80.65|                   79.48|                     70.65|                      80.20|                        52.52|

I wanted to change the column names to something more informative and found that the `col.names` argument can do this for me.

#### Scatterplot of life expectancy: Canada vs Indonesia

After forming the new dataset, I noticed right away the huge difference in life expectancies between Canada and Indonesia, especially in the 1950's-1970's. I thought this would be a good example to plot and visualize how life expectancies for the two countries changed over time. I wasn't sure how to do this initially using this new dataframe, as now each country had it's own row and I couldn't simply plot by year and life expectancy and then filter by country...

**Attempt \#1**

``` r
lifeExp_year %>% 
  select(year, Canada, Indonesia) %>% 
  ggplot(aes(x = year, y = Canada)) +
         geom_point()
```

![](hw04_tidy_data_and_joins_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-4-1.png)

This didn't work, it only plotted Canada and not Indonesia.

**Attempt \#2**

I added another geom\_point layer for Indonesia. This can't be the best way to do this, but it's what worked...I'd be interested in finding out if there's a simpler way to approach this problem.

``` r
ggplot(lifeExp_year, aes(year)) + 
  geom_point(aes(y = Canada, colour ="Canada")) + 
  geom_point(aes(y = Indonesia, colour="Indonesia")) + 
  scale_colour_discrete(name = "Country") +
  theme_bw() + 
  theme(axis.title = element_text(size=14, face="bold"),
        plot.title = element_text(size=15, face="bold", hjust=0.48),
        axis.title.x = element_text(size=13),
        axis.title.y = element_text(size=13)) + 
  labs(x = "Year", y = "Life Expectancy (years)", title = "Comparison of Canada and Indonesia Life Expectancies")
```

![](hw04_tidy_data_and_joins_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-5-1.png)

This plot took me a really long time - it is not like the simpler ggplots we have done before using life expectancy, as now we have to use the columns for the countries (Canada, Indonesia) and not just the general life expectancy column and select for Canada and Indonesia. Realistically it makes more sense to me to use the long form of the data like we have been using so far in this class, at least in ggplot. That being said, I tried to use the data from the dataframe I had just made, as I thought this was the point of the exercise. I finally was able to do so after much trial-and-error!

However, I think the graph shows the information pretty well: Canada has always had a relatively high life expectancy, and it has been slowly increasing with time. Indonesia, on the other hand, had a very low life expectancy of 37.5 years in 1952, but we have seen the life expectancy rise at a faster rate over time.

Join, merge, look up
--------------------

### Activity 1

Create a second data frame, complementary to Gapminder. Join this with (part of) Gapminder using a dplyr join function and make some observations about the process and result. Explore the different types of joins. Examples of a second data frame you could build:

One row per country, a country variable and one or more variables with extra info, such as language spoken, NATO membership, national animal, or capitol city.

I decided to take some random countries that came to my mind and look at the variables I'm personally interested in: national animal and national language(s).

``` r
country <- c("Bangladesh","Canada","Guatemala","India","Papua New Guinea","Romania","Russia","Qatar","Somalia")
nat_animal <- c("Bengal Tiger","Beaver","Quetzal","King Cobra","Dugong","Lynx","Eurasian Brown Bear","Arabian Oryx","Leopard")
nat_language <- c("Bengali", "English, French", "Spanish", "Hindi, English", "Hiri Motu, Tok Pisin, English", "Romanian", "Russian, Azerbaijani, Ukranian, Chuvash, Tatar, Tuvin", "Arabic", "Somali, Arabic") 
activity_1 <- data.frame(country, nat_animal, nat_language)
knitr::kable(activity_1)
```

| country          | nat\_animal         | nat\_language                                         |
|:-----------------|:--------------------|:------------------------------------------------------|
| Bangladesh       | Bengal Tiger        | Bengali                                               |
| Canada           | Beaver              | English, French                                       |
| Guatemala        | Quetzal             | Spanish                                               |
| India            | King Cobra          | Hindi, English                                        |
| Papua New Guinea | Dugong              | Hiri Motu, Tok Pisin, English                         |
| Romania          | Lynx                | Romanian                                              |
| Russia           | Eurasian Brown Bear | Russian, Azerbaijani, Ukranian, Chuvash, Tatar, Tuvin |
| Qatar            | Arabian Oryx        | Arabic                                                |
| Somalia          | Leopard             | Somali, Arabic                                        |

Then I made another dataframe from the original Gapminder dataset, filtering by the countries included in my first dataframe and summarizing by other variables mean GDP per capita and mean life expectancy.

``` r
gapminder_2 <- gapminder %>% 
  filter(country %in% c("Bangladesh", "Canada", "Guatemala", "India", "Papua New Guinea", "Romania", "Russia", "Qatar", "Somalia")) %>% 
  group_by(country) %>% 
  summarize(mean_gdpPercap = mean(gdpPercap), mean_lifeExp = mean(lifeExp))
knitr::kable(head(gapminder_2))
```

| country    |  mean\_gdpPercap|  mean\_lifeExp|
|:-----------|----------------:|--------------:|
| Bangladesh |         817.5588|       49.83408|
| Canada     |       22410.7463|       74.90275|
| Guatemala  |        4015.4028|       56.72942|
| India      |        1057.2963|       53.16608|
| Romania    |        7300.1700|       68.29067|
| Somalia    |        1140.7933|       40.98867|

#### Left join

The left join takes only the countries in the data from the first dataset (activity\_1) and uses the gapminder\_2 dataset to complete the information for the missing columns (e.g. mean GDP per capita, mean life expectancy). If Gapminder does not have information for a country that is within the first dataframe, then it will populate that cell with 'NA'.

``` r
leftjoin <- left_join(activity_1, gapminder_2, by="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

``` r
knitr::kable(leftjoin)
```

| country          | nat\_animal         | nat\_language                                         |  mean\_gdpPercap|  mean\_lifeExp|
|:-----------------|:--------------------|:------------------------------------------------------|----------------:|--------------:|
| Bangladesh       | Bengal Tiger        | Bengali                                               |         817.5588|       49.83408|
| Canada           | Beaver              | English, French                                       |       22410.7463|       74.90275|
| Guatemala        | Quetzal             | Spanish                                               |        4015.4028|       56.72942|
| India            | King Cobra          | Hindi, English                                        |        1057.2963|       53.16608|
| Papua New Guinea | Dugong              | Hiri Motu, Tok Pisin, English                         |               NA|             NA|
| Romania          | Lynx                | Romanian                                              |        7300.1700|       68.29067|
| Russia           | Eurasian Brown Bear | Russian, Azerbaijani, Ukranian, Chuvash, Tatar, Tuvin |               NA|             NA|
| Qatar            | Arabian Oryx        | Arabic                                                |               NA|             NA|
| Somalia          | Leopard             | Somali, Arabic                                        |        1140.7933|       40.98867|

We can see that the rows for Papua New Guinea, Russia and Qatar all have NA's, implying that Gapminder does not contain data for those countries, but they are kept as they exist in the first dataframe (activity\_1).

#### Inner join

Inner join takes everything that is in common for both datasets (countries I chose + the countries available in Gapminder) and removes what isn't common.

``` r
innerjoin <- inner_join(activity_1, gapminder_2, by="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

``` r
knitr::kable(innerjoin)
```

| country    | nat\_animal  | nat\_language   |  mean\_gdpPercap|  mean\_lifeExp|
|:-----------|:-------------|:----------------|----------------:|--------------:|
| Bangladesh | Bengal Tiger | Bengali         |         817.5588|       49.83408|
| Canada     | Beaver       | English, French |       22410.7463|       74.90275|
| Guatemala  | Quetzal      | Spanish         |        4015.4028|       56.72942|
| India      | King Cobra   | Hindi, English  |        1057.2963|       53.16608|
| Romania    | Lynx         | Romanian        |        7300.1700|       68.29067|
| Somalia    | Leopard      | Somali, Arabic  |        1140.7933|       40.98867|

The Gapminder dataset does not have data for the countries Papua New Guinea, Russia or Qatar. This is why there are 3 less rows than the `left_join` output (72 rows versus 75).

#### Semi join

This will return all rows from activity\_1 where there are matching values in the Gapminder dataset, and keeps just the columns from activity\_1 dataset.

``` r
semijoin <- semi_join(activity_1, gapminder_2, by="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

``` r
knitr::kable(semijoin)
```

| country    | nat\_animal  | nat\_language   |
|:-----------|:-------------|:----------------|
| Bangladesh | Bengal Tiger | Bengali         |
| Canada     | Beaver       | English, French |
| Guatemala  | Quetzal      | Spanish         |
| India      | King Cobra   | Hindi, English  |
| Romania    | Lynx         | Romanian        |
| Somalia    | Leopard      | Somali, Arabic  |

#### Anti join

This is the opposite of `semi_join`, and returns all rows from the gapminder dataset where there are NOT matching values in the activity\_1 dataset, and it keeps just the columns from activity\_1.

``` r
antijoin <- anti_join(activity_1, gapminder_2, by="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

``` r
knitr::kable(antijoin)
```

| country          | nat\_animal         | nat\_language                                         |
|:-----------------|:--------------------|:------------------------------------------------------|
| Papua New Guinea | Dugong              | Hiri Motu, Tok Pisin, English                         |
| Russia           | Eurasian Brown Bear | Russian, Azerbaijani, Ukranian, Chuvash, Tatar, Tuvin |
| Qatar            | Arabian Oryx        | Arabic                                                |

I guess this can be useful for when you have massive datasets and you need to know what values (in this case countries) are not common.

#### Full join

This will return all rows and all columns from both the activity\_1 dataset and the gapminder\_2 dataset.

``` r
fulljoin <- full_join(activity_1, gapminder_2, by="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

``` r
knitr::kable(fulljoin)
```

| country          | nat\_animal         | nat\_language                                         |  mean\_gdpPercap|  mean\_lifeExp|
|:-----------------|:--------------------|:------------------------------------------------------|----------------:|--------------:|
| Bangladesh       | Bengal Tiger        | Bengali                                               |         817.5588|       49.83408|
| Canada           | Beaver              | English, French                                       |       22410.7463|       74.90275|
| Guatemala        | Quetzal             | Spanish                                               |        4015.4028|       56.72942|
| India            | King Cobra          | Hindi, English                                        |        1057.2963|       53.16608|
| Papua New Guinea | Dugong              | Hiri Motu, Tok Pisin, English                         |               NA|             NA|
| Romania          | Lynx                | Romanian                                              |        7300.1700|       68.29067|
| Russia           | Eurasian Brown Bear | Russian, Azerbaijani, Ukranian, Chuvash, Tatar, Tuvin |               NA|             NA|
| Qatar            | Arabian Oryx        | Arabic                                                |               NA|             NA|
| Somalia          | Leopard             | Somali, Arabic                                        |        1140.7933|       40.98867|

We can see that the values for the three countries not included in the gapminder dataset have 'NA' values as they cannot be populated by this dataset. In this case, the output is the same as the `left_join` output, however if we were to use the entire Gapminder dataset instead of the smaller gapminder\_2 dataframe I made, the output would be much larger.

#### One row per continent, a continent variable and one or more variables with extra info, such as northern versus southern hemisphere.

I decided to use the variable population, and created a dataframe with one row per continent and their average population in the year 2007.

``` r
pop2007 <- gapminder %>% 
  filter(year==2007) %>% 
  group_by(continent) %>% 
  summarise(Average_Population=mean(pop))
knitr::kable(pop2007)
```

| continent |  Average\_Population|
|:----------|--------------------:|
| Africa    |             17875763|
| Americas  |             35954847|
| Asia      |            115513752|
| Europe    |             19536618|
| Oceania   |             12274974|

I then had to make a dataframe for continent variables (west or east of Atlantic Ocean) - this is kind of silly in my opinion but I couldn't think of many suitable continent-wide variables where continents weren't cut off in some places.

``` r
atlantic_ocean <- c("East", "West", "East", "West", "West")
continent <- c("Africa", "Americas", "Asia", "Europe", "Oceania")

ocean <- data.frame(continent, atlantic_ocean)
knitr::kable(ocean)
```

| continent | atlantic\_ocean |
|:----------|:----------------|
| Africa    | East            |
| Americas  | West            |
| Asia      | East            |
| Europe    | West            |
| Oceania   | West            |

Now we join this new dataframe with the continent variable with the gapminder dataset we made before summarizing continent by average population in the year of 2007:

``` r
innerjoin2 <- inner_join(pop2007, ocean, by="continent")
knitr::kable(innerjoin2, col.names = c("Continent", "Avg Population (2007)", "Position to Atlantic Ocean"))
```

| Continent |  Avg Population (2007)| Position to Atlantic Ocean |
|:----------|----------------------:|:---------------------------|
| Africa    |               17875763| East                       |
| Americas  |               35954847| West                       |
| Asia      |              115513752| East                       |
| Europe    |               19536618| West                       |
| Oceania   |               12274974| West                       |

Reporting my process
--------------------

Again, I struggled to use wide format data in ggplot, whereas I know the exact same graph could have been made using the long data in far less time (and also allow for more manipulation). I'm wondering what data might be better to plot in wide format...? I also wasn't sure if I was able to add multiple national languages, however after trying it out (by just using a comma to separate the languages within the ""), it worked. I can see this being a problem if I would need to filter by language though - i.e. I would need to filter by "English,""", "English, French"" or """, French""). I'm not sure if there's an easier way to do this, maybe add another column for other national languages? But there are some countries like South Africa that have 11 national languages - I'm interested in seeing if there is a way around this.

#### Useful Links

-   [Spread function](https://www.rdocumentation.org/packages/tidyr/versions/0.7.1/topics/spread) and how it handles [factor levels](https://stackoverflow.com/questions/26221752/how-spread-in-tidyr-handles-factor-levels)

-   [Tidy data](http://r4ds.had.co.nz/tidy-data.html)

-   [Data wrangling cheat sheet](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf) though I have to admit 95% of this stuff is still way over my head!
