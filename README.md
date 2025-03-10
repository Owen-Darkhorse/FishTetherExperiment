# FishTetherExperiment

## Project Description
Each year, the government of Ontario spends over 70 million dollars on fish species management. Developing machine learning algorithms that automatically classify fish using hydroacoustic data can help Ontario save time and resources. While a previous study led by Jessica Leivesley has applied recurrent neural network to classify Lake Trout and Small Mouth Bass with a prediction accuracy of 80%, it did not address classification of more than two species, and it relies on species labels train the model, which is costly to obtain. The Fish Tethering project aims to develop an unsupervised learning approach that classifies fish primarily based on broadband hydroacoustic data. To fill the research gap, this project will experiment Principal Component Analysis to identify characteristic frequencies, attempt and evaluate unsupervised classification such as UMAP and clustering, and compare unsupervised learning results with the benchmark: the Recurrent Neural Net.


## Folder Structure
Folder | Description
---------- | --------------------------------------------------
[Analyis_Scripts](Analysis_Scripts) | data analysis scripts
[Data](Data) | contains the raw acoustic data and Echoview processing scripts
[ExploratoryAnalysis](ExploratoryAnalysis) | contains Jupiter Notebooks and scripts to explore and filter the data, and Python scripts that builds statistical Models
[ExploratoryAnalysis/FishTrack-EDA-Tool](ExploratoryAnalysis/FishTrack-EDA-Tool) | home folder for shiny app
[ProcessedData](ProcessedData) | contains data that has been generated from data in *Data*, including training, validation, test split, summary statistics from models.
[NonPingData](NonPingData) | contains fish bio data
[ExportedFigures](ExportedFigures) | Figures generated from analysis scripts
WritingSections | Report writing sections


-----------------------------------------------------------------------
Below are readme from Jessica Levesley's Team
## Clone this repo
Instructions for installing GIT are here: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

Create a local copy of the repo by cloning the contents of this repo. 
From the command line:
```
git clone https://github.com/WidebandPingFest/FishTetherExperiment.git
```
From within RStudio:
1. File ->  New Project
2. From Version Control
3. From Git
4. Repository URL = https://github.com/WidebandPingFest/FishTetherExperiment.git

## Folder Structure
Folder | Description
---------- | --------------------------------------------------
[Analyis_Scripts](Analysis_Scripts) | data analysis scripts
[Data](Data) | contains the raw acoustic data and Echoview processing scripts
[ExploratoryAnalysis](ExploratoryAnalysis) | contains code to explore and filter the data
[ExploratoryAnalysis/FishTrack-EDA-Tool](ExploratoryAnalysis/FishTrack-EDA-Tool) | home folder for shiny app
[ProcessedData](ProcessedData) | contains data that has been generated from data in *Data*
[NonPingData](NonPingData) | contains fish bio data
[ExportedFigures](ExportedFigures) | Figures generated from analysis scripts
WritingSections | Report writing sections

## FishTrack-EDA-Tool
A shiny app that provides visual representation of fish tracks within the beam and targets strengths for each quadrat. The app can be viewed at: [FishTrack-EDA-Tool](https://gfs8966.shinyapps.io/FishTrack-EDA-Tool/)

## Large Files
The fish track exports used in downstream models are necessarily large files. These files have been added to the [.gitignore](.gitignore) file so as to not exceed storage capacity of the Github repo. Large files need to be created locally using the instructions included below. Any additional large datasets that need to be added to the repo should use the same protocol.

In the future these files may be included in the repo using Git Large File Storage (LFS) protocol.  Details can be found at [Git LFS](https://git-lfs.github.com/). LFS Files can still be read directly from the repo by using the link obtained by viewing *raw* data. A full tutorial on how to use Git LFS is located [HERE](https://github.com/git-lfs/git-lfs/wiki/Tutorial#migrating-existing-repository-data-to-lfs). Prior to cloning the git repository to your local drive, it's necessary to have Git-LFS downloaded on your physical computer. If your local directory is not showing the large files you're looking for, ensuring the LFS software is installed should be your first troubleshooting step. Follow download steps at [Git LFS](https://git-lfs.github.com/).

## Check required R packages are installed
```
if(!("dplyr" %in% installed.packages())){
  install.packages("dplyr")
} else {cat("dplyr is installed")}

if(!("tidyverse" %in% installed.packages())){
  install.packages("tidyverse")
} else {cat("tidyverse is installed")}

if(!("readr" %in% installed.packages())){
  install.packages("readr")
} else {cat("readr is installed")}

if(!("tidyr" %in% installed.packages())){
  install.packages("tidyr")
} else {cat("tidyr is installed")}

if(!("janitor" %in% installed.packages())){
  install.packages("janitor")
} else {cat("janitor is installed")}

# Additional packages for Echoview COM
if(!("devtools" %in% installed.packages())){
  install.packages("devtools")
} else {cat("devtools is installed")}

if(!("here" %in% installed.packages())){
  install.packages("here")
} else {cat("here is installed")}

if(!("RDCOMClient" %in% installed.packages())){
  devtools::install_github("omegahat/RDCOMClient")
} else {cat("RDCOMClient is installed")}

if(!("EchoviewR" %in% installed.packages())){
  devtools::install_github('AustralianAntarcticDivision/EchoviewR')
} else {cat("EchoviewR is installed")}

```

## Creating the model data
1. Data has been processed in Echoview (v. 13) and exported as csv files. The raw acoustic exports are located in [Data](Data) with each fish having it's own folder.  
2. A preliminary filtering of the data to remove values that had large TS compensation applied. This analysis [ExploratoryAnalysis/generating_filtered_compensated_targets.R](ExploratoryAnalysis/generating_filtered_compensated_targets.R)) provides a list of single target detections that have <6 dB compensation that is used to filter the dataset for analysis.
3. On your local system, the exported acoustic data is compiled to a master data set using [Analysis_Scripts/read_tidy_export_EVfiles.R](Analysis_Scripts/read_tidy_export_EVfiles.R). This script will import and format each fish's exported data and then compile a large dataframe *ProcessedData/processed_AllFishCombined_unfiltered.csv* (and a *Rdata* version also). The script will also apply a 6dB filter to the data and write *ProcessedData/processed_AnalysisData.csv* and *ProcessedData/processed_AnalysisData.Rdata*. **Note: generating the local data can take several minutes depending on the speed of your computer.**
4. Subsequent sessions/scripts can easily access the filtered data directly from *ProcessedData/processed_AnalysisData.Rdata* to load a tibble called *processed_data*. This approach is preferred over *read.csv* or *readr::read_csv* as it avoids any accidental changes in variable types that may be introduced while reading the csv file.

### Create local data
``` {r}
# compile unfiltered data
source('Analysis_Scripts/read_tidy_export_EVfiles.R')
file.exists("ProcessedData/processed_AnalysisData.csv") # expect TRUE if model data has been created
```
### Load data from clean environment
``` r
load("ProcessedData/processed_AnalysisData.Rdata")
processed_data
```
