# TOC
- [S3](#S3)
- [Plots](#Plots)
- [Tidyverse](#Tidyverse)
- [Factors](#Factors)
- [survival analysis](#survival-analysis)
- [Fit an exponention distribution](#exponential-distribution-fit)
- [create var names dynamically](#create-var-names-dynamically)
- [Memory](#Memory)
- [Packages](#Packages)
- [RMarkdown](#RMarkdown)


# AWS.S3 
- create a folder
```
put_folder("folder_name", bucket  = "bucket-path")
```
- get the list of files in a bucket
```
get_bucket(bucket  = "s3://capsule-analytics/")
```
- delete
```
delete_object("file path in the bucket")
```
- add a file
```
put_object(file = "file-name", object = "destination-name", bucket = "bucket path")
```

# Plots

## Colors

* core-blue #1F2E5C
* core-green #3BBF9E
* core-pink #E22279

## Missing data plots

- counts

```
data_quality<- rownames_to_column(as.data.frame(t(sapply(hs_vars, function(x) c(sum(!is.na(x)), sum(is.na(x))))))) %>% 
  gather( key = "status", value = "Count", - rowname) %>% mutate( status = case_when( status == "V1" ~ "Complete",
                                                                                      status == "V2" ~ "Missing"))
ggplot(data_quality, aes(x = rowname))+
  geom_col( aes(y = Count, fill = status)) + coord_flip()
```

- Per row 
-- source: https://jenslaufer.com/data/analysis/visualize_missing_values_with_ggplot.html
```
row.plot <- df %>%
  mutate(id = row_number()) %>%
  gather(-id, key = "key", value = "val") %>%
  mutate(isna = is.na(val)) %>%
  ggplot(aes(key, id, fill = isna)) +
  geom_raster(alpha=0.8) +
  scale_fill_manual(name = "",
                    values = c('steelblue', 'tomato3'),
                    labels = c("Present", "Missing")) +
  scale_x_discrete(limits = levels) +
  labs(x = "Variable",
       y = "Row Number", title = "Missing values in rows") +
  coord_flip()

row.plot
```

## Plotting two graphs side by side

```
library(gridExtra)

ggplot(mtcars) +
  aes(x = hp, y = mpg) +
  geom_point() -> p1

ggplot(mtcars) +
  aes(x = factor(cyl), y = mpg) +
  geom_boxplot() +
  geom_smooth(aes(group = 1), se = FALSE) -> p2

grid.arrange(p1, p2, ncol = 2)
```

## Bar plots with percentage
-ref: https://sebastiansauer.github.io/percentage_plot_ggplot2_V2/

- way 1
```
ggplot(tips, aes(x= day,  group=sex)) + 
  geom_bar(aes(y = ..prop.., fill = factor(..x..)), stat="count") +
  geom_text(aes( label = scales::percent(..prop..),
                 y= ..prop.. ), stat= "count", vjust = -.5) +
  labs(y = "Percent", fill="day") +
  facet_grid(~sex) +
  scale_y_continuous(labels = scales::percent)
```
- way 2
```
ggplot(tips, aes(x = day)) +  
  geom_bar(aes(y = (..count..)/sum(..count..)))
```
## Density per group

```
ggplot(dat, aes(x=rating, fill=cond)) + geom_density(alpha=.3)
```

## Changing the angle of axis labels 
theme(axis.text.x = element_text(angle = 90, hjust = 1))

## Scatterplot matrix
This is through python
```
seaborn.pairplot(data = xdata, vars = ['month', 'monthPlanType'])
plt.show()
```

## Plot constant lines

```
geom_hline(yintercept, linetype, color, size)
geom_vline(xintercept, linetype, color, size)
```

## facets
change vars in facet dynamically
```
facet_wrap(as.formula(paste("~", variable_name)))
```

## automate exploratory plots
https://aosmith.rbind.io/2018/08/20/automating-exploratory-plots/
https://www.brodrigues.co/blog/2017-03-29-make-ggplot2-purrr/

## Labelling the points in ggplot
```
p <- ggplot(mtcars, aes(wt, mpg, label = rownames(mtcars)))

p + geom_text()
```
# Tidyverse

Find the last of a var value in a group

```
group_by(group_name) %>% last(var)
group_by(group_name) %>% first(var)

```

Generate A Frequency Table (1-, 2-, Or 3-Way).

```
library(janitor)
tabyl(dat, var1, var2, var3, show_na = TRUE, show_missing_levels = TRUE, ...)

mtcars %>%
  tabyl(cyl, gear)
```

Print percentages in the right format, works in prints. If you add it to your dataset it will change the var from numeric into character.

```
library(scales)
percent(0.23)
 # "23.0%"
```
```
starwars %>% select(ends_with("color"))
starwars %>% select(matches("^[nm]a"))
starwars %>% select(10, everything())
```
# Factors
- fct_reorder(): Reordering a factor by another variable.
- fct_infreq(): Reordering a factor by the frequency of values.
- fct_relevel(): Changing the order of a factor by hand.
- fct_lump(): Collapsing the least/most frequent values of a factor into “other”.
- fct_explicit_na(): rename NA cases

# percentages

```
customers %>% tabyl(cohort_year, conversion_stage) %>% adorn_totals("row") %>%
  adorn_percentages("row") %>%
  adorn_pct_formatting(digits = 1) %>%
  adorn_ns %>%
  adorn_title
```

# functions

```
myfun <- function() list(a = 1, b = 2)

list[a, b] <- myfun()
a + b

# same
with(myfun(), a + b)
```
## Split data frame by groups

```
ir <- iris %>%
  group_by(Species)

group_split(ir)
group_keys(ir
```
## work with column names as strings

```
for (col in colnames(combos)) {
  combos[[col]] <- rlang::syms(combos[[col]])
}
```
```
pryr::get("abc")
```

```
eval(parse(text = "x"))

x <- 42
eval(parse(text = "x"))
#[1] 42

x <- 42
deparse(substitute(x))
#[1] "x"
```

## change text to columns in tidyverse

```
separate()
extract()

```
https://tidyr.tidyverse.org/reference/separate.html
# survival analysis

```
library(survminer)
fit <- survfit(Surv(time, status) ~ sex, data = lung)
ggsurvplot(fit, data = lung)
```
# exponential distribution fit
 [source](https://stats.stackexchange.com/questions/76994/how-do-i-check-if-my-data-fits-an-exponential-distribution)
```
require(vcd)
require(MASS)

# data generation
ex <- rexp(10000, rate = 1.85) # generate some exponential distribution
control <- abs(rnorm(10000)) # generate some other distribution

# estimate the parameters
fit1 <- fitdistr(ex, "exponential") 
fit2 <- fitdistr(control, "exponential")

# goodness of fit test
ks.test(ex, "pexp", fit1$estimate) # p-value > 0.05 -> distribution not refused
ks.test(control, "pexp", fit2$estimate) #  significant p-value -> distribution refused

# plot a graph
hist(ex, freq = FALSE, breaks = 100, xlim = c(0, quantile(ex, 0.99)))
curve(dexp(x, rate = fit1$estimate), from = 0, col = "red", add = TRUE)
```


# create var names dynamically

```
assign(paste("perf.a", "1", sep=""),5)
```

# Memory 
ref: https://bookdown.org/rdpeng/RProgDA/the-role-of-physical-memory.html

```
library(pryr)
mem_used() #How much memory is used
ls() # lists the vars in environment
object_size() #gets the size of an object
```
- To find the size of the largest objects

```
  library(magrittr)
  sapply(ls(), function(x) object.size(get(x))) %>% sort %>% tail(5)
```



# Packages

## package checks

```
installed.packages()
remove.packages("vioplot") # uninstall packages
```

## How to start a package

- Ref http://web.mit.edu/insong/www/pdf/rpackage_instructions.pdf section 3
- Make sure your enviromnet is empty.

```
getwd()
ls()
library(devtools)
library(roxygen2)
```
You should work from the root folder of the package.
```
devtools::create(pkg_name)
# package.skeleton("test9", code_files = "test_funcs.R")
```
Comment the function codes based on Roxygen standards so that the manuals are created automatically through `roxigenize()`.

```
roxygen2::roxygenize(pkg_name)
```

```
build(name_package)
install(name_package)
test8::get_date(xfile = "tenant_meta.csv")
```
# RMarkdown

Add an image in rmarkdown with 
```
![caption](image path)
```

reproducible examples

see [https://reprex.tidyverse.org](reprexpackage)
