# Post Pipeline Tools

After fMRI processing we include a couple post processing and analysis steps

## Connectivity Matrix Generation
Parcellated connectivity matrices (pconns) are much smaller, and can be further explored to estimate within-study results reproducibility. Using the different sets of parcellated timeseries, we calculated the lag-zero pearson’s correlation coefficient between every pair of parcellated regions of interest (ROIs). Per subject and parcellation scheme, this results in an ROI x ROI correlation matrix. The provided connectivity derivatives were extracted with an FD threshold of 0.2 mm for [5 minutes and 10 minutes of data](https://collection3165.readthedocs.io/en/stable/derivatives/#4-functional). A variance stabilization procedure was applied to the correlations prior to uploading. Specifically, the inverse hyperbolic tangent was applied to the correlations: z = arctanh(r). The maximum value displayed is 7.254329. Applying the hyperbolic tangent will recover the pearson's correlation: r = tanh(z).

## Individual-specific network maps 

Multiple versions of the time series are provided, to allow investigator flexibility in their desired analysis: either exactly 10 minutes of randomly sampled frames, all available frames below the 0.2mm FD threshold, or concatenated rest and task time series data in the following order: rest, MID, n-back, and SST (provided that the participant had an available scan for the task). For full details of inter- and intra- participant reliability, and motion correction, see Hermosillo et al. 2021 (in prep).

### Infomap

Infomap community detection is an unsupervised method of assigning nodes to communities in a graph based on information theory. Here, grayordinates are treated as nodes, and the edges are the correlation between the nodes.  There are two versions of individual-specific maps available depending on whether not investigators are interested in the contribution of tasks to global network topography. 

The following maps are generated for subjects with at least 10 minutes of low-motion (See Hermosillo et al 2021) resting state data. The following are data subset names:

```
fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfonly_infomap_singlenet_dscalar.nii 
```

The following maps are generated with all available minutes below the FD threshold (and corresponding BOLD outlier detection) using concatenated rest and task data.

```
fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfonly_infomap_singlenet_dscalar.nii
```

Because the tie density scales exponentially with the number of grayordinates, infomap community detection was only performed on the cortical surface and did not include subcortical structures (i.e. neither brainstem, cerebellum, nor diencephalon).  Note, because infomap is an unsupervised community detection method, the subject may have more or fewer networks than a canonical network set. Where possible, we have attempted to assign networks based on the networks observed in an average dataset using the jaccard similarity (see Gordon et al. 2017), however in some instances the jaccard similarity sufficiently low (<0.1) such that the network did not resemble any of the canonical networks, in which case the network was provided a novel network assignment. 

### Template matching

Template matching is a supervised algorithm for identifying neural networks using resting state connectivity data, based on the spatial topography.  [Click here for Documentation of source code as well as a written tutorial.](https://github.com/DCAN-Labs/compare_matrices_to_assign_networks)

### Creating a template

This technique has been used previously to identify networks ([Gordon et al. 2017](https://pubmed.ncbi.nlm.nih.gov/26464473/); [Dworetsky 2021](https://www.sciencedirect.com/science/article/pii/S1053811921004419)). Briefly, in order to generate the templates, Infomap community detection was performed at several tie densities (for full details of average networks, see ([Gordon, Laumann, Gilmore, et al. 2017](https://pubmed.ncbi.nlm.nih.gov/26464473/); [Gordon, Laumann, Adeyemo, et al. 2017](https://doi.org/10.1016/j.neuron.2015.06.037); [Laumann et al. 2015](https://pubmed.ncbi.nlm.nih.gov/26212711/)) on an average connectivity matrix (n=120 participants) using a two level solution. This provides a common set of networks based on average brain activity from which one can “match” the spatial topography of brain activation of a given grayordinate. To generate a set independent network templates, a seed-based correlation was performed using an average time series for each network correlated with a smoothed dense time series (spatial Gaussian smoothing kernel of 2.55 mm using each participant’s own mid-thickness surfaces) from each template participant. Seed-based correlation values were averaged across all the participants in the template group (n=164, 9-10 year olds), resulting in a vector (91282 x 1) of average correlation values for each network correlated with each grayordinate. Each network vector was averaged independently across subjects in the template group to generate seed-based templates for each network. We then thresholded each network template at Z ≥ 1.

### Matching connectivity to a network template

To generate individual-specific maps for each participant in ABCD groups 1 and 2, we examined the whole-brain connectivity for each grayordinate by correlating the motion-censored dense time series with all other grayordinates resulting in a 91282 x 91282 (or where cortex-only analyses were performed: 59412 x 59412) correlation matrix. The correlation matrix was Z-scored separately for each hemisphere, and within and between the cortex and subcortical structures. Whole-brain connectivity for each grayordinate was thresholded to only include correlated grayordinates with Z-score values greater than or equal to one. This resulted in a vector of whole-brain connectivity for each grayordinate that only includes grayordinates that are strongly correlated to a given network template. We then calculate a η2 value between the remaining grayordinates and each of the network templates. The grayordinate is assigned to whichever network with the maximum eta2 value. Two versions of individual-specific networks are available: one version was created without using subcortical data from the timeseries, the other was created including subcortical timeseries data.  For the following files, participants had varying amounts of data below the framewise displacement threshold (FD=0.2mm, see [Hermosillo et al. 2021](https://www.biorxiv.org/content/10.1101/2022.01.12.475422v1) for additional motion censor criteria).  Next were generated concatenated rest and task timeseries data.

    - fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfandsub_templatematching_singlenet_dscalar.nii
    - fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfonly_templatematching_singlenet_dscalar.nii

#### Movement Criteria

 We are providing individual-specific maps using concatenated tasks and rest using only 10 minutes of low-motion data or using all available low motion data.

    - fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfandsub_templatematching_singlenet_dscalar.nii
    - fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfonly_templatematching_singlenet_dscalar.nii
    - fmriresults01_derivatives.func.networkmaps_task-restonly_allmin_Surfandsub_templatematching_singlenet_dscalar.nii

Dscalars are provided in a fsLR32k format. In the dscalars, each grayordinate (n=91282) has a single value, where each value corresponds with the following key:

- 1 = Default mode network (DMN)
- 2 = Visual network (VIS)
- 3 = Frontoparietal network (FPN)
- 4 = *there is no network 4*
- 5 = dorsal attention network (DAN)
- 6 = *there is no network 6*
- 7 = Ventral attention network (VAN)
- 8 = Salience network (Sal)
- 9 = Cingulo-opercular network (CO)
- 10 = Dorsal sensorimotor network (SMd)
- 11 = the lateral sensorimotor Network(SMl)
- 12 = the auditory network(AUD)
- 13 = the temporal pole network (TP)
- 14 = the medial temporal network(MTL)
- 15 =  the parietal occipital network(PON)
- 16 = Parietal medial network(PMN)

---

## Template matching (multiple networks per grayordinate)

To generate overlapping networks for each participant, we used the identical template networks as described above, however, rather than assigning the grayordinate to the network with the maximum eta^2 value, a data-driven approach was used to assign multiple networks to each grayordinate. For each network we plotted the distribution of eta^2 values. The distribution of similarity (eta^2) for each network is both bimodal and skewed, such that most grayordinates do not resemble the network of interest (left peak), and some grayordiante have a spatial connectivity that are very similar to the template network (right peak). The distribution for eta^2 values was distributed into 10,000 bins and fitted with a cubic spline then smoothed (2,000 point Savitzky-Golay window), and the local minimum was taken. We then used this local minimum as the threshold for whether or not a grayordinate would be labelled with this network, where grayordinates above this threshold would receive the network assignment.

#### Movement Criteria
The following versions of individual-specific maps are available for subjects that had at least 10 minutes of low-motion resting state data. Networks were generated using exactly 10 minutes of data to ensure that an identical amount of time was used to generate correlation matrices for all participants.

    -fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfandsub_templatematching_overlappingnet_dtseries.nii
    -fmriresults01_derivatives.func.networkmaps_task-restonly_10min_Surfonly_templatematching_overlappingnet_dtseries.nii

In order to include additional participants that had less than 10 minutes of data and to capture additional information for participants that had more than 10 minutes, we have also included networks for participants using all available low-motion data. 
Note that subjects will have varying numbers of frames due to each participant’s movement in the scanner at the time of collection.

    fmriresults01_derivatives.func.networkmaps_task-restonly_allmin_Surfandsub_templatematching_overlappingnet_dtseries.nii

Lastly, as mentioned above, task and rest data were concatenated to leverage additional hemodynamic coactivation information related to network connectivity, under the assumption that task-based activations contribute only a miniscule variation to global network communication ([Gratton et al. 2018](https://www.sciencedirect.com/science/article/pii/S0896627318302411)).

    fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfandsub_templatematching_overlapping_dtseries.nii
    fmriresults01_derivatives.func.networkmaps_task-restandtask_allmin_Surfonly_templatematching_overlappingnet_dtseries.nii

Overlapping .dtseries.nii cifiti files are provided with 1 network per column of the timeseries file in the same order provided above in the single network example.