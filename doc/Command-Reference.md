---
layout: userdoc
title: "Command Reference"
author: Dominik Schrempf, Heiko Schmidt, Jana Trifinopoulos, Minh Bui
date:    2017-04-12
docid: 7
icon: book
doctype: tutorial
tags:
- manual
description: Commprehensive documentation of command-line options.
sections:
  - name: General options
    url: general-options
  - name: Checkpointing to resume stopped run
    url: checkpointing-to-resume-stopped-run
  - name: Likelihood mapping analysis
    url: likelihood-mapping-analysis
  - name: Automatic model selection
    url: automatic-model-selection
  - name: Specifying substitution models
    url: specifying-substitution-models
  - name: Rate heterogeneity
    url: rate-heterogeneity
  - name: Partition model options
    url: partition-model-options
  - name: Site-specific frequency model options
    url: site-specific-frequency-model-options
  - name: Tree search parameters
    url: tree-search-parameters
  - name: Ultrafast bootstrap parameters
    url: ultrafast-bootstrap-parameters
  - name: Nonparametric bootstrap
    url: nonparametric-bootstrap
  - name: Single branch tests
    url: single-branch-tests
  - name: Tree topology tests
    url: tree-topology-tests
  - name: Constructing consensus tree
    url: constructing-consensus-tree
  - name: Robinson-Foulds distance
    url: computing-robinson-foulds-distance
  - name: Generating random trees
    url: generating-random-trees
  - name: Miscellaneous options
    url: miscellaneous-options
---

Command reference
=================

Commprehensive documentation of command-line options.
<!--more-->


IQ-TREE is invoked from the command-line with e.g.:

    iqtree -s <alignment> [OPTIONS]

assuming that IQ-TREE can be run by simply entering `iqtree`. If not, please change `iqtree` to the actual path of the executable or read the [Quick start guide](Quickstart).


General options
---------------
<div class="hline"></div>

General options are mainly intended for specifying input and output files:

