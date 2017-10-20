
Factor Management
=================

The goals for this homework: - Define factor variables - Drop factor/levels - Reorder levels based on knowledge from data

### Gapminder version:

I decided to stick with the Gapminder dataset as I find it much easier to use intuitively (especially the column names) compared to the Singer database, and also because this part of the class is getting to be a bit over my head at times, so keeping it simple is key :ok\_hand:

**Drop Oceania**

I'm first filtering by continent, to exclude Oceania.

``` r
gap_noocean <- gapminder %>% 
  filter(continent != "Oceania")
```

Comparing this to the original Gapminder dataframe, which has 5 continents and 1704 rows, 24 rows being associated with Oceania. Checking to see if Oceania has been dropped: We can see that now Oceania has 0 values, but *still shows up in the summary for this new dataframe.*

``` r
summary(gapminder)
```

    ##         country        continent        year         lifeExp     
    ##  Afghanistan:  12   Africa  :624   Min.   :1952   Min.   :23.60  
    ##  Albania    :  12   Americas:300   1st Qu.:1966   1st Qu.:48.20  
    ##  Algeria    :  12   Asia    :396   Median :1980   Median :60.71  
    ##  Angola     :  12   Europe  :360   Mean   :1980   Mean   :59.47  
    ##  Argentina  :  12   Oceania : 24   3rd Qu.:1993   3rd Qu.:70.85  
    ##  Australia  :  12                  Max.   :2007   Max.   :82.60  
    ##  (Other)    :1632                                                
    ##       pop              gdpPercap       
    ##  Min.   :6.001e+04   Min.   :   241.2  
    ##  1st Qu.:2.794e+06   1st Qu.:  1202.1  
    ##  Median :7.024e+06   Median :  3531.8  
    ##  Mean   :2.960e+07   Mean   :  7215.3  
    ##  3rd Qu.:1.959e+07   3rd Qu.:  9325.5  
    ##  Max.   :1.319e+09   Max.   :113523.1  
    ## 

``` r
nrow(gapminder)
```

    ## [1] 1704

``` r
levels(gapminder$continent)
```

    ## [1] "Africa"   "Americas" "Asia"     "Europe"   "Oceania"

``` r
summary(gap_noocean)
```

    ##         country        continent        year         lifeExp     
    ##  Afghanistan:  12   Africa  :624   Min.   :1952   Min.   :23.60  
    ##  Albania    :  12   Americas:300   1st Qu.:1966   1st Qu.:48.08  
    ##  Algeria    :  12   Asia    :396   Median :1980   Median :60.34  
    ##  Angola     :  12   Europe  :360   Mean   :1980   Mean   :59.26  
    ##  Argentina  :  12   Oceania :  0   3rd Qu.:1993   3rd Qu.:70.75  
    ##  Austria    :  12                  Max.   :2007   Max.   :82.60  
    ##  (Other)    :1608                                                
    ##       pop              gdpPercap       
    ##  Min.   :6.001e+04   Min.   :   241.2  
    ##  1st Qu.:2.780e+06   1st Qu.:  1189.1  
    ##  Median :7.024e+06   Median :  3449.5  
    ##  Mean   :2.990e+07   Mean   :  7052.4  
    ##  3rd Qu.:1.987e+07   3rd Qu.:  8943.2  
    ##  Max.   :1.319e+09   Max.   :113523.1  
    ## 

We need to remove Oceania from this dataframe altogether. To do so, I used the `droplevels()` argument, to drop the unused levels:

``` r
gap_noocean_drop <- gap_noocean %>%
  droplevels()

nlevels(gap_noocean_drop$continent)
```

    ## [1] 4

