![banner image](https://github.com/eMapR/LT-GEE/blob/master/docs/lt_gee_symbols_small.png)

# **LT-GEE**

**LandTrendr (Landsat-based detection of trends in disturbance and recovery) implementation in the Google Earth Engine platform**

## Introduction

LandTrendr is set of spectral-temporal segmentation algorithms that are useful for change detection in moderate resolution satellite imagery (primarily Landsat) and for generating trajectory-based spectral time series data largely absent of inter-annual signal noise. LandTrendr was originally implemented in IDL (Interactive Data Language), but with the help of engineers at Google, it has been ported to the Google Earth Engine (GEE) platform ([overview](https://earthengine.google.com/), [paper](https://github.com/eMapR/LT-GEE/blob/master/docs/gorelick_etal_2017_google_earth_engine.pdf)). The GEE framework nearly eliminates the onerous data management and image-preprocessing aspects of the IDL implementation. It is also light-years faster than the IDL implementation, where computing time is measured in minutes instead of days.

This guide is intended to introduce the basics of running LandTrendr in GEE. It walks though parameters definitions, building an image collection, and formatting the outputs for three use cases. It is assumed that you have a [GEE account](https://signup.earthengine.google.com/#!/), that you are somewhat familiar with the [GEE JavaScript API](https://developers.google.com/earth-engine/), and have a basic understanding of LandTrendr ([method](https://github.com/eMapR/LT-GEE/blob/master/docs/kennedy_etal_2010_landtrendr.pdf), [application](https://github.com/eMapR/LT-GEE/blob/master/docs/kennedy_etal_2012_disturbance_nwfp.pdf)).

The three use case examples described include:

**1. Exploration and parameterization**

LandTrendr can be run in point mode to visualize the segmentation for a pixel. This is really useful for quickly testing the performance of various parameter settings and spectral indices, as well as simply viewing and interpreting change in the x-y space of time and spectral value for both the source and LandTrendr trajectory-fitted data.

![time series](https://github.com/eMapR/LT-GEE/blob/master/docs/time_series.png)

[Example script](https://code.earthengine.google.com/3aef4bddbae77d3205b0408a84c83a4b)
<br><br><br><br>

**2. Data generation**

LandTrendr can be run in a data generation mode where every pixel time series within the bounds of a given region is segmented and a data cube containing the segmented line structure and trajectory-fitted time series stack is returned. The results are the basic building blocks for historical landscape state and change mapping.

![data stack](https://github.com/eMapR/LT-GEE/blob/master/docs/stack.gif)

[Example script](https://code.earthengine.google.com/c11bcd88ed5b3cc4ff027c7ac295a16d)
<br><br><br><br>

**3. Change mapping**

Change events can be extracted and mapped from LandTrendr's segmented line vertices. Information regarding the year of change event detection, magnitude of change, duration of change, and pre-change event spectral data can all be mapped.

![change map](https://github.com/eMapR/LT-GEE/blob/master/docs/yod_mapped.png)

[Example script](https://code.earthengine.google.com/8b247c3a18fb9cc2e2fe781724fe352e)
<br><br><br><br>

## Documentation





### Argument definitions


Each of these use cases begins with the same process of parameter definition and collection building.

***timeSeries (ImageCollection):***

Collection from which to extract trends (it's assumed that each image in the collection represents one year). The first band is used to find breakpoints, and all subsequent bands are fitted using those breakpoints.

***maxSegments (Integer):***

Maximum number of segments to be fitted on the time series.

***spikeThreshold (Float, default: 0.9):***

Threshold for dampening the spikes (1.0 means no dampening).

***vertexCountOvershoot (Integer, default: 3):***

The inital model can overshoot the maxSegments + 1 vertices by this amount. Later, it will be prunned down to maxSegments + 1.

***preventOneYearRecovery (Boolean, default: false):***

Prevent segments that represent one year recoveries.

***recoveryThreshold (Float, default: 0.25):***

If a segment has a recovery rate faster than 1/recoveryThreshold (in years), then the segment is disallowed.

***pvalThreshold (Float, default: 0.1):***

If the p-value of the fitted model exceeds this threshold, then the current model is discarded and another one is fitted using the Levenberg-Marquardt optimizer.

***bestModelProportion (Float, default: 1.25):***

Takes the model with most vertices that has a p-value that is at most this proportion away from the model with lowest p-value.

***minObservationsNeeded (Integer, default: 6):***

Min observations needed to perform output fitting.


ee.Algorithms.Test.LandTrendr()







>[Kennedy, R. E., Yang, Z., & Cohen, W. B. (2010). Detecting trends in forest disturbance and recovery using yearly Landsat time series: 1. LandTrendr—Temporal segmentation algorithms. *Remote Sensing of Environment, 114*(12), 2897-2910.]()

>[Gorelick, N., Hancher, M., Dixon, M., Ilyushchenko, S., Thau, D., & Moore, R. (2017). Google Earth Engine: Planetary-scale geospatial analysis for everyone. *Remote Sensing of Environment, 202*, 18-27.]()