| Option | Usage and meaning |
|--------------|------------------------------------------------------------------------------|
|`-h` or `-?`| Print help usage. |
| `-s`   | Specify input alignment file in PHYLIP, FASTA, NEXUS, CLUSTAL or MSF format. |
| `-st`  | Specify sequence type as either of `DNA`, `AA`, `BIN`, `MORPH`, `CODON` or `NT2AA` for DNA, amino-acid, binary, morphological, codon or DNA-to-AA-translated sequences. This is only necessary if IQ-TREE did not detect the sequence type correctly. Note that `-st CODON` is always necessary when using codon models (otherwise, IQ-TREE applies DNA models) and you also need to specify a [genetic code like this](Substitution-Models#codon-models) if differed from the standard genetic code. `-st NT2AA` tells IQ-TREE to translate protein-coding DNA into AA sequences and then subsequent analysis will work on the AA sequences. You can also use a genetic code like `-st NT2AA5` for the Invertebrate Mitochondrial Code (see [genetic code table](Substitution-Models#codon-models)). |
| `-t`   | Specify a file containing starting tree for tree search. The special option `-t BIONJ` starts tree search from BIONJ tree and `-t RANDOM` starts tree search from completely random tree. *DEFAULT: 100 parsimony trees + BIONJ tree* |
| `-te`  | Like `-t` but fixing user tree. That means, no tree search is performed and IQ-TREE computes the log-likelihood of the fixed user tree. |
| `-o`   | Specify an outgroup taxon name to root the tree. The output tree in `.treefile` will be rooted accordingly. *DEFAULT: first taxon in alignment* |
| `-pre` | Specify a prefix for all output files. *DEFAULT: either alignment file name (`-s`) or partition file name (`-q`, `-spp` or `-sp`)* |
| `-nt` | Specify the number of CPU cores for `iqtree-omp` version. A special option `-nt AUTO` will tell IQ-TREE to automatically determine the best number of cores given the current data and computer. |
| `-seed` | Specify a random number seed to reproduce a previous run. This is normally used for debugging purposes. *DEFAULT: based on current machine clock* |
| `-v`   | Turn on verbose mode for printing more messages to screen. This is normally used for debugging purposes. *DFAULT: OFF* |
| `-quiet` |  Silent mode, suppress printing to the screen. Note that `.log` file is still written. |
|  `-keep-ident` | Keep identical sequences in the alignment. Bu default: IQ-TREE will remove them during the analysis and add them in the end. |
|  `-safe`  | Turn on safe numerical mode to avoid numerical underflow for large data sets with many sequences (typically in the order of thousands). This mode is automatically turned on when having more than 2000 sequences. |
| `-mem` | Specify maximal RAM usage, for example, `-mem 64G` to use at most 64 GB of RAM. By default, IQ-TREE will try to not to exceed the computer RAM size. |

### Example usages:

* Reconstruct a maximum-likelihood tree given a sequence alignment file `data.phy`:

        iqtree -s data.phy 

* Reconstruct a maximum-likelihood tree using 4 CPU cores and at most 8 GB RAM:

        iqtree-omp -s data.phy -nt 4 -mem 8G

* Like above and also write all output to `myrun.*` files:

        iqtree-omp -s data.phy -nt 4 -mem 8G -pre myrun


Checkpointing to resume stopped run
-----------------------------------
<div class="hline"></div>

Starting with version 1.4.0 IQ-TREE supports checkpointing: If an IQ-TREE run was interrupted for some reason, rerunning it with the same command line and input data will automatically resume the analysis from the last stopped point. The previous log file will then be appended. If a run successfully finished, IQ-TREE will deny to rerun and issue an error message. A few options that control checkpointing behavior:

|Option| Usage and meaning |
|-----------|------------------------------------------------------------------------------|
| `-redo`   | Redo the entire analysis no matter if it was stopped or successful. WARNING: This option will overwrite all existing output files. |
| `-cptime` | Specify the minimum checkpoint time interval in seconds (default: 20s) | 

>**NOTE**: IQ-TREE writes a checkpoint file with name suffix `.ckp.gz` in gzip format. Do not modify this file. If you delete this file, all checkpointing information will be lost!


Likelihood mapping analysis
---------------------------
<div class="hline"></div>

Starting with version 1.4.0, IQ-TREE implements the likelihood mapping approach  ([Strimmer and von Haeseler, 1997]) to assess the phylogenetic information of an input alignment. The detailed results will be printed to `.iqtree` report file. The likelihood mapping plots will be printed to `.lmap.svg` and `.lmap.eps` files. 

Compared with the original implementation in TREE-PUZZLE, IQ-TREE is much faster and supports many more substitution models (including partition and mixture models). 


|Option| Usage and meaning |
|------------|------------------------------------------------------------------------------|
| `-lmap`    | Specify the number of quartets to be randomly drawn. If you specify `-lmap ALL`, all unique quartets will be drawn, instead.|
| `-lmclust` | Specify a NEXUS file containing taxon clusters (see below for example) for quartet mapping analysis. |
| `-wql`     | Write quartet log-likelihoods into `.lmap.quartetlh` file (typically not needed). |
| `-n 0`     | Skip subsequent tree search, useful when you only want to assess the phylogenetic information of the alignment. |

>**TIP**: The number of quartets specified via `-lmap` is recommended to be at least 25 times the number of sequences in the alignment, such that each sequence is covered ~100 times in the set of quartets drawn.
{: .tip}

An example NEXUS cluster file (where A, B, C, etc. are sequence names):

    #NEXUS
    begin sets;
        taxset Cluster1 = A B C;
        taxset Cluster2 = D E;
        taxset Cluster3 = F G H I;
        taxset Cluster4 = J;
        taxset IGNORED = X;
    end;

Here, `Cluster1` to `Cluster4` are four user-defined clusters of sequences. Note that users can give any names to the clusters instead of `Cluster1`, etc. If a cluster is named `ignored` or `IGNORED` the sequences included will be ignored in the likelihood mapping analysis.

If you provide a cluster file it has to contain between 1 and 4 clusters (plus an optional `IGNORED` or `ignored` cluster), which will tell IQ-TREE to perform an unclustered (default without a cluster file) or a clustered analysis with 2, 3 or 4 groups, respectively.

The names given to the clusters in the cluster file will be used to label the corners of the triangle diagrams.


### Example usages:

* Perform solely likelihood mapping analysis (ignoring tree search) with 2000 quartets for an alignment `data.phy` with model being automatically selected:

        iqtree -s data.phy -lmap 2000 -n 0 -m TEST

Automatic model selection
-------------------------
<div class="hline"></div>

The default model (e.g., `HKY+F` for DNA, `LG` for protein data) may not fit well to the data. Therefore, IQ-TREE
allows to automatically determine the best-fit model via a series of `-m TEST...` option:

|Option| Usage and meaning |
|----------------------|------------------------------------------------------------------------------|
| `-m TESTONLY`          | Perform standard model selection like jModelTest (for DNA) and ProtTest (for protein). Moreover, IQ-TREE also works for codon, binary and morphogical data. |
| `-m TEST`              | Like `-m TESTONLY` but immediately followed by tree reconstruction using the best-fit model found. So this performs both model selection and tree inference within a single run. |
| `-m TESTNEWONLY` or `-m MF` | Perform an extended model selection that additionally includes FreeRate model compared with `-m TESTONLY`. *Recommended as replacement for `-m TESTONLY`*. Note that `LG4X` is a FreeRate model, but by default is not included because it is also a protein mixture model. To include it, use `-madd` option (see table below).  |
| `-m TESTNEW` or `-m MFP` | Like `-m MF` but immediately followed by tree reconstruction using the best-fit model found. |
| `-m TESTMERGEONLY`     | Select best-fit partitioning scheme like PartitionFinder. |
| `-m TESTMERGE`         | Like `-m TESTMERGEONLY` but immediately followed by tree reconstruction using the best partitioning scheme found.     |
| `-m TESTNEWMERGEONLY` or `-m MF+MERGE` | Like `-m TESTMERGEONLY` but additionally includes FreeRate model. |
| `-m TESTNEWMERGE` or `-m MFP+MERGE` | Like `-m MF+MERGE` but immediately followed by tree reconstruction using the best partitioning scheme found. |

>**TIP**: During model section run, IQ-TREE will write a file with suffix `.model` that stores information of all models tested so far. Thus, if IQ-TREE is interrupted for whatever reason, restarting the run will load this file to reuse the computation. Thus, this file acts like a checkpoint to resume the model selection.
{: .tip}

Several parameters can be set to e.g. reduce computations:

|Option| Usage and meaning |
|-------------|------------------------------------------------------------------------------|
| `-rcluster` | Specify the percentage for the relaxed clustering algorithm ([Lanfear et al., 2014]). This is similar to `--rcluster-percent` option of PartitionFinder. For example, with `-rcluster 10` only the top 10% partition schemes are considered to save computations. |
| `-mset`     | Specify the name of a program (`raxml`, `phyml` or `mrbayes`) to restrict to only those models supported by the specified program. Alternatively, one can specify a comma-separated list of base models. For example, `-mset WAG,LG,JTT` will restrict model selection to WAG, LG, and JTT instead of all 18 AA models to save computations. |
| `-msub`     | Specify either `nuclear`, `mitochondrial`, `chloroplast` or `viral` to restrict to those AA models designed for specified source. |
| `-mfreq`    | Specify a comma-separated list of frequency types for model selection. *DEFAULT: `-mfreq FU,F` for protein models (FU = AA frequencies given by the protein matrix, F = empirical AA frequencies from the data), `-mfreq ,F1x4,F3x4,F` for codon models* |
| `-mrate`    | Specify a comma-separated list of rate heterogeneity types for model selection. *DEFAULT: `-mrate E,I,G,I+G` for standard procedure, `-mrate E,I,G,I+G,R` for new selection procedure* |
| `-cmin`     | Specify minimum number of categories for FreeRate model. *DEFAULT: 2* |
| `-cmax`     | Specify maximum number of categories for FreeRate model. It is recommended to increase if alignment is long enough. *DEFAULT: 10* |
| `-merit` | Specify either `AIC`, `AICc` or `BIC` for the optimality criterion to apply for new procedure. *DEFAULT: all three criteria are considered* |
| `-mtree`    | Turn on full tree search for each model considered, to obtain more accurate result. Only recommended if enough computational resources are available. *DEFAULT: fixed starting tree* |
| `-mredo`    | Ignore `.model` file computed earlier. *DEFAULT: `.model` file (if exists) is loaded to reuse previous computations* |
| `-madd`     | Specify a comma-separated list of mixture models to additionally consider for model selection. For example, `-madd LG4M,LG4X` to additionally include these two [protein mixture models](Substitution-Models#protein-models). |
| `-mdef`     | Specify a [NEXUS model file](Complex-Models#nexus-model-file) to define new models. |

>**NOTE**: Some of the above options require a comma-separated list, which should not contain any empty space!

### Example usages:

* Select best-fit model for alignment `data.phy` based on Bayesian information criterion (BIC):

        iqtree -s data.phy -m TESTONLY

* Select best-fit model for a protein alignment `prot.phy` using the new testing procedure and only consider WAG, LG and JTT matrix to save time:

        iqtree -s prot.phy -m MF -mset WAG,LG,JTT
        
* Find the best partitioning scheme for alignment `data.phy` and partition file `partition.nex` with a relaxed clustering at 10% to save time:

        iqtree -s data.phy -spp partition.nex -m TESTMERGEONLY -rcluster 10


Specifying substitution models
------------------------------
<div class="hline"></div>

`-m` is a powerful option to specify substitution models, state frequency and rate heterogeneity type. The general syntax is:

    -m MODEL+FreqType+RateType

where `MODEL` is a model name, `+FreqType` (optional) is the frequency type and `+RateType` (optional) is the rate heterogeneity type. 

The following `MODEL`s are available:

| DataType | Model names |
|------------|------------------------------------------------------------------------------|
| DNA        | JC/JC69, F81, K2P/K80, HKY/HKY85, TN/TrN/TN93, TNe, K3P/K81, K81u, TPM2, TPM2u, TPM3, TPM3u, TIM, TIMe, TIM2, TIM2e, TIM3, TIM3e, TVM, TVMe, SYM, GTR and 6-digit specification. See [DNA models](Substitution-Models#dna-models) for more details. |
| Protein    | BLOSUM62, cpREV, Dayhoff, DCMut, FLU, HIVb, HIVw, JTT, JTTDCMut, LG, mtART, mtMAM, mtREV, mtZOA, Poisson, PMB, rtREV, VT, WAG. |
| Protein    | Mixture models: C10, ..., C60 (CAT model) ([Lartillot and Philippe, 2004]), EX2, EX3, EHO, UL2, UL3, EX_EHO, LG4M, LG4X, CF4. See [Protein models](Substitution-Models#protein-models) for more details. |
| Codon      | MG, MGK, MG1KTS, MG1KTV, MG2K, GY, GY1KTS, GY1KTV, GY2K, ECMK07/KOSI07, ECMrest, ECMS05/SCHN05 and combined empirical-mechanistic models. See [Codon models](Substitution-Models#codon-models) for more details. |
| Binary     | JC2, GTR2. See [Binary and morphological models](Substitution-Models#binary-and-morphological-models) for more details. |
| Morphology | MK, ORDERED. See [Binary and morphological models](Substitution-Models#binary-and-morphological-models) for more details. |

The following `FreqType`s are supported:

| FreqType | Meaning |
|----------|------------------------------------------------------------------------------|
| `+F`     | Empirical state frequency observed from the data. |
| `+FO`    | State frequency optimized by maximum-likelihood from the data. Note that this is with letter-O and not digit-0. |
| `+FQ`    | Equal state frequency. |
| `+F1x4`  | See [Codon frequencies](Substitution-Models#codon-frequencies). |
| `+F3x4`  | See [Codon frequencies](Substitution-Models#codon-frequencies). |

Further options:

| Option | Usage and meaning |
|----------|------------------------------------------------------------------------------|
| `-mwopt` | Turn on optimizing weights of mixture models. Note that for models like `LG+C20+F+G` this mode is automatically turned on, but not for `LG+C20+G`. |

### Example usages:

* Infer an ML tree for a DNA alignment `dna.phy` under GTR+I+G model:

        iqtree -s dna.phy -m GTR+I+G

* Infer an ML tree for a protein alignment `prot.phy` under LG+F+G model:

        iqtree -s prot.phy -m LG+F+G

* Infer an ML tree for a protein alignment `prot.phy` under profile mixture model LG+C10+F+G:

        iqtree -s prot.phy -m LG+C10+F+G


Rate heterogeneity
------------------
<div class="hline"></div>

The following `RateType`s are supported:

| RateType | Meaning |
|----------|------------------------------------------------------------------------------|
| `+I`     | Allowing for a proportion of invariable sites. |
| `+G`     | Discrete Gamma model ([Yang, 1994]) with default 4 rate categories. The number of categories can be changed with e.g. `+G8`. |
| `+I+G`   | Invariable site plus discrete Gamma model ([Gu et al., 1995]). |
| `+R`     | FreeRate model ([Yang, 1995]; [Soubrier et al., 2012]) that generalizes `+G` by relaxing the assumption of Gamma-distributed rates. The number of categories can be specified with e.g. `+R6`. *DEFAULT: 4 categories* |
| `+I+R`   | invariable site plus FreeRate model. | 

See [Rate heterogeneity across sites](Substitution-Models#rate-heterogeneity-across-sites) for more details.

Further options:

| Option | Usage and meaning |
|--------------|------------------------------------------------------------------------------|
| `-a`         | Specify the Gamma shape parameter (default: estimate) |
| `-gmedian`   | Perform the *median* approximation for Gamma rate heterogeneity instead of the default *mean* approximation ([Yang, 1994]) |
| `-i`         | Specify the proportion of invariable sites (default: estimate) |
| `--opt-gamma-inv` | Perform more thorough estimation for `+I+G` model parameters |
| `-wsr`       | Write per-site rates to `.rate` file | 

Optionally, one can specify [Ascertainment bias correction](Substitution-Models#ascertainment-bias-correction) by appending `+ASC` to the model string. [Advanced mixture models](Complex-Models#mixture-models) can also be specified via `MIX{...}` and `FMIX{...}` syntax. Option `-mwopt` can be used to turn on optimizing weights of mixture models.


Partition model options
-----------------------
<div class="hline"></div>

Partition models are used for phylogenomic data with multiple genes. You first have to prepare [a partition file in NEXUS or RAxML-style format](Complex-Models#partition-file-format). Then use the following options to input the partition file:

| Option | Usage and meaning |
|--------|------------------------------------------------------------------------------|
| `-q`   | Specify partition file for edge-equal [partition model](Complex-Models#partition-models). That means, all partitions share the same set of branch lengths (like `-q` option of RAxML). |
| `-spp` | Like `-q` but allowing partitions to have different evolutionary speeds ([edge-proportional partition model](Complex-Models#partition-models)). |
| `-sp`  | Specify partition file for [edge-unlinked partition model](Complex-Models#partition-models). That means, each partition has its own set of branch lengths (like `-M` option of RAxML). This is the most parameter-rich partition model to accomodate *heterotachy*. |

Site-specific frequency model options
-------------------------------------
<div class="hline"></div>

The site-specific frequency model is used to substantially reduce the time and memory requirement compared with full profile mixture models `C10` to `C60`. For full details see [site-specific frequency model](Complex-Models#site-specific-frequency-models). To use this model you have to specify a profile mixture model with e.g. `-m LG+C20+F+G` together with a guide tree or a site frequency file: 

| Option | Usage and meaning |
|---------|------------------------------------------------------------------------------|
| `-ft`   | Specify a guide tree (in Newick format) to infer site frequency profiles. |
| `-fs`   | Specify a site frequency file, e.g. the `.sitefreq` file obtained from `-ft` run. This will save memory used for the first phase of the analysis. | 
| `-fmax` | Switch to posterior maximum mode for obtaining site-specific profiles. Default: posterior mean. |

With `-fs` option you can input a file containing your own site frequency profiles. The format of this file is that each line contains the site ID (starting from 1) and the state frequencies (20 for amino-acid) separated by white space. So it has as many lines as the number of sites in the alignment. The order of amino-acids is:


     A   R   N   D   C   Q   E   G   H   I   L   K   M   F   P   S   T   W   Y   V


Tree search parameters
----------------------
<div class="hline"></div>

The new IQ-TREE search algorithm ([Nguyen et al., 2015]) has several parameters that can be changed with:

| Option | Usage and meaning |
|-----------|------------------------------------------------------------------------------|
| `-ninit`  | Specify number of initial parsimony trees. *DEFAULT: 100*. Here [the PLL library](http://www.libpll.org) ([Flouri et al., 2015]) is used, which implements the randomized stepwise addition and parsimony subtree pruning and regafting (SPR). |
| `-ntop`   | Specify number of top initial parsimony trees to optimize with ML nearest neighbor interchange (NNI) search to initialize the candidate set. *DEFAULT: 20* |
| `-nbest`  | Specify number of trees in the candidate set to maintain during ML tree search. *DEFAULT: 5* |
| `-nstop`  | Specify number of unsuccessful iterations to stop. *DEFAULT: 100* |
| `-n`      | Specify number of iterations to stop. This option overrides `-nstop` criterion. |
| `-sprrad` | Specify SPR radius for the initial parsimony tree search. *DEFAULT: 6* |
| `-pers`   | Specify perturbation strength (between 0 and 1) for randomized NNI. *DEFAULT: 0.5* |
| `-allnni` | Turn on more thorough and slower NNI search. It means that IQ-TREE will consider all possible NNIs instead of only those in the vicinity of previously applied NNIs. *DEFAULT: OFF* |
| `-djc`    | Avoid computing ML pairwise distances and BIONJ tree. |
| `-g`      | Specify a topological constraint tree file in NEWICK format. The constraint tree can be a multifurcating tree and need not to include all taxa. |

>**NOTE**: While the default parameters were empirically determined to work well under our extensive benchmark ([Nguyen et al., 2015]), it might not hold true for all data sets. If in doubt that tree search is still stuck in local optima, one should repeat analysis with at least 10 IQ-TREE runs. Moreover, our experience showed that `-pers` and `-nstop` are the most relevant options to change in such case. For example, data sets with many short sequences should be analyzed with smaller perturbation strength (e.g. `-pers 0.2`) and larger number of stop iterations (e.g. `-nstop 500`).

### Example usages:

* Infer an ML tree for an alignment `data.phy` with increased stopping iteration of 500 and reduced perturbation strength of 0.2:

        iqtree -s data.phy -m TEST -nstop 500 -pers 0.2

* Infer an ML tree for an alignment `data.phy` obeying a topological constraint tree `constraint.tree`:

        iqtree -s data.phy -m TEST -g constraint.tree

Ultrafast bootstrap parameters
------------------------------
<div class="hline"></div>

The ultrafast bootstrap (UFBoot) approximation ([Minh et al., 2013]) has several parameters that can be changed with:

| Option | Usage and meaning |
|----------|------------------------------------------------------------------------------|
| `-bb`    | Specify number of bootstrap replicates (>=1000). |
| `-wbt`   | Turn on writing bootstrap trees to `.ufboot` file. *DEFAULT: OFF* |
| `-wbtl`  | Like `-wbt` but bootstrap trees written with branch lengths. *DEFAULT: OFF* |
| `-nm`    | Specify maximum number of iterations to stop. *DEFAULT: 1000* |
| `-bcor`  | Specify minimum correlation coefficient for UFBoot convergence criterion. *DEFAULT: 0.99* |
| `-nstep` | Specify iteration interval checking for UFBoot convergence. *DEFAULT: every 100 iterations* |
| `-beps`  | Specify a small epsilon to break tie in RELL evaluation for bootstrap trees. *DEFAULT: 0.5* |
| `-bspec` | Specify the resampling strategies for partitioned analysis. By default, IQ-TREE resamples alignment sites within partitions. With `-bspec GENE` IQ-TREE will resample partitions. With `-bspec GENESITE` IQ-TREE will resample partitions and then resample sites within resampled partitions ([Gadagkar et al., 2005]). |

### Example usages:

* Select best-fit model, infer an ML tree and perform ultrafast bootstrap with 1000 replicates:

        iqtree -s data.phy -m TEST -bb 1000

* Reconstruct ML and perform ultrafast bootstrap (5000 replicates) under a partition model file `partition.nex`:

        iqtree -s data.phy -spp partition.nex -m TEST -bb 5000


Nonparametric bootstrap
-----------------------
<div class="hline"></div>

The slow standard nonparametric bootstrap ([Felsenstein, 1985]) can be run with:

| Option | Usage and meaning |
|--------|------------------------------------------------------------------------------|
| `-b`   | Specify number of bootstrap replicates (recommended >=100). This will perform both bootstrap and analysis on original alignment and provide a consensus tree. |
| `-bc`  | Like `-b` but omit analysis on original alignment. |
| `-bo`  | Like `-b` but only perform bootstrap analysis (no analysis on original alignment and no consensus tree). |


Single branch tests
-------------------
<div class="hline"></div>

The following single branch tests are faster than all bootstrap analysis and recommended for extremely large data sets (e.g., >10,000 taxa):

| Option | Usage and meaning |
|-----------|------------------------------------------------------------------------------|
| `-alrt`   | Specify number of replicates (>=1000) to perform SH-like approximate likelihood ratio test (SH-aLRT) ([Guindon et al., 2010]). If number of replicates is set to 0 (`-alrt 0`), then the parametric aLRT test ([Anisimova and Gascuel 2006]) is performed, instead of SH-aLRT. |
| `-abayes` | Perform approximate Bayes test ([Anisimova et al., 2011]). |
| `-lbp`    | Specify number of replicates (>=1000) to perform fast local bootstrap probability method ([Adachi and Hasegawa, 1996]). |

>**TIP**: One can combine all these tests (also including UFBoot `-bb` option) within a single IQ-TREE run. Each branch in the resulting tree will be assigned several support values separated by slash (`/`), where the order of support values is stated in the `.iqtree` report file.
{: .tip}

### Example usages:

* Infer an ML tree and perform SH-aLRT test as well as ultrafast bootstrap with 1000 replicates:

        iqtree -s data.phy -m TEST -alrt 1000 -bb 1000


Tree topology tests
-------------------
<div class="hline"></div>

IQ-TREE provides a number of tests for significant topological differences between trees:

| Option | Usage and meaning |
|-------|------------------------------------------------------------------------------|
| `-z`  | Specify a file containing a set of trees. IQ-TREE will compute the log-likelihoods of all trees. |
| `-zb` | Specify the number of RELL ([Kishino et al., 1990]) replicates (>=1000) to perform several tree topology tests for all trees passed via `-z`. The tests include bootstrap proportion (BP), KH test ([Kishino and Hasegawa, 1989]), SH test ([Shimodaira and Hasegawa, 1999]) and expected likelihood weights (ELW) ([Strimmer and Rambaut, 2002]). |
| `-zw` | Used together with `-zb` to additionally perform the weighted-KH and weighted-SH tests. |
| `-au` | Used together with `-zb` to additionally perform the approximately unbiased (AU) test ([Shimodaira, 2002]). Note that you have to specify the number of replicates for the AU test via `-zb`. |
| `-n 0` | Only estimate model parameters on an initial parsimony tree and ignore a full tree search to save time. |
| `-te` | Specify a fixed user tree to estimate model parameters. Thus it behaves like `-n 0` but uses a user-defined tree instead of parsimony tree. |

>**NOTE**: The AU test implementation in IQ-TREE is much more efficient than the original CONSEL by supporting SSE, AVX and multicore parallelization. Moreover, it is more appropriate than CONSEL for partition analysis by bootstrap resampling sites *within* partitions, whereas CONSEL is not partition-aware.

### Example usages:

* Given alignment `data.phy`, test a set of trees in `data.trees` using AU test with 10,000 replicates:

        iqtree -s data.phy -m GTR+G -n 0 -z data.trees -zb 10000 -au 

* Same above but for a partitioned data `partition.nex` and additionally performing weighted test:

        iqtree -s data.phy -spp partition.nex -n 0 -z data.trees -zb 10000 -au -zw


Constructing consensus tree
---------------------------
<div class="hline"></div>

IQ-TREE provides a fast implementation of consensus tree construction for post analysis:

| Option | Usage and meaning |
|-----------|------------------------------------------------------------------------------|
| `-t`      | Specify a file containing a set of trees. |
| `-con`    | Compute consensus tree of the trees passed via `-t`. Resulting consensus tree is written to `.contree` file. |
| `-net`    | Compute consensus network of the trees passed via `-t`. Resulting consensus network is written to `.nex` file. |
| `-minsup` | Specify a minimum threshold  (between 0 and 1) to keep branches in the consensus tree. `-minsup 0.5` means to compute majority-rule consensus tree. *DEFAULT: 0 to compute extended majority-rule consensus.* |
| `-bi`     | Specify a burn-in, which is the number of beginning trees passed via `-t` to discard before consensus construction. This is useful e.g. when summarizing trees from MrBayes analysis. |
| `-sup`    | Specify an input "target" tree file. That means, support values are first extracted from the trees passed via `-t`, and then mapped onto the target tree. Resulting tree with assigned support values is written to `.suptree` file. This option is useful to map and compare support values from different approaches onto a single tree. |
| `-suptag` | Specify name of a node in `-sup` target tree. The corresponding node of `.suptree` will then be assigned with IDs of trees where this node appears. Special option `-suptag ALL` will assign such IDs for all nodes of the target tree. |


Computing Robinson-Foulds distance
----------------------------------
<div class="hline"></div>

IQ-TREE provides a fast implementation of Robinson-Foulds (RF) distance computation between trees:

| Option | Usage and meaning |
|-----------|------------------------------------------------------------------------------|
| `-t`      | Specify a file containing a set of trees. |
| `-rf`     | Specify a second set of trees. IQ-TREE computes all pairwise RF distances between two tree sets passed via `-t` and `-rf` |
| `-rf_all` | Compute all-to-all RF distances between all trees passed via `-t` |
| `-rf_adj` | Compute RF distances between adjacent trees  passed via `-t` |



### Example usages:

* Compute the pairwise RF distances between 2 sets of trees:

        iqtree -rf tree_set1 tree_set2


* Compute the all-to-all RF distances within a set of trees:

        iqtree -rf_all tree_set



Generating random trees
-----------------------
<div class="hline"></div>

IQ-TREE provides several random tree generation models:

| Option | Usage and meaning |
|---------|------------------------------------------------------------------------------|
| `-r`    | Specify number of taxa. IQ-TREE will create a random tree under Yule-Harding model with specified number of taxa |
| `-ru`   | Like `-r`, but a random tree is created under uniform model. |
| `-rcat` | Like `-r`, but a random caterpillar tree is created. |
| `-rbal` | Like `-r`, but a random balanced tree is created. |
| `-rcsg` | Like `-r`, bur a random circular split network is created. |
| `-rlen` | Specify three numbers: minimum, mean and maximum branch lengths of the random tree. *DEFAULT: `-rlen 0.001 0.1 0.999`* |


### Example usages:


* Generate a 100-taxon random tree into the file `100.tree` under the Yule Harding model:

        iqtree -r 100 100.tree 


* Generate 100-taxon random tree with mean branch lengths of 0.2 and branch lengths are between 0.05 and 0.3:

        iqtree -r 100 -rlen 0.05 0.2 0.3 100.tree 


* Generate a random tree under uniform model:

        iqtree -ru 100 100.tree 


* Generate a random tree where taxon labels follow an alignment:

        iqtree -s example.phy -r 17 example.random.tree 

Note that, you still need to specify the `-r` option being equal to the number of taxa in the alignment. 

Miscellaneous options
---------------------
<div class="hline"></div>

| Option | Usage and meaning |
|-----------|------------------------------------------------------------------------------|
| `-wt`     | Turn on writing all locally optimal trees into `.treels` file. *DEFAULT: OFF* |
| `-fixbr`  | Turn on fixing branch lengths of tree passed via `-t` or `-te`. This is useful to evaluate the log-likelihood of an input tree with fixed tolopogy and branch lengths. *DEFAULT: OFF* |
| `-wsl`    | Turn on writing site log-likelihoods to `.sitelh` file in [TREE-PUZZLE](http://www.tree-puzzle.de) format. Such file can then be passed on to [CONSEL](http://www.sigmath.es.osaka-u.ac.jp/shimo-lab/prog/consel/) for further tree tests. *DEFAULT: OFF* |
| `-wslg`   | Turn on writing site log-likelihoods per rate category. *DEFAULT: OFF* |
| `-fconst` | Specify a list of comma-separated integer numbers. The number of entries should be equal to the number of states in the model (e.g. 4 for DNA and 20 for protein). IQ-TREE will then add a number of constant sites accordingly. For example, `-fconst 10,20,15,40` will add 10 constant sites of all A, 20 constant sites of all C, 15 constant sites of all G and 40 constant sites of all T into the alignment. |



[Adachi and Hasegawa, 1996]: http://www.is.titech.ac.jp/~shimo/class/doc/csm96.pdf
[Anisimova and Gascuel 2006]: http://dx.doi.org/10.1080/10635150600755453
[Anisimova et al., 2011]: http://dx.doi.org/10.1093/sysbio/syr041
[Felsenstein, 1985]: http://dx.doi.org/10.2307/2408678
[Flouri et al., 2015]: https://doi.org/10.1093/sysbio/syu084
[Gadagkar et al., 2005]: http://dx.doi.org/10.1002/jez.b.21026
[Gu et al., 1995]: http://mbe.oxfordjournals.org/content/12/4/546.abstract
[Guindon et al., 2010]: http://dx.doi.org/10.1093/sysbio/syq010
[Kishino et al., 1990]: http://dx.doi.org/10.1007/BF02109483
[Kishino and Hasegawa, 1989]: http://dx.doi.org/10.1007/BF02100115
[Lanfear et al., 2014]: http://dx.doi.org/10.1186/1471-2148-14-82
[Lartillot and Philippe, 2004]: http://dx.doi.org/10.1093/molbev/msh112
[Minh et al., 2013]: http://dx.doi.org/10.1093/molbev/mst024
[Nguyen et al., 2015]: http://dx.doi.org/10.1093/molbev/msu300
[Shimodaira and Hasegawa, 1999]: http://dx.doi.org/10.1093/oxfordjournals.molbev.a026201
[Shimodaira, 2002]: http://dx.doi.org/10.1080/10635150290069913
[Soubrier et al., 2012]: http://dx.doi.org/10.1093/molbev/mss140
[Strimmer and Rambaut, 2002]: http://dx.doi.org/10.1098/rspb.2001.1862
[Strimmer and von Haeseler, 1997]: http://www.pnas.org/content/94/13/6815.long
[Yang, 1994]: http://dx.doi.org/10.1007/BF00160154
[Yang, 1995]: http://www.genetics.org/content/139/2/993.abstract
