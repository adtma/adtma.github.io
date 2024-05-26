---
title: 'Geology Time Scale in Matplotlib'
date: 2024-02-16
categories:
  - blog
tags:
  - geology
  - python
---

One of the most common things that I do as an earth scientist / geologist / stratigrapher is plotting data alongside a geologic time scale.

The idea is to show a chronostratigraphic framework of trends / data that we would want to highlight. It can be geochemical data, paleobiology data (e.g., fossil occurences, cladograms, etc), stratigraphic thickness / stacking patterns, etc.

When I first started grad school in 2017, I used an R package called [deeptime](https://github.com/willgearty/deeptime). It's a pretty nifty package, very easy to use. If you work with R, you should give this tool a try. I couldn't recommend this enough.

However, since I converted into a devout pythonista in 2020, I had to find a way to plot geologic time scale using python. After spending days, learning about "artists" object in matplotlib, I managed to come up with a "working" (i.e., not super elegant but does its job) "go to" script that I always use whenever I need to put a geologic time scale of my data.

This is an example of the plot I made.
![geologic time scale python](https://user-images.githubusercontent.com/59095982/251918388-54b75d56-7d4b-4960-922c-523d75d65be6.png)

Here is the full code.


{% gist b34c6841ea05ab19538041619de04166%}


At some point I will post a companion post to explain each line in the code (I am not sure if it's really necessary), but let me know if you have any questions.