``` r
summary(gap_noocean_drop)
```

    ##         country        continent        year         lifeExp     
    ##  Afghanistan:  12   Africa  :624   Min.   :1952   Min.   :23.60  
    ##  Albania    :  12   Americas:300   1st Qu.:1966   1st Qu.:48.08  
    ##  Algeria    :  12   Asia    :396   Median :1980   Median :60.34  
    ##  Angola     :  12   Europe  :360   Mean   :1980   Mean   :59.26  
    ##  Argentina  :  12                  3rd Qu.:1993   3rd Qu.:70.75  
    ##  Austria    :  12                  Max.   :2007   Max.   :82.60  
    ##  (Other)    :1608                                                
    ##       pop              gdpPercap       
    ##  Min.   :6.001e+04   Min.   :   241.2  
    ##  1st Qu.:2.780e+06   1st Qu.:  1189.1  
    ##  Median :7.024e+06   Median :  3449.5  
    ##  Mean   :2.990e+07   Mean   :  7052.4  
    ##  3rd Qu.:1.987e+07   3rd Qu.:  8943.2  
    ##  Max.   :1.319e+09   Max.   :113523.1  
    ## 

Now we can see that Oceania no longer exists within the continent variable, and the number of levels within this variable is 4 (as opposed to 5 in the original gapminder). Since Oceania was associated with 24 rows in the original Gapminder, we should now see this new dataframe having 24 less rows:

``` r
nrow(gap_noocean_drop)
```

    ## [1] 1680

1704 - 1680 = 24. Yay!

Just to confirm that only 4 continents are present:

``` r
levels(gap_noocean_drop$continent)
```

    ## [1] "Africa"   "Americas" "Asia"     "Europe"

Great, Oceania no longer exists in this new dataframe.

**Reorder the levels of `country` or `continent`**

Reordering countries based on ascending life expectancies:

``` r
fct_reorder(gap_noocean_drop$country, gap_noocean_drop$lifeExp) %>% 
  levels()
```

    ##   [1] "Sierra Leone"             "Guinea-Bissau"           
    ##   [3] "Afghanistan"              "Angola"                  
    ##   [5] "Somalia"                  "Guinea"                  
    ##   [7] "Niger"                    "Mozambique"              
    ##   [9] "Liberia"                  "Mali"                    
    ##  [11] "Equatorial Guinea"        "Gambia"                  
    ##  [13] "Rwanda"                   "Central African Republic"
    ##  [15] "Eritrea"                  "Malawi"                  
    ##  [17] "Ethiopia"                 "Burundi"                 
    ##  [19] "Nigeria"                  "Congo, Dem. Rep."        
    ##  [21] "Zambia"                   "Yemen, Rep."             
    ##  [23] "Burkina Faso"             "Djibouti"                
    ##  [25] "Madagascar"               "Swaziland"               
    ##  [27] "Cote d'Ivoire"            "Nepal"                   
    ##  [29] "Cambodia"                 "Uganda"                  
    ##  [31] "Chad"                     "Bangladesh"              
    ##  [33] "Tanzania"                 "Sudan"                   
    ##  [35] "Lesotho"                  "Cameroon"                
    ##  [37] "Benin"                    "Senegal"                 
    ##  [39] "Haiti"                    "Comoros"                 
    ##  [41] "Bolivia"                  "Mauritania"              
    ##  [43] "Ghana"                    "Botswana"                
    ##  [45] "Zimbabwe"                 "Namibia"                 
    ##  [47] "South Africa"             "Kenya"                   
    ##  [49] "Congo, Rep."              "Togo"                    
    ##  [51] "Indonesia"                "Egypt"                   
    ##  [53] "Gabon"                    "Pakistan"                
    ##  [55] "India"                    "Mongolia"                
    ##  [57] "Myanmar"                  "Guatemala"               
    ##  [59] "Vietnam"                  "El Salvador"             
    ##  [61] "Morocco"                  "Iraq"                    
    ##  [63] "Nicaragua"                "Iran"                    
    ##  [65] "Honduras"                 "Sao Tome and Principe"   
    ##  [67] "Algeria"                  "Libya"                   
    ##  [69] "Peru"                     "Oman"                    
    ##  [71] "Turkey"                   "Saudi Arabia"            
    ##  [73] "Philippines"              "Tunisia"                 
    ##  [75] "Brazil"                   "Jordan"                  
    ##  [77] "West Bank and Gaza"       "Dominican Republic"      
    ##  [79] "Ecuador"                  "Syria"                   
    ##  [81] "Thailand"                 "China"                   
    ##  [83] "Colombia"                 "Mauritius"               
    ##  [85] "Korea, Rep."              "Mexico"                  
    ##  [87] "Lebanon"                  "Paraguay"                
    ##  [89] "Malaysia"                 "Korea, Dem. Rep."        
    ##  [91] "Bahrain"                  "Sri Lanka"               
    ##  [93] "Venezuela"                "Reunion"                 
    ##  [95] "Trinidad and Tobago"      "Chile"                   
    ##  [97] "Argentina"                "Romania"                 
    ##  [99] "Hungary"                  "Panama"                  
    ## [101] "Albania"                  "Uruguay"                 
    ## [103] "Serbia"                   "Bosnia and Herzegovina"  
    ## [105] "Kuwait"                   "Croatia"                 
    ## [107] "Jamaica"                  "Czech Republic"          
    ## [109] "Bulgaria"                 "Slovak Republic"         
    ## [111] "Poland"                   "Slovenia"                
    ## [113] "Singapore"                "Taiwan"                  
    ## [115] "Portugal"                 "Costa Rica"              
    ## [117] "Ireland"                  "Austria"                 
    ## [119] "Germany"                  "Cuba"                    
    ## [121] "Belgium"                  "United Kingdom"          
    ## [123] "Montenegro"               "Finland"                 
    ## [125] "Puerto Rico"              "Israel"                  
    ## [127] "United States"            "Italy"                   
    ## [129] "France"                   "Greece"                  
    ## [131] "Hong Kong, China"         "Denmark"                 
    ## [133] "Canada"                   "Spain"                   
    ## [135] "Norway"                   "Netherlands"             
    ## [137] "Switzerland"              "Sweden"                  
    ## [139] "Japan"                    "Iceland"

