# ABCD-BIDS Community Collection (ABCC) Documentation Summary

The ABCC read-the-docs page contains extensive documentation on the data, tools, and utilities that can be used to access, download, analyze, and share derived ABCD datasets. 

The ABCC houses a community-shared and continually updated ABCD neuroimaging dataset available under Brain Imaging Data Structure (BIDS) standards. Source data are converted to BIDS from the [NIMH Data Archive (NDA) share of ABCD fast-track data](https://nda.nih.gov/edit_collection.html?id=2573). Only data that passed the Data Analysis Imaging Center (DAIC) quality control are included. More information on the data available, including the different processed derivatives available, can be found in the [**Release Notes**](https://collection3165.readthedocs.io/en/stable/release_notes/).

In addition to the data, the ABCC read-the-docs provides helpful links to how to use, process, and analyze ABCD data. The documentation guide below will take users to different sections for more information.

# How to Contribute

If you would like to contribute to this effort, please visit our [Git NDA Uploads Repository](https://github.com/ABCD-STUDY/nda-abcd-collection-3165). Additionally, you may contact us by emailing arueter@umn.edu. 

Latest updates are detailed below. 

# Collection News

## Curator's Latest Update

- 10/7/2020: An important release today to assist investigators with quality control (QC): Brain coverage quality control scores for the `derivatives.func.runs_task-(MID|nback|rest|SST)_volume` data subsets.  Remember to QC your data from the release with the [ExecutiveSummary visual report files (see Pipeline stage 7)](https://collection3165.readthedocs.io/en/stable/pipeline/#stage-7-executivesummary), available in the `derivatives.executivesummary.all` data subset.



## Coming Soon: Release 2.0.0 (#Remove/Replace it with 3.0.0 year2 BIDs/derivatives, QSIPrep revisions, new fMRIPrep, zero-padding corrections)

- Uploading 144 participants with new data due to revised fast track QC
- Providing Connectivity matrices for those participants with discrepancies in the number of timepoints used
- Uploading JSONs for the diffusion inputs in some participants
- `derivatives.func.runs_task-rest_volume` data subset all runs
- `derivatives_qc.(json|tsv)` version 1.0.1, including all `task-rest` runs
- Updated `participants.tsv` file containing new columns indicating which participants were updated under the new `released` fast track and have `updated_dwi_input_json` files, see the [release-notes](https://collection3165.readthedocs.io/en/stable/release_notes.html#5-corrections) for more information. 

- [fMRIPrep](https://fmriprep.org/)-processed derivatives for subjects in the ABCC. We are really excited to provide fMRIprep standardized outputs. Special thanks to Dylan Nielson, Oscar Esteban, and their teams!

- [QSIPrep](https://qsiprep.readthedocs.io/en/stable/)-processed derivatives for subjects in the ABCC. We are really excited to provide the ABCC with the new QSIPrep standardized pipeline. Special thanks to Matt Cieslak, Sydney Covitz, Tim Hendrickson, Ted Satterthwaite, and their teams!

- [ABCD-BIDS-tfmri-pipeline](https://github.com/DCAN-Labs/ABCD-BIDS-task-fmri-pipeline)-processed derivatives for subjects in the ABCC. We are really excited to provide level-2 contrast parameter estimates based off a BIDS-extension of task-derived outputs! Special thanks to Anthony Juliano and Greg Conan!

- [Indiviudalized parcellated network]() derivatives for subjects in the ABCC. We are really excited to provide individualized networks derived from two different approavhes. Special thanks to Robert Hermosillo and Lucille Moore!



## Documentation Guide

Clicking any link within the readthedocs site will not open a new web browser tab.  If you want to keep your docs open, either middle-click or right-click and choose open in new tab for the links you would like to follow.

---

These documents are generated from [GitHub](https://github.com/ABCD-STUDY/nda-abcd-collection-3165) as a live readthedocs.org website describing various aspects of [the ABCD-BIDS community collection (ABCC)](https://nda.nih.gov/edit_collection.html?id=3165).

1. [**Release Notes**](https://collection3165.readthedocs.io/en/stable/release_notes/)

    A document describing the overarching goals and summary of collection 3165.

1. [**Inputs**](https://collection3165.readthedocs.io/en/stable/inputs/)

    A reference for the curated BIDS anatomical, functional, and field map input data.  This reference describes how the data were selected and prepared for processing.

1. [**Pipeline**](https://collection3165.readthedocs.io/en/stable/pipeline/)

    A description of the ABCD-BIDS MRI processing pipeline used on the input data to create the derivative data.

1. [**Derivatives**](https://collection3165.readthedocs.io/en/stable/derivatives/)

    A reference for the anatomical, functional, and executive summary derivative data.

1. [**Recommendations**](https://collection3165.readthedocs.io/en/stable/recommendations/)

    Recommended software, methods, and procedures for [downloading](https://github.com/ABCD-STUDY/nda-abcd-s3-downloader) and analyzing the data.

1. [**Useful Links**](https://collection3165.readthedocs.io/en/stable/useful/)

    Links found throughout the documents collected into one page.
