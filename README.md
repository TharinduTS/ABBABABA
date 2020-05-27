# ABBABABA
The methods used to create ABBABABA 
# Declaring species/outgroups

Samples were devided as following
```text
outgroup - XM_1_sorted.bam

H1 - Lwiro X. victorianus - BJE1488_sorted.bam  BJE263_sorted.bam  BJE265_sorted.bam  BJE267_sorted.bam
BJE1489_sorted.bam  BJE264_sorted.bam  BJE266_sorted.bam

H2 - South Africa X. laevis - BJE3536.fqsorted.bam  JMEC006.fqsorted.bam   XGUAE_92_sorted.bam
BJE3540.fqsorted.bam  jonk_02.fqsorted.bam   XGUAE_93_sorted.bam
BJE3545_sorted.bam    XGL713_232_sorted.bam  XGUAE_97_sorted.bam
BJE3574.fqsorted.bam  XGUAE_124_sorted.bam   XL_CPT1_sorted.bam
BJE3581.fqsorted.bam  XGUAE_59_sorted.bam    XL_CPT2_sorted.bam
BJE3608.fqsorted.bam  XGUAE_65_sorted.bam    XL_CPT3_sorted.bam
BJE3639_sorted.bam    XGUAE_70_sorted.bam    XL_CPT4_sorted.bam
CoGH105.fqsorted.bam  XGUAE_71_sorted.bam    XLJONK_14_sorted.bam
JMEC003.fqsorted.bam  XGUAE_72_sorted.bam

H3 - all the XG sequences - XG12_07_sorted.bam     XGL713_177_sorted.bam  XGUAE_36_sorted.bam
XG153_sorted.bam       XGL713_179_sorted.bam  XGUAE_42_sorted.bam
XG92_sorted.bam        XGL713_180_sorted.bam  XGUAE_43_sorted.bam
XGL713_123_sorted.bam  XGL713_181_sorted.bam  XGUAE_44_sorted.bam
```
# install ANGSD (use the latest from https://github.com/ANGSD/angsd.git)
```bash
git clone https://github.com/samtools/htslib.git;

git clone https://github.com/angsd/angsd.git;

cd htslib;make;cd ../angsd;make HTSSRC=../htslib

```
# Create symbolic links to angsd and the necessary R script

```bash
ln -s ./angsd/angsd ANGSD
ln -s ./angsd/R/estAvgError.R DSTAT
```
# Load samtools,index samples and create a file bam.filelist listing the pathnames of those datasets
```bash
module load nixpkgs/16.09
module load gcc/5.4.0
module load samtools/1.10
for i in all_samples/*.bam;do samtools index $i;done #index bam files
```
listed samples in the vi file bam.filelist to keep the order of samples according to populations.

```text
all_samples/BJE1488_sorted.bam
all_samples/BJE1489_sorted.bam
all_samples/BJE263_sorted.bam
all_samples/BJE264_sorted.bam
all_samples/BJE265_sorted.bam
all_samples/BJE266_sorted.bam
all_samples/BJE267_sorted.bam
all_samples/BJE3536.fqsorted.bam
all_samples/BJE3540.fqsorted.bam
all_samples/BJE3545_sorted.bam
all_samples/BJE3574.fqsorted.bam
all_samples/BJE3581.fqsorted.bam
all_samples/BJE3608.fqsorted.bam
all_samples/BJE3639_sorted.bam
all_samples/CoGH105.fqsorted.bam
all_samples/JMEC003.fqsorted.bam
all_samples/JMEC006.fqsorted.bam
all_samples/jonk_02.fqsorted.bam
all_samples/XGL713_232_sorted.bam
all_samples/XGUAE_124_sorted.bam
all_samples/XGUAE_59_sorted.bam
all_samples/XGUAE_65_sorted.bam
all_samples/XGUAE_70_sorted.bam
all_samples/XGUAE_71_sorted.bam
all_samples/XGUAE_72_sorted.bam
all_samples/XGUAE_92_sorted.bam
all_samples/XGUAE_93_sorted.bam
all_samples/XGUAE_97_sorted.bam
all_samples/XL_CPT1_sorted.bam
all_samples/XL_CPT2_sorted.bam
all_samples/XL_CPT3_sorted.bam
all_samples/XL_CPT4_sorted.bam
all_samples/XLJONK_14_sorted.bam
all_samples/XG12_07_sorted.bam
all_samples/XG153_sorted.bam
all_samples/XG92_sorted.bam
all_samples/XGL713_123_sorted.bam
all_samples/XGL713_177_sorted.bam
all_samples/XGL713_179_sorted.bam
all_samples/XGL713_180_sorted.bam
all_samples/XGL713_181_sorted.bam
all_samples/XGUAE_36_sorted.bam
all_samples/XGUAE_42_sorted.bam
all_samples/XGUAE_43_sorted.bam
all_samples/XGUAE_44_sorted.bam
```
# **************** optional****************************
# generate a fasta file for one of our bam file. We assume such a genome has very high quality and we can use it as a reference for estimating error rates in others of our datasets. 

Based on depth results from previous study, sample with highest depth - XGUAE_59_sorted.bam was selected for this.

```bash
./ANGSD -i all_samples/XGUAE_59_sorted.bam -doFasta 1 -doCounts 1 -out perfectSampleCEU
gunzip perfectSampleCEU.fa.gz
samtools faidx perfectSampleCEU.fa
```
# Generate files for the error correction
We will apply error correction to the group with 12 individuals, using "perfectSampleCEU" as high-quality reference genome. The population containing 3 individuals affected by transition error goes from line 6 to line 8 in the file bam.filelist. We select those individuals and write them in another file.
```bash
sed -n 33,45p bam.filelist > bamWithErrors.filelist
```
# **************************************************
# turn outgroup into fasta
```bash
./ANGSD -i samples_seperated/outgroup/XM_1_sorted.bam -doFasta 1 -doCounts 1 -out outgroup_XM_1
gunzip outgroup_XM_1.fa.gz
module load nixpkgs/16.09
module load gcc/5.4.0
module load samtools/1.10
samtools faidx outgroup_XM_1.fa
```
# Created size file - sizeFile.size
```text
7
26
12
1
```
# Do ABBABABA
```bash
./ANGSD -doAbbababa2 1 -bam bam.filelist -sizeFile sizeFile.size -doCounts 1 -out bam.Angsd -anc outgroup_XM_1.fa -useLast 0 -minQ 20 -minMapQ 20 -p 1
```
# Create errorList.error
```text
NA
```
# create popNames.name
```text
H1
H2
H3
O
```
#load r
```bash
module load nixpkgs/16.09
module load gcc/8.3.0
module load r/4.0.0
R
install.packages('pracma')

```
