# Release Notes

Note: Clicking any link within the readthedocs site will not open a new web browser tab.  If you want to keep your docs open, either middle-click or right-click and choose open in new tab for the links you would like to follow.

---

## 1. The ABCC Collection

The ABCC houses a community-shared and continually updated ABCD neuroimaging dataset available under Brain Imaging Data Structure (BIDS) standards. Source data are converted to BIDS from the [NIMH Data Archive (NDA) share of ABCD fast-track data](https://nda.nih.gov/edit_collection.html?id=2573). Only data that passed the Data Analysis Imaging Center (DAIC) quality control are included. 

Currently, the ABCD-BIDS Community Collection (ABCC) from the Developmental Cognition and Neuroimaging (DCAN) Labs contains a regularly updated dataset of ABCD Brain Imaging Data Structure (BIDS) version 1.2.0 pipeline inputs and derivatives. Source data are currently comprised of all the ABCD Study participants baseline year 1 arm 1 DICOM imaging data that passed initial acquisition quality control from the ABCD Data Analysis and Informatics Center (DAIC) and were processed by DCAN Labs. Per task, the first two bold runs that passed QC were pulled and converted to BIDS.

The version 1.0.0 release focuses on anatomical and resting-state fMRI derivative data. Version 2.0.0 will add new derivatives including diffusion and task fMRI deriative data.

## 2. Background

As a community share, the ABCC enables researchers to access **available derivatives** and share their **own derivatives.**. The `inputs`, `abcd-hcp-pipeline`, and `freesurfer-5.3.0-HCP` shares are continually updated as new ABCD releases become available. A list of currently available datasets are provided below. The list will be updated continously as the NDA publically releases new shared datasets:

1. `inputs` The input DICOM data to this [BIDS version 1.2.0](https://www.nature.com/articles/sdata201644) data collection were retrieved from the [NIMH Data Archive (NDA) share of ABCD fast-track data](https://nda.nih.gov/edit_collection.html?id=2573) and were last accessed on May 1, 2019. BIDS input data were converted from DICOMs using [Dcm2Bids](https://github.com/cbedetti/Dcm2Bids). 
1. `abcd-hcp-pipeline` BIDS derivatives data were derived from the [DCAN Labs ABCD-BIDS MRI processing pipeline](https://doi.org/10.5281/zenodo.2587210) which outputs [Human Connectome Project (HCP) Minimal Preprocessing Pipelines-style data](https://doi.org/10.1016/j.neuroimage.2013.04.127) in both volume and surface spaces. This collection is independent from ABCD Data Collection 2573. Users may access ABCD DICOM files via the ABCD fast-track imaging data release in Collection 2573.
1. `freesurfer-5.3.0-HCP` segmentation statistics and surface morphometrics from the FreeSurfer stage within the [DCAN Labs ABCD-BIDS MRI processing pipeline](https://doi.org/10.5281/zenodo.2587210) are provided here.


## 3. Ongoing Releases

A guiding principle for this collection is to release essential data for analysis.  This collection will be updated with waves of data preparation and processing.  As waves complete preparation or processing they will be uploaded and version-stamped with updated and versioned release notes.

User feedback will guide our course for future releases.  Provide feedback on what you would like to see included in future releases using [GitHub issues for this repository](https://github.com/ABCD-STUDY/nda-abcd-collection-3165/issues):

- [NDA Collection 3165 documentation repository](https://github.com/ABCD-STUDY/nda-abcd-collection-3165)
- [Direct link to repository's GitHub issues for requests and feedback](https://github.com/ABCD-STUDY/nda-abcd-collection-3165/issues)

## 4. Release History

### Release 1.0.0 (2/17/2020)

This was the initial release of DCAN Labs ABCD-BIDS inputs and derivatives containing **10,038 MRI sessions worth of NDA imagingcollection01 data** and **9,647 MRI sessions worth of NDA fmriresults01 data**.

### Release 1.1.0 (7/27/2020)

This was the next big release with the addition of:

1. `participants.(json|tsv)` version 1.0.0: BIDS standard participants files with matched groups
1. `sourcedata.func.task_events`: Task-based fMRI E-Prime files
1. `inputs.dwi.dwi`: DWI BIDS input data
1. `derivatives.anat.stats`: FreeSurfer stats files
1. `derivatives.anat.(T1w|T2w)`: T1 and T2 volumes
1. `derivatives.anat.wmparc`: white-matter volume ROIs
1. `derivatives.func.updated_motion_task-(MID|nback|SST|rest)`: Improved motion files (including outlier calculation)
1. `derivatives.func.pconns`: Curated parcellated connectivity files
1. `derivatives.func.runs_task-(MID|nback|SST|rest)_volume`: Minimally-processed fMRI volumes

### Release 1.1.1 (10/7/2020)

This was a small version 1.0.0 release of the `derivatives_qc.(json|tsv)` with additional BIDS derivatives quality control data including a "brain coverage score" for the `derivatives.func.runs_task-(MID|nback|rest|SST)_volume` data subsets.


## 5. Corrections

This sub-section is for describing errors which have occurred over time.

### `task-rest_bold.json`

Discovered in the middle of June 2020, the modality-specific BIDS inherited `task-rest_bold.json` file at the top of the directory tree which is nested in almost every `task-rest` associated record in the NDA database has a typo in it.  The `"TaskDescription"` key has a value of `"See http://www.cognitiveatlas.org/task/id/tsk_4a57abb949e1a/"`.  However, this link goes to the stop signal task page on the Cognitive Atlas website.  Instead you should refer to [the Cognitive Atlas website for "rest eyes open"](http://www.cognitiveatlas.org/task/id/trm_4c8a834779883/).  This site describes the task as:

> Subjects rest passively with their eyes open. Often used as a baseline for comparison for other tasks.

### `derivatives.func.runs_task-rest_volume`

This data subset was originally uploaded in Release 1.1.0, but was missing all runs chronologically numbered 3 and up.  We are uploading these missing data in Release 1.1.2.

### `released`

The initial release was processed prior to new updates to the fast track QC spreadsheet that affected the original inputs for 157 participants. This led to discrepancies in the number of timepoints reported for connectivity matrices relative to the inputs.

### `updated_dwi_input_json`

The DWI acquisition parameters from all subjects scanned on GE with MR Software release DV25.0_R02_1549.b (n=281) are missing the required field, PhaseEncodingDirection. This omission is because they reported the axis and not direction.