# Recommendations

Note: Clicking any link within the readthedocs site will not open a new web browser tab.  If you want to keep your docs open, either middle-click or right-click and choose open in new tab for the links you would like to follow.

---

## 1. About this Document

This document highlights common recommendations for usage of the collection 3165 data.

## 2. Downloading and Unpacking Data

There are two ways to download ABCD Study data and get BIDS inputs or derivatives:

1. (*PREFERRED*) Downloading from NDA Collection 3165 will provide you an "associated files" spreadsheet with AWS S3 links and other key information.  DCAN Labs has designed [a GitHub repository for selectively downloading only parts of the BIDS input and derivative data, the "nda-abcd-s3-downloader"](https://github.com/ABCD-STUDY/nda-abcd-s3-downloader).
1. [ABCD Fast Track Data on the NDA](https://nda.nih.gov/abcd/query/abcd-fast-track-data.html) can alternatively be downloaded and unpacked into BIDS with the [ABCD-STUDY abcd-dicom2bids GitHub repository](https://github.com/ABCD-STUDY/abcd-dicom2bids).

## 3. MATLAB Motion Mask Files

In order to make an accurate correlation matrix, use the MATLAB motion mask file described in release document 4, [Derivatives](https://collection3165.readthedocs.io/en/stable/derivatives/), under the **Motion MAT File** heading.

## 4. Interacting with CIFTI Data Types

Released data follows the standards defined by the Human Connectome Project, such as reporting different metrics in standard grayordinate space and saving data using CIFTI standard file formats.

A couple of great blog posts can be read online for more detailed coverage of CIFTI data types and interaction.  These topics will only be briefly discussed in this document.

- [**NIfTI, CIFTI, GIFTI in the HCP and Workbench: a primer** by Jo Etzel from Washington University in St. Louis](http://mvpa.blogspot.com/2014/03/nifti-cifti-gifti-in-hcp-and-workbench.html)
- [**A layman’s guide to working with CIFTI files** by Mandy Mejia from Indiana University](https://mandymejia.com/2015/08/10/a-laymans-guide-to-working-with-cifti-files/)

The following data types, listed by file name extension, are available in this collection's BIDS derivatives.

1. `.dlabel.nii`: "Dense label files" contain the "labels" (a.k.a. parcels) within parcellations.
1. `.dscalar.nii`: "Dense scalar files" contain things like cortical thickness, curvature, and myelin maps on a scalar value per surface vertex basis.
1. `.dtseries.nii`: "Dense time series" contain functional time series from fMRI runs in surface space on a vector time series per surface vertex basis.
1. `.ptseries.nii`: "Parcellated time series," contain the dense time series parcellated by the corresponding dense label file.
1. `.surf.gii`: "GIFTI surface files" contain the "geometry" surface delineations/definitions of a particular surface, like the midthickness surface for example.

### Dense and Parcellated Time Series

The dense and parcellated time series files should regularly be analyzed using their corresponding motion files.  Periods of high motion should be censored out for the purposes of regular connectivity/correlation matrix analysis.

### Correlation Matrices

Correlation matrices should be generated from either the dense or parcellated time series using frame censoring from the aforementioned MATLAB motion mask files.  The [DCAN-Labs/cifti-connectivity tools](https://github.com/DCAN-Labs/cifti-connectivity) should be used which account for choosing a framewise displacement threshold, an acceptable amount of remaining minutes threshold, and outputting either dense (`.dconn.nii`) or parcellated (`.pconn.nii`) connectivity matrices.

### Connectome Workbench

For visualization of all of these CIFTI files, use [Connectome Workbench](https://www.humanconnectome.org/software/connectome-workbench).  

## 5. DCAN Labs Software

We have built tools to utilize this data using our recommended methods.  Read on for descriptions of each publicly-hosted open-source software GitHub repository from [DCAN-Labs](https://github.com/DCAN-Labs).  

### ABCD-BIDS Pipeline: [https://github.com/ABCD-STUDY/abcd-hcp-pipeline](https://github.com/ABCD-STUDY/abcd-hcp-pipeline)

See these release notes' document 3: [**Pipeline**](https://collection3165.readthedocs.io/en/stable/pipeline/).

### Custom Clean: [https://github.com/DCAN-Labs/CustomClean](https://github.com/DCAN-Labs/CustomClean)

Custom clean is a generalized piece of software which is great for defining common output files to delete when presented with similar folders of files.  This is a common occurrence in data processing where you process an input dataset and end up with a similar set of output files for every processed job following some output folder convention.

First you use a graphical user interface (GUI) to teach custom clean about what files should be regularly cleaned.  The custom clean GUI outputs a "cleaning JSON file" which has all the definitions for files to be cleaned within.  After that you can call the custom clean script with the cleaning JSON file as many times as you like on as many similar folders as you like.

### File Mapper: [https://github.com/DCAN-Labs/file-mapper](https://github.com/DCAN-Labs/file-mapper)

File mapper is another generalized piece of software which is great for defining a common output folder/file hierarchy based on a template set of files to be mapped and an output hierarchy to which you can map.  We use it to conform the commonly output "Human Connectome Project-styled" processed folders into BIDS-compliant derivative folders.

Much like custom clean, you define a JSON file which says how to map a file from some common input to some common output in order to "reshape" your data outputs.

## 6. BIDS Folder Layout

Your final BIDS folder structure will look like this tree if you download everything.  Full descriptions of these BIDS input and BIDS derivative data are located in these release notes' documents 2 and 4, [**Inputs**](https://collection3165.readthedocs.io/en/stable/inputs/) and [**Derivatives**](https://collection3165.readthedocs.io/en/stable/derivatives/) respectively.

```
.
├── dataset_description.json
├── README
├── CHANGES
├── task-(MID|nback|rest|SST)_bold.json
├── derivatives
│   └── abcd-hcp-pipeline
│       ├── ##FreeSurferSubcortical_dparc.dlabel.nii
│       └── sub-NDARINV########
│           └── ses-####
│               ├── img/*
│               ├── sub-NDARINV########_ses-####.html
│               ├── anat
│               │   ├── sub-NDARINV########_ses-####_hemi-(L|R)_space-(MNI|T1w)_mesh-(fsLR32k|fsLR164k|native)_midthickness.surf.gii
│               │   ├── sub-NDARINV########_ses-####_space-ACPC_dseg.nii.gz
│               │   ├── sub-NDARINV########_ses-####_space-fsLR32k_curv.dscalar.nii
│               │   ├── sub-NDARINV########_ses-####_space-fsLR32k(_desc-smoothed)_myelinmap.dscalar.nii
│               │   ├── sub-NDARINV########_ses-####_space-fsLR32k_sulc.dscalar.nii
│               │   └── sub-NDARINV########_ses-####_space-fsLR32k_thickness.dscalar.nii
│               └── func
│                   ├── sub-NDARINV########_ses-####_task-(MID|nback|rest|SST)_bold_desc-filtered_timeseries.dtseries.nii
│                   ├── sub-NDARINV########_ses-####_task-(MID|nback|rest|SST)_bold_atlas-##FreeSurferSubcortical_desc-filtered_timeseries.ptseries.nii
│                   ├── sub-NDARINV########_ses-####_task-(MID|nback|rest|SST)_desc-filtered_motion_mask.mat
│                   ├── sub-NDARINV########_ses-####_task-(MID|nback|rest|SST)_run-#_bold_timeseries.dtseries.nii
│                   ├── sub-NDARINV########_ses-####_task-(MID|nback|rest|SST)_run-#_desc-filtered_motion.tsv
│                   └── sub-NDARINV########_ses-####_task-(MID|nback|rest|SST)_run-#_motion.tsv
├── sourcedata
│   └── sub-NDARINV########
│       └── ses-####
│           └── func
│               └── sub-NDARINV########_ses-####_task-(MID|nback|SST)_run-##_bold_EventRelatedInformation.txt
└── sub-NDARINV########
    └── ses-####
        ├── anat
        │   ├── sub-NDARINV########_ses-####(_rec-normalized)_T#w.json
        │   └── sub-NDARINV########_ses-####(_rec-normalized)_T#w.nii.gz
        ├── fmap
        │   ├── sub-NDARINV########_ses-####_dir-(AP|PA)_run-##_epi.json
        │   └── sub-NDARINV########_ses-####_dir-(AP|PA)_run-##_epi.nii.gz
        └── func
            ├── sub-NDARINV########_ses-####_task-(MID|nback|rest|SST)_run-##_bold.json
            └── sub-NDARINV########_ses-####_task-(MID|nback|rest|SST)_run-##_bold.nii.gz
```
