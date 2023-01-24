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

[TODO: Info about zero padding update in DBP, QSIPrep, and future releases (zero byte data)]

### Release 1.2.0 (6/22/2022)

New updates (include dates) to the ABCD BIDS Community Collection cover both revisions to existing datasets and new derivatives. Revisions include:
1. Uploading 144 participants with new data due to revised fast track QC
2. Providing Connectivity matrices for those participants with discrepancies in the number of timepoints used
3. Uploading JSONs for the diffusion inputs in some participants.

New additions include:
1. Individual-specific network labels based on a template matching approach and infomap approaches
2. Derivatives for the fmriprep pipeline, and 
3. Level-2 task files from the ABCD-task-fMRI pipeline.

Details about each update are given below.

#### fMRIPrep outputs
fMRIPrep is a tool for preprocessing BIDS compatible fMRI datasets. If groups would like to analyze the ABCD fMRI results, these outputs will be helpful for analysis of resting state and task based fMRI data. fMRIPrep v20.2.0 was run on all 10,038 participants whose visit one data was successfully converted to BIDS. This is the command that was used:


```
singularity run --cleanenv /data/ABCD_MBDU/singularity_images/fmriprep_20.2.0.simg \
	/data/ABCD_MBDU/abcd_bids/bids \
$TMPDIR/out \
participant \
--participant_label $PARTICIPANTID \
	-w $TMPDIR/wrk \
	--nthreads $SLURM_CPUS_PER_TASK \
	--mem_mb $SLURM_MEM_PER_NODE \
	--fs-license-file /data/ABCD_MBDU/singularity_images/license.txt \
	--output-spaces MNI152NLin2009cAsym:res-2 fsnative fsaverage5 fsLR \
	--cifti-output \
	--skip-bids-validation \
--notrack \
--omp-nthreads 1
```

The only errors were due to subjects that did not have any valid fMRI runs, but we did not do any manual quality control of outputs. 9,484 participants have at least one output.The data is available in 18 submissions (a summary, including number of files and submission size can be found here [TODO: link]). Detailed information about the files included in each submission are on the second tab of that spreadsheet. Files with no submission name listed have not yet been uploaded. If additional outputs are desired, please reach out to [TODO: contact]. fMRIPrep was run in a singularity container on resources from the NIH High Performance Computing Biowulf cluster. Any papers using these outputs should acknowledge this contribution of computational resources with the following line:

