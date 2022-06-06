# SEAI 2022 - R - Extra - rgeoda

Vincenzo Nardelli - <vincnardelli@gmail.com> -
<https://github.com/vincnardelli>

Reference: Xun Li - Geoda Tutorial
[Link](https://geodacenter.github.io/rgeoda/articles/rgeoda_tutorial.html)

`rgeoda` is an R library for spatial data analysis. It is an R wrapper
of the libgeoda C++ library, which is built based on the `GeoDa`
software. The version used in this tutorial is version 0.0.8.

## 1. Install `rgeoda`

The rgeoda package can be installed using “install.packages()” command:

    install.packages("rgeoda")

, and then can be loaded using the customary “library()” command:

    library(rgeoda)

    ## Loading required package: digest

In addition, the package sf needs to be loaded, since it is a
dependency:

    library(sf)

    ## Linking to GEOS 3.10.2, GDAL 3.4.2, PROJ 8.2.1; sf_use_s2() is TRUE

## 2. Load Spatial Data

The rgeoda package for R relies on the sf (simple features) package for
basic spatial data handling functions. In a typical R workflow, one
first reads a shape file or other GIS format file with the data using
the sf st\_read(file path) command. For example, to load the ESRI
Shapefile `Guerry.shp` comes with the package:

    guerry_path <- system.file("extdata", "Guerry.shp", package = "rgeoda")
    guerry <- st_read(guerry_path)

    ## Reading layer `Guerry' from data source 
    ##   `/Library/Frameworks/R.framework/Versions/4.2/Resources/library/rgeoda/extdata/Guerry.shp' 
    ##   using driver `ESRI Shapefile'
    ## Simple feature collection with 85 features and 29 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 47680 ymin: 1703258 xmax: 1031401 ymax: 2677441
    ## Projected CRS: NTF (Paris) / Lambert zone II

Once the spatial object has been created, it can be used to compute a
spatial weights matrix using one of the several weights functions in
rgeoda.

## 3. Spatial Weights

Spatial weights are central components in spatial data analysis. The
spatial weights represent the possible spatial interactions between
observations in space. `rgeoda` provides 6 functions to create 4
different types of spatial weights:

-   Contiguity Based Weights: `queen_weights()`, `rook_weights()`
-   Distance Based Weights: `distance_weights()`
-   K-Nearest Neighbor Weights: `knn_weights()`
-   Kernel Weights: `distance_weights()` and `knn_weights()` with kernel
    parameters

### 3.1 Queen Contiguity Weights

Contiguity means that two spatial units share a common border of
non-zero length. Operationally, we can further distinguish between a
rook and a queen criterion of contiguity, in analogy to the moves
allowed for the such-named pieces on a chess board. The queen criterion
is somewhat more encompassing and defines neighbors as spatial units
sharing a common edge or a common vertex.

To create a Queen contiguity weights, one can call the function

    queen_weights(sf_obj, order=1, include_lower_order = False, precision_threshold = 0)

For example, to create a Queen contiguity weights using the sf object
`guerry`:

    queen_w <- queen_weights(guerry)
    summary(queen_w)

    ##                      name              value
    ## 1 number of observations:                 85
    ## 2          is symmetric:                TRUE
    ## 3               sparsity: 0.0581314878892734
    ## 4        # min neighbors:                  2
    ## 5        # max neighbors:                  8
    ## 6       # mean neighbors:   4.94117647058824
    ## 7     # median neighbors:                  5
    ## 8           has isolates:              FALSE

The function `queen_weights()` returns an instance of `Weight` object.
One can access the meta data of the spatial weights by accessing the
attributes of `GeoDaWeight` object:

#### Attributes of `Weight` object

    is_symmetric(queen_w)

    ## [1] TRUE

    has_isolates(queen_w)

    ## [1] FALSE

    weights_sparsity(queen_w)

    ## [1] 0.05813149

To access the details of the weights: e.g. list the neighbors of a
specified observation:

    nbrs <- get_neighbors(queen_w, idx = 1)
    cat("\nNeighbors of the 1-st observation are:", nbrs)

    ## 
    ## Neighbors of the 1-st observation are: 36 37 67 69

To compute the spatial lag of a specified observation by passing the
values of the selected variable:

    lag <- spatial_lag(queen_w, guerry['Crm_prs'])
    lag

    ##    Spatial.Lag
    ## 1     23047.50
    ## 2     26919.67
    ## 3     26195.50
    ## 4     14401.25
    ## 5     15038.67
    ## 6     15749.00
    ## 7     22111.67
    ## 8     13672.33
    ## 9     22859.20
    ## 10    11475.20
    ## 11    12200.14
    ## 12    13278.67
    ## 13    24734.00
    ## 14    11752.83
    ## 15    17992.60
    ## 16    21974.40
    ## 17    26711.00
    ## 18    19344.00
    ## 19    23696.71
    ## 20    25108.67
    ## 21    21643.17
    ## 22    18306.00
    ## 23    24280.00
    ## 24    14451.60
    ## 25    21047.67
    ## 26    21421.33
    ## 27    25961.50
    ## 28    10869.83
    ## 29    13415.67
    ## 30    17172.17
    ## 31    20238.25
    ## 32    12504.25
    ## 33    26723.00
    ## 34    21772.83
    ## 35    26462.20
    ## 36    19252.00
    ## 37    24683.20
    ## 38    20607.25
    ## 39    24412.00
    ## 40    19373.71
    ## 41    16000.20
    ## 42    23993.25
    ## 43    20337.86
    ## 44    16818.67
    ## 45    17113.83
    ## 46    13013.00
    ## 47    22133.00
    ## 48    24093.75
    ## 49    25661.67
    ## 50    22190.17
    ## 51    29030.00
    ## 52    16951.00
    ## 53    24509.00
    ## 54    24982.75
    ## 55    19491.50
    ## 56    24176.00
    ## 57    27639.67
    ## 58    21274.33
    ## 59    24510.33
    ## 60    30166.00
    ## 61    23459.00
    ## 62    16184.00
    ## 63    18002.00
    ## 64    10910.00
    ## 65    16251.25
    ## 66    15572.00
    ## 67    25884.25
    ## 68    23020.60
    ## 69    26495.00
    ## 70    24690.50
    ## 71    17339.00
    ## 72    25522.33
    ## 73    18970.00
    ## 74    19701.83
    ## 75    21841.00
    ## 76    24520.40
    ## 77    14025.80
    ## 78    14565.17
    ## 79    13306.67
    ## 80    12579.00
    ## 81    21529.50
    ## 82    23474.50
    ## 83    24373.17
    ## 84    19900.50
    ## 85    23373.60

### 3.2 Rook Contiguity Weights

The rook criterion defines neighbors by the existence of a common edge
between two spatial units. To create a Rook contiguity weights, one can
call function:

    rook_weights(sf_obj, order=1,include_lower_order=False, precision_threshold = 0)

For example, to create a Rook contiguity weights using the sf object
`guerry`:

    rook_w <- rook_weights(guerry)
    summary(rook_w)

    ##                      name              value
    ## 1 number of observations:                 85
    ## 2          is symmetric:                TRUE
    ## 3               sparsity: 0.0581314878892734
    ## 4        # min neighbors:                  2
    ## 5        # max neighbors:                  8
    ## 6       # mean neighbors:   4.94117647058824
    ## 7     # median neighbors:                  5
    ## 8           has isolates:              FALSE

The weights we created are in memory. To save the weights to a file, one
can call the function:

    save_weights(gda_w, id_variable, out_path, layer_name = "")

The `id_variable` defines the unique value of each observation when
saving a weights file

The `layer_name` is the layer name of loaded dataset. For a ESRI
shapefile, the layer name is the file name without the suffix
(e.g. Guerry).

For example, using Guerry dataset, the column “CODE\_DE” can be used as
a key to save a weights file:

    save_weights(rook_w, guerry['CODE_DE'], out_path = '/Users/xun/Downloads/Guerry_r.gal', 
                 layer_name = 'Guerry')

    ## [1] FALSE

### 3.3 Distance Based Weights

The most straightforward spatial weights matrix constructed from a
distance measure is obtained when i and j are considered neighbors
whenever j falls within a critical distance band from i. In order to
start the distance based neighbors, we first need to compute a threshold
value. `rgeoda` provides a function `min_distthreshold` to help you find
a optimized distance threshold that guarantees that every observation
has at least one neighbor:

    min_distthreshold(GeoDa gda, bool is_arc = False, is_mile = True)
    To create a Distance based weights, one can call the function `distance_weights`:

Then, with this distance threshold, we can create a distance-band
weights using the function:

    distance_weights(geoda_obj, dist_thres, power=1.0,  is_inverse=False, is_arc=False, is_mile=True)

For example:

    dist_thres <- min_distthreshold(guerry)
    dist_thres

    ## [1] 96726.14

    dist_w <- distance_weights(guerry, dist_thres)
    summary(dist_w)

    ##                      name              value
    ## 1 number of observations:                 85
    ## 2          is symmetric:                TRUE
    ## 3               sparsity: 0.0434602076124567
    ## 4        # min neighbors:                  1
    ## 5        # max neighbors:                  7
    ## 6       # mean neighbors:   3.69411764705882
    ## 7     # median neighbors:                  4
    ## 8           has isolates:              FALSE

### 3.4 K-Nearest Neighbor Weights

A special case of distance based weights is K-Nearest neighbor weights,
in which every obersvation will have exactly k neighbors. It can be used
to avoid the problem of isolate in distance-band weights when a smaller
cut-off distance is used. To create a KNN weights, we can call the
function `knn_weights`:

    knn_weights(gda, k, power = 1.0,is_inverse = False, is_arc = False, is_mile = True)

For example, to create a 6-nearest neighbor weights using Guerry:

    knn6_w <- knn_weights(guerry, 6)
    summary(knn6_w)

    ##                      name              value
    ## 1 number of observations:                 85
    ## 2          is symmetric:               FALSE
    ## 3               sparsity: 0.0705882352941176
    ## 4        # min neighbors:                  6
    ## 5        # max neighbors:                  6
    ## 6       # mean neighbors:                  6
    ## 7     # median neighbors:                  6
    ## 8           has isolates:              FALSE

### 3.5 Kernel Weights

Kernel weights apply kernel function to determine the distance decay in
the derived continuous weights kernel. The kernel weights are defined as
a function K(z) of the ratio between the distance dij from i to j, and
the bandwidth hi, with z=dij/hi.

The kernel functions include

-   triangular
-   uniform
-   quadratic
-   epanechnikov
-   quartic
-   gaussian

Two functions are provided in `rgeoda` to create kernel weights.

#### Use `kernel_weights` for Kernel Weights with adaptive bandwidth

To create a kernel weights with fixed bandwith:

    bandwidth <- min_distthreshold(guerry)
    kernel_w <- kernel_weights(guerry, bandwidth, kernel_method = "uniform")
    summary(kernel_w)

    ##                      name              value
    ## 1 number of observations:                 85
    ## 2          is symmetric:               FALSE
    ## 3               sparsity: 0.0434602076124567
    ## 4        # min neighbors:                  1
    ## 5        # max neighbors:                  7
    ## 6       # mean neighbors:   3.69411764705882
    ## 7     # median neighbors:                  4
    ## 8           has isolates:              FALSE

The arguments `is_inverse`, `power`, `is_arc` and `is_mile` are the same
with the distance based weights. Additionally, `kernel_weights` has
another argument that user can specify:

    use_kernel_diagonals    
    (optional) FALSE (default) or TRUE, apply kernel on the diagonal of weights matrix

#### Use `kernel_knn_weights` for Kernel Weights with adaptive bandwidth

To create a kernel weights with adaptive bandwidth or using max Knn
distance as bandwidth:

    adptkernel_w = kernel_knn_weights(guerry, 6, "uniform")

    summary(adptkernel_w)

    ##                      name              value
    ## 1 number of observations:                 85
    ## 2          is symmetric:               FALSE
    ## 3               sparsity: 0.0705882352941176
    ## 4        # min neighbors:                  6
    ## 5        # max neighbors:                  6
    ## 6       # mean neighbors:                  6
    ## 7     # median neighbors:                  6
    ## 8           has isolates:              FALSE

This kernel weights function two more arguments that user can specify:

    adaptive_bandwidth  
    (optional) TRUE (default) or FALSE: TRUE use adaptive bandwidth calculated using distance of k-nearest neithbors, FALSE use max distance of all observation to their k-nearest neighbors

    use_kernel_diagonals    
    (optional) FALSE (default) or TRUE, apply kernel on the diagonal of weights matrix

## 4 Local Indicators of Spatial Association–LISA

`rgeoda` provides following methods for local spatial autocorrelation
statistics:

-   Local Moran: local\_moran(), local\_moran\_eb()
-   Local Geary: local\_geary(), local\_multigeary()
-   Local Getis-Ord statistics: local\_g() and local\_gstar()
-   Local Join Count: local\_joincount(), local\_bijoincount(),
    local\_multijoincount()
-   Quantile LISA: local\_quantilelisa(), local\_multiquantilelisa()
-   Local Neighbor Match Test: neighbor\_match\_test()

For more information about the local spatial autocorrelation
statisticis, please read Dr. Luc Anselin’s lab notes:
<http://geodacenter.github.io/workbook/6a_local_auto/lab6a.html>.

### 4.1 Local Moran

The Local Moran statistic is a method to identify local clusters and
local spatial outliers. For example, we can call the function
`local_moran()` with the created Queen weights and the data “crm\_prp =
guerry\[‘Crm\_prp’\]” as input parameters:

    crm_prp = guerry["Crm_prp"]
    lisa <- local_moran(queen_w, crm_prp)

The `local_moran()` function will return a `lisa` object, and we can
access its values/results of lisa computation using the following
functions:

-   lisa\_clusters(): Get the local cluster indicators returned from
    LISA computation.
-   lisa\_colors(): Get the cluster colors of LISA computation.
-   lisa\_labels(): Get the cluster labels of LISA computation.
-   lisa\_values(): Get the local spatial autocorrelation values
    returned from LISA computation.
-   lisa\_num\_nbrs(): Get the number of neighbors of every observations
    in LISA computation.
-   lisa\_pvalues(): Get the local pseudo-p values of significance
    returned from LISA computation.
-   lisa\_fdr(): Get the False Discovery Rate (FDR) in LISA.
-   lisa\_bo(): Get the False Discovery Rate (FDR) in LISA.

For example, we can call the function `lisa_values()` to get the values
of the local Moran:

    lms <- lisa_values(gda_lisa = lisa)
    lms

    ##  [1]  0.0154319783  0.3270633224  0.0212952962  0.0046105448 -0.0028342407
    ##  [6]  0.4149377158 -0.1379463091  0.0998657692  0.2823176310  0.1218745112
    ## [11] -0.0951205417  0.0326111938  0.3878324535  1.1888723840 -0.6452792226
    ## [16] -0.3096492740  0.3662775143  2.0375343539 -0.0050154800  0.0697110572
    ## [21]  2.2720355722  0.2886391551 -0.0070189726 -0.0009906605  0.9517469793
    ## [26]  0.7648322095  0.0432039929 -0.0045362842 -0.0964911860  0.0952360887
    ## [31]  0.0100895206 -0.0109401003 -0.0544245927 -0.0345553975  0.0887531916
    ## [36]  0.0954232056  0.0383617454 -0.1776714441  0.1638208393  1.2309154898
    ## [41]  2.9077203402 -0.0396340261  0.4458735227  0.2491123240  0.0905643622
    ## [46] -0.6212977834 -0.0308773407  0.0375870399  0.2270376112 -0.0918254739
    ## [51] -0.0112400086  0.1085584763 -0.0055113129 -0.0027097589  0.7235016208
    ## [56]  0.0163129939  0.4246564560  0.3787307767 -0.0597158189  0.5050011802
    ## [61]  2.7632125275  0.0656510809  0.1771899330 -0.0572150317  0.4943795537
    ## [66]  0.2870386197 -1.4593300774 -0.0055305930  0.4895990016 -0.0324125662
    ## [71]  2.1366499813  0.9041683235  0.7053678641  1.4098290925  0.0051652159
    ## [76]  0.2238144189 -0.1621373954  0.0195632289 -0.3233724187 -0.0337778226
    ## [81]  0.0118189869 -0.1164679533 -0.5699624657 -0.0859634996  0.2085373916

To get the pseudo-p values of significance of local Moran computation:

    pvals <- lisa_pvalues(lisa)
    pvals

    ##  [1] 0.414 0.123 0.001 0.474 0.452 0.087 0.243 0.326 0.299 0.303 0.237 0.461
    ## [13] 0.248 0.015 0.178 0.166 0.124 0.003 0.456 0.346 0.053 0.145 0.431 0.425
    ## [25] 0.005 0.037 0.464 0.395 0.138 0.316 0.495 0.431 0.359 0.129 0.295 0.058
    ## [37] 0.090 0.231 0.258 0.018 0.026 0.455 0.073 0.057 0.222 0.023 0.369 0.338
    ## [49] 0.282 0.359 0.483 0.252 0.450 0.434 0.138 0.327 0.063 0.005 0.097 0.292
    ## [61] 0.001 0.217 0.237 0.126 0.145 0.344 0.008 0.340 0.079 0.300 0.033 0.142
    ## [73] 0.001 0.001 0.460 0.005 0.212 0.384 0.110 0.409 0.455 0.353 0.006 0.287
    ## [85] 0.128

To get the cluster indicators of local Moran computation:

    cats <- lisa_clusters(lisa, cutoff = 0.05)
    cats

    ##  [1] 0 0 1 0 0 0 0 0 0 0 0 0 0 1 0 0 0 1 0 0 0 0 0 0 2 2 0 0 0 0 0 0 0 0 0 0 0 0
    ## [39] 0 1 1 0 0 0 0 3 0 0 0 0 0 0 0 0 0 0 0 2 0 0 1 0 0 0 0 0 3 0 0 0 2 0 2 2 0 2
    ## [77] 0 0 0 0 0 0 3 0 0

The predefined values of the indicators of LISA cluster are:

    0 Not significant
    1 High-High
    2 Low-Low
    3 High-Low
    4 Low-High
    5 Undefined
    6 Isolated

which can be accessed via the function `lisa_labels()`:

    lbls <- lisa_labels(lisa)
    lbls

    ## [1] "Not significant" "High-High"       "Low-Low"         "Low-High"       
    ## [5] "High-Low"        "Undefined"       "Isolated"

By default, the `local_moran()` function will run with some default
parameters, e.g.:

    significance_cutoff: 0.05
    permutation: 999
    permutation_method: 'complete'
    cpu_threads: 6
    seed (for random number generator): 123456789

, which are identical to GeoDa desktop software so to replicate the
results in GeoDa software. You can set different values when calling the
lisa functions.

For example, re-run the above local Moran example using 9,999
permutations.

    lisa <- local_moran(queen_w, crm_prp, permutations = 9999)

Then, we can use the same `lisa` object to get the new results after
9,999 permutations:

    pvals <- lisa_pvalues(lisa)
    pvals

    ##  [1] 0.4187 0.1265 0.0004 0.4679 0.4545 0.0728 0.2312 0.3071 0.3115 0.3088
    ## [11] 0.2187 0.4834 0.2686 0.0102 0.2024 0.1789 0.1320 0.0020 0.4558 0.3519
    ## [21] 0.0479 0.1376 0.4441 0.4195 0.0032 0.0388 0.4733 0.4187 0.1278 0.3300
    ## [31] 0.4939 0.4427 0.3393 0.1419 0.2714 0.0606 0.0724 0.2247 0.2628 0.0185
    ## [41] 0.0214 0.4899 0.0719 0.0589 0.2288 0.0189 0.3759 0.3217 0.2812 0.3735
    ## [51] 0.4695 0.2743 0.4518 0.4286 0.1471 0.3222 0.0647 0.0025 0.0917 0.2812
    ## [61] 0.0001 0.2419 0.2462 0.1266 0.1270 0.3553 0.0094 0.3123 0.0724 0.2975
    ## [71] 0.0307 0.1320 0.0001 0.0002 0.4633 0.0056 0.2162 0.3681 0.1335 0.4069
    ## [81] 0.4536 0.3547 0.0035 0.3096 0.1277

`rgeoda` uses `GeoDa` C++ code, in which multi-threading is used to
accelerate the computation of LISA. We can use the argument `ncpu` to
specify how many threads to run the computation:

    lisa <- local_moran(queen_w, crm_prp, cpu_threads = 4)

Get the False Discovery Rate value based on current pseudo-p values:

    fdr <- lisa_fdr(lisa, 0.05)
    fdr

    ## [1] 0.0005882353

Then, one can set the FDR value as the cutoff p-value to filter the
cluster results:

    cat_fdr <- lisa_clusters(lisa, cutoff = fdr)
    cat_fdr

    ##  [1] 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
    ## [39] 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
    ## [77] 0 0 0 0 0 0 0 0 0

### 4.2 Local Geary

Local Geary is a type of LISA that focuses on squared
differences/dissimilarity. A small value of the local geary statistics
suggest positive spatial autocorrelation, whereas large values suggest
negative spatial autocorrelation. For more details, please read:
<http://geodacenter.github.io/workbook/6b_local_adv/lab6b.html#local-geary>

For example, we can call the function local\_geary() with the created
Queen weights and the data “crm\_prp” as input parameters:

    geary_crmprp <- local_geary(queen_w, crm_prp)

To get the cluster indicators of the local Geary computation:

    lisa_clusters(geary_crmprp)

    ##  [1] 0 2 4 0 3 0 0 0 0 0 0 0 0 0 0 0 0 0 0 2 1 0 0 0 2 2 0 0 0 0 0 0 0 4 0 0 0 0
    ## [39] 0 0 1 0 0 0 1 0 0 0 0 0 3 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 2 0 0 2 0 0
    ## [77] 0 0 0 0 0 0 4 0 0

To get the pseudo-p values of the local Geary computation:

    lisa_pvalues(geary_crmprp)

    ##  [1] 0.398 0.027 0.025 0.126 0.017 0.314 0.610 0.141 0.284 0.110 0.559 0.456
    ## [13] 0.211 0.255 0.226 0.211 0.089 0.054 0.182 0.017 0.030 0.216 0.395 0.105
    ## [25] 0.024 0.033 0.089 0.416 0.504 0.059 0.380 0.254 0.211 0.048 0.113 0.154
    ## [37] 0.160 0.571 0.310 0.093 0.009 0.130 0.128 0.178 0.039 0.088 0.076 0.319
    ## [49] 0.291 0.438 0.043 0.464 0.177 0.099 0.100 0.232 0.141 0.317 0.615 0.208
    ## [61] 0.198 0.299 0.084 0.634 0.148 0.423 0.060 0.108 0.293 0.257 0.032 0.102
    ## [73] 0.057 0.003 0.706 0.560 0.605 0.066 0.320 0.391 0.175 0.601 0.007 0.365
    ## [85] 0.238

## Exploratory Spatial Data Analysis

For exploratory spatial data analysis (ESDA), rgeoa provides some
utility functions to allow users to easily work with sf to visualize the
results and do exploratory spatial data analysis.

### Start from `sf` package

The sf package has been popular tool to handle geospatial data. It is a
good substitue of sp package which will be deprecated soon.

For example, we can simply call plot() function to render the first 9
chorepleth maps using the frist 9 variables in the dataset:

    plot(guerry)

    ## Warning: plotting the first 9 out of 29 attributes; use max.plot = 29 to plot
    ## all

![](seai_extra_rgeoda_files/figure-markdown_strict/unnamed-chunk-29-1.png)

### 6.2 ESDA with rgeoda

Now, with the sf object `guerry`, you can call rgeoda’s spatial analysis
functions. For example, to examine the local Moran statistics of
variable “crm\_prs” (Population per Crime against persons):

    queen_w <- queen_weights(guerry)
    lisa <- local_moran(queen_w,  guerry['Crm_prs'])

Note: rgeoda uses wkb, which is a binary representation of geometries,
to exchange data between sf and libgeoda in memory.

### Create Local Moran Map

With the LISA results, we can make a local moran cluster map:

    lisa_colors <- lisa_colors(lisa)
    lisa_labels <- lisa_labels(lisa)
    lisa_clusters <- lisa_clusters(lisa)

    plot(st_geometry(guerry), 
         col=sapply(lisa_clusters, function(x){return(lisa_colors[[x+1]])}), 
         border = "#333333", lwd=0.2)
    title(main = "Local Moran Map of Crm_prs")
    legend('bottomleft', legend = lisa_labels, fill = lisa_colors, border = "#eeeeee")

![](seai_extra_rgeoda_files/figure-markdown_strict/unnamed-chunk-31-1.png)

In the above code, we use th values of cluster indicators from
`rgeoda`’s `LISA` object are used to make the LISA map. We can save the
clusters back to the original `sf` data.frame:

    guerry['moran_cluster'] <- lisa_clusters

Checking the values of the cluster indicators, we will see they are
integer numbers 0 (not significant), 1 (high-high cluster), 2 (low-low
cluster), 3 (low-high cluster), 4 (high-low cluster), 5
(neighborless/island), 6 (undefined):

    lisa_clusters

    ##  [1] 0 1 1 0 0 2 0 2 0 2 2 0 0 2 0 0 1 0 0 0 0 0 0 2 0 0 0 2 2 0 0 2 1 0 3 0 0 0
    ## [39] 0 0 0 0 0 0 0 2 0 0 3 0 1 0 0 0 0 0 1 0 0 1 0 0 0 2 0 0 0 0 1 1 0 0 0 0 0 0
    ## [77] 2 2 0 2 0 0 0 0 0

To create a significance map that is associated with the local Moran
map, we can do the same as making the local moran cluster map using the
results from lisa\_pvalues():

    lisa_p <- lisa_pvalues(lisa)
    p_labels <- c("Not significant", "p <= 0.05", "p <= 0.01", "p <= 0.001")
    p_colors <- c("#eeeeee", "#84f576", "#53c53c", "#348124")
    plot(st_geometry(guerry), 
         col=sapply(lisa_p, function(x){
           if (x <= 0.001) return(p_colors[4])
           else if (x <= 0.01) return(p_colors[3])
           else if (x <= 0.05) return (p_colors[2])
           else return(p_colors[1])
           }), 
         border = "#333333", lwd=0.2)
    title(main = "Local Moran Map of Crm_prs")
    legend('bottomleft', legend = p_labels, fill = p_colors, border = "#eeeeee")

![](seai_extra_rgeoda_files/figure-markdown_strict/unnamed-chunk-34-1.png)
