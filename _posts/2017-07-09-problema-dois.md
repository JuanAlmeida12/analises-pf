---
title: "Lab02"
output: html_notebook
---


{% highlight r %}
library(tidyverse, warn.conflicts = FALSE) 
{% endhighlight %}



{% highlight text %}
## Loading tidyverse: ggplot2
## Loading tidyverse: tibble
## Loading tidyverse: tidyr
## Loading tidyverse: readr
## Loading tidyverse: purrr
## Loading tidyverse: dplyr
{% endhighlight %}



{% highlight text %}
## Conflicts with tidy packages -----------------------------------------
{% endhighlight %}



{% highlight text %}
## filter(): dplyr, stats
## lag():    dplyr, stats
{% endhighlight %}



{% highlight r %}
library(plotly)
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'plotly'
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:ggplot2':
## 
##     last_plot
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:stats':
## 
##     filter
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:graphics':
## 
##     layout
{% endhighlight %}



{% highlight r %}
data <- read.csv(file="./series_from_imdb.csv", header=TRUE, sep=",")

tv_shows_resume <- group_by(data,series_name,season) %>%
  summarize(eps = n(), median = median(UserRating, na.rm = T))

g <- ggplot(tv_shows_resume, aes(season, series_name)) +
  geom_point(aes(colour = median, size = median)) +
  scale_colour_gradient(low = "white", high = "black") +
  xlab('Temporada') + 
  ylab('    ')
ggplotly(g)
{% endhighlight %}



{% highlight text %}
## We recommend that you use the dev version of ggplot2 with `ggplotly()`
## Install it with: `devtools::install_github('hadley/ggplot2')`
{% endhighlight %}



{% highlight text %}
## Error in loadNamespace(name): there is no package called 'webshot'
{% endhighlight %}

