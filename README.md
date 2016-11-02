# PleioPred
Leveraging pleiotropy and functional annotations in genetic risk prediction

## Introduction
This tool predicts disease risk from genotype data using large GWAS summary statistics of genetically correlated diseases as training data and integrating functional annotations.

## Prerequisites
The software is developed and tested in Linux. You will need Python 2.7 and several pacakges to run it:
* h5py
* plinkio
* scipy
* numpy

Besides these, you also need to have [LDSC](https://github.com/bulik/ldsc) installed (LDSC itself also has a list of prerequisites, please make sure they are also installed). If you only want to use your own heritability estimation for each SNP, you can skip this.

## Input Data
* GWAS Summary statistics with a fixed format, for example: test_data/GWAS_sumstats.txt
* Reference/Validation genotype files, plink binary format, http://pngu.mgh.harvard.edu/~purcell/plink/

## Setup and Usage Example
1) Clone this repository
```
git clone https://github.com/yiminghu/PleioPred.git
```
2) Download reference data
```
cd PleioPred
wget http://genocanyon.med.yale.edu/AnnoPredFiles/AnnoPred_ref.tar.gz
tar -zxvf AnnoPred_ref.tar.gz
```
This step will generated a folder named ref containing functional annotations.

3) Setup LDSC: open LDSC.config and change /absolute/path/to/ldsc to the absolute path to LDSC in your local directory

4) Example:
```
## PleioPred ##
python PleioPred.py\
  --sumstats_D1=GWAS_sumstats1.txt\
  --sumstats_D2=GWAS_sumstats2.txt\
  --ref_gt=validation\
  --val_gt=validation\
  --N_case1=12171\
  --N_ctrl1=56862\
  --N_case2=22233\
  --N_ctrl2=64762\
  --temp_dir=/.../temp/\
  --local_ld_prefix=/.../temp/test\
  --coord_D1=/.../temp/coord1\
  --coord_D2=/.../temp/coord2\
  --out_anno=/.../temp/test_anno\
  --out_ld=/.../temp/test_ld\
```
Keep in mind that this will generate intermediate data at /.../temp/. Contents in These folders are reused on different runs, not deleted: you might want to delete this folder before running PleioPred on a new dataset, or specify a different folder on each run.

The example command parameters mean:
* --sumstats_D1=GWAS_sumstats1.txt: GWAS summary statistics of D1, with seven fields: hg19chrc, snpid, a1, a2, bp, or and p. test_data/GWAS_sumstats.txt is a subset of DIAGRAM summary statistics. We thank DIAGRAM consortium for making the data publicly available. The oringinal download link is http://diagram-consortium.org/downloads.html
* --sumstats_D2=GWAS_sumstats2.txt: GWAS summary statistics of D2, same format as D1
* --ref_gt=validation: path to the reference genotype data. We suggest also using validation data as reference data. Plink binary format (.bed, .bim, .fam), description can be * found in .
* --val_gt=validation: path to the validation genotype data. Plink binary format, the sixth column in fam file cannot be missing.
* --N_case1=12171: number of cases in GWAS of D1
* --N_ctrl1=56862: number of controls in GWAS of D1
* --N_case2=22233: number of cases in GWAS of D2
* --N_ctrl2=64762: number of controls in GWAS of D2
* --temp_dir=/.../temp/: a path for saving temporary files generated during the procedure. We suggest using different temp_dir for different dataset.
* --local_ld_prefix=/.../temp/test: a path for saving a cPickle file, which contains LD matrix
* --coord_D1=/.../temp/coord1: path for saving a h5py file of D1, which contains validation genotypes, summary statistics and standardized effect sizes of SNPs in common.
* --coord_D2=/.../temp/coord2: path for saving a h5py file of D2, Same format as D1.
* --out_anno=/.../temp/test_anno: the path for output files of PleioPred-anno
* --out_ld=/.../temp/test_ld: the path for output files of PleioPred

## Output files
PleioPred output a set of files including two types of PleioPred polygenic risk scores, phenotypes of testing data, prediction accuracy, posterior expectation estimation of the effect size of each snp. Take the output of code shown in 4) of previous section as an example:
* test_anno_y_D1.txt: phenotypes of testing data.
* test_anno_prs_PleioPred_D1.txt: PleioPred-anno PRS.
* test_anno_auc_PleioPred_D1.txt: prediction accuracy of PleioPred-anno PRS using the first prior: AUC for binary traits and correlation between PRS and y for continuous traits.
* test_anno_betas_PleioPred_D1.pickled.gz: posterior expectation estimation of the effect size of each snps.
* test_ld_prs_PleioPred_D1.txt: PleioPred PRS.
* test_ld_auc_PleioPred_D1.txt: prediction accuracy of PleioPred PRS using the first prior: AUC for binary traits and correlation between PRS and y for continuous traits.
* test_ld_betas_PleioPred_D1.pickled.gz: posterior expectation estimation of the effect size of each snps.

## Acknowledgement
**Part of the code is modified from LDpred (https://bitbucket.org/bjarni_vilhjalmsson/ldpred). We thank Dr. Bjarni J. Vilhjalmsson for sharing his code.**


