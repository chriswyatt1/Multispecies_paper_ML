# Multispecies_paper_ML

Scripts used for Multispecies paper (Wyatt et al. Unpublished). These scripts allow a bespoke way to do svm machine learning on non-genome guided RNAseq data (Trinity), across different species. This process is not automated, and requires a lengthy process of acquiring Trinity assemblies for your species of interest, then compiling the data in a (later explained) data structure (folder organisation) to make the script perform without error. Please contact the authors if you wish to embark on a similar analysis (before trying out the various scripts), as likely the scripts would have to be changed for another analysis.


Master.ML.pl is a perl wrapper that creates all the R scripts necessary to run the svm analysis. 

# Set Up

The Master script requires the data to be in a specific folder ordering system with:

**1.** Template R scripts in the following folder:
```unix
MAIN/scripts/template_scripts/
```
R scripts found in : [template_scripts](https://github.com/Sumner-lab/Multispecies_paper_ML/tree/master/template_scripts)

**2.** **Species** gene expression data and Trinity assemblies in :
```unix
MAIN/DATA/Experimental_data_merged/Polybia_quadracincta/

MAIN/DATA/Experimental_data_merged/Polistes_canadensis/
```
In each of these folders you need:

a Trinity assembly in /Pool/

a Queen and Worker folder with RSEM gene expression counts based on the Trinity assembly (/RSEM.isoforms.results)

Each species should have, for example: 
```unix
MAIN/DATA/Experimental_data_merged/Polistes_canadensis/Pool/Pool_trinity.fnn

MAIN/DATA/Experimental_data_merged/Polistes_canadensis/Queen/RSEM.isoforms.results

MAIN/DATA/Experimental_data_merged/Polistes_canadensis/Worker/RSEM.isoforms.results
```
**3.** Gene orthology information from Orthofinder should be found in:

```unix
MAIN/Orthofinder/Orthogroups.copy.noMac.tsv
```

Orthology must be determined using Orthofinder, explained in methods of paper, using all the species in your analysis (based on Trinity gene names: TRINITY_DN16291_c0_g1). This involved calculation of protein fasta sequences of your species and running Orthofinder as default.

**4.** Folder to be intialised:

```unix
mkdir MAIN/FIGURES
mkdir MAIN/DEGS
```

# **Dependencies on UCL myriad cluster**:

It is recommended to run this script on a High performance cluster, as the script uses alot of RAM, but can be run locally if have a powerful machine. These settings will be different depending on your cluster.

For UCL myriad we need to load R to run the script.
```unix
module unload compilers/intel/2018/update3
module unload mpi/intel/2018/update3/intel
module unload java/1.8.0_45
module add r/recommended
```

# **R must have the following packages installed**:
```R
library(tximport)
library(tximportData)
library(edgeR)
library(seqinr)
library(pheatmap)
library(stringr)
library(e1071)
library(probsvm)
```


# **Running the script**
```unix
Usage: Master.ML.pl 
compulsory:
            -j <Version folder name> 
            -f <Foreground species list (comma sep)>  
            -b <Background species list (comma sep)> 
            -filter Choose a different daat processing procedure: <e.g. matrix_run7.filter.scale>

options:    -r     Run the scripts through R (default = OFF).
            -e     Choose expression data folder (default= \"Experimental_data_merged\").
            -orth  Choose Orthofinder file to use in this analysis (default= \"DATA/Orthofinder/Orthofinder_24.5.2020/Orthogroups.tsv\"). 
            -CPM   Choose the cut off of minimal expression to be considered. Default =1.  
```

Must be the top level folder (e.g. MAIN), which should have folders DATA and scripts and FIGURES.

A typical example of the script would be :
```unix
perl Master_ML.v5.paper.pl -j TOP5_Test_Angiopolybia_pallens_3MER -CPM 10 -f Angiopolybia_pallens -b Vespula_vulgaris,Vespa_crabro,Metapolybia_cingulata,Polybia_quadracincta -e Experimental_data_merged -orth Orthofinder/Orthogroups.copy.noMac.tsv.filt2.csv  -filter matrix.data.counts.orth.tpm.log2.quantile.species.scaled.filter.scale -r
```
