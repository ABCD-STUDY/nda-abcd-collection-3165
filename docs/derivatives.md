# Derivatives

Note: Clicking any link within the readthedocs site will not open a new web browser tab.  If you want to keep your docs open, either middle-click or right-click and choose open in new tab for the links you would like to follow.

---

## 1. About this Document

This document reports and describes the derivative files containing processed data that are in the `abcd-hcp-pipeline` and `freesurfer-5.3.0-HCP` derivative folders from the ABCC.  All BIDS derivative record data types within this document are written in `monospace` font.  They are prefaced by their short names. References for other derivatives from pipelines like:

1. [fMRIPrep](https://fmriprep.org/)
1. [QSIPrep](https://qsiprep.readthedocs.io/en/stable/)
1. [abcd-bids-tfmri-pipeline](https://github.com/DCAN-Labs/abcd-bids-tfmri-pipeline)

can be found by clicking on their respective links.

## 2. Anatomical

There are eleven possible BIDS anatomical (`anat`) derivative subsets depending on whether a T2w image is present or not.  If there is no T1w image present the data set is not processed.  The following `monospace` descriptions are names of the subsets.

### Prerequisite: At least one `T1w` MRI

- Cortical Thickness: `derivatives.anat.space-fsLR32k_thickness`
- Curvature: `derivatives.anat.space-fsLR32k_curv`
- Sulcal Depth: `derivatives.anat.space-fsLR32k_sulc`
- Discrete segmentation (native space): `derivatives.anat.space-ACPC_dseg`
- Mid-thickness surface (MNI space, fsLR164k mesh): `derivatives.anat.space-MNI_mesh-fsLR164k_midthickness`
- Mid-thickness surface (MNI space, fsLR32k mesh): `derivatives.anat.space-MNI_mesh-fsLR32k_midthickness`
- Mid-thickness surface (MNI space, native mesh): `derivatives.anat.space-MNI_mesh-native_midthickness`
- Mid-thickness surface (native space, fsLR32k mesh): `derivatives.anat.space-T1w_mesh-fsLR32k_midthickness`
- Mid-thickness surface (native space, native mesh): `derivatives.anat.space-T1w_mesh-native_midthickness`

### Prerequisites: At least one `T1w` MRI and at least one `T2w` MRI

- Myelin map (un-smoothed): `derivatives.anat.space-fsLR32k_myelinmap`
- Myelin map (smoothed): `derivatives.anat.space-fsLR32k_desc-smoothed_myelinmap`

## 3. Functional

There are eight possible BIDS functional (`func`) derivatives depending upon the presence of fMRI runs (all `_____` blanks below would instead be filled in by their fMRI type, see below bulleted list). See [here](https://pubmed.ncbi.nlm.nih.gov/29567376/) to learn more about the task paradigms.

- Individual run movement files (regression and censoring): `derivatives.func.motion_task-_____`
- Individual run time series: `derivatives.func.runs_task-_____`
- Concatenated dense time series: `derivatives.func.concat_task-______bold_desc-filtered_timeseries`
- Gordon 2014 parcellated time series: `derivatives.func.concat_task-______bold_atlas-Gordon2014FreeSurferSubcortical_desc-filtered_timeseries`
- Human Connectome Project 2016 parcellated time series: `derivatives.func.concat_task-______bold_atlas-HCP2016FreeSurferSubcortical_desc-filtered_timeseries`
- Markov 2012 parcellated time series: `derivatives.func.concat_task-______bold_atlas-Markov2012FreeSurferSubcortical_desc-filtered_timeseries`
- Power 2011 parcellated time series: `derivatives.func.concat_task-______bold_atlas-Power2011FreeSurferSubcortical_desc-filtered_timeseries`
- Yeo 2011 parcellated time series: `derivatives.func.concat_task-______bold_atlas-Yeo2011FreeSurferSubcortical_desc-filtered_timeseries`

### Prerequisites for each fMRI type to have eight derivative data subsets:

- Resting-state (`rest`): At least one `task-rest_bold` fMRI run
- Monetary Incentive Delay (`MID`): A pair of `task-MID_bold` fMRI runs
- N-back (`nback`): A pair of `task-nback_bold` fMRI runs
- Stop Signal Task (`SST`): A pair of `task-SST_bold` fMRI runs

## 4. Derivative Data Subsets Breakdown

Sections 3 and onward of this document generally describe what each of the derivative data subsets are.  This section breaks down the exact contents of each of the derivative data subsets. Each NDA data subset is depicted by each subheading with a description of the files underneath. Subject and session identifiers are instead labeled as `#`.  Each derivative data subset comes with modality-agnostic BIDS-compatible `dataset_description.json`, `README`, and `CHANGES` files and a `derivatives/abcd-hcp-pipeline/` subfolder.  For readability, the `derivatives/abcd-hcp-pipeline/` subfolder has been removed from all below derivative subfolders and filenames.

### Freesurfer statistics: derivatives.anat.stats

FreeSurfer stats folder.

- `../freesurfer-5.3.0-HCP/sub-#/ses-#/stats/<VARIOUS>`

    **NOTE:** `<VARIOUS>` here denotes files directly from the FreeSurfer stats folder.

### Discrete segmentation (native space): derivatives.anat.space-ACPC_dseg

Discrete segmentation in subject's native space in a NIfTI volume.

- `sub-#/ses-#/anat/sub-#_ses-#_space-ACPC_dseg.nii.gz`

### Discrete segmentation (fsLR32k space): derivatives.anat.space-fsLR32k_curv

Dense subject curvature CIFTI.

- `sub-#/ses-#/anat/sub-#_ses-#_space-fsLR32k_curv.dscalar.nii`

### Sulcal Depth (fsLR32k space): derivatives.anat.space-fsLR32k_sulc

Dense subject sulcal depth CIFTI.

- `sub-#/ses-#/anat/sub-#_ses-#_space-fsLR32k_sulc.dscalar.nii`

### Cortical Thickness (fsLR32k space): derivatives.anat.space-fsLR32k_thickness

Dense subject cortical thickness CIFTI.

- `sub-#/ses-#/anat/sub-#_ses-#_space-fsLR32k_thickness.dscalar.nii`

### Myelin Map (Smoothed, fsLR32k space): derivatives.anat.space-fsLR32k_desc-smoothed_myelinmap

Smoothed myelin map CIFTI (when a T2w image is present in the inputs).

- `sub-#/ses-#/anat/sub-#_ses-#_space-fsLR32k_desc-smoothed_myelinmap.dscalar.nii`

### Myelin Map (Unsmoothed, fsLR32k space): derivatives.anat.space-fsLR32k_myelinmap

Unsmoothed myelin map CIFTI (when a T2w image is present in the inputs).

- `sub-#/ses-#/anat/sub-#_ses-#_space-fsLR32k_myelinmap.dscalar.nii`

### Mid-thickness surface (MNI space, fsLR164k mesh): derivatives.anat.space-MNI_mesh-fsLR164k_midthickness

Left and Right mid-thickness CIFTIs in MNI space with fsLR164k surface mesh.

- `sub-#/ses-#/anat/sub-#_ses-#_hemi-L_space-MNI_mesh-fsLR164k_midthickness.surf.gii`
- `sub-#/ses-#/anat/sub-#_ses-#_hemi-R_space-MNI_mesh-fsLR164k_midthickness.surf.gii`

### Mid-thickness surface (MNI space, fsLR32k mesh):derivatives.anat.space-MNI_mesh-fsLR32k_midthickness

Left and Right mid-thickness CIFTIs in MNI space with fsLR32k surface mesh.

- `sub-#/ses-#/anat/sub-#_ses-#_hemi-L_space-MNI_mesh-fsLR32k_midthickness.surf.gii`
- `sub-#/ses-#/anat/sub-#_ses-#_hemi-R_space-MNI_mesh-fsLR32k_midthickness.surf.gii`

### Mid-thickness surface (MNI space, native mesh): derivatives.anat.space-MNI_mesh-native_midthickness

Left and Right mid-thickness CIFTIs in MNI space with native surface mesh.

- `sub-#/ses-#/anat/sub-#_ses-#_hemi-L_space-MNI_mesh-native_midthickness.surf.gii`
- `sub-#/ses-#/anat/sub-#_ses-#_hemi-R_space-MNI_mesh-native_midthickness.surf.gii`

### Mid-thickness surface (native space, fsLR32k mesh): derivatives.anat.space-T1w_mesh-fsLR32k_midthickness

Left and Right mid-thickness CIFTIs in native T1 space with fsLR32k surface mesh.

- `sub-#/ses-#/anat/sub-#_ses-#_hemi-L_space-T1w_mesh-fsLR32k_midthickness.surf.gii`
- `sub-#/ses-#/anat/sub-#_ses-#_hemi-R_space-T1w_mesh-fsLR32k_midthickness.surf.gii`

### Mid-thickness surface (native space, native mesh): derivatives.anat.space-T1w_mesh-native_midthickness

Left and Right mid-thickness CIFTIs in native T1 space with native surface mesh.

- `sub-#/ses-#/anat/sub-#_ses-#_hemi-L_space-T1w_mesh-native_midthickness.surf.gii`
- `sub-#/ses-#/anat/sub-#_ses-#_hemi-R_space-T1w_mesh-native_midthickness.surf.gii`

### Anatomical Volume (MNI space): derivatives.anat.(T1w|T2w)

Anatomical imaging masked brain or full head in MNI space in a volume.

- `sub-#/ses-#/anat/sub-#_ses-#_(T1w|T2w)_space-MNI_(brain|head).nii.gz`

### White matter parcellation discrete segmentation (MNI space): derivatives.anat.wmparc

White matter parcellation discrete segmentation file in a volume.

- `sub-#/ses-#/anat/sub-#_ses-#_T1w_space-MNI_desc-wmparc_dseg.nii.gz`

### Executive Summary: derivatives.executivesummary.all

DCAN Labs executive summary HTML processing visual inspection summary.

- `sub-#/ses-#/sub-#_ses-#.html`
- `sub-#/ses-#/img/<VARIOUS>`

    **NOTE:** `<VARIOUS>` denotes a variety of `.gif` and `.png` images used in conjunction with the `.html` file which vary in count based on counts of available input images.

### Parcellated functional timeseries: derivatives.func.concat_task-(MID|nback|SST|rest)_bold_atlas-(Gordon2014|HCP2016|Markov2012|Power2011|Yeo2011)FreeSurferSubcortical_desc-filtered_timeseries

A dense label parcellation with FreeSurfer subcorticals with names corresponding to the first author and publication year.

- `(Gordon2014|HCP2016|Markov2012|Power2011|Yeo2011)FreeSurferSubcortical_dparc.dlabel.nii`

A "5 contiguous frames" motion censoring algorithm file of temporal masks by FD threshold (0mm->0.5mm) with or without outlier detection in use.

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_desc-filtered_motion_mask.mat`

Concatenated functional task parcellated time series using the parcellations above.

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_bold_atlas-(Gordon2014|HCP2016|Markov2012|Power2011|Yeo2011)FreeSurferSubcortical_desc-filtered_timeseries.ptseries.nii`

`derivatives.func.concat_task-(MID|nback|SST|rest)_bold_desc-filtered_timeseries`

Concatenated functional task dense time series post-DCANBOLDProcessing (regression and filtering) in Atlas space.

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_bold_desc-filtered_timeseries.dtseries.nii`

A "5 contiguous frames" motion censoring algorithm file of temporal masks by FD threshold (0mm->0.5mm) with or without outlier detection in use.

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_desc-filtered_motion_mask.mat`

### Motion Framewise displacements: derivatives.func.motion_task-(MID|nback|SST|rest)

A "5 contiguous frames" motion censoring algorithm file of temporal masks by FD threshold (0mm->0.5mm) with or without outlier detection in use.

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_desc-filtered_motion_mask.mat`

Movement-artifact-unfiltered or Movement-artifact-filtered (`_desc-filtered`) movement numbers without an FD column.

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_motion.tsv`
- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_desc-filtered_motion.tsv`

### Motion and Framewise displacement (filtered): derivatives.func.updated_motion_task-(MID|nback|SST|rest)

Movement-artifact-unfiltered or Movement-artifact-filtered (`_desc-filtered`) movement numbers with an FD column included (this one is recommended over the original `derivatives.func.motion_task-(MID|nback|SST|rest)` motion files).

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_desc-includingFD_motion.tsv`
- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_desc-filteredincludingFD_motion.tsv`

### Parcellated Connectivity: derivatives.func.pconns

Connectivity matrix and its frame censor with either 5 minutes of data (`_censor-5min`), 10 minutes of data (`_censor-10min`), or all frames (`_censor-belowthresh`) below the FD threshold of 0.2 mm (`_thresh-fd0p2mm`).

- `sub-#/ses-#/func/sub-#_ses-#_task-rest_bold_atlas-Gordon2014FreeSurferSubcortical_desc-filtered_timeseries_thresh-fd0p2mm_censor-(5min|10min|belowthresh)_conndata-network_(censor.txt|connectivity.pconn.nii)`

### Individual functional run and motion files: derivatives.func.runs_task-(MID|nback|SST|rest)

Individual minimally-processed functional task run dense time series in Atlas space pre-DCANBOLDProcessing with original filtered motion numbers.

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_bold_timeseries.dtseries.nii`
- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_desc-filtered_motion.tsv`

### Individual functional volumes: derivatives.func.runs_task-(MID|nback|SST|rest)_volume

Motion-corrected individual functional task run in MNI space in a volume.

- `sub-#/ses-#/func/sub-#_ses-#_task-(MID|nback|SST|rest)_run-#_space-MNI_bold.nii.gz`


## 5. Executive Summary

The DCAN Labs executive summary is software for getting a basic visual quality control report to review processed output data.

### Prerequisites: At least one `T1w` and one fMRI

- DCAN Labs Executive Summary: `derivatives.executivesummary.all`

## 6. Derivative Filenames

Some BIDS derivative standards are still [BIDS Extension Proposals (BEPs)](https://bids-specification.readthedocs.io/en/stable/06-extensions.html#bids-extension-proposals) at the time of this writing, but we tried to conform to the available derivative standards at the time for common derivatives ([BEP003](https://docs.google.com/document/d/1Wwc4A6Mow4ZPPszDIWfCUCRNstn7d_zzaWPcfcHmgI4/view)), the structural preprocessing derivatives ([BEP011](https://docs.google.com/document/d/1YG2g4UkEio4t_STIBOqYOwneLEs1emHIXbGKynx7V0Y/view)), and the functional preprocessing derivatives ([BEP012](https://docs.google.com/document/d/1qBNQimDx6CuvHjbDvuFyBIrf2WRFUOJ-u50canWjjaw/view)).

## 7. Motion MAT File

The MATLAB motion .MAT files are a product of the DCANBOLDProcessing stage of the pipeline.  They should be used to select a frame censoring mask (frames to keep in analysis versus frames to censor out based on excessive motion).  They contain a 1x51 MATLAB cell of MATLAB structs where each struct is the censoring info at a given framewise displacement (FD) threshold (0 to 0.5 millimeters in steps of 0.01 millimeters).

These files use the motion censoring algorithm from the [Power, et al, 2014 paper](https://www.sciencedirect.com/science/article/pii/S1053811913009117).  In that paper, the authors describe a motion censoring method wherein you only exclude periods of data below FD thresholds which are less than five contiguous frames between sequential censored frames.

[*Power, J. D., Mitra, A., Laumann, T. O., Snyder, A. Z., Schlaggar, B. L., & Petersen, S. E. (2014). Methods to detect, characterize, and remove motion artifact in resting state fMRI. NeuroImage, 84, 320–41. doi:10.1016/j.neuroimage.2013.08.048*](https://www.sciencedirect.com/science/article/pii/S1053811913009117)

## 8. Caveats

There were a few parts of the NDA fmriresults01 and imagingcollection01 data structures where we could not conform to the NDA's established standard.  We plan to correct these in future releases.

### `qc_outcome` Field

All `qc_outcome` fields within NDA records are marked as questionable as the data have not been formally quality controlled.

### Executive Summary not-yet-available for Anatomical-only Subjects

There is a minor bug in the executive summary visual report in this round of processing not allowing it to support sessions without any fMRI runs.

### Executive Summary labeled as fMRI `scan_type` in records

For all NDA records within this first release the `scan_type` variable was set to fMRI for the executive summary visual reports.