Reordering countries based on descending minimum GDP per capita:

``` r
fct_reorder(gap_noocean_drop$country, gap_noocean_drop$gdpPercap, min, .desc = TRUE) %>% 
  levels()
```

    ##   [1] "Kuwait"                   "Switzerland"             
    ##   [3] "United States"            "Canada"                  
    ##   [5] "Norway"                   "United Kingdom"          
    ##   [7] "Bahrain"                  "Denmark"                 
    ##   [9] "Netherlands"              "Sweden"                  
    ##  [11] "Belgium"                  "Venezuela"               
    ##  [13] "Iceland"                  "Germany"                 
    ##  [15] "France"                   "Czech Republic"          
    ##  [17] "Saudi Arabia"             "Finland"                 
    ##  [19] "Austria"                  "Argentina"               
    ##  [21] "Uruguay"                  "Hungary"                 
    ##  [23] "Ireland"                  "Cuba"                    
    ##  [25] "Slovak Republic"          "Italy"                   
    ##  [27] "Lebanon"                  "South Africa"            
    ##  [29] "Gabon"                    "Slovenia"                
    ##  [31] "Israel"                   "Poland"                  
    ##  [33] "Chile"                    "Spain"                   
    ##  [35] "Peru"                     "Serbia"                  
    ##  [37] "Greece"                   "Ecuador"                 
    ##  [39] "Mexico"                   "Japan"                   
    ##  [41] "Romania"                  "Croatia"                 
    ##  [43] "Puerto Rico"              "Iraq"                    
    ##  [45] "Portugal"                 "Hong Kong, China"        
    ##  [47] "El Salvador"              "Iran"                    
    ##  [49] "Trinidad and Tobago"      "Jamaica"                 
    ##  [51] "Reunion"                  "Montenegro"              
    ##  [53] "Costa Rica"               "Panama"                  
    ##  [55] "Algeria"                  "Bulgaria"                
    ##  [57] "Guatemala"                "Namibia"                 
    ##  [59] "Libya"                    "Singapore"               
    ##  [61] "Angola"                   "Honduras"                
    ##  [63] "Nicaragua"                "Colombia"                
    ##  [65] "Bolivia"                  "Congo, Rep."             
    ##  [67] "Brazil"                   "Turkey"                  
    ##  [69] "Mauritius"                "Paraguay"                
    ##  [71] "Djibouti"                 "Oman"                    
    ##  [73] "Malaysia"                 "Syria"                   
    ##  [75] "Albania"                  "Morocco"                 
    ##  [77] "Jordan"                   "West Bank and Gaza"      
    ##  [79] "Sudan"                    "Egypt"                   
    ##  [81] "Dominican Republic"       "Tunisia"                 
    ##  [83] "Cote d'Ivoire"            "Senegal"                 
    ##  [85] "Philippines"              "Taiwan"                  
    ##  [87] "Haiti"                    "Cameroon"                
    ##  [89] "Swaziland"                "Korea, Dem. Rep."        
    ##  [91] "Sri Lanka"                "Zambia"                  
    ##  [93] "Korea, Rep."              "Nigeria"                 
    ##  [95] "Comoros"                  "Bosnia and Herzegovina"  
    ##  [97] "Benin"                    "Madagascar"              
    ##  [99] "Somalia"                  "Sao Tome and Principe"   
    ## [101] "Togo"                     "Kenya"                   
    ## [103] "Botswana"                 "Ghana"                   
    ## [105] "Chad"                     "Mongolia"                
    ## [107] "Yemen, Rep."              "Thailand"                
    ## [109] "Indonesia"                "Mauritania"              
    ## [111] "Central African Republic" "Tanzania"                
    ## [113] "Pakistan"                 "Afghanistan"             
    ## [115] "Bangladesh"               "Uganda"                  
    ## [117] "Vietnam"                  "Niger"                   
    ## [119] "Sierra Leone"             "India"                   
    ## [121] "Nepal"                    "Burkina Faso"            
    ## [123] "Guinea"                   "Rwanda"                  
    ## [125] "Gambia"                   "Mali"                    
    ## [127] "Liberia"                  "Zimbabwe"                
    ## [129] "China"                    "Mozambique"              
    ## [131] "Equatorial Guinea"        "Malawi"                  
    ## [133] "Cambodia"                 "Ethiopia"                
    ## [135] "Burundi"                  "Myanmar"                 
    ## [137] "Eritrea"                  "Guinea-Bissau"           
    ## [139] "Lesotho"                  "Congo, Dem. Rep."

