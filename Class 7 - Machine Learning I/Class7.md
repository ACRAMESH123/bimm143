# Class 7: Machine Learning I
Achyuta (PID: A16956100)

Today we are going to learn how to apply different machine learning
methods, beginning with clustering:

The goal here is to find groups/clusters in your input data.

First I will make up some data with clear groups. For this I will use
the `rnorm()` function.

``` r
rnorm(10)
```

     [1]  2.12266450 -0.53575449  0.35301581  0.22915965  1.08445927  0.55671624
     [7]  0.22417665 -1.88223130  0.08938129 -0.08111640

``` r
x <- c(rnorm(30, 3), rnorm(30, -3))
y <- rev(x)
z <- cbind(x, y)
plot(z)
```

![](Class7_files/figure-commonmark/unnamed-chunk-2-1.png)

``` r
km <- kmeans(z, 2)
km
```

    K-means clustering with 2 clusters of sizes 30, 30

    Cluster means:
              x         y
    1  3.247135 -3.186660
    2 -3.186660  3.247135

    Clustering vector:
     [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2
    [39] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2

    Within cluster sum of squares by cluster:
    [1] 56.93373 56.93373
     (between_SS / total_SS =  91.6 %)

    Available components:

    [1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
    [6] "betweenss"    "size"         "iter"         "ifault"      

``` r
attributes(km)
```

    $names
    [1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
    [6] "betweenss"    "size"         "iter"         "ifault"      

    $class
    [1] "kmeans"

``` r
km$center
```

              x         y
    1  3.247135 -3.186660
    2 -3.186660  3.247135

``` r
km$cluster
```

     [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2
    [39] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2

``` r
plot(z, col = c("red", "blue"))
```

![](Class7_files/figure-commonmark/unnamed-chunk-5-1.png)

``` r
plot(z, col = km$cluster)
points(km$center, col = "blue", pch = 15, cex = 3)
```

![](Class7_files/figure-commonmark/unnamed-chunk-5-2.png)

``` r
km_2 <- kmeans(z, 4)
plot(z, col = km_2$cluster)
points(km_2$center, col = "blue", pch = 15, cex = 1)
```

![](Class7_files/figure-commonmark/unnamed-chunk-6-1.png)

## Hierarchical Clustering

Let’s take our same made-up data `z` and see how hclust works.

``` r
d <- dist(z)
hc <- hclust(d)
hc
```


    Call:
    hclust(d = d)

    Cluster method   : complete 
    Distance         : euclidean 
    Number of objects: 60 

``` r
plot(hc)
abline(h=8, col = "red")
```

![](Class7_files/figure-commonmark/unnamed-chunk-7-1.png)

``` r
grps <- cutree(hc, h=8)
grps
```

     [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2
    [39] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2

``` r
plot(z, col = grps)
```

![](Class7_files/figure-commonmark/unnamed-chunk-9-1.png)

1.  PCA of UK food data

Read data from UK UK on food consumption in different parts of the UK

``` r
url <- "https://tinyurl.com/UK-foods"
x <- read.csv(url, row.names=1)
head(x)
```

                   England Wales Scotland N.Ireland
    Cheese             105   103      103        66
    Carcass_meat       245   227      242       267
    Other_meat         685   803      750       586
    Fish               147   160      122        93
    Fats_and_oils      193   235      184       209
    Sugars             156   175      147       139

> Q1. How many rows and columns are in your new data frame named x? What
> R functions could you use to answer this questions? There are 17 rows
> and 4 columns

> Q2. Which approach to solving the ‘row-names problem’ mentioned above
> do you prefer and why? Is one approach more robust than another under
> certain circumstances? Generally, I would prefer the method used in
> this code, as it is a much simpler way of solving this issue. However,
> since the method involving reading the data file again is much more
> thorough, it would be more failsafe.

``` r
nrow(x)
```

    [1] 17

``` r
ncol(x)
```

    [1] 4

``` r
barplot(as.matrix(x), beside=T, col=rainbow(nrow(x)))
```

![](Class7_files/figure-commonmark/unnamed-chunk-12-1.png)

> Q3: Changing what optional argument in the above barplot() function
> results in the following plot? Changing beside to false causes that
> change. Leaving it out would have the same effect because the default
> setting for beside is false.

``` r
pairs(x, col=rainbow(10), pch=16)
```

![](Class7_files/figure-commonmark/unnamed-chunk-13-1.png)

> Q5: Generating all pairwise plots may help somewhat. Can you make
> sense of the following code and resulting figure? What does it mean if
> a given point lies on the diagonal for a given plot? This plot
> compares the consumption of different foods in the countries, two
> countries at a time. Therefore, a point lying on the diagonal means
> that it is consumed in a similar amount in both countries on the
> graph.

> Q6. What is the main differences between N. Ireland and the other
> countries of the UK in terms of this data-set? Northern Ireland has a
> lot less in `other meats` and much more in `fresh potatoes`.

Its hard to see structure and trends in even this small data-set. How
will we ever do this when we have big datasets with 1000s or 10s of
thousands of things we are measuring …

\##PCA to the rescue

Let’s see how PCA deals with this dataset. Main function in base R to do
PCA is called `prcomp()`

``` r
pca <- prcomp(t(x))
summary(pca)
```

    Importance of components:
                                PC1      PC2      PC3       PC4
    Standard deviation     324.1502 212.7478 73.87622 3.176e-14
    Proportion of Variance   0.6744   0.2905  0.03503 0.000e+00
    Cumulative Proportion    0.6744   0.9650  1.00000 1.000e+00

``` r
attributes(pca)
```

    $names
    [1] "sdev"     "rotation" "center"   "scale"    "x"       

    $class
    [1] "prcomp"

> Q7. Complete the code below to generate a plot of PC1 vs PC2. The
> second line adds text labels over the data points.

``` r
plot(pca$x[,1], pca$x[,2], xlab="PC1", ylab="PC2", xlim=c(-270,500))
text(pca$x[,1], pca$x[,2], colnames(x))
```

![](Class7_files/figure-commonmark/unnamed-chunk-15-1.png)

> Q8. Customize your plot so that the colors of the country names match
> the colors in our UK and Ireland map and table at start of this
> document.

``` r
plot(pca$x[,1], pca$x[,2], xlab="PC1", ylab="PC2", xlim=c(-270,500))
text(pca$x[,1], pca$x[,2], colnames(x), col = c("gold", "red", "blue", "darkgreen"))
```

![](Class7_files/figure-commonmark/unnamed-chunk-16-1.png)

``` r
pca$x
```

                     PC1         PC2        PC3           PC4
    England   -144.99315   -2.532999 105.768945 -4.894696e-14
    Wales     -240.52915 -224.646925 -56.475555  5.700024e-13
    Scotland   -91.86934  286.081786 -44.415495 -7.460785e-13
    N.Ireland  477.39164  -58.901862  -4.877895  2.321303e-13

``` r
plot(pca$x[,1], pca$x[,2],
     col=c('black', "red", "blue", "darkgreen"), pch = 16,
     xlab= "PC1(67.4%)", ylab = "PC2 (29%)")
```

![](Class7_files/figure-commonmark/unnamed-chunk-17-1.png)

``` r
par(mar=c(10, 3, 0.35, 0))
barplot( pca$rotation[,1], las=2 )
```

![](Class7_files/figure-commonmark/unnamed-chunk-18-1.png)

``` r
par(mar=c(10, 3, 0.35, 0))
barplot( pca$rotation[,2], las=2 )
```

![](Class7_files/figure-commonmark/unnamed-chunk-19-1.png)

> Q9: Generate a similar ‘loadings plot’ for PC2. What two food groups
> feature prominantely and what does PC2 maninly tell us about? The two
> groups that feature prominently in this plot are fresh potatoes and
> soft drinks. This tells us what food groups account for most of the
> variance in PC2.

``` r
library(ggplot2)

df <- as.data.frame(pca$x)
df_lab <- tibble::rownames_to_column(df, "Country")

# Our first basic plot
ggplot(df_lab) + 
  aes(PC1, PC2, col=Country) + 
  geom_point()
```

![](Class7_files/figure-commonmark/unnamed-chunk-20-1.png)

``` r
ggplot(df_lab) + 
  aes(PC1, PC2, col=Country, label=Country) + 
  geom_hline(yintercept = 0, col="gray") +
  geom_vline(xintercept = 0, col="gray") +
  geom_point(show.legend = FALSE) +
  geom_label(hjust=1, nudge_x = -10, show.legend = FALSE) +
  expand_limits(x = c(-300,500)) +
  xlab("PC1 (67.4%)") +
  ylab("PC2 (28%)") +
  theme_bw()
```

![](Class7_files/figure-commonmark/unnamed-chunk-21-1.png)

``` r
ld <- as.data.frame(pca$rotation)
ld_lab <- tibble::rownames_to_column(ld, "Food")

ggplot(ld_lab) +
  aes(PC1, Food) +
  geom_col() 
```

![](Class7_files/figure-commonmark/unnamed-chunk-22-1.png)

``` r
biplot(pca)
```

![](Class7_files/figure-commonmark/unnamed-chunk-23-1.png)

``` r
url2 <- "https://tinyurl.com/expression-CSV"
rna.data <- read.csv(url2, row.names=1)
head(rna.data)
```

           wt1 wt2  wt3  wt4 wt5 ko1 ko2 ko3 ko4 ko5
    gene1  439 458  408  429 420  90  88  86  90  93
    gene2  219 200  204  210 187 427 423 434 433 426
    gene3 1006 989 1030 1017 973 252 237 238 226 210
    gene4  783 792  829  856 760 849 856 835 885 894
    gene5  181 249  204  244 225 277 305 272 270 279
    gene6  460 502  491  491 493 612 594 577 618 638

``` r
nrow(rna.data)
```

    [1] 100

``` r
ncol(rna.data)
```

    [1] 10

> Q10: How many genes and samples are in this data set? 10 samples and
> 100 genes

``` r
pca <- prcomp(t(rna.data), scale=TRUE)
plot(pca$x[,1], pca$x[,2], xlab="PC1", ylab="PC2")
```

![](Class7_files/figure-commonmark/unnamed-chunk-26-1.png)

``` r
summary(pca)
```

    Importance of components:
                              PC1    PC2     PC3     PC4     PC5     PC6     PC7
    Standard deviation     9.6237 1.5198 1.05787 1.05203 0.88062 0.82545 0.80111
    Proportion of Variance 0.9262 0.0231 0.01119 0.01107 0.00775 0.00681 0.00642
    Cumulative Proportion  0.9262 0.9493 0.96045 0.97152 0.97928 0.98609 0.99251
                               PC8     PC9      PC10
    Standard deviation     0.62065 0.60342 3.457e-15
    Proportion of Variance 0.00385 0.00364 0.000e+00
    Cumulative Proportion  0.99636 1.00000 1.000e+00

``` r
plot(pca, main="Quick scree plot")
```

![](Class7_files/figure-commonmark/unnamed-chunk-28-1.png)

``` r
## Variance captured per PC 
pca.var <- pca$sdev^2

## Percent variance is often more informative to look at 
pca.var.per <- round(pca.var/sum(pca.var)*100, 1)
pca.var.per
```

     [1] 92.6  2.3  1.1  1.1  0.8  0.7  0.6  0.4  0.4  0.0

``` r
barplot(pca.var.per, main="Scree Plot", 
        names.arg = paste0("PC", 1:10),
        xlab="Principal Component", ylab="Percent Variation")
```

![](Class7_files/figure-commonmark/unnamed-chunk-30-1.png)

``` r
colvec <- colnames(rna.data)
colvec[grep("wt", colvec)] <- "red"
colvec[grep("ko", colvec)] <- "blue"

plot(pca$x[,1], pca$x[,2], col=colvec, pch=16,
     xlab=paste0("PC1 (", pca.var.per[1], "%)"),
     ylab=paste0("PC2 (", pca.var.per[2], "%)"))

text(pca$x[,1], pca$x[,2], labels = colnames(rna.data), pos=c(rep(4,5), rep(2,5)))
```

![](Class7_files/figure-commonmark/unnamed-chunk-31-1.png)

``` r
library(ggplot2)

df <- as.data.frame(pca$x)

ggplot(df) + 
  aes(PC1, PC2) + 
  geom_point()
```

![](Class7_files/figure-commonmark/unnamed-chunk-32-1.png)

``` r
df$samples <- colnames(rna.data) 
df$condition <- substr(colnames(rna.data),1,2)

p <- ggplot(df) + 
        aes(PC1, PC2, label=samples, col=condition) + 
        geom_label(show.legend = FALSE)
p
```

![](Class7_files/figure-commonmark/unnamed-chunk-33-1.png)

``` r
p + labs(title="PCA of RNASeq Data",
       subtitle = "PC1 clealy seperates wild-type from knock-out samples",
       x=paste0("PC1 (", pca.var.per[1], "%)"),
       y=paste0("PC2 (", pca.var.per[2], "%)"),
       caption="Class example data") +
     theme_bw()
```

![](Class7_files/figure-commonmark/unnamed-chunk-34-1.png)

``` r
loading_scores <- pca$rotation[,1]


gene_scores <- abs(loading_scores) 
gene_score_ranked <- sort(gene_scores, decreasing=TRUE)

top_10_genes <- names(gene_score_ranked[1:10])
top_10_genes 
```

     [1] "gene100" "gene66"  "gene45"  "gene68"  "gene98"  "gene60"  "gene21" 
     [8] "gene56"  "gene10"  "gene90" 
