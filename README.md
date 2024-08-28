# ASCATlp

ASCAT low pass (ASCATlp) is a simple implementation of the logic of the [ASCAT algorithm](https://github.com/VanLoo-lab/ascat) (Van Loo Lab) using only log2 ratio data. It is therefore suitable for analysis of low pass / low coverage / shallow whole genome sequencing data where B-allele frequency information is not available.

Following the original ASCAT tool, the approach takes the following steps:

1) Calculate the goodness of fit of a combination of purity and ploidy as the sum of squared differences from (positive) integer states. Although `ASCATlp` can infer copy number = 0, we penalise it when calculating our distance measure by using copy number = 1 as the closest valid integer value. Negative copy number values are further penalised by comparing them to their modulus (i.e. -3.1 is compared to 3).

2) We then perform a grid search of purity and ploidy parameters and find local optima in all possible 3x3 grids and report all local minima. The local minima with the smallest distance is taken as the solution and the others are reported. A single dimension search of a single ploidy value (solving only purity) is also available and is initially the default setting (see options to perform grid search).

3) The solved combination of purity and ploidy is then used to recalculate integer states and values less than 0 are capped at 0.

### Input:

The only input that ASCATlp requires is segmented log2 ratio values. These are provided as a vector per single sample to `lrrs`. Typically we use per bin segment values which then weights segments by the size of the alteration.

### Options:

`fix_ploidy`: This value serves as either the fixed ploidy in a one dimensional search of purity values, or the centre ploidy value of a grid search (see `pad_ploidy` for details). (Default: 2).

`pad_ploidy`: To perform a grid search, make this value greater than 0 and the function will test multiple ploidy solutions. We recommend a `pad_ploidy` of 1.5 and a `fix_ploidy` of 3 to search a ploidy range of 1.5 - 4.5 (Default: 0).

`interval`: These are the steps between values in the grid search, decreasing this number adds more resolution but requires a longer run time (Default: 0.01).

`min_purity`: Purities below this value will not be considered. It can be reduced if samples are known to be impure but data with very little variance/noise is required. ASCATlp will struggle with purities < 5% even in very clean data. However with manual supervision this can also be achieved (Default: 0.2).

`max_purity`: This value can be reduced if there is good reason to suspect the sample is low purity to avoid results that are not expected. It can also be marginally increased in pure samples (e.g. cell lines, single cells) to allow for small amounts of noise to be tolerated across a known pure solution (e.g. 1.1) (Default: 1).

`max_lrr`: Sometimes high amplifications can dominate the distance calculation despite being difficult to infer to a precise integer value. These can be removed from the distance calculation by setting this value (e.g. maximum log2ratio of 3). The integer state of the amplification is then inferred from the solution derived from the other segments (Default: Inf).

`no_fit_psit`: It is possible that no solution is found, if this is the case the tool will return a pure solution with this ploidy. By default it is set to 2 as it expects that samples where no fit is possible are samples without copy number alterations, i.e. normal samples (Default: 2).

`preset`: This option, if set to TRUE, allows you to feed in a preset solution. Useful if you know the fit but just want the calculation of calls (Default: FALSE).
                      
`preset_purity`: Must be set if preset = TRUE, provide your preset purity estimation.

`preset_ploidy`: Must be set if preset = TRUE, provide your preset ploidy estimation.

# Credit

This function is entirely inspired by the original [ASCAT paper](https://pubmed.ncbi.nlm.nih.gov/20837533/) with some small modifications based on what improved analysis in our own datasets. Another major motivation for the development of this script was to add further customisable parameters. If you wish to use this tool we recommend providing a link to the GitHub repository but citing the original ASCAT paper. For example: "For this analysis we used the `ASCATlp` script (https://github.com/cresswell-lab/ASCATlp) which is based on the ASCAT approach [1](https://pubmed.ncbi.nlm.nih.gov/20837533/)".

The code was developed by George Cresswell and tested by Claire Lynn.