Reordering continents based on descending variance in GDP per capita

``` r
fct_reorder(gap_noocean_drop$continent, gap_noocean_drop$gdpPercap, var, .desc = TRUE) %>% 
  levels()
```

    ## [1] "Asia"     "Europe"   "Americas" "Africa"

For a more focussed example, let's look at the variable life expectancy, focusing on the countries with the lowest life expectancies.

``` r
country_reorder_lifeExp <- fct_reorder(gap_noocean_drop$country, gap_noocean_drop$lifeExp, min, .desc = FALSE) %>% 
  levels() %>% 
  head()

head(country_reorder_lifeExp)
```

    ## [1] "Rwanda"       "Afghanistan"  "Gambia"       "Angola"      
    ## [5] "Sierra Leone" "Cambodia"

We can see that now that the reordering has been done, Rwanda, Afghanistan, Gambia, Angola, Sierra Leone and Cambodia have the lowest life expectancies in the gapminder dataframe.

Now I want to define the life expectancies as a dataframe with only the country, year and its corresponding life expectancy to see if it's changing over time: This will get rid of the population, continent, and GDP per capita factors.

``` r
min_lifeExp <- gap_noocean_drop %>% 
  filter(country %in% country_reorder_lifeExp) %>% 
  select(year, country, lifeExp)
```

