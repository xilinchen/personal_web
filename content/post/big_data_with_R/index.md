---
authors:
- admin
categories: []
date: "2020-08-23T00:00:00Z"
draft: false
featured: false
image:
  caption: ""
  focal_point: ""
lastMod: "2020-08-23T00:00:00Z"
projects: []
subtitle: Several Packages for Big Data in R
summary: Some notes and tips summary based on my daily work experience
tags: []
title: Big Data in R
---

In my current position, I work with data with various sizes. Small data like several hundred observations with dozens of variable to data for several hundred gigabytes. In this post I want to talk about what to use depends on the size of your data, and pros and cons of diffrent packages you might come across. 


## Define dataset size

- Small data
Data that you can load instantly. (For me is I can just want it load and not need to open my chome brower to keep myslef occupid)

- Medium data
Data that you can load in within minutes to overnight to load, but fits in memory

- Big data
Data can't fit in your memory but can fit in your desk

- Super big data
Data that can't fit in your desk and need cloud.


## Stateges

