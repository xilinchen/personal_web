---
authors:
- admin
categories: []
date: "2020-04-25T00:00:00Z"
draft: false
featured: false
image:
  caption: ""
  focal_point: ""
lastMod: "2020-04-25T00:00:00Z"
projects: []
subtitle: Guidance to start your first shiny app
summary: Basics of starting Shiny app
tags: []
title: Build Your First Shiny App
---

# Intro and Background

Several weeks ago I started working on my first Shiny app in response for the Covid19 staffing needs. The team I worked with have an understanding of their needs but don't really have a solid specs of how we want to implement the ideas to form actual products that could help hospitals and nursing homes. Luckily, in our team, we have physicians, hospital admins and medical students who all know the needs and gaps, and how they would like to get helped.

Anyway, this was a pretty urgent task since the rapid growing Covid patients. The earlier we got the application done, the hospitals and admins can start to use immediately to address their problems.

Finally, I have never build Shiny app before but I have some understandings of the features and potentials that I could achieve in Shiny. So I started my Shiny journey and actually got my first shiny apps. In this post, I want to share some of my experience hoping it could help some new users like me. To find the Shiny app I build, you can check this [repo](https://github.com/UMCSTaR/staff_projection)

# Content

## Let's get started

### UI and Server

I like to keep UI and Serve in different files instead of lumping it into one script. It makes my life much easier in the long run.

**UI**: Anything related to the application user interface design is located in this file. For example, how you want you page look like (color, fond, titles, buttons, sliders etc.)

**Sever**: Your normal R code lives here. For example, your tidyverse data cleaning, join tables, running models, generate ggplots are here.

With that been said, the most significant difference is that when you need a variable to be reactive (or interactive based on your inputs), you need to use `input$your_reactive_variable_name` in the Server script. In the UI script, the interactive variable should be the same name as `your_reactive_variable_name`, so UI and Server can talk to each other and know to how react.

Below is an example to demonstrate the idea:

```r
library(shiny)

# UI: define how the app looks
ui <- fluidPage(sidebarLayout(

sidebarPanel(
    # my_interactive_var1 is defined here
    sliderInput(
        "my_interactive_var1",
        "Number of bins:",
        min = 1,
        max = 50,
        value = 30
    )
),

mainPanel(textOutput("changing_numbers"))))

# Server: nomal R code
server <- function(input, output) {
    output$changing_numbers <- renderText({
        # using input$my_interactive_var1 to communicate with UI
        paste0("My changing values: ", input$my_interactive_var1)
    })
}

# Run the application
shinyApp(ui = ui, server = server)
```

From the example above, how the output get display was also demonstrated. `changing_numbers` in the UI script is the output your wanted to display. As you can see, in the server script, `output$changing_numbers` was used to 
communicate with UI. You always need to use `output$` in your Server script if you want to output something in the UI.

### Input and Output

Let look at UI first, I like to separate input and output using `sidebarPanel` and `mainPanel` respectively. It makes things easier for beginner.

In the `sidebarPanel`, I would put all the input values there. You can design your inputs in UI first without worrying the server script ,like numeric `my_interactive_var2`.

```r
library(shiny)

# UI: define how the app looks
ui <- fluidPage(sidebarLayout(sidebarPanel(
  # my_interactive_var1 is defined here
  sliderInput(
    "my_interactive_var1",
    "Number of bins:",
    min = 1,
    max = 50,
    value = 30
  ),
  
  # add a new entry here
  numericInput(
    "my_interactive_var2",
    "My number entry",
    min = 1,
    max = 50,
    value = 30
  )
),

mainPanel(textOutput("changing_numbers"))))

# Server: nomal R code
server <- function(input, output) {
  output$changing_numbers <- renderText({
    # using input$my_interactive_var1 to communicate with UI
    paste0("My changing values: ", input$my_interactive_var1)
  })
}

# Run the application
shinyApp(ui = ui, server = server)

```

In the `mainPanel`, here is where you could define all your output, for example, tables, plots or a sentence.

Let's add a table.

```r
library(shiny)
library(tidyverse)

# UI: define how the app looks
ui <- fluidPage(sidebarLayout(sidebarPanel(
  # my_interactive_var1 is defined here
  sliderInput(
    "my_interactive_var1",
    "Number of bins:",
    min = 1,
    max = 50,
    value = 30
  ),
  
  numericInput(
    "my_interactive_var2",
    "My number entry",
    min = 1,
    max = 50,
    value = 30
  )
),

mainPanel(
  textOutput("changing_numbers"),
  # add my defined table here
  tableOutput("my_defined_table")
  )))

# Server: nomal R code
server <- function(input, output) {
  output$changing_numbers <- renderText({
    # using input$my_interactive_var1 to communicate with UI
    paste0("My changing values: ", input$my_interactive_var1)
  })
  

  # my define table is here, use output$output$my_defined_table
  output$my_defined_table <- renderTable({
    iris %>% 
      head()
  })
  
}

# Run the application
shinyApp(ui = ui, server = server)
```

# End

Hopefully, this simple introduction could help you a little when you first started your Shiny journey. Once you get the gist of it, you can build up a lot cool features by just googling how. Also, looking for Shiny examples other people put out there was very helpful for me to know the potential and what I can achieve. One of the most difficult things when you get started is ti know what the possibilities are.

Most importantly, Have fun!

![](./fun.jpg)