We can plot this to visualize the differences and trends over time:

``` r
plot_minlifeExp <- ggplot(min_lifeExp, aes(year, lifeExp, colour=country)) + 
  facet_wrap(~country) + 
  geom_line() + 
  scale_color_brewer(palette="Accent") + 
  labs(x = "Year", y = "Life Expectancy", title = "Countries with Minimum Life Expectancies") + theme_bw()
plot_minlifeExp
```

![](hw05_fctr_fig_mgmt_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-11-1.png)

Cool, now we can see the output of our reordered factors selecting for minimum life expectancy.

Common Part
===========

Characterize the derived data before and after your factor re-leveling. Exploring effects of reordering a factor and factor reordering coupled with `arrange()`.

To see how our countries are organized (most likely alphebetically):

``` r
min_lifeExp %>% 
  group_by(country)
```

    ## # A tibble: 72 x 3
    ## # Groups:   country [6]
    ##     year     country lifeExp
    ##    <int>      <fctr>   <dbl>
    ##  1  1952 Afghanistan  28.801
    ##  2  1957 Afghanistan  30.332
    ##  3  1962 Afghanistan  31.997
    ##  4  1967 Afghanistan  34.020
    ##  5  1972 Afghanistan  36.088
    ##  6  1977 Afghanistan  38.438
    ##  7  1982 Afghanistan  39.854
    ##  8  1987 Afghanistan  40.822
    ##  9  1992 Afghanistan  41.674
    ## 10  1997 Afghanistan  41.763
    ## # ... with 62 more rows

``` r
kable(min_lifeExp, digits=2)
```

|  year| country      |  lifeExp|
|-----:|:-------------|--------:|
|  1952| Afghanistan  |    28.80|
|  1957| Afghanistan  |    30.33|
|  1962| Afghanistan  |    32.00|
|  1967| Afghanistan  |    34.02|
|  1972| Afghanistan  |    36.09|
|  1977| Afghanistan  |    38.44|
|  1982| Afghanistan  |    39.85|
|  1987| Afghanistan  |    40.82|
|  1992| Afghanistan  |    41.67|
|  1997| Afghanistan  |    41.76|
|  2002| Afghanistan  |    42.13|
|  2007| Afghanistan  |    43.83|
|  1952| Angola       |    30.02|
|  1957| Angola       |    32.00|
|  1962| Angola       |    34.00|
|  1967| Angola       |    35.98|
|  1972| Angola       |    37.93|
|  1977| Angola       |    39.48|
|  1982| Angola       |    39.94|
|  1987| Angola       |    39.91|
|  1992| Angola       |    40.65|
|  1997| Angola       |    40.96|
|  2002| Angola       |    41.00|
|  2007| Angola       |    42.73|
|  1952| Cambodia     |    39.42|
|  1957| Cambodia     |    41.37|
|  1962| Cambodia     |    43.41|
|  1967| Cambodia     |    45.41|
|  1972| Cambodia     |    40.32|
|  1977| Cambodia     |    31.22|
|  1982| Cambodia     |    50.96|
|  1987| Cambodia     |    53.91|
|  1992| Cambodia     |    55.80|
|  1997| Cambodia     |    56.53|
|  2002| Cambodia     |    56.75|
|  2007| Cambodia     |    59.72|
|  1952| Gambia       |    30.00|
|  1957| Gambia       |    32.06|
|  1962| Gambia       |    33.90|
|  1967| Gambia       |    35.86|
|  1972| Gambia       |    38.31|
|  1977| Gambia       |    41.84|
|  1982| Gambia       |    45.58|
|  1987| Gambia       |    49.27|
|  1992| Gambia       |    52.64|
|  1997| Gambia       |    55.86|
|  2002| Gambia       |    58.04|
|  2007| Gambia       |    59.45|
|  1952| Rwanda       |    40.00|
|  1957| Rwanda       |    41.50|
|  1962| Rwanda       |    43.00|
|  1967| Rwanda       |    44.10|
|  1972| Rwanda       |    44.60|
|  1977| Rwanda       |    45.00|
|  1982| Rwanda       |    46.22|
|  1987| Rwanda       |    44.02|
|  1992| Rwanda       |    23.60|
|  1997| Rwanda       |    36.09|
|  2002| Rwanda       |    43.41|
|  2007| Rwanda       |    46.24|
|  1952| Sierra Leone |    30.33|
|  1957| Sierra Leone |    31.57|
|  1962| Sierra Leone |    32.77|
|  1967| Sierra Leone |    34.11|
|  1972| Sierra Leone |    35.40|
|  1977| Sierra Leone |    36.79|
|  1982| Sierra Leone |    38.45|
|  1987| Sierra Leone |    40.01|
|  1992| Sierra Leone |    38.33|
|  1997| Sierra Leone |    39.90|
|  2002| Sierra Leone |    41.01|
|  2007| Sierra Leone |    42.57|

