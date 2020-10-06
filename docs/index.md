# Releases

## Curator's Latest Update

- 10/6/2020: A small, but important release today.  Brain coverage quality control scores for the `derivatives.func.runs_task-(MID|nback|rest|SST)_volume` data subsets.

## Release 1.1.1

Released 10/6/2020, this is a small version 1.0.0 release of the `derivatives_qc.(json|tsv)` with BIDS derivatives quality control data including a "brain coverage score" for the `derivatives.func.runs_task-(MID|nback|rest|SST)_volume` data subsets.

**Note**: We only uploaded runs 1 and 2 of the `derivatives.func.runs_task-(MID|nback|rest|SST)_volume` data subsets in Release 1.1.0.  We are uploading resting state fMRI tasks with 3 or more runs to the NDA now and we will post a data set Release 1.1.2 here soon.  We will also update the above `derivatives_qc.(json|tsv)` file as a version 1.0.1 after data are uploaded to include `task-rest` volume runs 3 and up.

## Release 1.1.0

Released 7/27/2020, this is the next big release with the addition of:

1. `participants.(json|tsv)` version 1.0.0: BIDS standard participants files with matched groups
1. `sourcedata.func.task_events`: Task-based fMRI E-Prime files
1. `inputs.dwi.dwi`: DWI BIDS input data
1. `derivatives.anat.stats`: FreeSurfer stats files
1. `derivatives.anat.(T1w|T2w)`: T1 and T2 volumes
1. `derivatives.anat.wmparc`: white-matter volume ROIs
1. `derivatives.func.updated_motion_task-(MID|nback|SST|rest)`: Improved motion files (including outlier calculation)
1. `derivatives.func.pconns`: Curated parcellated connectivity files
1. `derivatives.func.runs_task-(MID|nback|SST|rest)_volume`: Minimally-processed fMRI volumes

## Release 1.0.0

Released 2/17/2020, this is the initial release of DCAN Labs ABCD-BIDS inputs and derivatives containing **10,038 MRI sessions worth of NDA imagingcollection01 data** and **9,647 MRI sessions worth of NDA fmriresults01 data**.

## Coming Soon

### Release 1.1.2

- `derivatives_qc.(json|tsv)` version 1.0.1, including `task-rest` runs 3 and up
- `derivatives.func.runs_task-rest_volume` data subset runs 3 and up

## Documentation Guide

Clicking any link within the readthedocs site will not open a new web browser tab.  If you want to keep your docs open, either middle-click or right-click and choose open in new tab for the links you would like to follow.

---

These documents are generated from [GitHub](https://github.com/ABCD-STUDY/nda-abcd-collection-3165) as a live readthedocs.org website describing various aspects of [the NDA data in collection 3165](https://nda.nih.gov/edit_collection.html?id=3165).

1. [**Release Notes**](https://collection3165.readthedocs.io/en/stable/release_notes/)

    A document describing the overarching goals and summary of collection 3165.

1. [**Inputs**](https://collection3165.readthedocs.io/en/stable/inputs/)

    A reference for the curated BIDS anatomical, functional, and field map input data.  This reference describes how the data were selected and prepared for processing.

1. [**Pipeline**](https://collection3165.readthedocs.io/en/stable/pipeline/)

    A description of the ABCD-BIDS MRI processing pipeline used on the input data to create the derivative data.

1. [**Derivatives**](https://collection3165.readthedocs.io/en/stable/derivatives/)

    A reference for the anatomical, functional, and executive summary derivative data.

1. [**Recommendations**](https://collection3165.readthedocs.io/en/stable/recommendations/)

    Recommended software, methods, and procedures for analyzing the data.

1. [**Useful Links**](https://collection3165.readthedocs.io/en/stable/useful/)

    Links found throughout the documents collected into one page.
