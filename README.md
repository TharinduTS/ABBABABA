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
all.samples/CoGH105.fqsorted.bam
all.samples/JMEC003.fqsorted.bam
all.samples/JMEC006.fqsorted.bam
all.samples/jonk.02.fqsorted.bam
all.samples/XGL713.232.sorted.bam
all.samples/XGUAE.124.sorted.bam
all.samples/XGUAE.59.sorted.bam
all.samples/XGUAE.65.sorted.bam
all.samples/XGUAE.70.sorted.bam
all.samples/XGUAE.71.sorted.bam
all.samples/XGUAE.72.sorted.bam
all.samples/XGUAE.92.sorted.bam
all.samples/XGUAE.93.sorted.bam
all.samples/XGUAE.97.sorted.bam
all.samples/XL.CPT1.sorted.bam
all.samples/XL.CPT2.sorted.bam
all.samples/XL.CPT3.sorted.bam
all.samples/XL.CPT4.sorted.bam
all.samples/XLJONK.14.sorted.bam
all.samples/XG12.07.sorted.bam
all.samples/XG153.sorted.bam
all.samples/XG92.sorted.bam
all.samples/XGL713.123.sorted.bam
all.samples/XGL713.177.sorted.bam
all.samples/XGL713.179.sorted.bam
all.samples/XGL713.180.sorted.bam
all.samples/XGL713.181.sorted.bam
all.samples/XGUAE.36.sorted.bam
all.samples/XGUAE.42.sorted.bam
all.samples/XGUAE.43.sorted.bam
all.samples/XGUAE.44.sorted.bam
[premacht@gra-login1 abbababa]$ clear

[premacht@gra-login1 abbababa]$ less bam.filelist 
[premacht@gra-login1 abbababa]$ sed -e 's/_/./g' bam.filelist | sed -e 's/samples/samples.renamed/g'>bam.filelist
[premacht@gra-login1 abbababa]$ ls
all_samples	     bam.Angsd.abbababa2  htslib	 samples_seperated
all.samples.renamed  bam.Angsd.arg	  outg.fa	 sizeFile.size
ANGSD		     bam.filelist	  outg.fa.fai
angsd		     DSTAT		  popNames.name
[premacht@gra-login1 abbababa]$ less bam.filelist 
[premacht@gra-login1 abbababa]$ rm bam.filelist 
[premacht@gra-login1 abbababa]$ vi list
[premacht@gra-login1 abbababa]$ sed -e 's/_/./g' file^C sed -e 's/samples/samples.renamed/g'>bam.filelist
[premacht@gra-login1 abbababa]$ ls
all_samples	     bam.Angsd.abbababa2  list		 samples_seperated
all.samples.renamed  bam.Angsd.arg	  outg.fa	 sizeFile.size
ANGSD		     DSTAT		  outg.fa.fai
angsd		     htslib		  popNames.name
[premacht@gra-login1 abbababa]$ sed -e 's/_/./g' list | sed -e 's/samples/samples.renamed/g'>bam.filelist
[premacht@gra-login1 abbababa]$ less bam.filelist 
[premacht@gra-login1 abbababa]$ ./ANGSD -doAbbababa2 1 -bam bam.filelist -sizeFile sizeFile.size -doCounts 1 -out bam.Angsd -anc outg.fa -useLast 0 -minQ 20 -minMapQ 20 -p 1
	-> angsd version: 0.933-37-g65283b1 (htslib: 1.10.2-82-g9de45b7) build(May 27 2020 14:19:48)
	-> ./ANGSD -doAbbababa2 1 -bam bam.filelist -sizeFile sizeFile.size -doCounts 1 -out bam.Angsd -anc outg.fa -useLast 0 -minQ 20 -minMapQ 20 -p 1 
	-> Inputtype is BAM/CRAM
[multiReader] 45 samples in 45 input files
	-> Reading fasta: outg.fa
	-> 4 Populations | 3 trees | 46 Individuals | outg.fa Outgroup
	-> Parsing 45 number of samples 
[E::faidx_adjust_position] The sequence "Scaffold100" was not found

