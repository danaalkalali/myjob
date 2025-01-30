# Finding your population structure 
This is a tutorial on how to find population structure groups to use as a confounder in your GWAS analysis!

Population structure is an important confounder to include in your genetic association analyses. You might see an association that you think is due to the genotype-phenotype relationship, but is actually due to the fact that the allele is common in one of the (sub)populations in your study. This would lead to false positives, which I'm sure you're not a fan of. 

## Tools required
- PLINK
- SnpEff
- SnpSift or bcftools 
- fastStructure
- EmmaX
- You will also need your vcf files, duh!

## 1. Convert your VCF to PLINK format
`plink --make-bed --vcf yourvcffilename.vcf --out outputfilenamedesired 
`

(_I add the argument --double-id at the end because of how my IDs are named, plink won't understand if you have an underscore or something funny._)

This will make 3 plink files: .bed, .bim, and .fam. From now on, any plink commands can be run with just the file name and not the file extension, and plink will use whichever of the 3 files it needs.

## 2. Kinship matrix 

Now we can use the plink files to generate the kinship matrix.

`plink --bfile plinkfilename --make-rel square --out filename_ofyourchoice
`

## 3. Frequency file 

Now the allele frequency file.

`plink --bfile plinkfilename --freq --out filename_ofyourchoice
`
Note that this is different than --maf because we are generating a frequency file, which is passive, not filtering out variants based on the minor allele frequency, which is active. 

Now we have our PLINK files that are needed for EmmaX later. Next, let's make the fastStructure population structure file.

## Preparing the fastStructure input VCF 

### 1. Filtering out non-synonymous SNPs 
We will use the raw VCF again in SnpEff. We first remove non-synonymous SNPs. This is important for population structure analysis because non-synonymous SNPs are under a lot of selective pressure and are therefore not truly represenative of the ancestry but rather adaptation.

First, we annotate our VCFs with SnpEff for functional annotations (synonymous, non-synonymous, etc.)


Second, we will filter out the non-synonymous SNPs and only keep synonymous SNPs. You can do this with bcftools or SnpSift, whichever you prefer. 

- Bcftools
`
- SnpSift
`

### 2. LD Pruning 
We only want to keep SNPs that are independent of one another to avoid redundancy, loci bias, and really, really big files.
- The plink command for LD pruning is --indep-pairwise, with 3 inputs. The first input is the window size, approx 50 for humans. 
- The second, 5, is the step size. 
- The final number is the r^2 threshold. 0.1 is good for a larger sample size like 2000, but I would make it 0.2 for my current batch of 100.

`plink --vcfname --indep-pairwise 50 5 0.1 
`
## 3. Population structure grouping 
Now we will use fastStructure to do population structure grouping with 

This file will be used as one of the EmmaX GWAS input files.

## Preparing PLINK files for GWAS input 

### 3. Kinship matrix 

`plink --make-rel square kinship_matrix_filename_ofyourchoice


## 5. Data Visualization (optional, not really)

## 6. GWAS 
This is where it all comes together! You have to use your ORIGINAL, UNFILTERED vcf for this, not the one you filtered - hence, genome-wide.
