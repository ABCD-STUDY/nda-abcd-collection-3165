# Release Notes

## 3. Ongoing Releases

A guiding principle for this collection is to release essential data for analysis.  This collection will be updated with waves of data preparation and processing.  As waves complete preparation or processing they will be uploaded and version-stamped with updated and versioned release notes.

User feedback will guide our course for future releases.  Provide feedback on what you would like to see included in future releases using [GitHub issues for this repository](https://github.com/ABCD-STUDY/nda-abcd-collection-3165/issues):

- [NDA Collection 3165 documentation repository](https://github.com/ABCD-STUDY/nda-abcd-collection-3165)
- [Direct link to repository's GitHub issues for requests and feedback](https://github.com/ABCD-STUDY/nda-abcd-collection-3165/issues)

## 4. Release History

### Release 2.0.0 (6/22/2022)

New updates (TODO: include dates) to the ABCD BIDS Community Collection cover both revisions to existing datasets and new derivatives. Revisions include:
1. Uploading 144 participants with new data due to revised fast track QC
2. Providing Connectivity matrices for those participants with discrepancies in the number of timepoints used
3. Uploading JSONs for the diffusion inputs in some participants.
4. Updated version of the participants.tsv to v1.0.2 includes correction to site and sex designation for a small subset of subjects based on new information from the DAIC.

New additions include:
1. Individual-specific network labels based on a template matching approach and infomap approaches
2. Derivatives for the fmriprep pipeline, and 
3. Level-2 task files from the ABCD-task-fMRI pipeline.

Details about each update are given below.

#### fMRIPrep outputs

fMRIPrep v20.2.0 was run on all 10,038 participants whose visit one data was successfully converted to BIDS. The limited fMRIPrep processing errors were due to subjects that did not have any valid fMRI runs, but we did not do any manual quality control of outputs. 9,484 participants have at least one output.The data is available in 18 submissions (a summary, including number of files and submission size can be found [here](https://docs.google.com/spreadsheets/d/1NbZ28vBvGVJb9miivgsJ695VVoFBSBuBQmWigN5pg_c/edit#gid=678992105)). Detailed information about the files included in each submission are on the second tab of that spreadsheet. Files with no submission name listed have not yet been uploaded. If additional outputs are desired, please reach out to [TODO: contact (Dylan Nielson?)]. fMRIPrep was run in a singularity container on resources from the NIH High Performance Computing Biowulf cluster. 

#### Replaced subjects
The initial release was processed prior to new updates to the fast track QC spreadsheet that affected the original inputs for 144 participants. This led to discrepancies in the number of timepoints reported for connectivity matrices (see below) relative to the inputs. The 144 participants were re-processed through the ABCD-BIDS pipeline at the Minnesota Supercomputing Institute (MSI), and being replaced, subsequent the required NDA review. The participants.tsv file indicates which subjects were reprocessed.

These subjects have had their connectivity matrices regenerated and replaced (see: Connectivity Matrices).

#### Connectivity matrices

The 144 participants with replaced fast track QC information mentioned above produced new Gordon 10 and 5 minute connectivity matrices. The old matrices remain valid, but may use different frames from the new matrices.  The labels for these connectivity matrices were defined in Gordon, et al, 2017. These connectivity matrices were created using the DCAN Labs cifti connectivity wrapper (https://github.com/DCAN-Labs/cifti-connectivity/). Timepoints used for connectivity calculations were thresholded based on data quality. Data quality was measured by the total frame displacement (FD) calculated from the frame-by-frame realignment parameters; Frames above an FD of 0.2 mm were excluded. An outlier detection procedure was used to exclude remaining frames that were 2 standard deviations away from the mean.  These procedures match the original procedures used to generate the connectivity matrices in the November release.

*Submission IDs: 36449 - 36452*

#### DWI sidecar JSON patch (Diffusion inputs)

The DWI acquisition parameters from subjects scanned on Philips and GE with MR Software release versions 5.3.0_5.3.0.0 and DV25.0_R02_1549.b respectively (n=423) are missing the required field, PhaseEncodingDirection. This omission is because they reported the axis and not direction; therefore we did a manual check of these images to check the phase encoding direction, so that these JSON inputs are BIDS compatible and can be processed by pipelines like QSIprep. These JSONs have been updated and uploaded.

*Submission ID: 36448*

#### Individual-specific network maps

 Multiple versions of the time series are provided, to allow investigator flexibility in their desired analysis: either exactly 10 minutes of randomly sampled frames, all available frames below the 0.2mm FD threshold, or concatenated rest and task time series data in the following order: rest, MID, n-back, and SST (provided that the participant had an available scan for the task). For full details of inter- and intra- participant reliability, and motion correction, see Hermosillo et al. 2021 (in prep).

TODO Submission IDs

#### Template Matching

(TODO Anders edit Robert's tempalte matching section and summarize here): link to description and brief overview of subject counts

*Submission IDs: 36458 - 36630*

#### Task outputs

(TODO Audrey: link to description)

(TODO: Add section about the task derivatives. @Audrey give Fez a list of all task derivatives)

(TODO Submission IDs)

### Release 1.1.1 (10/7/2020)

This was a small version 1.0.0 release of the `derivatives_qc.(json|tsv)` with additional BIDS derivatives quality control data including a "brain coverage score" for the `derivatives.func.runs_task-(MID|nback|rest|SST)_volume` data subsets.

### Release 1.1.0 (7/27/2020)

This was the next big release with the addition of:

1. 157 additional subjects due to updated fast track QC spreadsheet
1. `participants.(json|tsv)` version 1.0.0: BIDS standard participants files with matched groups
1. `sourcedata.func.task_events`: Task-based fMRI E-Prime files
1. `inputs.dwi.dwi`: DWI BIDS input data
1. `derivatives.anat.stats`: FreeSurfer stats files
1. `derivatives.anat.(T1w|T2w)`: T1 and T2 volumes
1. `derivatives.anat.wmparc`: white-matter volume ROIs
1. `derivatives.func.updated_motion_task-(MID|nback|SST|rest)`: Improved motion files (including outlier calculation)
1. `derivatives.func.pconns`: Curated parcellated connectivity files
1. `derivatives.func.runs_task-(MID|nback|SST|rest)_volume`: Minimally-processed fMRI volumes

### Release 1.0.0 (2/17/2020)

This was the initial release of DCAN Labs ABCD-BIDS inputs and derivatives containing **10,038 MRI sessions worth of NDA imagingcollection01 data** and **9,647 MRI sessions worth of NDA fmriresults01 data**.

#### Corrections

##### `task-rest_bold.json`

Discovered in the middle of June 2020, the modality-specific BIDS inherited `task-rest_bold.json` file at the top of the directory tree which is nested in almost every `task-rest` associated record in the NDA database has a typo in it.  The `"TaskDescription"` key has a value of `"See http://www.cognitiveatlas.org/task/id/tsk_4a57abb949e1a/"`.  However, this link goes to the stop signal task page on the Cognitive Atlas website.  Instead you should refer to [the Cognitive Atlas website for "rest eyes open"](http://www.cognitiveatlas.org/task/id/trm_4c8a834779883/).  This site describes the task as:

"Subjects rest passively with their eyes open. Often used as a baseline for comparison for other tasks."

##### `derivatives.func.runs_task-rest_volume`

This data subset was originally uploaded in Release 1.1.0, but was missing all runs chronologically numbered 3 and up.  We are uploading these missing data in Release 1.1.2.

#### `updated_dwi_input_json`

The DWI acquisition parameters from all subjects scanned on GE with MR Software release DV25.0_R02_1549.b (n=281) are missing the required field, PhaseEncodingDirection. This omission is because they reported the axis and not direction.