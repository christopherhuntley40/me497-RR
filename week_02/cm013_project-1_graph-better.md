
# create the calibration graph 





How to use this tutorial 

- ![](images/text-icon.png)<!-- --> *add text*: type the prose verbatim into the Rmd file 
- ![](images/code-icon.png)<!-- --> *add code*: insert a code chunk, then transcribe the R code 
- ![](images/knit-icon.png)<!-- --> *knit* after each addition. 
- *Install* packages one time only
- *Load* a package using `library()` every session

Packages used in this tutorial  

- readr
- ggplot2

## goal 

Earlier, we drew a simple draft calibration graph to examine the data for visual anomalies. 

Here, we repeat the same code to start (very briefly violating the DRY principle) but do additional work to prepare the graph suitable for publication in the calibration report. 

## open a new Rmd script

Open a new Rmd file, and name it `05_calibr_graph.Rmd`. Save it to the `scripts` directory.

Edit the YAML header:  

    ---
    title: "Load-cell calibration --- graph"
    author: "your name"
    date: "date"
    output: html_document
    ---

Delete the rest of the pre-populated text. 

Insert knitr setup code

![](images/code-icon.png)<!-- --> 

    library(knitr)
    opts_knit$set(root.dir = '../')
    opts_chunk$set(echo = TRUE,  collapse = TRUE)

Knowing the packages we'll be using, we can load them right away, near the top of the file.

![](images/code-icon.png)<!-- -->


```r
# load packages
library(readr)
library(ggplot2)
```

 

## data 

![](images/text-icon.png)<!-- -->

    # Data

    Read the tidy data set.

![](images/code-icon.png)<!-- -->


```r
graph_data <- read_csv("data/02_calibr_data-tidy.csv")
str(graph_data)
```


## repeat our earlier draft

![](images/text-icon.png)<!-- -->

    # Building the graph in layers

    Just show the data. 

![](images/code-icon.png)<!-- -->


```r
calibr_graph <- ggplot(data = graph_data, aes(x = input_lb, y = output_mV)) +
	geom_point()

print(calibr_graph)
```

This was the point at which we stopped in the earlier graph script. From here on the material is new. 

## building a graph in layers

![](images/text-icon.png)<!-- -->

    Draw the regression line. 

![](images/code-icon.png)<!-- -->


```r
calibr_graph <- ggplot(data = graph_data, aes(x = input_lb, y = output_mV)) +
	geom_smooth(method = 'lm') + 
	geom_point()

print(calibr_graph)
```

Learning ggplot2

- `geom_smooth()` computes and draws a fitted curve and confidence interval
- `method` assigns the type of curve fit, `lm` is a linear model

## check yourself

Confer with a neighbor

1. Explain why I inserted `geom_smooth()` before  `geom_point()`

## changing the attributes of the graphical elements

![](images/text-icon.png)<!-- -->

    # Changing the attributes of the graphical elements

    Start with the line and the data markers. 

![](images/code-icon.png)<!-- -->


```r
calibr_graph <- ggplot(data = graph_data, aes(x = input_lb, y = output_mV)) +
	geom_smooth(method = 'lm', se = FALSE, color = 'gray70',  size = 0.5) + 
	geom_point(size = 1.5, shape = 21, stroke = 0.7, color = 'black', fill = 'gray70')

print(calibr_graph)
```

Learning ggplot2

