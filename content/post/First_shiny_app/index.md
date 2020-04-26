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

Several weeks ago, I started working on my first Shiny app in response for the Covid19 staffing needs. The team I worked with have understandings of their needs, but didn't really have solid specs of how we wanted to implement the ideas to form actual products that could help hospitals and nursing homes. Luckily, in our team, we had physicians, hospital admins and medical students who knew the needs and gaps, and how they would like to get help.

Anyway, this was a pretty urgent task since the rapid growing number of Covid patients. The earlier we got the application done, the earlier that hospitals and admins could start to use it to address their problems.

Finally, I have never built a Shiny app before, but I had some understanding of the features and potential that I could achieve in Shiny. So I started my Shiny journey and actually completed my first shiny apps. In this post, I want to share some of my experience hoping it will help some new users like me. To find the Shiny app I built, you can check this [repo](https://github.com/UMCSTaR/staff_projection)

# Content

## Let's get started

### UI and Server

Shiny requires a UI component and a Server component. I like to keep UI and Server in different files instead of lumping them into one script. It makes my life much easier in the long run.

**UI**: Anything related to the application user interface design is located in this file. For example, how you want your page look like (color, fond, titles, buttons, sliders etc.)

**Sever**: Your normal R code lives here. For example, your data cleaning, table joins, running models, generating plots are here.

With that been said, the most significant difference between what you have in the Server code and normal R code is reactive inputs. When you need a variable to be reactive (or interactive based on your inputs), you need to use `input$your_reactive_variable_name` in the Server script. In the UI script, the interactive variable should be the same name as `your_reactive_variable_name`, so UI and Server can talk to each other.

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

In the example above, I also demonstrated how to display the `output`. `changing_numbers` in the UI script is the output I wanted to display. As you can see, in the Server script, `output$changing_numbers` was used to 
communicate with UI. You always need to use `output$` in your Server script if you want to output something in the UI.

### Input and Output

Let's look at UI first. I like to separate input and output using `sidebarPanel` and `mainPanel` respectively. It makes things easier for a beginner like me.

In the `sidebarPanel`, I would put all the input values there. You can design your inputs in UI first without worrying the server script, like the numeric input `my_interactive_var2` below.

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

The `mainPanel`is where you can define all your outputs, for example, tables, plots or a sentence.

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

Hopefully, this simple introduction can help you a little when you first started your Shiny journey. Once you get the gist of it, you can build up a lot cool features by just googling how. Also, looking at Shiny examples other people put out there was very helpful for me to know the potential with Shiny. One of the most difficult things when you get started is to know what the possibilities are.

Most importantly, Have fun!

![](./fun.jpg)


