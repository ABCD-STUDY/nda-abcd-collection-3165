# Pipeline

[**NIMH Data Archive (NDA) Collection 3165 - DCAN Labs ABCD-BIDS**](https://nda.nih.gov/edit_collection.html?id=3165)

[**Developmental Cognition and Neuroimaging (DCAN) Labs**](http://www.ohsu.edu/dcan)

[**Adolescent Brain Cognitive Development (ABCD) Study**](https://abcdstudy.org/)

[**Brain Imaging Data Structure (BIDS)**](https://bids.neuroimaging.io/)

---

## 1. About this Document

This document lightly describes the ABCD-BIDS pipeline used to process the BIDS input data and output the BIDS derivative data.

## 2. Pipeline Overview

The ABCD-BIDS pipeline is available on [GitHub](https://github.com/ABCD-STUDY/abcd-hcp-pipeline), [Zenodo](https://zenodo.org/record/2587210#.Xc59yldKg7Y), and [DockerHub](https://hub.docker.com/r/dcanlabs/abcd-hcp-pipeline) at the time of this release as the `abcd-hcp-pipeline`.  It is a [BIDS App](https://bids-apps.neuroimaging.io/about/) which takes BIDS input data and uses the methods from both the [Human Connectome Project's minimal preprocessing pipeline](https://doi.org/10.1016/j.neuroimage.2013.04.127) and the [DCAN Labs resting state fMRI analysis tools](https://github.com/DCAN-Labs/dcan_bold_processing) to output preprocessed MRI data in both volume and surface spaces.

It has been designed to be as BIDS compliant and user friendly as possible.   While it has been used here specifically to process the ABCD data, it can be run by any investigator to process a wide variety of BIDS input MRI data as long as the data set contains a T1w image.

Each stage of the larger pipeline has a distinct beginning and ending which is why we consider them stages.  The pipeline completes each step in serial, though some steps can utilize multiple processor cores to speed up processing time.  Below is a short explanation of each stage's intent and some of the methods.

For full details read the following references:

1. [The minimal preprocessing pipelines for the Human Connectome Project. Glasser, et al. NeuroImage. 2013.](https://doi.org/10.1016/j.neuroimage.2013.04.127)
1. [Correction of respiratory artifacts in MRI head motion estimates. Fair, et al. bioRxiv. 2018.](https://www.biorxiv.org/content/10.1101/337360v1.article-info)

## 3. Pipeline Stages

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

### Stage 6: DCANBOLDProcessing

[DCAN BOLD Processing](https://github.com/DCAN-Labs/dcan_bold_processing) is a signal processing software developed primarily by Dr. Oscar Miranda-Dominguez in the DCAN Labs with the primary function of nuisance regression from the dense time series and providing motion censoring information in accordance with [Power, et al. 2014](https://www.ncbi.nlm.nih.gov/pubmed/23994314).  The motion numbers produced in the FMRIVolume stage are also filtered to remove artifactual motion caused by respiration.  For more information on the respiration filtering see [Correction of respiratory artifacts in MRI head motion estimates. Fair, et al. 2018](https://www.biorxiv.org/content/10.1101/337360v1.article-info).

### Stage 7: ExecutiveSummary

The ExecutiveSummary stage produces an HTML visual quality control page that displays a [BrainSprite](https://github.com/simexp/brainsprite.js) viewer of the T1w and T2w segmentation, an overlay of the atlas registration on each single band reference created by FSL's slicer, and a visualization of the movement and grayordinate time series for each fMRI run pre- and post-regression.

### Stage 8: CustomClean

[Custom clean](https://github.com/DCAN-Labs/CustomClean) is an optional (though recommended) stage we use that is especially meant for processing large volumes of data. Custom clean removes some non-critical pipeline outputs to minimize the footprint of a subject's processed dataset.

### Stage 9: FileMapper

[File Mapper](https://github.com/DCAN-Labs/file-mapper) is responsible for mapping the HCP pipeline outputs into valid BIDS derivatives.
