# Collection News

## Curator's Latest Update

- 10/7/2020: An important release today to assist investigators with quality control (QC): Brain coverage quality control scores for the `derivatives.func.runs_task-(MID|nback|rest|SST)_volume` data subsets.  Remember to QC your data from the release with the [ExecutiveSummary visual report files (see Pipeline stage 7)](https://collection3165.readthedocs.io/en/stable/pipeline/#stage-7-executivesummary), available in the `derivatives.executivesummary.all` data subset.

## Latest Release: Release 1.1.1

Released 10/7/2020, this is a small version 1.0.0 release of the `derivatives_qc.(json|tsv)` with additional BIDS derivatives quality control data including a "brain coverage score" for the `derivatives.func.runs_task-(MID|nback|rest|SST)_volume` data subsets.

## Coming Soon: Release 1.1.2

Additional minimally pre-processed runs for the `task-rest` volumes and their QC scores:

- `derivatives.func.runs_task-rest_volume` data subset all runs
- `derivatives_qc.(json|tsv)` version 1.0.1, including all `task-rest` runs

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
