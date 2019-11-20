# Inputs

[**Developmental Cognition and Neuroimaging (DCAN) Labs**](http://www.ohsu.edu/dcan)

[**Adolescent Brain Cognitive Development (ABCD) Study**](https://abcdstudy.org/)

[**Brain Imaging Data Structure (BIDS)**](https://bids.neuroimaging.io/)

**Version 1.0.0, 11/22/2019**

---

## 1. About this Document

The data collection contains anatomical (T1w and T2w) and functional MRI images processed through [a modified version](https://github.com/DCAN-Labs/DCAN-HCP) of the [minimal preprocessing pipeline for the Human Connectome Project (HCP)](https://doi.org/10.1016/j.neuroimage.2013.04.127).  This document describes the BIDS input data and the steps involved in setting it up for processing through the ABCD-BIDS pipeline.

## 2. Input Data Subsets Breakdown

Sections 3 and onward of this document generally describe what each of the input data subsets are.  This section breaks down the exact contents of each of the input data subsets.  Subject and session identifiers are instead labeled as `#`.  Each input data subset comes with modality-agnostic BIDS-compatible `dataset_description.json`, `README`, and `CHANGES` files.

`inputs.anat.(T1w|T2w)`

- `sub-#/ses-#/anat/sub-#_ses-#(_rec-normalized)(_run-#)_(T1w|T2w).nii.gz`
- `sub-#/ses-#/anat/sub-#_ses-#(_rec-normalized)(_run-#)_(T1w|T2w).json`

`inputs.fmap.all`

- `sub-#/ses-#/fmap/sub-#_ses-#_dir-(AP|PA)_run-#_epi.nii.gz`
- `sub-#/ses-#/fmap/sub-#_ses-#_dir-(AP|PA)_run-#_epi.json`

`inputs.func.task-(MID|nback|SST|rest)`

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_bold.nii.gz`
- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_bold.json`

## 3. DAIC Quality Control (QC) Selection Process

QC is performed by scan operators at the time of the scan. Subjects may fail for a variety of reasons whether the subject was moving, began talking, fell asleep, used the squeeze ball, the scan had to be stopped, etc. These operator notes are taken into account and each scan is given a binary pass or fail.  This QC information is now provided by the NIMH Data Archive (NDA) and instructions to download it can be found on the [ABCD repository for downloading and setting up the BIDS dataset (ABCD-STUDY/abcd-dicom2bids)](https://github.com/ABCD-STUDY/abcd-dicom2bids).  Only scans that passed this initial QC were considered for processing.  Subjects without a passing T1w image were excluded from this dataset.  If there were valid anatomical scans, but no functional scans the subjects were processed through an anatomical-only version of the pipeline that excludes any functional image processing.

## 4. DICOM to BIDS Conversion

DICOMs were first converted into NIfTIs using [Christophe Bedetti's Dcm2Bids](https://github.com/cbedetti/Dcm2Bids), which is a wrapper for [the Chris Rorden's Lab dcm2niix](https://github.com/rordenlab/dcm2niix) that restructures NIfTIs into BIDS format.

## 5. MRI Acquisition Parameters

For a summary of the MRI acquisition parameters for each modality and scanner see [https://abcdstudy.org/images/Protocol_Imaging_Sequences.pdf](https://abcdstudy.org/images/Protocol_Imaging_Sequences.pdf).

## 6. Scanner Differences

Images in this dataset were acquired from three brands of MRI scanner: Siemens, Philips, and General Electric (GE).  After initially processing the dataset we noticed a relatively high post-processing quality control failure rate, particularly for images derived from GE and Philips scanners.  Upon further investigation we noticed that images from GE scanners had no intensity normalization applied to them and images from both Philips and GE appeared more "grainy" than those from Siemens scanners.

This motivated inclusion of [Advanced Normalization Tools (ANTs)](https://github.com/ANTsX/ANTs) de-noising as well as ANTs N4 bias field correction during the [PreFreeSurfer](https://github.com/DCAN-Labs/DCAN-HCP/tree/master/PreFreeSurfer) stage of the processing pipeline for all data.  We also implemented ANTs-based atlas registration in PostFreeSurfer instead of [FSL's FNIRT](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FNIRT)-based method.

While these changes had little effect on the high-quality anatomical Siemens data they significantly improved results for both Philips and GE scanner data.

## 7. Field Map Selection Process

A pair of positive (posterior to anterior) and negative (anterior to posterior) spin echo field maps are taken along with each functional scan.  These field maps are highly susceptible to motion artifacts and can have a negative effect on procesing if they are not high-quality.  Field maps are largely consistent between runs so we select the field map with the least variance from the registered group average and apply the pair to each functional scan to avoid processing with a poor-quality field map.  This assumes that field maps with large motion artifacts will have more noise and higher variance from the average.

## 8. BIDS Field Map "IntendedFor" Metadata

The chosen field map pair is used for all anatomical and functional bias field corrections.  This is specified with the `IntendedFor` field in the side car JSONs of the associated field map's BIDS metadata.

## 9. BIDS Modality-Agnostic Files

To maintain a valid BIDS data structure `dataset_description.json`, `README`, and `CHANGES` files are included.  They respectively: minimally describe the dataset, provide a small blurb about the datsaet, and log the changes from version to version.

## 10. BIDS Validator Compliance

This dataset was validated using [the official BIDS validator](https://github.com/bids-standard/bids-validator).
