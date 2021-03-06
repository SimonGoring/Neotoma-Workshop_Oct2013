**Navigation - [Home](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/README.md) - [Intro to R](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/IntroToR/IntroR_1.md) - [Web Services & APIs](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/WebServices/WebServices.md) - [Basic Search](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/BasicSearches/BasicSearches.md) - [Pollen Objects](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/PollenObjects.md) - [Manipulating Pollen](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/ManipulatingPollen.md)**

-----------------------------------




Manipulating pollen objects, compressing lists.
========================================================

The central thesis for our project is that there might be differences between turnover rates in high and low elevation sites.  Turnover is simply a metric of dissimilarity, and we can use squared-chord dissimilarity to do this:

$$diss_{ij} = \sum\limits_{k=1}^n \left ({{p_{ik}}^{1/2} - {p_{jk}}^{1/2}}  \right )^2$$

Of course, it is important to note that the number of taxa used can have a significant impact on the estimate of dissimilarity.  So one site with only two taxa could have much different baselines of dissimilarity than a site with one hundred taxa.  Since investgator skill (as a proxy for taxonomic specificity) can impact the taxa reported it would be best for us to use standardized taxonomy for analyis.  In the package we (with lots of help from _**Jeremiah Marsicek**_) have developed several taxon equivalencies from the published literature.  These are embedded in the function `compile_list`.

**Table**. *Table compilations for neotoma pollen taxonomies.*

