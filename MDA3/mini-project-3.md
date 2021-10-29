Mini Data-Analysis Deliverable 3
================
Mathias Delhaye

# Welcome to your last milestone in your mini data analysis project!

In Milestone 1, you explored your data and came up with research
questions. In Milestone 2, you obtained some results by making summary
tables and graphs.

In this (3rd) milestone, you’ll be sharpening some of the results you
obtained from your previous milestone by:

-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

## Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-3.Rmd)
directly. Fill in the sections that are tagged with
`<!--- start your work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from
`output: html_document` to `output: github_document`. Commit and push
all of your work to your mini-analysis GitHub repository, and tag a
release on GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 40 points (compared to the usual 30
points): 30 for your analysis, and 10 for your entire mini-analysis
GitHub repository. Details follow.

**Research Questions**: In Milestone 2, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- might contain the data you picked!
library(tidyverse)
```

From Milestone 2, you chose two research questions. What were they? Put
them here.

<!-------------------------- Start your work below ---------------------------->

1.  *Is there a trend in the type of trees that are planted over the
    years and per neighborhood?*
2.  *Is there an evolution of the physiological properties of the trees
    (diameter and height), depending on the year? Are these properties
    related to any other variables of the tibble?*
    <!----------------------------------------------------------------------------->

# Exercise 1: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

In the milestone 2, I created a new tibble based on the original
*vancouver_trees*, called *vancouver_trees_year*, in which I added a
column containing only the year when each tree was planted. I am going
to use it to plot the number of trees planted for each genus in 1998 as
it is the year with the highest number of trees planted.

``` r
vancouver_trees_year <- vancouver_trees%>%
  mutate(year_planted = format(date_planted, "%Y"))

vancouver_trees_year%>%
  filter(year_planted == 1998)%>% #only keep year 1998 as it is the year with the most trees planted
  group_by(genus_name)%>%
  summarise(n = n())%>% #counting the number of trees planted for each genus
  drop_na()%>% #getting ride of the NA
  ggplot(aes(genus_name,n)) + #plotting number of trees planted by genus_name
  geom_point() +
  coord_flip() + #flipping the coordinate to make it easier to read
  theme(axis.text.y = element_text(size = 6)) + #reducing the size of the genus_name to make it easier to read
  labs(x = "", y = "Number of trees planted", title = "Genus trees, 1998")
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->
<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats` package
    (3 points). Then, in a sentence or two, briefly explain why you
    chose this grouping (1 point here for demonstrating understanding of
    the grouping, and 1 point for demonstrating some justification for
    the grouping, which could be subtle or speculative.)

<!-------------------------- Start your work below ---------------------------->

**Task Number**: 1

``` r
vancouver_trees_year%>%
  filter(year_planted == 1998)%>% #only keep year 1998 as it is the year with the most trees planted
  group_by(genus_name)%>%
  summarise(n = n())%>% #counting the number of trees planted for each genus
  drop_na()%>% #getting ride of the NA
  mutate(genus_name = fct_reorder(genus_name, n))%>% #reordering the genus_name by the number of trees planted
  ggplot(aes(genus_name,n)) + #plotting number of trees planted by genus_name
  geom_point() +
  coord_flip() + #flipping the coordinate to make it easier to read
  theme(axis.text.y = element_text(size = 6)) + #reducing the size of the genus_name to make it easier to read
  labs(x = "", y = "Number of trees planted", title = "Genus trees, 1998")
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-3-1.png)<!-- --> In
this plot, I reordered the genus of the trees depending on the number of
trees planted, allowing to have a better visualization of which genus
was the most planted in 1998.
<!----------------------------------------------------------------------------->

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):
    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)
        -   Note that you might first have to *make* a time-based column
            using a function like `ymd()`, but this doesn’t count.
        -   Examples of something you might do here: extract the day of
            the year from a date, or extract the weekday, or let 24
            hours elapse on your dates.
    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).
        -   For example, you could say something like “Investigating the
            day of the week might be insightful because penguins don’t
            work on weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number**: 3

``` r
library(lubridate) #charging the lubridate package
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

