# Pipeline

## 1. About this Document

This document lightly describes the ABCD-BIDS pipeline, fMRIPrep, and QSIPrep used to process the BIDS input data and output the BIDS derivative data. 

Further documentation for these pipelines can be found by clicking on their respective links.:
1. [abcd-hcp-pipeline](https://hub.docker.com/r/dcanlabs/abcd-hcp-pipeline)
1. [fMRIPrep](https://fmriprep.org/)
1. [QSIPrep](https://qsiprep.readthedocs.io/en/stable/)

## 2. ABCD-BIDS Pipeline

The ABCD-BIDS pipeline is available on [GitHub](https://github.com/ABCD-STUDY/abcd-hcp-pipeline), [Zenodo](https://zenodo.org/record/2587210#.Xc59yldKg7Y), and [DockerHub](https://hub.docker.com/r/dcanlabs/abcd-hcp-pipeline) at the time of this release as the `abcd-hcp-pipeline`.  It is a [BIDS App](https://bids-apps.neuroimaging.io/about/) which takes BIDS input data and uses the methods from both the [Human Connectome Project's minimal preprocessing pipeline](https://doi.org/10.1016/j.neuroimage.2013.04.127) and the [DCAN Labs resting state fMRI analysis tools](https://github.com/DCAN-Labs/dcan_bold_processing) to output preprocessed MRI data in both volume and surface spaces.

It has been designed to be as BIDS compliant and user friendly as possible.   While it has been used here specifically to process the ABCD data, it can be run by any investigator to process a wide variety of BIDS input MRI data as long as the data set contains a T1w image.

Each stage of the larger pipeline has a distinct beginning and ending which is why we consider them stages.  The pipeline completes each step in serial, though some steps can utilize multiple processor cores to speed up processing time.  Below is a short explanation of each stage's intent and some of the methods.

For full details read the following references:

1. [The minimal preprocessing pipelines for the Human Connectome Project. Glasser, et al. NeuroImage. 2013.](https://doi.org/10.1016/j.neuroimage.2013.04.127)
1. [Correction of respiratory artifacts in MRI head motion estimates. Fair, et al. NeuroImage. 2019.](https://doi.org/10.1016/j.neuroimage.2019.116400)


### Stage 1: PreFreeSurfer

The primary goal of PreFreeSurfer is to remove distortions from the anatomical data and align and extract the brain in the subject's native volume space.

Notable deviations from the original HCP minimal preprocessing pipeline include the use of [Advanced Normalization Tools (ANTs)](http://stnava.github.io/ANTs/) to perform denoising and N4 bias field correction, which significantly improves results for subjects scanned on General Electric (GE) and Philips scanners that tend to have more noise and are not always normalized following the scan.  We moved Montreal Neurological Institute (MNI) standard space registration to the PostFreeSurfer stage to make use of the refined brain mask created in the FreeSurfer stage.

We also enabled the ability to process subjects without a T2w image.  A T2w image is necessary for myelin mapping estimates, but it does not make a big enough impact on other outputs such as segmentation and surface generation to require it for all image processing.

### Stage 2: FreeSurfer

The brain gets segmented into predefined structures, the white and pial cortical surfaces are reconstructed, and [FreeSurfer's](https://surfer.nmr.mgh.harvard.edu/) standard folding-based surface registration to FreeSurfer's surface atlas is performed.  This stage remains largely unchanged from the original HCP minimal preprocessing pipeline so refer to [Glasser, et al. 2013](https://doi.org/10.1016/j.neuroimage.2013.04.127) for more information.

### Stage 3: PostFreeSurfer

The primary function of the PostFreeSurfer stage is generating CIFTI surface files and applying surface registration to the Conte-69 surface template.  In addition to this, atlas registration is performed.  By using the refined native space brain mask generated in FreeSurfer the pipeline is able to produce a more robust registration.  Furthermore we found that ANTs' diffeomorphic symmetric image normalization method for registration outperforms FSL's FNIRT-based registration.  Outputs from the GE and Philips scanners benefit from this modification as their input anatomical image contrast was less clear than the normalized from-the-scanner Siemens images (discussed in these release notes' document 2: [Inputs](https://collection3165.readthedocs.io/en/stable/inputs/)).

### Stage 4: FMRIVolume

The FMRIVolume stage marks the start of the functional part of the image processing pipeline and it begins similarly to the anatomical portion with correction of gradient-nonlinearity-induced distortions to the EPIs.  Each volume of the time series is aligned using an [FSL FLIRT](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FLIRT) rigid-body (six degrees-of-freedom) registration to the initial frame to correct for motion.  The initial frame is used because it tends to have greater anatomical contrast.  The registration of each volume results in a twelve column text file containing translation and rotation along each axis and their derivatives.  A de-meaned and linearly de-trended motion parameter file is provided as well for nuisance regression.

One pair of spin echo EPI scans with opposite phase encoding directions are used to correct for distortions in the phase encoding direction of each fMRI volume using [FSL's topup](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/topup).  Only a single pair of spin echo field maps is used for all EPI images despite a pair being taken for each EPI image.  This pair is denoted by the "IntendedFor" field populated in the JSON sidecar metadata associated among field maps.  After removing the distortions, the single-band reference is registered to the T1w image and this registration is used to align all fMRI volumes to the anatomical data independently.  Each fMRI volume is non-linearly registered to MNI space and finally masked.

### Stage 5: FMRISurface

The purpose of the FMRISurface stage is primarily to take a volume time series and map it to the standard CIFTI grayordinates space.  This stage has not been altered from the original pipeline so refer to [Glasser, et al. 2013](https://doi.org/10.1016/j.neuroimage.2013.04.127) for more information.

### Stage 6: DCANBOLDProcessing (DBP)

[DCAN BOLD Processing](https://github.com/DCAN-Labs/dcan_bold_processing) is a signal processing software developed primarily by Dr. Oscar Miranda-Dominguez in the DCAN Labs with the primary function of nuisance regression from the dense time series and providing motion censoring information in accordance with [Power, et al. 2014](https://www.ncbi.nlm.nih.gov/pubmed/23994314).  The motion numbers produced in the FMRIVolume stage are also filtered to remove artifactual motion caused by respiration.  For more information on the respiration filtering see [Correction of respiratory artifacts in MRI head motion estimates. Fair, et al. NeuroImage. 2019.](https://doi.org/10.1016/j.neuroimage.2019.116400).

This stage involves four broad steps:

1. Standard pre-processing
1. Application of a respiratory motion filter
1. Motion censoring followed by standard re-processing
1. Construction of parcellated timeseries

#### 1. DBP Standard pre-processing

Standard pre-processing comprises three steps.  First all fMRI data are de-meaned and de-trended with respect to time.  Next a general linear model is used to denoise the processed fMRI data.  Denoising regressors comprise signal and movement variables.  Signal variables comprise mean time series for white matter, CSF, and the global signal, which are derived from individualized segmentations generated during PostFreesurfer in ACPC-aligned subject native space.  Movement variables comprise translational (X,Y,Z) and rotational (roll, pitch, and yaw) measures estimated by re-alignment during FMRIVolume and their Volterra expansion.  The inclusion of mean greyordinate timeseries regression is critical for most resting-state functional MRI comparisons, as demonstrated empirically by multiple independent labs (Ciric et al., 2017; Power et al., 2017, 2019b; Satterthwaite et al., 2013).  After denoising the fMRI data, the time series are band-pass filtered between 0.008 and 0.09 Hz using a 2nd order Butterworth filter.  Such a band-pass filter is softer than other filters, and avoids potential aliasing of the time series signal.

##### On Global Signal Regression

Global signal regression (GSR) has been consistently shown to reduce the effects of motion on BOLD signals and eliminate known batch effects that directly impact group  comparisons (Ciric et al., 2017; Power et al., 2015, 2019b). Motion censoring (see below) combined with GSR has been shown to be the best existing method for eliminating artifacts produced by motion.

#### 2. DBP Respiratory Motion Filter

In working with ABCD data, we have found that a respiratory artifact is produced within multi-band data (Fair et al., 2020).  While this artifact occurs outside the brain, it can affect estimates of frame alignment, leading to inappropriate motion censoring.  By filtering the frequencies (18.582 to 25.726 breaths per minute) of the respiratory signal from the motion realignment data, our respiratory motion filter produces better estimates of FD.

#### 3. DBP Motion censoring

Our motion censoring procedure is used for performing the standard pre-processing and for the final construction of parcellated timeseries.  For standard pre-processing, data are labeled as "bad" frames if they exceed an FD threshold of 0.3 mm.  Such "bad" frames are removed when demeaning and detrending, and betas for the denoising are calculated using only the "good" frames. For band-pass filtering, interpolation is used initially to replace the "bad" frames and the residuals are extracted from the denoising GLM.  In such a way, standard pre-processing of the timeseries only uses the "good" data but avoids potential aliasing due to missing timepoints.  After motion censoring, timepoints are further censored using an outlier detection approach. Both a mask including outlier detection and a mask without outlier detection are created. [These masks](https://collection3165.readthedocs.io/en/stable/derivatives/#7-motion-mat-file) are HDF5 compatible .MAT files, which contain temporal masks from 0 ("No censoring") to 0.5 mm FD thresholds in steps of 0.01 mm. 

#### 4. DBP Generation of parcellated timeseries for specific atlases

Using the processed resting-state fMRI data, this stage constructs parcellated time series for pre-defined atlases making it easy to construct correlation matrices or perform time series analysis on putative brain areas defined by independent datasets.  The atlases comprise recent parcellations of brain regions that comprise different networks.  In particular, parcellated timeseries are extracted for Evan Gordon’s 333 ROI atlas template (Gordon et al., 2014), Jonathan Power’s 264 ROI atlas template (Power et al., 2011), Thomas Yeo’s 118 ROI atlas template (Yeo et al., 2011), and the HCP’s 360 ROI atlas template (Glasser et al., 2016). These parcellations also include 19 individualized subcortical parcellations.  Since we anticipate newer parcellated atlases as data acquisition, analytic techniques, and knowledge all improve, it is trivial to add new templates for this final stage.

### Stage 7: ExecutiveSummary

The ExecutiveSummary stage produces an HTML visual quality control page that displays a [BrainSprite](https://github.com/simexp/brainsprite.js) viewer of the T1w and T2w segmentation, an overlay of the atlas registration on each single band reference created by FSL's slicer, and a visualization of the movement and grayordinate time series for each fMRI run pre- and post-regression.

### Stage 8: CustomClean

[Custom clean](https://github.com/DCAN-Labs/CustomClean) is an optional (though recommended) stage we use that is especially meant for processing large volumes of data. Custom clean removes some non-critical pipeline outputs to minimize the footprint of a subject's processed dataset.

### Stage 9: FileMapper

[File Mapper](https://github.com/DCAN-Labs/file-mapper) is responsible for mapping the HCP pipeline outputs into valid BIDS derivatives.

## abcd-bids-fmri

abcd-bids-tfmri, a modified version of the TaskfMRIAnalysis stage of the HCP-pipeline (Glasser et al., 2013) developed at University of Vermont by Anthony Juliano, was used to process task-fmri data from the minimally processed ABCD-BIDS (Feczko et al., 2020b) processing pipeline (v.1.0) data, as well as derived ABCC data (Feczko, 2020; ABCD-3165). Given the abcd-bids-tfmri pipeline's focus on reproducibility in neuroimaging, it allows for minimal user input while providing vast flexibility with regard to the task-based fMRI data that can be processed (including the type of task and the number of subject-level runs). Transparency is easily achieved with the abcd-bids-tfmri pipeline as users can efficiently share their command-line that was used in processing their data when presenting their findings. 

Given its focus on CIFTI (like a dtseries) data, the abcd-bids-tfmri pipeline heavily relies on HCP workbench commands (https://www.humanconnectome.org/software/workbench-command). This includes completing the user-specified spatial smoothing (wb_command -cifti-smoothing), converting the smoothed data to and from a format that FSL (Jenkinson et al. 2012) can interpret (wb_command -cifti-convert), separating the dtseries data into its comprised components (wb_command -cifti-separate-all), and reading in pertinent information from the dtseries data (wb_command -file-information), among others. Based on the user-specified parameters for censoring volumes (i.e. initial and/or high-motion frames), the pipeline will read in the filtered motion file (Fair et al., 2020) produced by the ABCD-BIDS processing pipeline and create a matrix for nuisance regression. Finally, high-pass filtering, with a cutoff of 0.005 Hz (200 seconds), is completed before running FSL's FILM (Woolrich et al. 2001). 

For FILM to run, users must supply their own subject-, task-, and run-specific event timing files that are in the FSL standard three column format (i.e. onset, duration, weight/magnitude). Additionally, users need to supply a task-specific fsf template file per task that they will be processing using the abcd-bids-tfmri pipeline. As the abcd-bids-tfmri pipeline modifies this template to make it subject- and run-specific, certain values need to be replaced with specific variables that the abcd-bids-tfmri pipeline will be able to recognize. An example fsf file template for ABCD’s MID task is made available for users to review on ABCC (https://osf.io/psv5m/).

Users can specify which task data they would like to process by providing a list of task names within the abcd-bids-tfmri pipeline’s command line interface. If the user specifies multiple runs of the task, the pipeline will complete higher-level analyses (i.e. fixed effects modeling) to combine a given subject's run-level data. Therefore if a study has three different fMRI tasks that consist of two runs, all six level 1 analyses and all three level 2 analyses can be completed for a subject with a single run of the abcd-bids-tfmri pipeline. 

The outputs of the abcd-bids-tfmri pipeline include the fully-processed dtseries data that are subsequently ready for the user to perform their desired third-level or group-wise analyses.

## fMRIPrep

(TODO: Add more detailed information on pipeline and stages @Thomas Madison)

fMRIPrep is a tool for preprocessing BIDS compatible fMRI datasets. If groups would like to analyze the ABCD fMRI results, these outputs will be helpful for analysis of resting state and task based fMRI data. This is the command that was used:

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

Any papers using outputs from this pipeline should acknowledge this contribution of computational resources with the following line:

“This work used the computational resources of the NIH HPC (high-performance computing) Biowulf cluster (http://hpc.nih.gov).”

## QSIPrep

(TODO: Anders will add from MSI run dir)