Yes, the countries are arranged alphabetically. I want them to be displayed in terms of minimum life expectancy, so the dataframe must be re-arranged to display life expectancy levels in ascending order. \*This is a note to myself, if I wanted to arrange by descending order I would use `arrange(desc(lifeExp))`, as `arrange()` automatically uses ascending order.

``` r
ascending_lifeExp <- min_lifeExp %>% 
  group_by(country) %>% 
  arrange(lifeExp)
kable(ascending_lifeExp, digits =2)
```

|  year| country      |  lifeExp|
|-----:|:-------------|--------:|
|  1992| Rwanda       |    23.60|
|  1952| Afghanistan  |    28.80|
|  1952| Gambia       |    30.00|
|  1952| Angola       |    30.02|
|  1952| Sierra Leone |    30.33|
|  1957| Afghanistan  |    30.33|
|  1977| Cambodia     |    31.22|
|  1957| Sierra Leone |    31.57|
|  1962| Afghanistan  |    32.00|
|  1957| Angola       |    32.00|
|  1957| Gambia       |    32.06|
|  1962| Sierra Leone |    32.77|
|  1962| Gambia       |    33.90|
|  1962| Angola       |    34.00|
|  1967| Afghanistan  |    34.02|
|  1967| Sierra Leone |    34.11|
|  1972| Sierra Leone |    35.40|
|  1967| Gambia       |    35.86|
|  1967| Angola       |    35.98|
|  1997| Rwanda       |    36.09|
|  1972| Afghanistan  |    36.09|
|  1977| Sierra Leone |    36.79|
|  1972| Angola       |    37.93|
|  1972| Gambia       |    38.31|
|  1992| Sierra Leone |    38.33|
|  1977| Afghanistan  |    38.44|
|  1982| Sierra Leone |    38.45|
|  1952| Cambodia     |    39.42|
|  1977| Angola       |    39.48|
|  1982| Afghanistan  |    39.85|
|  1997| Sierra Leone |    39.90|
|  1987| Angola       |    39.91|
|  1982| Angola       |    39.94|
|  1952| Rwanda       |    40.00|
|  1987| Sierra Leone |    40.01|
|  1972| Cambodia     |    40.32|
|  1992| Angola       |    40.65|
|  1987| Afghanistan  |    40.82|
|  1997| Angola       |    40.96|
|  2002| Angola       |    41.00|
|  2002| Sierra Leone |    41.01|
|  1957| Cambodia     |    41.37|
|  1957| Rwanda       |    41.50|
|  1992| Afghanistan  |    41.67|
|  1997| Afghanistan  |    41.76|
|  1977| Gambia       |    41.84|
|  2002| Afghanistan  |    42.13|
|  2007| Sierra Leone |    42.57|
|  2007| Angola       |    42.73|
|  1962| Rwanda       |    43.00|
|  2002| Rwanda       |    43.41|
|  1962| Cambodia     |    43.41|
|  2007| Afghanistan  |    43.83|
|  1987| Rwanda       |    44.02|
|  1967| Rwanda       |    44.10|
|  1972| Rwanda       |    44.60|
|  1977| Rwanda       |    45.00|
|  1967| Cambodia     |    45.41|
|  1982| Gambia       |    45.58|
|  1982| Rwanda       |    46.22|
|  2007| Rwanda       |    46.24|
|  1987| Gambia       |    49.27|
|  1982| Cambodia     |    50.96|
|  1992| Gambia       |    52.64|
|  1987| Cambodia     |    53.91|
|  1992| Cambodia     |    55.80|
|  1997| Gambia       |    55.86|
|  1997| Cambodia     |    56.53|
|  2002| Cambodia     |    56.75|
|  2002| Gambia       |    58.04|
|  2007| Gambia       |    59.45|
|  2007| Cambodia     |    59.72|

