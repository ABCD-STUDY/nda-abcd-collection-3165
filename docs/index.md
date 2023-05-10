# ABCD-BIDS Community Collection (ABCC) Documentation Summary

The ABCC read-the-docs page contains extensive documentation on the data, tools, and utilities that can be used to access, download, analyze, and share derived ABCD datasets. 

The ABCC houses a community-shared and continually updated ABCD neuroimaging dataset available under Brain Imaging Data Structure (BIDS) standards. Source data are converted to BIDS from the [NIMH Data Archive (NDA) share of ABCD fast-track data](https://nda.nih.gov/edit_collection.html?id=2573). Only data that passed the Data Analysis Imaging Center (DAIC) quality control are included. More information on the data available, including the different processed derivatives available, can be found in the [**Release Notes**](https://collection3165.readthedocs.io/en/stable/release_notes/).

In addition to the data, the ABCC read-the-docs provides helpful links to how to use, process, and analyze ABCD data. The documentation guide below will take users to different sections for more information.

## 2. Background

As a community share, the ABCC enables researchers to access **available derivatives** and share their **own derivatives.**. The ABCD-BIDS datasets are continually updated as new ABCD releases become available. A list of currently available datasets are provided below.

1. `BIDS inputs` The input DICOM data to this [BIDS version 1.2.0](https://www.nature.com/articles/sdata201644) data collection were retrieved from the [NIMH Data Archive (NDA) share of ABCD fast-track data](https://nda.nih.gov/edit_collection.html?id=2573) and were last accessed on May 1, 2019. BIDS input data were converted from DICOMs using [Dcm2Bids](https://github.com/cbedetti/Dcm2Bids). 
2. `abcd-hcp-pipeline` BIDS derivatives data were derived from the [DCAN Labs ABCD-BIDS MRI processing pipeline](https://doi.org/10.5281/zenodo.2587210) which outputs [Human Connectome Project (HCP) Minimal Preprocessing Pipelines-style data](https://doi.org/10.1016/j.neuroimage.2013.04.127) in both volume and surface spaces. This collection is independent from ABCD Data Collection 2573. Users may access ABCD DICOM files via the ABCD fast-track imaging data release in Collection 2573.
3. `abcd-task-hcp-pipeline`
4. `freesurfer-5.3.0-HCP` segmentation statistics and surface morphometrics from the FreeSurfer stage within the [DCAN Labs ABCD-BIDS MRI processing pipeline](https://doi.org/10.5281/zenodo.2587210) are provided here.
5. `QSIPrep`
6. `fMRIPrep`

# How to Contribute

If you would like to contribute to this effort, please visit our [Git NDA Uploads Repository](https://github.com/ABCD-STUDY/nda-abcd-collection-3165).

Latest updates are detailed below.

# Collection News

## Coming Soon:

- Year 2 BIDS inputs and abcd-hcp-pipeline derivatives

- Additional year 1 BIDS input and abcd-hcp-pipeline derivatives

- The timeseries data will be reprocessed with an updated version of the abcd-hcp-pipeline (v1.0.3) with improved bandpass filtering to the BOLD data. The new implementation zero pads the BOLD data prior to filtering to minimize distortions at the beginning and ending timepoints. It's important to note that this is not a bug, but rather an improvement. This release does not invalidate previous results, it reduces variance towards the beginning and end of the time-series data. In the previous release, those frames are labeled as "outliers" and discarded according to the provided mask. Using these updated timeseries users should be able to include more data in their analyses.

- New version of [QSIPrep](https://qsiprep.readthedocs.io/en/stable/) v0.14.2 year 1 derivatives.
  - There was in issue for some subjects in distortion correction that resulted in very inaccurate distortion correction results. This was due to TOPUP being given a denoised b=0 image from the DWI series and a raw b=0 image in the opposite phase encoding direction (taken from the image in the fmap/ directory). We updated QSIPrep to use the unprocessed b=0 images in both phase encoding directions, which resulted in TOPUP performing as expected.
   
    The bug affected a subset of subjects, but it is worth suggesting that anyone using the initial data re-calculate their analysis using the updated version.

- New version of [fMRIPrep](https://fmriprep.org/) 23.0.0rc0 year 1 derivatives. For specifics on what has changed since fMRIprep v20.2.0 and fMRIprep 23.0.0rc0, see the change log for the software [here](https://fmriprep.org/en/stable/changes.html).
  - Improved distortion correction
  - Improved bold projection to surface
  - New CIFTI outputs
  - T2w in T1w volume space

- Change to participants.tsv format
  - The combined race & ethnicity variable from v1.0.1 has been replaced with more descriptive individual race columns.
