# Release Notes

Note: Clicking any link within the readthedocs site will not open a new web browser tab.  If you want to keep your docs open, either middle-click or right-click and choose open in new tab for the links you would like to follow.

---

## 1. Collection

This data collection from the Developmental Cognition and Neuroimaging (DCAN) Labs contains a regularly updated dataset of ABCD Brain Imaging Data Structure (BIDS) version 1.2.0 pipeline inputs and derivatives. Source data are currently comprised of all the ABCD Study participants baseline year 1 arm 1 DICOM imaging data that passed initial acquisition quality control from the ABCD Data Analysis and Informatics Center (DAIC) and were processed by DCAN Labs.

The version 1.0.0 release focuses on anatomical and resting-state fMRI derivative data.

## 2. Background

The input DICOM data to this [BIDS version 1.2.0](https://www.nature.com/articles/sdata201644) data collection were retrieved from the [NIMH Data Archive (NDA) share of ABCD fast-track data](https://nda.nih.gov/edit_collection.html?id=2573) and were last accessed on May 1, 2019. BIDS input data were converted from DICOMs using [Dcm2Bids](https://github.com/cbedetti/Dcm2Bids). BIDS derivatives data were derived from the [DCAN Labs ABCD-BIDS MRI processing pipeline](https://doi.org/10.5281/zenodo.2587210) which outputs [Human Connectome Project (HCP) Minimal Preprocessing Pipelines-style data](https://doi.org/10.1016/j.neuroimage.2013.04.127) in both volume and surface spaces. This collection is independent from ABCD Data Collection 2573. Users may access ABCD DICOM files via the ABCD fast-track imaging data release in Collection 2573.

## 3. Ongoing Releases

A guiding principle for this collection is to release essential data for analysis.  This collection will be updated with waves of data preparation and processing.  As waves complete preparation or processing they will be uploaded and version-stamped with updated and versioned release notes.

User feedback will guide our course for future releases.  Provide feedback on what you would like to see included in future releases using [GitHub issues for this repository](https://github.com/DCAN-Labs/nda-abcd-collection-3165/issues):

- [NDA Collection 3165 documentation repository](https://github.com/DCAN-Labs/nda-abcd-collection-3165)
- [Direct link to repository's GitHub issues for requests and feedback](https://github.com/DCAN-Labs/nda-abcd-collection-3165/issues)

The following updates to this collection are already planned.

1. Diffusion Weighted Imaging BIDS input data
1. Task-based fMRI (MID, n-back, and SST) contrast files

**NOTE (12/12/2019): We are actively downloading the entirety of this dataset's derivative files and checking them against our originally uploaded data for being binary identical files.  We have found some discrepancies, but anecdotally in the handful of cases we have investigated the differences have been visually inspected as small when they are present.  Please anticipate our next rolling release to be soon after the first rolling release and to include many more subjects making the total exceed 10,000 MRI sessions.**
