## Finding your population structure 
### This is a tutorial on how to find population structure groups to use as a confounder in your GWAS analysis! 

### Population structure is an important confounder to include in your genetic association analyses. You might see an association that you think is due to the genotype-phenotype relationship, but is actually due to the fact that the allele is common in one of the (sub)populations in your study. This would lead to false positives, which I'm sure you're not a fan of. 

### Tools required
### - Plink 
### - SnpEff
### - FastStructure
### - EmmaX


### 1. Filtering out non-synonymous SNPs 
### We first remove non-synonymous SNPs. This is important for population structure analysis because non-synonymous SNPs are under a lot of selective pressure and are therefore not truly represenative of the ancestry but rather adaptation.



### 2. LD Pruning 
### We have to remove SNPs that are not et
### The plink command for LD pruning is --indep-pairwise, with 3 inputs. The first input is the window size, approx 50 for humans. 
### The second, 5, is the step size. 
### The final number is the r^2 threshold. 0.1 is good for a larger sample size like 2000, but I would make it 0.2 for my current batch of 100.

'plink --vcfname --indep-pairwise 50 5 0.1' 