Name | Description
---- | -----------
P25  | Derived from Gavin et al ([2003](http://dx.doi.org/10.1016/S0033-5894%2803%2900088-7))
WS64 | Derived from Williams and Shuman (2008).
WhitmoreFull | Derived from Whitmore et al. (2005)
WhitmoreSmall | Derived from Whitmore et al. (2005) but all taxa to lowest resolution.

`compile_list` works directly on your pollen object, so you can call `compile_list(high.pol, 'WhitmoreFull')` and it would create a new pollen object with the now standardized taxonomy.  Since we have a whole bunch of samples we need to do this in a loop (or vectorize it, if you've read [The R Inferno](http://www.burns-stat.com/documents/books/the-r-inferno/), so that we process each sample individually before we bring them all together.  We can do this one of two ways, either through a list apply (`lapply`) or through a loop.  The loop is a bit more intuitive, so lets do it that way:


```r
new.high <- list()
new.low <- list()

for (i in 1:length(high.el)) {
    new.high[[i]] <- try(compile_list(high.pol[[i]], "WhitmoreSmall", type = TRUE, 
        cf = TRUE))
}

for (i in 1:length(low.el)) {
    new.low[[i]] <- try(compile_list(low.pol[[i]], "WhitmoreSmall", type = TRUE, 
        cf = TRUE))
}
```


So we've compressed the taxon list, we can take a look at which taxa were lumped by looking at the `taxon.list` attribute of your sample.  The `taxon.list` has a number of columns, and you can see what they are using `colnames(high.pol[[1]]$taxon.list)`, and if you check the compressed pollen dataset you'll see that a new column `compressed` has been added.  This allows you to compare the original taxa described for the core and the equivalent taxa used in the compressed list.  **This is an important check!**  You can also look at the raw pollen equivalence table, it is stored in the package as a data.frame:

```
data(pollen.equiv)
head(pollen.equiv)
```

Now we have a set of pollen samples from low elevation sites, and a set of samples from high elevation sites.  This next step is probably the most complicated, we need to do a few things:

1.  We need to calculate turnover between stratigraphic levels
2.  We need to normalize it by the time-frame between samples
3.  We need to bring this data together into a single `data.frame`

There are some reasons why step 2 is not entirely correct, but it's okay for the purposes of this workshop.  So lets go step by step.  First we need to calculate the turnover, but we're only doing it for samples that are younger than 1000ybp.  So lets write a little loop again.  In reality, it might be better to do this as a function, but lets' not worry about that.


```r
high.steps <- sum(sapply(new.high, function(x) sum(x$sample.meta$Age < 1000, 
    na.rm = TRUE)))
low.steps <- sum(sapply(new.low, function(x) sum(x$sample.meta$Age < 1000, na.rm = TRUE)))

steps <- high.steps + low.steps

output <- data.frame(midpoint = rep(NA, steps), dissim = rep(NA, steps), site = rep(NA, 
    steps), lat = rep(NA, steps), long = rep(NA, steps), elev = rep(NA, steps))

get_dissim <- function(x, elev) {
    
    good <- x$sample.meta$Age < 1000
    
    if (sum(good, na.rm = TRUE) > 1) {
        
        samples <- x$counts[good, !colnames(x$count) %in% "Other"]
        samp.pct <- samples/rowSums(samples, na.rm = TRUE)
        
        dissims <- rowSums(diff(samp.pct^0.5)^2)
        age.diffs <- diff(x$sample.meta$Age[good])
        
        age.midpoint <- diff(x$sample.meta$Age[good])/2 + x$sample.meta$Age[good][-sum(good)]
        
        output <- data.frame(midpoint = age.midpoint, dissim = dissims/age.diffs, 
            site = x$metadata$site.data$SiteName, lat = x$metadata$site.data$LatitudeNorth, 
            long = x$metadata$site.data$LongitudeWest, elev = elev)
    } else {
        output <- data.frame(midpoint = NA, dissim = NA, site = x$metadata$site.data$SiteName, 
            lat = x$metadata$site.data$LatitudeNorth, long = x$metadata$site.data$LongitudeWest, 
            elev = elev)
    }
    output
}

get_dissim(new.high[[1]], elev = "high")
```

```
##     midpoint    dissim    site    lat   long elev
## 392    151.5 0.0023656 Aguilar -23.83 -65.75 high
## 393    378.5 0.0005803 Aguilar -23.83 -65.75 high
## 394    605.5 0.0000907 Aguilar -23.83 -65.75 high
```

```r

dissim.time <- rbind(ldply(new.high, get_dissim, elev = "high"), ldply(new.low, 
    get_dissim, elev = "low"))
```


So, in this case, we get a really nicely formed `data.frame`. We can plot these out


```r
dissim.time <- dissim.time[rowSums(is.na(dissim.time)) == 0 & is.finite(dissim.time[, 
    2]) & dissim.time[, 2] > 0, ]
dissim.time <- dissim.time[dissim.time$long < -20 & dissim.time$lat > 20, ]

ggplot(dissim.time, aes(x = midpoint, y = dissim, color = elev)) + geom_point() + 
    scale_x_reverse(expand = c(0, 0)) + scale_y_sqrt(expand = c(0, 0)) + # geom_smooth(method='gam', family=Gamma, formula = y ~ s(x), size = 2) +
theme_bw() + theme(text = element_text(size = 24, family = "serif"), axis.text = element_text(size = 14, 
    family = "serif")) + xlab("Years Before Present") + ylab("Sq. Chord Turnover / yr")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 


I don't want to plot the curve yet until we test for the best fit model, but low elevation sites seem to have overall higher turnover through the last 1000 years (possibly due to higher overall diversity of pollen taxa), but in particular the last 100 years show what appears to be a significant increase in turnover.


```r

model.0 <- gam(dissim ~ 1, data = dissim.time, family = Gamma)
model.1 <- gam(dissim ~ s(midpoint), data = dissim.time, family = Gamma)
model.2 <- gam(dissim ~ s(midpoint) + elev, data = dissim.time, family = Gamma)
model.3 <- gam(dissim ~ s(midpoint, by = elev), data = dissim.time, family = Gamma)
```


So now that the models are built, lets look at the model results to see which model we ultimately accept:


```r
anova(model.0, model.1, test = "F")
```

```
## Analysis of Deviance Table
## 
## Model 1: dissim ~ 1
## Model 2: dissim ~ s(midpoint)
##   Resid. Df Resid. Dev  Df Deviance  F Pr(>F)    
## 1       438        768                           
## 2       436        515 2.3      253 93 <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
anova(model.1, model.2, test = "F")
```

```
## Analysis of Deviance Table
## 
## Model 1: dissim ~ s(midpoint)
## Model 2: dissim ~ s(midpoint) + elev
##   Resid. Df Resid. Dev    Df Deviance    F  Pr(>F)    
## 1       436        515                                
## 2       435        490 0.947     24.1 22.6 4.4e-06 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
anova(model.2, model.3, test = "F")
```

```
## Analysis of Deviance Table
## 
## Model 1: dissim ~ s(midpoint) + elev
## Model 2: dissim ~ s(midpoint, by = elev)
##   Resid. Df Resid. Dev    Df Deviance F Pr(>F)
## 1       435        490                        
## 2       434        507 0.737    -16.1
```


And so, this simple exploratory work, that really took me one day to code up (along with all this writing) gets us to a point where we can test our hypothesis.

--------

**H<sub>0</sub>**: The ANOVA comparing `model.1` and `model.2` indicates that there is a difference between high elevation and low elevation curves, but that this can be explained by a binary variable, so we can reject **H<sub>0</sub>**.

**H<sub>1</sub>**: When we compare `model.2` to `model.3` we see that letting the two spline curves differ in shape (as in the figure) doesn't improve the model.  This means that we would reject **H<sub>2</sub>**.  

**H<sub>2</sub>**:  I gave it away already, this one is rejected.

So, the rates of change have increased uniformly for both high and low sites, but lower sites have uniformly higher baseline rates of turnover and an increase in turnover rates since ~250ybp (since `model.0` was rejected).

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 


-----------------

**Navigation - [Home](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/README.md) - [Intro to R](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/IntroToR/IntroR_1.md) - [Web Services & APIs](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/WebServices/WebServices.md) - [Basic Search](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/BasicSearches/BasicSearches.md) - [Pollen Objects](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/PollenObjects.md) - [Manipulating Pollen](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/ManipulatingPollen.md)**
