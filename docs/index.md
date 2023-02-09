# ABCD-BIDS Community Collection (ABCC) Documentation Summary

The ABCC read-the-docs page contains extensive documentation on the data, tools, and utilities that can be used to access, download, analyze, and share derived ABCD datasets. 

The ABCC houses a community-shared and continually updated ABCD neuroimaging dataset available under Brain Imaging Data Structure (BIDS) standards. Source data are converted to BIDS from the [NIMH Data Archive (NDA) share of ABCD fast-track data](https://nda.nih.gov/edit_collection.html?id=2573). Only data that passed the Data Analysis Imaging Center (DAIC) quality control are included. More information on the data available, including the different processed derivatives available, can be found in the [**Release Notes**](https://collection3165.readthedocs.io/en/stable/release_notes/).

In addition to the data, the ABCC read-the-docs provides helpful links to how to use, process, and analyze ABCD data. The documentation guide below will take users to different sections for more information.

# How to Contribute

If you would like to contribute to this effort, please visit our [Git NDA Uploads Repository](https://github.com/ABCD-STUDY/nda-abcd-collection-3165). Additionally, you may contact us by emailing arueter@umn.edu. 

Latest updates are detailed below. 

# Collection News

## Coming Soon:

- Year 2 BIDS input data

- Year 2 abcd-hcp-pipeline derivatives

- Additional year 1 BIDS input and abcd-hcp-pipeline derivatives

- Zero padding correction (TODO: Anders)

- New version of [QSIPrep](https://qsiprep.readthedocs.io/en/stable/)- year 1 derivatives.
    - There was in issue for some subjects in distortion correction that resulted in very inaccurate distortion correction results. This was due to TOPUP being given a denoised b=0 image from the DWI series and a raw b=0 image in the opposite phase encoding direction (taken from the image in the fmap/ directory). We updated QSIPrep to use the unprocessed b=0 images in both phase encoding directions, which resulted in TOPUP performing as expected.
    
    The bug affected a subset of subjects, but it is worth suggesting that anyone using the initial data re-calculate their analysis using the updated version.

(TODO: Edit this section @Feczko)
- New version of [fMRIPrep](https://fmriprep.org/) 23.x.x year 1 derivatives. Special thanks to Thomas Madison, etc.
    - Improved distortion correction
    - Improved bold projection to surface
    - New CIFTI outputs
    - T2w in T1w volume space

- Change to participants.tsv format (TODO: Anders link to recomendations section)

## Table of Contents (TODO: Audrey cleanup)

These documents are generated from [GitHub](https://github.com/ABCD-STUDY/nda-abcd-collection-3165) as a live readthedocs.org website describing various aspects of [the ABCD-BIDS community collection (ABCC)](https://nda.nih.gov/edit_collection.html?id=3165).

1. [**Release Notes**](https://collection3165.readthedocs.io/en/stable/release_notes/)

    A document describing the overarching goals and summary of collection 3165.

1. [**Inputs**](https://collection3165.readthedocs.io/en/stable/inputs/)

    A reference for the curated BIDS anatomical, functional, and field map input data.  This reference describes how the data were selected and prepared for processing.

1. [**Pipeline**](https://collection3165.readthedocs.io/en/stable/pipeline/)

    A description of the ABCD-BIDS MRI processing pipeline used on the input data to create the derivative data.

1. [**Derivatives**](https://collection3165.readthedocs.io/en/stable/derivatives/)

    A reference for the anatomical, functional, and executive summary derivative data.

1. [**Post Pipeline**](https://collection3165.readthedocs.io/en/stable/postpipeline/)

1. [**Recommendations**](https://collection3165.readthedocs.io/en/stable/recommendations/)

    Recommended software, methods, and procedures for [downloading](https://github.com/ABCD-STUDY/nda-abcd-s3-downloader) and analyzing the data.

1. [**Useful Links**](https://collection3165.readthedocs.io/en/stable/useful/)

    Links found throughout the documents collected into one page.