``` r
vancouver_trees_month <- vancouver_trees%>%
  mutate(month_planted = month(date_planted)) #adding a column containing the month for each plantation
vancouver_trees_month$month_name <- month.name[vancouver_trees_month$month_planted] #adding a column using the newly created new column to have the name of the month 
glimpse(vancouver_trees_month)
```

    ## Rows: 146,611
    ## Columns: 22
    ## $ tree_id            <dbl> 149556, 149563, 149579, 149590, 149604, 149616, 149…
    ## $ civic_number       <dbl> 494, 450, 4994, 858, 5032, 585, 4909, 4925, 4969, 7…
    ## $ std_street         <chr> "W 58TH AV", "W 58TH AV", "WINDSOR ST", "E 39TH AV"…
    ## $ genus_name         <chr> "ULMUS", "ZELKOVA", "STYRAX", "FRAXINUS", "ACER", "…
    ## $ species_name       <chr> "AMERICANA", "SERRATA", "JAPONICA", "AMERICANA", "C…
    ## $ cultivar_name      <chr> "BRANDON", NA, NA, "AUTUMN APPLAUSE", NA, "CHANTICL…
    ## $ common_name        <chr> "BRANDON ELM", "JAPANESE ZELKOVA", "JAPANESE SNOWBE…
    ## $ assigned           <chr> "N", "N", "N", "Y", "N", "N", "N", "N", "N", "N", "…
    ## $ root_barrier       <chr> "N", "N", "N", "N", "N", "N", "N", "N", "N", "N", "…
    ## $ plant_area         <chr> "N", "N", "4", "4", "4", "B", "6", "6", "3", "3", "…
    ## $ on_street_block    <dbl> 400, 400, 4900, 800, 5000, 500, 4900, 4900, 4900, 7…
    ## $ on_street          <chr> "W 58TH AV", "W 58TH AV", "WINDSOR ST", "E 39TH AV"…
    ## $ neighbourhood_name <chr> "MARPOLE", "MARPOLE", "KENSINGTON-CEDAR COTTAGE", "…
    ## $ street_side_name   <chr> "EVEN", "EVEN", "EVEN", "EVEN", "EVEN", "ODD", "ODD…
    ## $ height_range_id    <dbl> 2, 4, 3, 4, 2, 2, 3, 3, 2, 2, 2, 5, 3, 2, 2, 2, 2, …
    ## $ diameter           <dbl> 10.00, 10.00, 4.00, 18.00, 9.00, 5.00, 15.00, 14.00…
    ## $ curb               <chr> "N", "N", "Y", "Y", "Y", "Y", "Y", "Y", "Y", "Y", "…
    ## $ date_planted       <date> 1999-01-13, 1996-05-31, 1993-11-22, 1996-04-29, 19…
    ## $ longitude          <dbl> -123.1161, -123.1147, -123.0846, -123.0870, -123.08…
    ## $ latitude           <dbl> 49.21776, 49.21776, 49.23938, 49.23469, 49.23894, 4…
    ## $ month_planted      <dbl> 1, 5, 11, 4, 12, NA, 12, 12, 12, 12, 12, 12, 12, 11…
    ## $ month_name         <chr> "January", "May", "November", "April", "December", …

Here I used the function *month* from the *lubridate* package, with the
mutate function, to add a column with the month (in number) when each
tree was planted, and then I add another column with the name of the
month. This new column can be useful to study when, during the year, the
trees are usually planted in Vancouver.
<!----------------------------------------------------------------------------->

# Exercise 2: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

**Research Question**: Is there an evolution of the physiological
properties of the trees (diameter and height), depending on the year?
Are these properties related to any other variables of the tibble?

**Variable of interest**: diameter

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

-   **Note**: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.
    -   You could fit a model that makes predictions on Y using another
        variable, by using the `lm()` function.
    -   You could test whether the mean of Y equals 0 using `t.test()`,
        or maybe the mean across two groups are different using
        `t.test()`, or maybe the mean across multiple groups are
        different using `anova()` (you may have to pivot your data for
        the latter two).
    -   You could use `lm()` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

``` r
vty_acer <- vancouver_trees_year%>%
  group_by(genus_name, year_planted)%>% #grouping by the genus and the year
  filter(genus_name == "ACER")%>% #keep only ACER trees, as it is the most numerous
  drop_na()%>% #drop any NA
  summarise(mean_diameter = mean(diameter))#calculating mean diameter for each year
```

    ## `summarise()` has grouped output by 'genus_name'. You can override using the `.groups` argument.

