---
authors:
- admin
categories: []
date: "2020-10-07T00:00:00Z"
draft: false
featured: false
image:
  caption: ""
focal_point: ""
lastMod: "`r 2020-12-14T00:00:00Z`"
projects: []
subtitle: writing R functions involving variables
summary: writing R functions involving dynamic variable names from data
tags: []
title: R Functional Programming
---

I write functions involving variables names from datasets a lot. But, I always find myself googling how everytime. I think it's hard because it involves treating the variable names differently than just strings, and it requires some special symbols I rarely use.

The code below demonstrates several different scenarios.

1.  use variable names.`enquo()` and `!!` are used in pair

    ``` r
    library(tidyverse)
    # group by a variable from data
    group_by_var <- function(data, group_var) {
      group_var <- enquo(group_var)

      data %>% 
        group_by(!!group_var) %>% 
        summarise(n = n())
    }

    #  don't use "" for the variable name
    group_by_var(mtcars, cyl)
    # A tibble: 3 x 2
    #     cyl     n
    #   <dbl> <int>
    # 1     4    11
    # 2     6     7
    # 3     8    14
    ```

2.  To get string value of the variable, use `as_label()`

    ``` r
    grouped_mean <- function(data, group_var) {
      group_var <- enquo(group_var)

      # create new names based on chosen names
      new_name = paste0(as_label(group_var), ".n")

      print(new_name)
    }

    grouped_mean(mtcars, cyl)
    # [1] "cyl.n"
    ```

3.  Create a new variable use pair `!!` and `:=`

    ``` r
    create_new_var <- function(data, old_var, new_var) {
      old_var =  enquo(old_var)
      new_var =  enquo(new_var)

      # create new name on the fly = old_var+new_var
      sum_all = paste0(as_label(old_var), "_", as_label(new_var))

      data %>% 
        mutate(!!new_var := !!old_var*3,
               !!sum_all := !!old_var + !!new_var) %>% 
        select(!!old_var, !!new_var, !!sum_all) %>% 
        head(2)
    }

    #  don't use "" for the variable name
    create_new_var(mtcars, cyl, time_3)
    #   cyl time_3 cyl_time_3
    # 1   6     18         24
    # 2   6     18         24
    ```

4.  Filter created variables using `!!rlang::sym(new_var)`

    ``` r
    create_new_var <- function(data, old_var) {
      old_var =  enquo(old_var)

      # create new name = old_var+new_var
      new_var = paste0(as_label(old_var), "_new_binary")

      data %>% 
        head(10) %>% 
        mutate(!!new_var := c(1:10))  %>% 
        filter(!!rlang::sym(new_var) != 10,
               !!old_var == 4) %>%
        mutate(!!new_var := ifelse(
          !!rlang::sym(new_var)>5,
          "not good",
          "good"
        )) %>%
        select(!!old_var, !!new_var) 
    }

    create_new_var(mtcars, cyl)
    #   cyl cyl_new_binary
    # 1   4          good
    # 2   4      not good
    # 3   4      not good
    ```
    
5. Take list of quoted strings to use in map.

  ```r
  select_quoted_var <- function(data, quoted_var_from_list) {
    data %>%
      head(10) %>%
      select(!!rlang::sym(quoted_var_from_list)) %>% 
      summarise(sum = sum(!!rlang::sym(quoted_var_from_list))) %>% 
      mutate(var = quoted_var_from_list) %>% 
      select(var, sum)
  }
  
  # Have to pass the variable names as string
  map_df(names(mtcars),
          ~ select_quoted_var(data = mtcars,
                              quoted_var_from_list = .x))
  ```
    
6. Useful debugging tool `rlang::qq_show` to show actual values to help you better understand what's going on with all the !! and quo

    ```r
    library(tidyverse)

    group_by_var <- function(data, group_var) {
      group_var <- enquo(group_var)
      
      rlang::qq_show(data %>%
                       group_by(!!group_var) %>%
                       summarise(n = n()))
    }
    
    group_by_var(mtcars, cyl)
    # data %>% group_by(^cyl) %>% summarise(n = n())
    ```
