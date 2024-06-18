# ABCD-BIDS Community Collection (ABCC) Documentation Summary

The ABCC read-the-docs page contains extensive documentation on the data, tools, and utilities that can be used to access, download, analyze, and share derived ABCD datasets. 

The ABCC houses a community-shared and continually updated ABCD neuroimaging dataset available under Brain Imaging Data Structure (BIDS) standards. Only data that passed the Data Analysis Imaging Center (DAIC) quality control are included. More information on the data available, including the different processed derivatives available, can be found in the [**Release Notes**](https://collection3165.readthedocs.io/en/stable/release_notes/).

In addition to the data, the ABCC read-the-docs provides helpful links to how to use, process, and analyze ABCD data. The documentation guide below will take users to different sections for more information.

## Background

As a community share, the ABCC enables researchers to access **available derivatives** and share their **own derivatives**. The ABCD-BIDS datasets are continually updated as new ABCD releases become available. A list of currently available datasets are provided below.

1. `BIDS inputs` The input DICOM data to this [BIDS version 1.2.0](https://www.nature.com/articles/sdata201644) data collection were retrieved from the [NIMH Data Archive (NDA) share of ABCD fast-track data](https://nda.nih.gov/edit_collection.html?id=2573) and were last accessed on June, 2024. BIDS input data were converted from DICOMs using [Dcm2Bids](https://github.com/cbedetti/Dcm2Bids).
2. `abcd-hcp-pipeline` BIDS derivatives data were derived from the [DCAN Labs ABCD-BIDS MRI (version 0.0.3) processing pipeline](https://doi.org/10.5281/zenodo.2587210) which outputs [Human Connectome Project (HCP) Minimal Preprocessing Pipelines-style data](https://doi.org/10.1016/j.neuroimage.2013.04.127) in both volume and surface spaces. This collection is independent from ABCD Data Collection 2573. Users may access ABCD DICOM files via the ABCD fast-track imaging data release in Collection 2573.
3. `abcd-task-hcp-pipeline` a modified version of the TaskfMRIAnalysis stage of the HCP-pipeline (Glasser et al., 2013) developed at University of Vermont by Anthony Juliano, was used to process task-fmri data from the minimally processed ABCD-BIDS (Feczko et al., 2020b) processing pipeline (v.1.0) data, as well as derived ABCC data (Feczko, 2020; ABCD-3165).
4. `freesurfer-5.3.0-HCP` segmentation statistics and surface morphometrics from the FreeSurfer stage within the [DCAN Labs ABCD-BIDS MRI processing pipeline](https://doi.org/10.5281/zenodo.2587210) are provided here.
5. `fMRIPrep` fMRIPrep v20.2.0 was run on all 10,038 participants whose visit one data was successfully converted to BIDS. The limited fMRIPrep processing errors were due to subjects that did not have any valid fMRI runs, but we did not do any manual quality control of outputs. 9,484 participants have at least one output. The data is available in 18 submissions (a summary, including number of files and submission size can be found [here](https://docs.google.com/spreadsheets/d/1NbZ28vBvGVJb9miivgsJ695VVoFBSBuBQmWigN5pg_c/edit#gid=678992105)).

# How to Contribute

If you would like to contribute to this effort, please visit our [Git NDA Uploads Repository](https://github.com/ABCD-STUDY/nda-abcd-collection-3165).

Latest updates are detailed below.

# Collection News

## Coming Soon:

Year 2 and year 1  BIDS inputs and abcd-hcp-pipeline derivatives.

The timeseries data will be reprocessed with an updated version of the abcd-hcp-pipeline (v0.1.4).

There was in issue for some subjects in distortion correction that resulted in very inaccurate distortion correction results. This was due to TOPUP being given a denoised b=0 image from the DWI series and a raw b=0 image in the opposite phase encoding direction (taken from the image in the fmap/ directory). We updated QSIPrep to use the unprocessed b=0 images in both phase encoding directions, which resulted in TOPUP performing as expected. The bug affected a subset of subjects, but it is worth suggesting that anyone using the initial data re-calculate their analysis using the updated version.

New version of fMRIPrep 23.0.0rc1 year 1 derivatives. For specifics on what has changed since fMRIprep v20.2.0 and fMRIprep 23.0.0rc1, see the change log for the software here.

Improved distortion correction

Improved bold projection to surface

New CIFTI outputs

T2w in T1w volume space

Change to participants.tsv format

The combined race & ethnicity variable from v1.0.1 has been replaced with more descriptive individual race columns.

