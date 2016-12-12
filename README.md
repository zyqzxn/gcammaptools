[![Build Status](https://travis-ci.org/JGCRI/gcammaptools.svg?branch=master)](https://travis-ci.org/JGCRI/gcammaptools)

# gcammaptools

## Overview

The gcammaptools package provides functions for plotting GCAM data on
world or regional maps.  This includes functions for parsing the
output of the GCAM ModelInterface, as well as functions for making the
plots, and default projection and theme settings that provide a house
style for GCAM plots.

## Installation

This package must be installed from the github repository using
`install_github`.  You will also need to install the development
version of the [`ggalt`](https://github.com/hrbrmstr/ggalt)
package. (Installing from CRAN won't get you the right version.)  To
do this, you will need to install `devtools` first if you
don't have it already.  

``` r
install.packages('devtools')         # if you don't have it already
devtools::install_github('hrbrmstr/ggalt')
devtools::install_github('JGCRI/gcammaptools', build_vignettes=TRUE)
```  

The `build_vignettes` argument is optional, but the "examples"
vignette shows how to do several common mapping tasks.

Two of the package dependencies, `proj4` and `rgeos`, require certain
software libraries to be installed on your system.  If you don't have
those libraries installed, then the package installation will fail.
Windows and Mac users can get around this problem by installing the
binary versions of these packages:  

```
install.packages(c('proj4','rgeos'), type='binary')
```

Alternatively, you can download and install the required system
libraries yourself.  The PROJ4 library required by `proj4` is
available at [https://github.com/OSGeo/proj.4].  The GEOS library
required by `rgeos` is available at [http://trac.osgeo.org/geos/].


## Usage

### Reading GCAM data

You read the data in three stages:  
* Parse the output from the ModelInterface.
* Extract the query you want to plot.
* Translate the region names to a numerical ID.

In operation it looks like this:  
``` r
tables <- parse_mi_output(fn = 'batch-output.csv')
oil.cons <- process_batch_q(tables, 'primary_energy',
                                  'Reference', c(fuel='a oil'))
oil.cons <- addRegionID(oil.cons, lookupfile=rgn32, drops=rgn32)
```

### Plotting maps

The main plotting function is `plot_GCAM`.  You can supply your own
map of region boundaries, but most of the time you will want to use
the ones supplied with the package.  You start by merging your data
frame with the map structure, and then passing the result to
`plot_GCAM`:  
``` r
data(map.rgn32)
map.oil <- merge(map.rgn32, oil.cons, by='id')
plot_GCAM(map.oil, col='X2050', title='Oil Consumption (2050)', legend=TRUE)
```