- `se = FALSE` turns off the confidence interval band
- `geom_smooth()` arguments edit the line color and size (line width in mm).
- `geom_point()` arguments edit  the data marker size (diameter in mm),  [shape](http://docs.ggplot2.org/current/vignettes/ggplot2-specs.html), stroke and color (outer circle), and fill color.

R has 657 built-in named colors such as `gray70` and `black` I've used here. You can see the full list of color names by typing `colors()` in the Console. View the colors by name  [here](http://www.stat.columbia.edu/~tzheng/files/Rcolor.pdf).

![](images/text-icon.png)<!-- -->

    Edit the axis labels. 

![](images/code-icon.png)<!-- -->


```r
calibr_graph <- calibr_graph + 
	xlab("Applied force (lb)") + 
	ylab("Sensor reading (mV)")

print(calibr_graph)
```

Learning ggplot2

- This chunk could be read "Assign `calibr_graph` to itself, add a layer for the x-axis label, `xlab()`, and add a layer for the y-axis label, `ylab()`."

![](images/text-icon.png)<!-- -->

    Edit the locations of the axis markings to match the test points. 

![](images/code-icon.png)<!-- -->


```r
calibr_graph <- calibr_graph + 
	scale_x_continuous(breaks = seq(from = 0.5, to = 4.5, by = 1)) +
	scale_y_continuous(breaks = seq(from = 10, to = 90, by = 20))

print(calibr_graph)
```

Learning R

- `breaks` is ggplot2 syntax for tick mark locations
- Sequences have the form `seq (from = ..., to = ..., by = ...)` as shown here. You can shorten the code by omitting the *key* words as long as the *values* are in the order shown. For example, for the y-scale markings I could have written `seq(10, 90, 20)`. 

![](images/text-icon.png)<!-- -->

    Assign a different theme. 

![](images/code-icon.png)<!-- -->


```r
calibr_graph <- calibr_graph + 
	theme_light()

print(calibr_graph)
```

Learning ggplot2

- A theme, in ggplot2, is a collection of settings that controls the appearance of the graph. Example images of the built-in themes are [here](http://docs.ggplot2.org/current/ggtheme.html).
- Additional themes are available in the [ggthemes package](https://github.com/jrnold/ggthemes), including themes inspired by Stephen Few, Edward Tufte, The Economist magazine, and even the classic 2003 ugly gray charts in Excel ("for ironic purposes only").

![](images/text-icon.png)<!-- -->

    Edit the theme.  

![](images/code-icon.png)<!-- -->


```r
calibr_graph <- calibr_graph +
        theme(panel.grid.minor = element_blank(), axis.ticks.length = unit(2, "mm"))

print(calibr_graph)
```

Learning ggplot2

- `element_blank()` turns off the minor grid lines
- `axis.ticks.length` edits the length of the axis tick marks

The ability to edit or create a theme is the essential tool for customizing graphs in `ggplot2`. I've show two small customizations; the full list is given [here](http://rstudio-pubs-static.s3.amazonaws.com/3364_d1a578f521174152b46b19d0c83cbe7e.html).

## one code chunk would have sufficed 

We've written out the layers in separate code chunks for clarity, but they could have been assembled into one code chunk, giving you an idea of how compact and readable (well, once you've learned the grammar) the `ggplot2` syntax can be. 

![](images/code-icon.png)<!-- --> (optional)
 

```r
calibr_graph <- ggplot(graph_data, aes(input_lb, output_mV)) +
	geom_smooth(method = 'lm', se = FALSE, color = 'gray70',  size = 0.5) + 
	geom_point(size = 1.5, stroke = 0.7, shape = 21, color = 'black', fill= 'gray70') +
	xlab("Applied force (lb)") + 
	ylab("Sensor reading (mV)") +
	scale_x_continuous(breaks = seq(0.5, 4.5, 1)) +
	scale_y_continuous(breaks = seq(10, 90, 20)) +
	theme_light() +
	theme(panel.grid.minor = element_blank(), axis.ticks.length = unit(2, "mm"))

print(calibr_graph)
```

Learn ggplot2. Earlier, we shortened the `seq()` function by writing the arguments in a particular order. We can do the same with other functions:

- From `ggplot()`, you can omit `data = `, leaving the data frame name
- From `aes()`, you can omit `x = ` and `y = `, leaving the x-variable and y-variable names in that order

## print to file 

![](images/text-icon.png)<!-- -->

    # Print to file  

![](images/code-icon.png)<!-- -->


```r
# print to file
ggsave("results/05_calibr_graph.png", plot = calibr_graph, 
			 width = 6, height = 4, units = "in", dpi = 300)
```

Learning ggplot2

- Save to the `results` directory because the graph will be used in a report
- `dpi = 300` for using in a print document
- `dpi` $\times$ `width` and `dpi` $\times$ `height` in inches yields screen dimension in pixels (72 ppi for web resolution is a  [myth](http://www.photoshopessentials.com/essentials/the-72-ppi-web-resolution-myth/)).









## check yourself


Your directories should contain these files:

    data\
      |-- 007_wide-data.csv
      |-- 01_calibr_data_active-report.csv
      `-- 02_calibr_data-tidy.csv

    reports\
    
    resources\
      `-- load-cell-setup-786x989px.png 
      
    results\
      |-- 01_calibr_data-wide.csv 
      |-- 03_calibr_graph-draft.png
      |-- 04_calibr_outcomes.csv
      `-- 05_calibr_graph.png
      
    scripts\
      |-- 01_calibr_data-wide.Rmd 
      |-- 02_calibr_data-tidy.Rmd 
      |-- 03_calibr_graph-draft.Rmd
      |-- 04_calibr_regression.Rmd
      `-- 05_calibr_graph.Rmd
      
## push to github

- RStudio Environment pane,`Git` tab  
- Check the `Staged` box for new files and folders 
- `Commit` 
- Type a unique commit message 
- `Commit` 
- `Close` 
- `Push` 
- `Close` 

## check yourself

- Online, navigate to your project repo on GitHub. 
- The new files and folders should be there 


## graph extras

There's much more we can do to bring the graph up to publication standards.

I've prepared a (completely optional) tutorial that develops some graph extras  for those of you who want to know MORE and you want it NOW.  Topics include:

- Jitter the data markers to reduce printing overlap and set a random seed so jittering is repeatable
- Use the results table to dynamically assign units to the axis labels and set  tick mark locations
- Use the results table to dynamically edit the calibration equation and print it on the graph
- Format the calibration equation for significant figures
- Change font sizes

---

[main page](../README.md)  


