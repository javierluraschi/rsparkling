# RSparkling

[![Join the chat at https://gitter.im/h2oai/sparkling-water](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/h2oai/rsparkling?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![][travis img]][travis]
[![][license img]][license]

[travis]:https://travis-ci.org/h2oai/rsparkling
[travis img]:https://travis-ci.org/h2oai/rsparkling.svg?branch=master

[license]:LICENSE
[license img]:https://img.shields.io/badge/License-Apache%202-blue.svg

The **rsparkling** R package is an extension package for
[sparklyr](http://spark.rstudio.com)
that creates an R front-end for a Spark package
([Sparkling Water](https://mvnrepository.com/search?q=h2o+sparkling+water)
from
[H2O](http://h2o.ai))
.
This provides an interface to H2O's machine learning algorithms on Spark, using R.

This package implements basic functionality (creating an H2OContext, showing the H2O Flow interface, and converting between Spark DataFrames and H2O Frames). 


## Additional Resources

* [Main documentation site for Sparkling Water (and all H2O software projects)](http://docs.h2o.ai)
* [H2O.ai website](http://h2o.ai)
* Code for the example shown below is [here](https://github.com/h2oai/rsparkling/blob/master/inst/examples/example_rsparkling.R).

## Integration

`rsparkling` requires certain integration settings (specific Sparkling Water versions must match specific Spark and H2O versions).
Refer to integration info below.

| Spark Version | Sparkling Water Version | H2O Version | H2O Version Name | H2O Version Number |
| ------------- | ----------------------- | ----------- | ---------------- | ------------------ |
| 2.0           | 2.0.0                   | 3.10.0.7    | "rel-turing"     |        "7"         |
|               | 2.0.1                   | 3.10.0.10   | "rel-turing"     |        "10"        |                  
|               | 2.0.2                   | 3.10.0.10   | "rel-turing"     |        "10"        |
|               | 2.0.3                   | 3.10.1.2    | "rel-turnbull"   |        "2"         |
|               |                         |             |                  |                    |
| 1.6           | 1.6.1                   | 3.8.1.3     | "rel-turan"      |        "3"         |
|               | 1.6.2                   | 3.8.1.3     | "rel-turan"      |        "3"         |
|               | 1.6.3                   | 3.8.2.3     | "rel-turchin"    |        "3"         |
|               | 1.6.4                   | 3.8.2.4     | "rel-turchin"    |        "4"         |
|               | 1.6.5                   | 3.8.2.6     | "rel-turchin"    |        "6"         |
|               | 1.6.6                   | 3.10.0.4    | "rel-turing"     |        "4"         |
|               | 1.6.7                   | 3.10.0.6    | "rel-turing"     |        "6"         |
|               | 1.6.8                   | 3.10.0.7    | "rel-turing"     |        "7"         |

**NOTE**: A call to `h2o_release_table()` will display the above table in your R console.

To install any one of the above versions you can call the helper function `install_h2o` from the `rsparkling` package:

```r
#We will install the H2O release `rel-turnbull` version `2` (H2O Version: 3.10.1.2)
install_h2o(release_name = "rel-turnbull", release_number = "2")
```

The example below will use Spark version 2.0, Sparkling Water version 2.0.3, & H2O version 3.10.1.2.

## Installation

The **rsparkling** R package requires the **sparklyr** and **h2o** R packages to run.

### Install sparklyr

We recommend the latest stable version of [sparklyr](http://spark.rstudio.com/index.html).

```r
install.packages("sparklyr")
```

### Install h2o

```r
# The following two commands remove any previously installed H2O packages for R.
if ("package:h2o" %in% search()) { detach("package:h2o", unload=TRUE) }
if ("h2o" %in% rownames(installed.packages())) { remove.packages("h2o") }

# Next, we download packages that H2O depends on.
pkgs <- c("methods","statmod","stats","graphics","RCurl","jsonlite","tools","utils")
for (pkg in pkgs) {
    if (! (pkg %in% rownames(installed.packages()))) { install.packages(pkg) }
}

# Now we download, install, and initialize the H2O package for R. 
# In this case we are using rel-turnbull 2 (3.10.1.2).
install.packages("h2o", type = "source", repos = "http://h2o-release.s3.amazonaws.com/h2o/rel-turnbull/2/R")
```

### Install rsparkling

The latest version of rsparkling on CRAN can be installed as follows:

```r
install.packages("rsparkling")
```

The latest stable version of rsparkling can be installed from the "stable" branch as follows:

```r
library(devtools)
devtools::install_github("h2oai/rsparkling", ref = "stable")
```  

The development version can be installed from the "master" branch as follows:

```r
library(devtools)
devtools::install_github("h2oai/rsparkling", ref = "master")
``` 

## Connecting to Spark

If Spark needs to be installed, that can be done using the following sparklyr command:

``` r
library(sparklyr)
spark_install(version = "2.0.0")
```

The call to `options(rsparkling.sparklingwater.version = ...)` will globally set up a specific Sparkling Water version, which is the version of Sparkling Water that will be called in the `library(rsparkling)` command.

**NOTE**:
If you do not set `rsparkling.sparklingwater.version`, then the latest version of Sparkling Water will be used based on the version of Spark installed (Spark version 2.0.* or 1.6.*)

**NOTE**: 
If you would like to use a custom Sparkling Water jar, then you need to call the following:
`options(rsparkling.sparklingwater.location = "path/to/sparkling_water.jar")`. 
This will be the version of Sparkling Water that will be called in the `library(rsparkling)` command.

``` r
options(rsparkling.sparklingwater.version = "2.0.3") # Using Sparkling Water 2.0.3
library(rsparkling) 
```

We can create a Spark connection as follows:

``` r
sc <- spark_connect(master = "local")
```

## H2O Context and Flow

The call to `library(rsparkling)` automatically registered the Sparkling Water extension, which in turn specified that the [Sparkling Water Spark package](https://spark-packages.org/package/h2oai/sparkling-water) should be made available for Spark connections. Let's inspect the `H2OContext` for our Spark connection:

``` r
h2o_context(sc)
```

    ## <jobj[6]>
    ##   class org.apache.spark.h2o.H2OContext
    ##   
    ## Sparkling Water Context:
    ##  * H2O name: sparkling-water-jjallaire_-1482215501
    ##  * number of executors: 1
    ##  * list of used executors:
    ##   (executorId, host, port)
    ##   ------------------------
    ##   (driver,localhost,54323)
    ##   ------------------------
    ## 
    ##   Open H2O Flow in browser: http://127.0.0.1:54323 (CMD + click in Mac OSX)
    ## 

We can also view the H2O Flow web UI:

``` r
h2o_flow(sc)
```

## H2O with Spark DataFrames

As an example, let's copy the mtcars dataset to to Spark so we can access it from H2O Sparkling Water:

``` r
library(dplyr)
mtcars_tbl <- copy_to(sc, mtcars, overwrite = TRUE)
mtcars_tbl
```

    ## Source:   query [?? x 11]
    ## Database: spark connection master=local[8] app=sparklyr local=TRUE
    ## 
    ##      mpg   cyl  disp    hp  drat    wt  qsec    vs    am  gear  carb
    ##    <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1   21.0     6 160.0   110  3.90 2.620 16.46     0     1     4     4
    ## 2   21.0     6 160.0   110  3.90 2.875 17.02     0     1     4     4
    ## 3   22.8     4 108.0    93  3.85 2.320 18.61     1     1     4     1
    ## 4   21.4     6 258.0   110  3.08 3.215 19.44     1     0     3     1
    ## 5   18.7     8 360.0   175  3.15 3.440 17.02     0     0     3     2
    ## 6   18.1     6 225.0   105  2.76 3.460 20.22     1     0     3     1
    ## 7   14.3     8 360.0   245  3.21 3.570 15.84     0     0     3     4
    ## 8   24.4     4 146.7    62  3.69 3.190 20.00     1     0     4     2
    ## 9   22.8     4 140.8    95  3.92 3.150 22.90     1     0     4     2
    ## 10  19.2     6 167.6   123  3.92 3.440 18.30     1     0     4     4
    ## # ... with more rows

The use case we'd like to enable is calling the H2O algorithms and feature transformers directly on Spark DataFrames that we've manipulated with dplyr. This is indeed supported by the Sparkling Water package. Here is how you convert a Spark DataFrame into an H2O Frame:

``` r
mtcars_hf <- as_h2o_frame(sc, mtcars_tbl)
mtcars_hf
```

    ## <jobj[103]>
    ##   class water.fvec.H2OFrame
    ##   Frame frame_rdd_39 (32 rows and 11 cols):
    ##                        mpg  cyl                disp   hp                drat                  wt                qsec  vs  am  gear  carb
    ##     min               10.4    4                71.1   52                2.76               1.513                14.5   0   0     3     1
    ##    mean          20.090625    6          230.721875  146           3.5965625             3.21725  17.848750000000003   0   0     3     2
    ##  stddev  6.026948052089104    1  123.93869383138194   68  0.5346787360709715  0.9784574429896966  1.7869432360968436   0   0     0     1
    ##     max               33.9    8               472.0  335                4.93               5.424                22.9   1   1     5     8
    ## missing                0.0    0                 0.0    0                 0.0                 0.0                 0.0   0   0     0     0
    ##       0               21.0    6               160.0  110                 3.9                2.62               16.46   0   1     4     4
    ##       1               21.0    6               160.0  110                 3.9               2.875               17.02   0   1     4     4
    ##       2               22.8    4               108.0   93                3.85                2.32               18.61   1   1     4     1
    ##       3               21.4    6               258.0  110                3.08               3.215               19.44   1   0     3     1
    ##       4               18.7    8               360.0  175                3.15                3.44               17.02   0   0     3     2
    ##       5               18.1    6               225.0  105                2.76                3.46               20.22   1   0     3     1
    ##       6               14.3    8               360.0  245                3.21                3.57               15.84   0   0     3     4
    ##       7               24.4    4               146.7   62                3.69                3.19                20.0   1   0     4     2
    ##       8               22.8    4               140.8   95                3.92                3.15                22.9   1   0     4     2
    ##       9               19.2    6               167.6  123                3.92                3.44                18.3   1   0     4     4
    ##      10               17.8    6               167.6  123                3.92                3.44                18.9   1   0     4     4
    ##      11               16.4    8               275.8  180                3.07                4.07                17.4   0   0     3     3
    ##      12               17.3    8               275.8  180                3.07                3.73                17.6   0   0     3     3
    ##      13               15.2    8               275.8  180                3.07                3.78                18.0   0   0     3     3
    ##      14               10.4    8               472.0  205                2.93                5.25               17.98   0   0     3     4
    ##      15               10.4    8               460.0  215                 3.0               5.424               17.82   0   0     3     4
    ##      16               14.7    8               440.0  230                3.23               5.345               17.42   0   0     3     4
    ##      17               32.4    4                78.7   66                4.08                 2.2               19.47   1   1     4     1
    ##      18               30.4    4                75.7   52                4.93               1.615               18.52   1   1     4     2
    ##      19               33.9    4                71.1   65                4.22               1.835                19.9   1   1     4     1


## Sparkling Water: H2O Machine Learning

Using the same mtcars dataset, here is an example where we train a Gradient Boosting Machine (GBM) to predict "mpg".

First, we do a library call to h2o:

```
library(h2o)
```

### Prep data:
Define the response, `y`, and set of predictor variables, `x`:

``` r
y <- "mpg"
x <- setdiff(names(mtcars_hf), y)
```

Let's split the data into a train and test set using H2O.  The `h2o.splitFrame` function defaults to a 75-25 split (`ratios = 0.75`), but here we will make a 70-30 train-test split:

``` r
# Split the mtcars H2O Frame into train & test sets
splits <- h2o.splitFrame(mtcars_hf, ratios = 0.7, seed = 1)
```

### Training: 
Now train an H2O GBM using the training H2OFrame.

``` r
fit <- h2o.gbm(x = x, 
               y = y, 
               training_frame = splits[[1]],
               min_rows = 1,
               seed = 1)
print(fit)
```

```
Model Details:
==============

H2ORegressionModel: gbm
Model ID:  GBM_model_R_1474763476171_1 
Model Summary: 
  number_of_trees number_of_internal_trees model_size_in_bytes min_depth
1              50                       50               14807         5
  max_depth mean_depth min_leaves max_leaves mean_leaves
1         5    5.00000         17         21    18.64000


H2ORegressionMetrics: gbm
** Reported on training data. **

MSE:  0.001211724
RMSE:  0.03480983
MAE:  0.02761402
RMSLE:  0.001929304
Mean Residual Deviance :  0.001211724
```


### Model Performance:

We can evaluate the performance of the GBM by evaluating its performance on a test set.
 
``` r
perf <- h2o.performance(fit, newdata = splits[[2]])
print(perf)
```

```
H2ORegressionMetrics: gbm

MSE:  2.707001
RMSE:  1.645297
MAE:  1.455267
RMSLE:  0.08579109
Mean Residual Deviance :  2.707001
```


### Prediction: 

To generate predictions on a test set, you do the following.  This will return an H2OFrame with a single (or multiple) columns of predicted values.  If regression, it will be a single colum, if binary classification it will be 3 columns and in multi-class prediction it will be C+1 columns (where C is the number of classes).

``` r
pred_hf <- h2o.predict(fit, newdata = splits[[2]])
head(pred_hf)
```
```
   predict
1 21.39512
2 16.92804
3 15.19558
4 20.47695
5 20.47695
6 15.24433
```			


Now let's say you want to make this H2OFrame available to Spark.  You can convert an H2OFrame into a Spark DataFrame using the `as_spark_dataframe` function:

``` r
pred_sdf <- as_spark_dataframe(sc, pred_hf)
head(pred_sdf)
```
```
Source:   query [?? x 1]
Database: spark connection master=local[8] app=sparklyr local=TRUE

   predict
     <dbl>
1 21.39512
2 16.92804
3 15.19558
4 20.47695
5 20.47695
6 15.24433
```

### H2O Machine Learning Tutorials

If you are new to H2O for machine learning, we recommend you start with the [Intro to H2O Tutorial](https://github.com/h2oai/h2o-tutorials/blob/master/h2o-open-tour-2016/chicago/intro-to-h2o.R), followed by the [H2O Grid Search & Model Selection Tutorial](https://github.com/h2oai/h2o-tutorials/blob/master/h2o-open-tour-2016/chicago/grid-search-model-selection.R).  There are a number of other H2O R [tutorials](https://github.com/h2oai/h2o-tutorials) and [demos](https://github.com/h2oai/h2o-3/tree/master/h2o-r/demos) available, as well as the [H2O World 2015 Training Gitbook](http://learn.h2o.ai/content/), and the [Machine Learning with R and H2O Booklet (pdf)](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/booklets/RBooklet.pdf). 


## Logs & Disconnect

Look at the Spark log from R:

``` r
spark_log(sc, n = 100)
```

Now we disconnect from Spark, this will result in the H2OContext being stopped as well since it's owned by the spark shell process used by our Spark connection:

``` r
spark_disconnect(sc)
```