``` r
#print(vty_acer)

vty_acer$year_planted <- as.numeric(vty_acer$year_planted) #changing the class of year, from chr to numeric

model_vty_acer <- lm(mean_diameter ~ I(year_planted-1989), vty_acer) #fitting a linear model to the data 

print(model_vty_acer)
```

    ## 
    ## Call:
    ## lm(formula = mean_diameter ~ I(year_planted - 1989), data = vty_acer)
    ## 
    ## Coefficients:
    ##            (Intercept)  I(year_planted - 1989)  
    ##                 9.4629                 -0.2469

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

-   Be sure to indicate in writing what you chose to produce.
-   Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
-   Obtain your results using the `broom` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

I want to predict the mean_diameter of the ACER trees that are going to
be potentially planted in the future, using the fitted model obtained
above.

``` r
futur_vty_acer <- tibble(year_planted = 2020:2030) #creating a tibble containing the years I want to model

prediction_vty_acer <- broom::augment(model_vty_acer, newdata = futur_vty_acer, interval = "prediction") #use the augment function of broom package

print(prediction_vty_acer)
```

    ## # A tibble: 11 × 4
    ##    year_planted .fitted  .lower .upper
    ##           <int>   <dbl>   <dbl>  <dbl>
    ##  1         2020  1.81    0.444   3.18 
    ##  2         2021  1.56    0.189   2.94 
    ##  3         2022  1.32   -0.0664  2.70 
    ##  4         2023  1.07   -0.322   2.46 
    ##  5         2024  0.822  -0.578   2.22 
    ##  6         2025  0.575  -0.835   1.98 
    ##  7         2026  0.328  -1.09    1.75 
    ##  8         2027  0.0812 -1.35    1.51 
    ##  9         2028 -0.166  -1.61    1.28 
    ## 10         2029 -0.413  -1.86    1.04 
    ## 11         2030 -0.659  -2.12    0.805

We can see in the prediction that, after 2027, the values for diameter
are negative, which is not possible. Thus, the model I chose to predict
the diameter in the future years is not accurate (it is not a linear
model).
<!----------------------------------------------------------------------------->

# Exercise 3: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 2 (Exercise 1.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

-   **Robustness criteria**: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
-   **Reproducibility criteria**: You should be able to delete the csv
    file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

``` r
library(here)
```

    ## here() starts at /Users/Mathias/Documents/PhD/Courses/STAT 545/Mini_data_analysis/mathias_mini_data_analysis

``` r
finalversion_vancouver_trees <- vancouver_trees %>%
  select(tree_id, genus_name, neighbourhood_name, height_range_id, diameter, date_planted) %>% #keeping only the data that are interesting
  mutate(year_planted = format(date_planted, "%Y"))%>% #adding the year planted
  arrange(genus_name, year_planted)%>% #re-ordering the data
  drop_na() #keeping only the rows without NA

dir.create(here::here("output")) #create the output folder
```

    ## Warning in dir.create(here::here("output")): '/Users/Mathias/Documents/PhD/
    ## Courses/STAT 545/Mini_data_analysis/mathias_mini_data_analysis/output' already
    ## exists

``` r
write_csv(finalversion_vancouver_trees, here::here("output/finalversion_vancouver_trees.csv"))#writing the file into the output folder
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Exercise 2 to an R binary file (an RDS),
and load it again. Be sure to save the binary file in your `output`
folder. Use the functions `saveRDS()` and `readRDS()`.

-   The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

``` r
saveRDS(model_vty_acer, file = here::here("output/model_vty_acer.rds")) #write RDS file for the model
readRDS(here::here("output/model_vty_acer.rds")) #reading the RDS file for the model
```

    ## 
    ## Call:
    ## lm(formula = mean_diameter ~ I(year_planted - 1989), data = vty_acer)
    ## 
    ## Coefficients:
    ##            (Intercept)  I(year_planted - 1989)  
    ##                 9.4629                 -0.2469

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

-   In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
-   In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a sentence
or two what is in the folder, in plain language (it’s enough to say
something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

-   All Rmd files have been `knit`ted to their output, and all data
    files saved from Exercise 3 above appear in the `output` folder.
-   All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
-   There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 3 document knits error-free. (We’ve already graded this
aspect for Milestone 1 and 2)

## Tagged release (1 point)

You’ve tagged a release for Milestone 3. (We’ve already graded this
aspect for Milestone 1 and 2)
