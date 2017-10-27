# Lesson on biodiversity data and species distribution models

## Overview
Generate range maps for butterfly-host plant interactions and predict shifts 
distributional shifts using publicly available biodiversity data and data science tools

Originally forked from [https://github.com/jcoliver/ebutterfly-sdm.git](https://github.com/jcoliver/ebutterfly-sdm.git)

## Dependancies
Four additional R packages are required:

+ raster
+ sp
+ dismo
+ maptools

## Deliverables for SDM lesson
1. Instructions / setup for where csv files with latitude/longitude data belong
2. SDM code based on bioclim (current) data
3. SDM code based on forecast data [http://www.worldclim.org/CMIP5v1](http://www.worldclim.org/CMIP5v1)
    + Instructions for getting these data can be found in the documentation
    for `raster::getData`. e.g. `getData('CMIP5', var='tmin', res=10, rcp=85, model='AC', year=70)`

For 2, the basic workflow will be:
1. Run the models & create single-species raster files
    + data/butterfly.csv -> butterfly.model -> butterfly.map (raster)
    + data/plant.csv -> plant.model -> plant.map (raster)
2. Take the butterfly map and composite plant map and overlay the former on the
latter; have some means of showing areas we predict the plant(s) but not the bug
(vice-versa should not happen often, but if it does, it's a discussion point)

For 3, the predictions would be based on the models developed in 2.1
1. Run predictions based on forecast climate data (CMIP5) and models
    + data/butterfly.csv -> butterfly.model + CMIP5 -> butterfly.forecast (raster)
    + data/plant.csv -> plant.model  + CMIP5 -> plant.forecast (raster)
2. Add together as before and...what would be the most useful output?
    + Separate range maps for butterfly and plant(s)? This could have 
    current and future ranges on one another
        + raster pixels would have values of 1, 2, 1 by default; would 
        need to change one raster [future?] so values would end up being unique; 
        i.e. turn all 1's to 3, so when they are summed, pixels have possible 
        values of 1, 4, and 3, corresponding to current, current + future, and 
        future ranges); would then probably re-number yet again to something 
        like 1, 3, 2, so coloring scheme is easy to interpret
    + Combined map of butterfly and plant(s)? See previous point on combining
    rasters so they caon be interpreted in a reasonable way

## MVP
+ Instructions for data download and where to put data
+ Code to create rasters and models for [2]
+ Code to use models from [2] and forecast data to create maps for [3]

**TODO** how are we going to get the threshold without training/testing data...

## Structure
+ data
  + inaturalist: data harvested from [iNaturalist](http://www.inaturalist.org)
    + 50931-iNaturalist.txt: Gray Hairstreak, _Strymon melinus_
    + 509627-iNaturalist.txt: Western Giant Swallowtail, _Papilio rumiko_
    + 59125-iNaturalist.txt: Great Copper, _Lycaena xanthoides_
  + wc2-5: climate data at 2.5 minute resolution from [WorldClim](http://www.worldclim.org)
+ output (not included in repository, but this structure is assumed on local)
  + images
  + rasters
+ scripts
  + get-observation-data.R: Harvest data from iNaturalist using their API; 
  called from command line terminal
    + Usage: `Rscript --vanilla get-observation-data.R <taxon_id>`
    + Example: `Rscript --vanilla get-observation-data.R 60606`
  + run-sdm.R: Run species distribution model and create map and raster output; 
  called from command line terminal
    + Usage: `Rscript --vanilla run-sdm.R <path/to/data/file> <output-file-prefix> <path/to/output/directory/>`
    + Example: `Rscript --vanilla run-sdm.R data/inaturalist/60606-iNaturalist.txt 60606 output/`
  + sdm-for-ACIC-lecture.R: Script to create map graphic used in ACIC lecture
  + sdm-iNat-melinus.R: Pilot species distribution modeling for _Strymon melinus_
  + sdm-iNat-xanthoides.R: Pilot species distribution modeling for _Lycaena xanthoides_

## Resources
### Species distribution models in R
+ [Vignette for `dismo` package](https://cran.r-project.org/web/packages/dismo/vignettes/sdm.pdf)
+ [Fast and flexible Bayesian species distribution modelling using Gaussian processes](http://onlinelibrary.wiley.com/doi/10.1111/2041-210X.12523/pdf)
+ [Species distribution models in R](http://www.molecularecologist.com/2013/04/species-distribution-models-in-r/)
+ [Run a range of species distribution models](https://rdrr.io/cran/biomod2/man/BIOMOD_Modeling.html)
+ [SDM polygons on a Google map](https://rdrr.io/rforge/dismo/man/gmap.html)
+ [R package 'maxnet' for functionality of Java maxent package](https://cran.r-project.org/web/packages/maxnet/maxnet.pdf)

### Tests of spatial overlap
+ [http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0056568](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0056568)
+ [http://onlinelibrary.wiley.com/doi/10.1111/geb.12455/pdf](http://onlinelibrary.wiley.com/doi/10.1111/geb.12455/pdf)

### iNaturalist
+ [API documentation](https://www.inaturalist.org/pages/api+reference)