This is what I wanted, now we can see our dataframe is sorted in terms of increasing life expectancy, and not country or year. I want to see if this has any effect on a figure:

``` r
ggplot(ascending_lifeExp, aes(year, lifeExp, colour=country)) + 
  facet_wrap(~country) + 
  geom_line() + 
  scale_color_brewer(palette="Accent") + 
  labs(x = "Year", y = "Life Expectancy", title = "Countries with Minimum Life Expectancies") + theme_bw()
```

![](hw05_fctr_fig_mgmt_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-14-1.png)

No, unless I'm doing it wrong...the visualizations are exactly the same! Even the countries in the legend are still in alphabetical order. I played around with this for a long time and found that nothing I did with `arrange()` made a change in the resulting plot.

Visualization design
====================

File I/O
========

Writing and reading to file

**Writing:**

``` r
write.csv(ascending_lifeExp, file = "ascending_lifeExp.csv")
```

I can see that the .csv file shows up in the homework 5 folder.

**Reading:**

Now to read the .csv file I just wrote, I will use the `read_csv` function:

``` r
read_csv_asc <- read_csv("ascending_lifeExp.csv")
```

    ## Warning: Missing column names filled in: 'X1' [1]

    ## Parsed with column specification:
    ## cols(
    ##   X1 = col_integer(),
    ##   year = col_integer(),
    ##   country = col_character(),
    ##   lifeExp = col_double()
    ## )

``` r
kable(read_csv_asc, digits=2)
```

