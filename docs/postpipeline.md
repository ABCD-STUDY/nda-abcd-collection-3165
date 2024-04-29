# Post Pipeline Tools

After fMRI processing we include a couple post processing and analysis steps

## Connectivity Matrix Generation
Parcellated connectivity matrices (pconns) are much smaller, and can be further explored to estimate within-study results reproducibility. Using the different sets of parcellated timeseries, we calculated the lag-zero pearson’s correlation coefficient between every pair of parcellated regions of interest (ROIs). Per subject and parcellation scheme, this results in an ROI x ROI correlation matrix. The provided connectivity derivatives were extracted with an FD threshold of 0.2 mm for [5 minutes and 10 minutes of data](https://collection3165.readthedocs.io/en/stable/derivatives/#4-functional). A variance stabilization procedure was applied to the correlations prior to uploading. Specifically, the inverse hyperbolic tangent was applied to the correlations: z = arctanh(r). The maximum value displayed is 7.254329. Applying the hyperbolic tangent will recover the pearson's correlation: r = tanh(z).

## Individual-specific network maps 

Multiple versions of the time series are provided, to allow investigator flexibility in their desired analysis: either exactly 10 minutes of randomly sampled frames, all available frames below the 0.2mm FD threshold, or concatenated rest and task time series data in the following order: rest, MID, n-back, and SST (provided that the participant had an available scan for the task). For full details of inter- and intra- participant reliability, and motion correction, see Hermosillo et al. 2021 (in prep).

### Infomap

Because brain synapses grow as a complex system of learning and evolving, neural networks don’t dutifully conform to anatomical coordinates across individuals. Therefore, it often makes sense to consider “function” as the pattern of connections between brain regions rather than assume function occurs at a specific anatomical location.  Graph theory is an appropriate avenue for investigation because we can redefine brain regions as anatomically-irrespective nodes, and define the correlation (i.e. connectivity) between them as edges.  Nodes that communicate heavily with each other are considered to be a part of the same community or network.  Networks for the ABCD collection were detected using infomap (D. Edler, A. Holmgren and M. Rosvall, The MapEquation software package, available online at http://www.mapequation.org).  Infomap is an algorithm that describes information flow in the network, by attempting to minimize the number of bits necessary to describe the whole network (Martin Rosvall and Bergstrom 2008; M. Rosvall, Axelsson, and Bergstrom 2009). For example, would it require fewer bits to describe the whole brain with few networks containing many nodes, or many networks with fewer nodes? Similar algorithms maximize modularity metrics, however, Infomap uses a random walk algorithm that uses edge weights (in this case, it uses connectivity) to determine the minimum descriptor code length necessary. Importantly, while the solution provides modules, it is not designed to maximize modularity.  Importantly, neural networks have been shown to be scalable. As others have done previously (Gordon et al. 2017), we thresholded the whole brain correlation matrix  (91282 x 91292 grayordinates) to the top x% of connections (or edges) because of the computational limitations of using a full set of 8.1 billion connections as descriptors in the map equation. We thresholded the connectivity matrix at a threshold of 0.3%, 0.4%, 0.5%, 1%, 1.5%, 2%, 2.5%,  and 3%. These threshold were chosen to scale the number of edges.

To generate a consensus across multiple edge percentages, we implemented a methodology developed by Gordon and colleagues(Gordon et al. 2017). Briefly, after infomap detected communities for each subject, Putative network assignments were then assigned to each subject’s communities by matching them at each threshold to the independent group networks from the University of Washington (n=120). For each individual, at each percentage threshold, the spatial overlap of each unknown community was compared to each one of the independent group networks separately using the Jaccard similarity index. The unknown community was then assigned that network identity to which it had the highest Jaccard similarity index.  If the Jaccard Index was less than 0.1, the community remained unassigned, so as to avoid assigning communities to known networks based on only a few vertices. Assignments were first made with the large, well-known networks (Default, Lateral Visual, Motor, Fronto-Parietal, Cingulo-Opercular, Dorsal Attention), and then to the smaller, less well-known networks (e.g. Ventral Attention, Salience, Parietal Memory, lateral hand-face motor ).  In each individual, a “consensus” network assignment was created by giving each grayordinate the canonical assignment it had at the sparsest threshold. 

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

### Movement Criteria
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

## Demographically Matched Subsets

Generating randomly-chosen demographically matched subsets is one of the first steps in `automated_subset_analysis.py`. If you only need subset pairs, then you do not need to run the script to completion.

`automated_subset_analysis.py` randomly selects subsets of each ABCD ARM and checks whether they have a statistically significant difference on any of the given demographic variables. Once it finds a subset of each ARM where each does not statistically differ on those variables from the other ARM, it saves each subset pair to a `.csv` file.

For more information on what the subset code does, see the `Explanation of Process` section of [the top-level README.md on GitHub](https://github.com/DCAN-Labs/automated-subset-analysis/blob/main/README.md).

### Requirements

You will need:

**Demographics Files**

ABCD Study ARM-1 and ARM-2 demographics `.csv` files. 

Each demographics file should contain subjects only for its ARM.

- The demographics files must be in `.csv` format (text files with columns separated by commas and rows separated by newlines).
- The first line of each demographics file should list all of the column names. The first column should be blank, because it is an index/enumerated column. The second should name the Subject ID variable, the third should name the group/ARM variable, and the rest should name demographic variables.
- Every other line in the demographics file should list each subject's details for each column.
  - For more details, see the `Required Arguments (2)` section of the [top-level automated-subset-analysis `README.md` file](https://github.com/DCAN-Labs/automated-subset-analysis/blob/main/README.md).

Several copies and subsets of the ABCD ARMS demographics files are kept in the directory at the path below:

`/home/feczk001/shared/projects/ABCD/core_task_activation_study/code/demographics/`

I typically use the `group1_demo_original.csv` and `group2_demo_original.csv` demographics files in the directory above.

**Group Average Matrices**

You will need either

1. two averaged matrix `.pconn.nii` files, one for ARM-1 and another for ARM-2; or
2. two `.conc` files listing `.pconn.nii` file paths. Each `.conc` file must list the path to every  each file matrix file for every individual subject in each ARM. 

### Example

To generate matched subsets using the existing subset analysis script on the MSI, run `automated_subset_analysis.py` using the Linux Bash Shell code provided below.

In the code below, you can and should change the variable declarations so they match (a) the data that you can access and (b) what you are trying to do. I filled in the paths to the input data I have normally used.

```bash
# Parent directory containing ABCD data, including input files for this script
dir_ABCD="/home/feczk001/shared/projects/ABCD";

# Directory containing average brain scan .pconn.nii files 
dir_pconns="${dir_ABCD}/conan_subset_analysis/gp-avg-pconns";

# Directory containing demographics .csv files
dir_demo="${dir_ABCD}/core_task_activation_study/code/demographics";

# Directory containing subset analysis code
dir_ASA="/home/feczk001/shared/code/internal/utilities/automated_subset_analysis";

# Directory to save subset analysis output files into
dir_output="./test/output_dir/"; 

# How many participants should be in each subset? Do you want to generate
# subsets of multiple sizes, e.g. 50 participants in one and 500 in another?
subjects_per_subset="50 100 500";

# How many subset pairs of each subset size do you want to generate? 
pairs_to_make=2;

# Run the subset analysis script
python3 ${dir_ASA}/automated_subset_analysis.py \
  ${dir_demo}/group1_demo_original.csv \
  ${dir_demo}/group2_demo_original.csv \
  --group-1-avg-file ${dir_pconns}/gp1_pconns_AVG.pconn.nii  \
  --group-2-avg-file ${dir_pconns}/gp2_pconns_AVG.pconn.nii  \
  --subset-size ${subjects_per_subset} \
  --n-analyses ${pairs_to_make} \
  --output ${dir_output}  
```

### Output Files

The script will save the demographically-matched subset pair into a text file in the `--output` directory. Each one will be named `subset_{x}_with_{y}_subjects.csv`, where `x` ranges from 1 to the `--n-analyses` value and `y` is every value in the `--subset-size` list. 

### Warnings and Troubleshooting

When you tried to run the script, did it...

**Print Too Much Text?**

In its current form, the subset analysis script will print a *lot* of text to the terminal. If you would rather hide that text, then you can add ` 2>1 1>/dev/null` to the end of the command in the `Example` section above.

**Take Too Much Time?**

Several factors can make the `automated_subset_analysis.py` script take a longer amount of time than necessary to generate subset pairs:

- If one of the `--subset-size` values is below `50`, it can take a longer time to randomly generate a demographically matched subset pair. I think that is mostly because a smaller number of subjects usually means a higher standard deviation in the values of the demographic variables.
  - Subset sizes below 25 (or so) take an impossibly long time to generate. If the subsets are taking far too long to generate, you may need to either (a) try a higher subset size or (b) use `--no-matching` to skip demographic matching on family ID.
- If you provide `.conc` files (`--matrices-conc-1` and `--matrices-conc-2`) instead of average `pconn.nii` files (`--group-1-avg-file` and `--group-2-avg-file`), then the subset analysis script will generate an average `.pconn.nii` file for each `ARM`. That takes significantly longer than using an existing average `pconn.nii` file.

**Crash and/or Display an Error?**

Below are errors that I have commonly encountered while testing and using the `automated_subset_analysis.py` script, what they mean, and how I prevented/fixed them.

`Not enough subjects in population to randomly select a sample with`... or `ValueError: Cannot take a larger sample than population when 'replace=False'`

Full error message: `Not enough subjects in population to randomly select a sample with {X} subjects, because {Y} subjects cannot be randomly swapped out from a pool of {Z} subjects`

Problem: At least one of the `--subset-size` values is too high.

Solutions:

1. Reduce the largest `--subset-size` value to, at most, about 45% of the smallest ARM's size. 
1. Include the `--no-matching` flag to skip family matching. 

Explanation: 

1. All `--subset-size` values must be large enough to demographically match the other ARM, but small enough to swap out any participants whose inclusion is invalid for any reason (e.g. they have family members outside the subset). For example, if the smallest ARM has 3000 subjects, then errors will occur unless you keep the `--subset-size` numbers under 1500. If the errors may still occur,you can try reducing the largest `--subset-size` further. The new subset size must be less than about 45% of the smallest ARM's size excluding participants with NaNs in the demographic file. The number and percentage of participants with NaNs in each group is printed right after the script begins.
1. By default, `automated_subset_analysis.py` checks that every subset (a) has the same proportion of twins/triplets as the other ARM, and (b) excludes anyone with family members outside the subset. The `--no-matching` flag turns both checks off. It lets you to generate subsets of less than 25, or larger than half the ARM size. It also speeds up subset generation/checking.

`FileNotFoundError: No such file or no access: '/home/exacloud/`...

Problem: Incorrect `.pconn.nii` paths were read either from the group demographics file(s), from the `--matrices-conc-1` file, or from the `--matrices-conc-2` file.

Solutions:

1. If you only want to generate subset pairs, then simply ignore this error. The subset pairs were already generated.
1. Ensure that every line/row in the last column of the group 1 and 2 demographics files contains one path to an existing, readable `.pconn.nii` file.
1. If you can't/won't change the demographics file(s), then use the `--matrices-conc-1` and `--matrices-conc-2` flags with a path to a valid `.conc` file after each. 

Explanation:

1. Even using the code I gave in the `Example` section above, the script may still crash with an error message -- *after* generating your subsets. That's fine if you only need the subsets pairs. (A later version of the script should include a specific option to only generate subsets.)
1. The `automated_subset_analysis` code was originally written to run on OHSU's `Rushmore` and `Exacloud` servers. It has directory paths on those servers hardcoded. So, if the script cannot find a `.pconn.nii` file at the given path, it will look for the file using the hardcoded Exacloud directory path.
1. The `--matrices-conc-1` (`-conc1`) and `--matrices-conc-2` (`-conc2`) arguments exist to replace the paths in the last column of the demographics file. If there are no valid paths in the last column of the demographics file, then you can provide `-conc1` and `-conc2` arguments. Each should be a `.conc` file with one valid path to an existing `.nii` file per line. Every line of each `.conc` file must be for the same subject as the corresponding line number in the demographics files: Because the demographics files should have header lines but the `.conc` files shouldn't, line 1 of the group 1 `.conc` file corresponds to line 2 of the group 1 demographics `.csv` file, line 2 of the `.conc` to line 3 of the `.csv`, etc.

**For More Info**

For more information on how to run `automated_subset_analysis.py` and which arguments/options to run it with, see the `README.md` files in the [automated subset analysis repository on GitHub](https://github.com/DCAN-Labs/automated-subset-analysis), especially the top-level `README.md`

See [this page](recommendations.md/#2-the-bids-participants-files-and-matched-groups) for the names and descriptions of the main demographic variables.

### Contact Me

Feel free to contact me with questions about how to run the automated-subset-analysis code. I will include my contact information below:

- GitHub: @GregConan
- Primary email: gregmconan@gmail.com

This document is a work-in-progress.

- Created by @GregConan on 2023-12-22
- Updated by @GregConan on 2024-01-04