[loadChr] Error loading fasta info from chr:'Scaffold100' 
	-> Problem with length of fastafile vs length of chr in BAM header
	-> Chromosome name: 'Scaffold100' length from BAM header:313023 length from fai file:0
Trying to access fasta efter end of chromsome+200:Scaffold100/Scaffold100 pos=68706 ref_len=0
Trying to access fasta efter end of chromsome+200:Scaffold100/Scaffold100 pos=307797 ref_len=0

	-> Done reading data waiting for calculations to finish
	-> Done waiting for threads
	-> Output filenames:
		->"bam.Angsd.arg"
		->"bam.Angsd.abbababa2"
	-> Wed May 27 18:15:17 2020
	-> Arguments and parameters for all analysis are located in .arg file
	-> Total number of sites analyzed: 2311
	-> Number of sites retained after filtering: 2268 
	[ALL done] cpu-time used =  15.12 sec
	[ALL done] walltime used =  20.00 sec
[premacht@gra-login1 abbababa]$ ./ANGSD -doAbbababa2 1 -bam bam.filelist -sizeFile sizeFile.size -doCounts 1 -out bam.Angsd -anc outg.fa -useLast 0 -minQ 20 -minMapQ 20 -p 1^C
[premacht@gra-login1 abbababa]$ less bam.filelist 

all.samples.renamed/BJE3639.sorted.bam
all.samples.renamed/CoGH105.fqsorted.bam
all.samples.renamed/JMEC003.fqsorted.bam
all.samples.renamed/JMEC006.fqsorted.bam
all.samples.renamed/jonk.02.fqsorted.bam
all.samples.renamed/XGL713.232.sorted.bam
all.samples.renamed/XGUAE.124.sorted.bam
all.samples.renamed/XGUAE.59.sorted.bam
all.samples.renamed/XGUAE.65.sorted.bam
all.samples.renamed/XGUAE.70.sorted.bam
all.samples.renamed/XGUAE.71.sorted.bam
all.samples.renamed/XGUAE.72.sorted.bam
all.samples.renamed/XGUAE.92.sorted.bam
all.samples.renamed/XGUAE.93.sorted.bam
all.samples.renamed/XGUAE.97.sorted.bam
all.samples.renamed/XL.CPT1.sorted.bam
all.samples.renamed/XL.CPT2.sorted.bam
all.samples.renamed/XL.CPT3.sorted.bam
all.samples.renamed/XL.CPT4.sorted.bam
all.samples.renamed/XLJONK.14.sorted.bam
all.samples.renamed/XG12.07.sorted.bam
all.samples.renamed/XG153.sorted.bam
all.samples.renamed/XG92.sorted.bam
all.samples.renamed/XGL713.123.sorted.bam
all.samples.renamed/XGL713.177.sorted.bam
all.samples.renamed/XGL713.179.sorted.bam
all.samples.renamed/XGL713.180.sorted.bam
all.samples.renamed/XGL713.181.sorted.bam
all.samples.renamed/XGUAE.36.sorted.bam
all.samples.renamed/XGUAE.42.sorted.bam
all.samples.renamed/XGUAE.43.sorted.bam
all.samples.renamed/XGUAE.44.sorted.bam
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
# Create errorList.error
```text
NA
NA
./errorFile.ancError
NA
```
# **************************************************
# turn outgroup into fasta
```bash
module load nixpkgs/16.09
module load gcc/5.4.0
module load samtools/1.10
samtools fasta samples_seperated/outgroup/XM_1_sorted.bam > outg.fa
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
./ANGSD -doAbbababa2 1 -bam bam.filelist -sizeFile sizeFile.size -doCounts 1 -out bam.Angsd -anc outg.fa -useLast 0 -minQ 20 -minMapQ 20 -p 1
```

# create popNames.name
```text
H1
H2
H3
O
```
# load r
```bash
module load nixpkgs/16.09
module load gcc/8.3.0
module load r/4.0.0
R
install.packages('pracma')
Rscript DSTAT angsdFile="bam.Angsd" out="result" sizeFile=sizeFile.size nameFile=popNames.name
```