|   X1|  year| country      |  lifeExp|
|----:|-----:|:-------------|--------:|
|    1|  1992| Rwanda       |    23.60|
|    2|  1952| Afghanistan  |    28.80|
|    3|  1952| Gambia       |    30.00|
|    4|  1952| Angola       |    30.02|
|    5|  1952| Sierra Leone |    30.33|
|    6|  1957| Afghanistan  |    30.33|
|    7|  1977| Cambodia     |    31.22|
|    8|  1957| Sierra Leone |    31.57|
|    9|  1962| Afghanistan  |    32.00|
|   10|  1957| Angola       |    32.00|
|   11|  1957| Gambia       |    32.06|
|   12|  1962| Sierra Leone |    32.77|
|   13|  1962| Gambia       |    33.90|
|   14|  1962| Angola       |    34.00|
|   15|  1967| Afghanistan  |    34.02|
|   16|  1967| Sierra Leone |    34.11|
|   17|  1972| Sierra Leone |    35.40|
|   18|  1967| Gambia       |    35.86|
|   19|  1967| Angola       |    35.98|
|   20|  1997| Rwanda       |    36.09|
|   21|  1972| Afghanistan  |    36.09|
|   22|  1977| Sierra Leone |    36.79|
|   23|  1972| Angola       |    37.93|
|   24|  1972| Gambia       |    38.31|
|   25|  1992| Sierra Leone |    38.33|
|   26|  1977| Afghanistan  |    38.44|
|   27|  1982| Sierra Leone |    38.45|
|   28|  1952| Cambodia     |    39.42|
|   29|  1977| Angola       |    39.48|
|   30|  1982| Afghanistan  |    39.85|
|   31|  1997| Sierra Leone |    39.90|
|   32|  1987| Angola       |    39.91|
|   33|  1982| Angola       |    39.94|
|   34|  1952| Rwanda       |    40.00|
|   35|  1987| Sierra Leone |    40.01|
|   36|  1972| Cambodia     |    40.32|
|   37|  1992| Angola       |    40.65|
|   38|  1987| Afghanistan  |    40.82|
|   39|  1997| Angola       |    40.96|
|   40|  2002| Angola       |    41.00|
|   41|  2002| Sierra Leone |    41.01|
|   42|  1957| Cambodia     |    41.37|
|   43|  1957| Rwanda       |    41.50|
|   44|  1992| Afghanistan  |    41.67|
|   45|  1997| Afghanistan  |    41.76|
|   46|  1977| Gambia       |    41.84|
|   47|  2002| Afghanistan  |    42.13|
|   48|  2007| Sierra Leone |    42.57|
|   49|  2007| Angola       |    42.73|
|   50|  1962| Rwanda       |    43.00|
|   51|  2002| Rwanda       |    43.41|
|   52|  1962| Cambodia     |    43.41|
|   53|  2007| Afghanistan  |    43.83|
|   54|  1987| Rwanda       |    44.02|
|   55|  1967| Rwanda       |    44.10|
|   56|  1972| Rwanda       |    44.60|
|   57|  1977| Rwanda       |    45.00|
|   58|  1967| Cambodia     |    45.41|
|   59|  1982| Gambia       |    45.58|
|   60|  1982| Rwanda       |    46.22|
|   61|  2007| Rwanda       |    46.24|
|   62|  1987| Gambia       |    49.27|
|   63|  1982| Cambodia     |    50.96|
|   64|  1992| Gambia       |    52.64|
|   65|  1987| Cambodia     |    53.91|
|   66|  1992| Cambodia     |    55.80|
|   67|  1997| Gambia       |    55.86|
|   68|  1997| Cambodia     |    56.53|
|   69|  2002| Cambodia     |    56.75|
|   70|  2002| Gambia       |    58.04|
|   71|  2007| Gambia       |    59.45|
|   72|  2007| Cambodia     |    59.72|

It works, I can write a .csv file and pull it up again.

Writing Figures to File
=======================

Using `ggsave`, I will save a plot made earlier in this homework:

``` r
ggsave("MinLifeExpPlot.pdf", plot_minlifeExp, width = 8, height = 6)
```

I played around a bit with figure height and width. After running, I noticed the .pdf immediately appearing in my homework 5 folder, found ![here](https://github.com/auduman/stat545-hw-uduman-aisha/blob/master/hw05/MinLifeExpPlot.pdf)

Reporting my Progress
=====================

-   I spent too long trying to make sure I only selected a few of the countries with lowest expectancy, and realised instead of using `head()` to view them and then manually select them like I've been doing before, I can just pipe `head()` after the `levels()` argument to only retain the first 6 levels that I want to visualize.

-   I had issues figuring out how to enter my pathway to save my csv files....using the `file=""` I realised it saves automatically to the folder that this markdown file is in (so my homework 5 folder), but what if I wanted to save it somewhere else? I kept getting error messages, so need to look into that later.

**Useful Links**

-   Helpful information on [`arrange()`](https://www.r-bloggers.com/dplyr-example-1/)
