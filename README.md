# SS_MSE

This repository contains wrapper R code for performing a MSE using the Stock Synthesis (SS) framework as applied to sandbar shark. A brief description of the methods follows.

A recent SS assessment model is used as the base of each operating model (OM) and as the estimating model (EM) for an MSE. The OM was subjected to additional complexity. The basic premise for this wrapper function follows Maunder's (2014) application to Pacific bluefin tuna. An MCMC simulation of the SS-based OM is run to generate future OM states of nature, while incorporating process uncertainty (including recruitment deviations, and time-invariant and time-varying parameters). We assume that the MCMC steps are completed in accordance with the SS MSE protocol (Maunder 2014) prior to calling this wrapper function. 

This wrapper function serves to modify pre-built SS input files (starter.ss, forecast.ss, data.dat, data.ctl) to reflect each new state of nature, as obtained from the MCMC process, and contains the observation model (data-generating process), estimating model (EM), harvest control rule (HCR), and implementation model. Each function is developed specifically for the current example (namely, BuildParFile.R, HCR.R, and ImplementHCR.R). The data-generating process uses SS's parametric bootstrapping protocol to generate future observed data streams with associated observation uncertainty (e.g., catch stream uncertainty, CPUE observations, length/age compositions, etc.). The EM is a simplified variant of the OM within SS, reflecting similar assumptions made in the most recent SS assessment. The HCR in this example is a threshold constant harvest rate control rule, and the implementation model reflects the recent state of the fishery and thereby includes implementation uncertainty based on recent observations within the fishery. 

This protocol was developed using SSv3.30.15. 


*Disclaimer: I did not follow best coding formatting guidelines in these scripts. Apologies.*


## Functions included:
 
### BuildParFile

`BuildParFile()` takes resampled parameter values from the completed MCMC posterior.sso file and inserts one set of parameter estimates into the ss.par file. This .par file will be read into the OM (with no parameter estimation) to generate a unique state of nature. 

Note that this function is written specifically for this sandbar shark example, and the code is somewhat clunky, as it was written before the `SS_readpar()` update to `r4ss`. There is also a second script labeled BuildParFile_BH.R, which includes the `BuildParFile()` designated for the OMs that specify a Beverton-Holt "BH" stock-recruit relationship. *These should be condensed with an `if` statement into a single function at some point.*


### EditStarterFile

`EditStarterFile()` sets the random number seed for the data-generating bootstrap process. 


### RunOM_NoHess 

`RunOM_NoHess()` runs the operating model while specifying the `-nohess` option. This is the data-generating bootstrap process. Note that the OM SS files should be pre-specified according to the SS MSE protocol (Maunder 2014), such that dummy data is included, starting values are read from the .par file, and no parameters are estimated. 


### BuildEMDatFile

This script contains the `BuildOM()` and `BuildEM()` functions.These functions take the information obtained from the 1st data-generating bootstrap process and inserts expected historical values in the OM and observed historical values in the EM. These functions are only to build the OM and EM data files in the first time-step. 


### UpdateEMDatFile

This script contains the `UpdateOM()` and `UpdateEM()` functions. These take the new information obtained from the data-generating bootstrap process and inserts expected future values in the OM and observed future data in the EM. These functions are called each time step beyond the first. 


### RunEM

`RunEM()` runs the estimation model after observation uncertainty has been added via the results of the data-generating process. 


### HCR

`HCR()` function applies the harvest control rule. This function is a customizable threshold constant F control rule. 


### ImplementHCR

`ImplementHCR()` takes the results from the HCR and applies the ACL (quota) to the fishery. This includes allocating commercial catch among commercial fisheries and estimating future catches that are not specified by the commercial quota (in the sandbar shark example: Mexican + Recreational catches, and Menhaden bycatch). Note that this function must be specific to each example and is not generalizeable. Further note that a limitation of this approach is that catch of all fisheries is assumed be constant within a time block; for example, if the stock assessment frequency is 5 years, then catches will be constant (with unique implementation and observation uncertainty) for the next 5 years. 


### MSE_Master 

Designed to pull in all the other function scripts and loop over time-steps to complete the MSE simulation. 


\


**Reference**

Maunder MN (2014) Management strategy evaluation (MSE) implementation in Stock Synthesis: application to Pacific bluefin tuna. IATTC Stock Assessment Report. 15: 100-117.


### Disclaimer
This repository is a scientific product and is not official communication of the National Oceanic and Atmospheric Administration, or the United States Department of Commerce. All NOAA GitHub project code is provided on an ‘as is’ basis and the user assumes responsibility for its use. Any claims against the Department of Commerce or Department of Commerce bureaus stemming from the use of this GitHub project will be governed by all applicable Federal law. Any reference to specific commercial products, processes, or services by service mark, trademark, manufacturer, or otherwise, does not constitute or imply their endorsement, recommendation or favoring by the Department of Commerce. The Department of Commerce seal and logo, or the seal and logo of a DOC bureau, shall not be used in any manner to imply endorsement of any commercial product or activity by DOC or the United States Government.