“This work used the computational resources of the NIH HPC (high-performance computing) Biowulf cluster (http://hpc.nih.gov).”

#### Replaced subjects
The initial release was processed prior to new updates to the fast track QC spreadsheet that affected the original inputs for 144 participants. This led to discrepancies in the number of timepoints reported for connectivity matrices (see below) relative to the inputs. The 144 participants were re-processed through the ABCD-BIDS pipeline at the Minnesota Supercomputing Institute (MSI), and being replaced, subsequent the required NDA review. The participants.tsv file indicates which subjects were reprocessed.

These subjects have had their connectivity matrices regenerated and replaced (see: Connectivity Matrices).

#### Connectivity matrices

The 144 participants with replaced fast track QC information mentioned above produced new Gordon 10 and 5 minute connectivity matrices. The old matrices remain valid, but may use different frames from the new matrices.  The labels for these connectivity matrices were defined in Gordon, et al, 2017. These connectivity matrices were created using the DCAN Labs cifit connectivity wrapper (https://github.com/DCAN-Labs/cifti-connectivity/). Timepoints used for connectivity calculations were thresholded based on data quality. Data quality was measured by the total frame displacement (FD) calculated from the frame-by-frame realignment parameters; Frames above an FD of 0.2 mm were excluded. An outlier detection procedure was used to exclude remaining frames that were 2 standard deviations away from the mean.  These procedures match the original procedures used to generate the connectivity matrices in the November release.

*Submission IDs: 36449 - 36452*

#### DWI sidecar JSON patch (Diffusion inputs)

The DWI acquisition parameters from subjects scanned on Philips and GE with MR Software release versions 5.3.0_5.3.0.0 and DV25.0_R02_1549.b respectively (n=423) are missing the required field, PhaseEncodingDirection. This omission is because they reported the axis and not direction; therefore we did a manual check of these images to check the phase encoding direction, so that these JSON inputs are BIDS compatible and can be processed by pipelines like QSIprep. These JSONs have been updated and uploaded.

*Submission ID: 36448*

#### Individual-specific network maps

 Multiple versions of the time series are provided, to allow investigator flexibility in their desired analysis: either exactly 10 minutes of randomly sampled frames, all available frames below the 0.2mm FD threshold, or concatenated rest and task time series data in the following order: rest, MID, n-back, and SST (provided that the participant had an available scan for the task). For full details of inter- and intra- participant reliability, and motion correction, see Hermosillo et al. 2021 (in prep).


#### Infomap

Infomap community detection is an unsupervised method of assigning nodes to communities in a graph based on information theory. Here, grayordinates are treated as nodes, and the edges are the correlation between the nodes.  There are two versions of individual-specific maps available depending on whether not investigators are interested in the contribution of tasks to global network topography. 

The following maps are generated for subjects with at least 10 minutes of low-motion (See Hermosillo et al 2021) resting state data. The following are data subset names:
fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfonly_infomap_singlenet_dscalar.nii 
The following maps are generated with all available minutes below the FD threshold (and corresponding BOLD outlier detection) using concatenated rest and task data.
fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfonly_infomap_singlenet_dscalar.nii

Because the tie density scales exponentially with the number of grayordinates, infomap community detection was only performed on the cortical surface and did not include subcortical structures (i.e. neither brainstem, cerebellum, nor diencephalon).  Note, because infomap is an unsupervised community detection method, the subject may have more or fewer networks than a canonical network set. Where possible, we have attempted to assign networks based on the networks observed in an average dataset using the jaccard similarity (see Gordon et al. 2017), however in some instances the jaccard similarity sufficiently low (<0.1) such that the network did not resemble any of the canonical networks, in which case the network was provided a novel network assignment. 

#### Template matching (single network per grayordinate)

Briefly, in order to generate the templates, Infomap community detection was performed at several tie densities (for full details of average networks, see (Gordon, Laumann, Gilmore, et al. 2017; Gordon, Laumann, Adeyemo, et al. 2017; Laumann et al. 2015) on an average connectivity matrix (n=120 participants) using a two level solution. This provides a common set of networks based on average brain activity from which one can “match” the spatial topography of brain activation of a given grayordinate.
To generate an independent template, a seed-based correlation was performed using an average time series for each network correlated with a smoothed dense time series (spatial Gaussian smoothing kernel of 2.55 mm using each participant’s own mid-thickness surfaces) from each template participant. Seed-based correlation values were averaged across all the participants in the template group (n=164, 9-10 year olds), resulting in a vector (91282 x 1) of average correlation values for each network correlated with each grayordinate. Each network vector was averaged independently across subjects in the template group to generate seed-based templates for each network. We then thresholded each network template at Z ≥ 1.
To generate individual-specific maps for each participant in ABCD groups 1 and 2, we examined the whole-brain connectivity for each grayordinate by correlating the motion-censored dense time series with all other grayordinates resulting in a 91282 x 91282 (or where cortex-only analyses were performed:  59412 x 59412) correlation matrix.  The correlation matrix was Z-scored separately for each hemisphere, and within and between the cortex and subcortical structures. Whole-brain connectivity for each grayordinate was thresholded to only include correlated grayordinates with Z-score values greater than or equal to one. This resulted in a vector of whole-brain connectivity for each grayordinate that only includes grayordinates that are strongly correlated to a given network template.  We then calculate a η2 value between the remaining grayordinates and each of the network templates. The grayordinate is assigned to whichever network with the maximum eta2 value.

```
fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfandsub_templatematching_singlenet_dscalar.nii
fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfonly_templatematching_singlenet_dscalar.nii
```
We are providing individual-specific maps using concatenated tasks and rest using only 10 minutes of low-motion data or using all available low motion data.

```
fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfandsub_templatematching_singlenet_dscalar.nii
fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfonly_templatematching_singlenet_dscalar.nii
fmriresults01_derivatives.func.networkmaps_task-restonly_allmin_Surfandsub_templatematching_singlenet_dscalar.nii
```

#### Template matching (multiple networks per grayordinate)

To generate overlapping networks for each participant, rather than assigning the grayordinate to the network with the maximum eta2 value, we used a data-driven approach to assign multiple networks to each grayordinate. For each network we plotted the distribution of eta2 values. The distribution of similarity (eta2) for each network is both bimodal and skewed, such that most grayordinates do not resemble the network of interest (left peak), and some grayordiante have a spatial connectivity that are very similar to the template network (right peak). The distribution for eta2 values was distributed into 10,000 bins and fitted with a cubic spline then smoothed (2,000 point Savitzky-Golay window), and the local minimum was taken.  We then used this local minimum as the threshold for whether or not a grayordinate would be labelled with this network, where grayordinates above this threshold would receive the network assignment.

The following versions of individual-specific maps are available for subjects that had at least 10 minutes of low-motion resting state data.  Networks were generated using exactly 10 minutes of data to ensure that an identical amount of  time was used to generate correlation matrices for all participants.

```
fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfandsub_templatematching_overlappingnet_dtseries.nii
fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfonly_templatematching_overlappingnet_dtseries.nii
```

In order to include additional participants that had less than 10 minutes of data and to capture additional information for participants that had more than 10 minutes, we have also included networks for participants using all available low-motion data.  Note that subjects will have varying numbers of frames due to each participant’s movement in the scanner at the time of collection.

```
fmriresults01_derivatives.func.networkmaps_task-restonly_allmin_Surfandsub_templatematching_overlappingnet_dtseries.nii
```

Lastly, as mentioned above, task and rest data were concatenated to leverage additional hemodynamic coactivation information related to network connectivity, under the assumption that task-based activations contribute only a miniscule variation to global network communication (Gratton et al. 2018).

```
fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfandsub_templatematching_overlapping_dtseries.nii
fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfonly_templatematching_overlappingnet_dtseries.nii
```

*Submission IDs: 36458 - 36630*

#### Task outputs

abcd-bids-tfmri, a modified version of the TaskfMRIAnalysis stage of the HCP-pipeline (Glasser et al., 2013) developed at University of Vermont by Anthony Juliano, was used to process task-fmri data from the minimally processed ABCD-BIDS (Feczko et al., 2020b) processing pipeline (v.1.0) data, as well as derived ABCC data (Feczko, 2020; ABCD-3165). Given the abcd-bids-tfmri pipeline's focus on reproducibility in neuroimaging, it allows for minimal user input while providing vast flexibility with regard to the task-based fMRI data that can be processed (including the type of task and the number of subject-level runs). Transparency is easily achieved with the abcd-bids-tfmri pipeline as users can efficiently share their command-line that was used in processing their data when presenting their findings. 

Given its focus on CIFTI (like a dtseries) data, the abcd-bids-tfmri pipeline heavily relies on HCP workbench commands (https://www.humanconnectome.org/software/workbench-command). This includes completing the user-specified spatial smoothing (wb_command -cifti-smoothing), converting the smoothed data to and from a format that FSL (Jenkinson et al. 2012) can interpret (wb_command -cifti-convert), separating the dtseries data into its comprised components (wb_command -cifti-separate-all), and reading in pertinent information from the dtseries data (wb_command -file-information), among others. Based on the user-specified parameters for censoring volumes (i.e. initial and/or high-motion frames), the pipeline will read in the filtered motion file (Fair et al., 2020) produced by the ABCD-BIDS processing pipeline and create a matrix for nuisance regression. Finally, high-pass filtering, with a cutoff of 0.005 Hz (200 seconds), is completed before running FSL's FILM (Woolrich et al. 2001). 

For FILM to run, users must supply their own subject-, task-, and run-specific event timing files that are in the FSL standard three column format (i.e. onset, duration, weight/magnitude). Additionally, users need to supply a task-specific fsf template file per task that they will be processing using the abcd-bids-tfmri pipeline. As the abcd-bids-tfmri pipeline modifies this template to make it subject- and run-specific, certain values need to be replaced with specific variables that the abcd-bids-tfmri pipeline will be able to recognize. An example fsf file template for ABCD’s MID task is made available for users to review on ABCC (https://osf.io/psv5m/).

Users can specify which task data they would like to process by providing a list of task names within the abcd-bids-tfmri pipeline’s command line interface. If the user specifies multiple runs of the task, the pipeline will complete higher-level analyses (i.e. fixed effects modeling) to combine a given subject's run-level data. Therefore if a study has three different fMRI tasks that consist of two runs, all six level 1 analyses and all three level 2 analyses can be completed for a subject with a single run of the abcd-bids-tfmri pipeline. 

The outputs of the abcd-bids-tfmri pipeline include the fully-processed dtseries data that are subsequently ready for the user to perform their desired third-level or group-wise analyses.


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


## 5. Corrections

This sub-section is for describing errors which have occurred over time.

### `task-rest_bold.json`

Discovered in the middle of June 2020, the modality-specific BIDS inherited `task-rest_bold.json` file at the top of the directory tree which is nested in almost every `task-rest` associated record in the NDA database has a typo in it.  The `"TaskDescription"` key has a value of `"See http://www.cognitiveatlas.org/task/id/tsk_4a57abb949e1a/"`.  However, this link goes to the stop signal task page on the Cognitive Atlas website.  Instead you should refer to [the Cognitive Atlas website for "rest eyes open"](http://www.cognitiveatlas.org/task/id/trm_4c8a834779883/).  This site describes the task as:

> Subjects rest passively with their eyes open. Often used as a baseline for comparison for other tasks.

### `derivatives.func.runs_task-rest_volume`

This data subset was originally uploaded in Release 1.1.0, but was missing all runs chronologically numbered 3 and up.  We are uploading these missing data in Release 1.1.2.

### `updated_dwi_input_json`

The DWI acquisition parameters from all subjects scanned on GE with MR Software release DV25.0_R02_1549.b (n=281) are missing the required field, PhaseEncodingDirection. This omission is because they reported the axis and not direction.