# Finding your population structure 
This is a tutorial on how to find population structure groups to use as a confounder in your GWAS analysis!

Population structure is an important confounder to include in your genetic association analyses. You might see an association that you think is due to the genotype-phenotype relationship, but is actually due to the fact that the allele is common in one of the (sub)populations in your study. This would lead to false positives, which I don't think you want if you're hoping your paper gets published. 

## Tools required 
(Tsk, colleagues, most of these are available on the NYUAD Jubail HPC).

- [PLINK](https://www.cog-genomics.org/plink/)
- [SnpEff](https://pcingola.github.io/SnpEff/snpeff/running/)
- [SnpSift](https://pcingola.github.io/SnpEff/snpsift/introduction/) or [bcftools](https://samtools.github.io/bcftools/howtos/install.html) 
- [fastStructure](https://rajanil.github.io/fastStructure/)
- [Pong](https://github.com/ramachandran-lab/pong/blob/master/README.md)
- [EmmaX](https://genome.sph.umich.edu/wiki/EMMAX)
  - If you are doing GWAS, you will also need your phenotype file(s). Check the EMMAX documentation on how to prepare phenotype files. 
- You will also need your vcf file(s), duh!

## _Preparing PLINK files_

## 1. Convert your VCF to PLINK format
```
plink --make-bed --vcf yourvcffilename.vcf --out outputfilenamedesired
```

(_I add the argument `--double-id` at the end because of how my IDs are named, plink won't understand if you have an underscore or something funny._)

This will make 3 plink files: .bed, .bim, and .fam. From now on, any plink commands can be run with just the file name and not the file extension, and plink will use whichever of the 3 files it needs.

## 2. Kinship matrix 
Now we can use the plink files to generate the kinship matrix.

```
plink --bfile plinkfilename --make-rel square --out filename_ofyourchoice
```

Plink's kinship matrix is an identity-by-state matrix. This means that 

We also input the argument square because that it the matrix required for EmmaX, rather than the default which is triangle.

## 3. Frequency file 
Same thing now but for the allele frequency file.

```
plink --bfile plinkfilename --freq --out filename_ofyourchoice
```

Note that this is different than the `--maf` argument because we are generating a frequency file, which is passive and retains all variants, _not_ filtering out variants based on the minor allele frequency, which is active. 

Now we have our 2 PLINK files that are needed for EMMAX later. Next, let's make the fastStructure population structure file.

## _Preparing VCF for fastStructure_

## 4. Filtering out non-synonymous SNPs
We will use the raw VCF again in SnpEff. We first remove non-synonymous SNPs. This is important for population structure analysis because non-synonymous SNPs are under a lot of selective pressure and are therefore not truly represenative of the ancestry but rather adaptation.

First, we annotate our VCFs with SnpEff for functional annotations (synonymous, non-synonymous, intergenic, intron, UTR, up/downstream, etc.)

```
java -Xmx90g -jar snpEff.jar GRCh38.105  yourvcf.vcf > yourannotatedvcf.vcf 
```

Please change how much RAM you allocate for java in the `-Xmx__g` argument. Also make sure you are using your correct reference genome, in my case GRCh38.105.


Second, we will filter out the non-synonymous SNPs and only keep synonymous SNPs. You can do this with bcftools or SnpSift, whichever you prefer. 

- **Bcftools**

```
bcftools filter -i 'INFO/ANN~"missense_variant|stop_gained|stop_lost|start_lost|frameshift_variant"' yourfile.vcf -o nonsyn.vcf
```


- **SnpSift**

```
java -jar SnpSift.jar filter "(ANN[*].EFFECT has 'missense_variant') | (ANN[*].EFFECT has 'stop_gained') | (ANN[*].EFFECT has 'frameshift_variant')" yourfile.vcf > nonsyn.vcf
```


## 5. LD Pruning 
Now we will filter this only-synonymous-variants VCF one step further. We only want to keep SNPs that are independent of one another to avoid redundancy, loci bias, and really, really big files.
- The plink command for LD pruning is `--indep-pairwise`, with 3 inputs.
  - The first input is the window size, approx 50 for humans. 
  - The second, 5, is the step size. 
  - The final number is the r^2 threshold. 0.1 is good for a larger sample size and if you want to be more strict, but I would make it 0.2 for my current batch of 100.

```
plink --vcfname --indep-pairwise 50 5 0.2 
```

## _Population structure grouping_

## 6. fastStructure  
Now we will use fastStructure to do population structure grouping. The main fastStructure script is `structure.py`
- The inputs needed for faststructure are the path to the 3 plink files, so they should be all under the same path
- The `-K` argument is the K-mer of populations you would like fastStructure to divide your sample into, usually around K = 1-10
- `--full` is an optional argument to output all variational parameters

```
python structure.py -K 3 --input=path/to/plink --output=path/to/output
```

You will have 3 output files, `.log`, `.meanP` for the mean of allele frequencies, and `.meanQ` for the mean of admixture proportions. Basically, in the meanQ file, each line is an individual with each column showing the proportion of ancestry from a cluster. So we're going to use PONG to help us cluster the individuals. 

This file will be used as one of the EmmaX GWAS input files, however we need to convert it to a `.txt` file to input as a covariate. 

## 7. Data Visualization (optional, not really?)

We will use PONG to visualize our fastStructure `.meanQ` file. If you don't want to use another tool, fastStructure actually has the `distruct.py` script that visualizes the `.meanQ`, however does it much rougher than PONG.

- **PONG** 

- **fastStructure `distruct.py` option**

```
python distruct.py
  -K 3
  --input=path/to/plink
  --output=path/to/output
  --popfile=file/with/labels/ifyouwish
  --title="figuretitleifyouwish"
```

Now you are done with your population structure analysis. The next two steps are if you want to do GWAS and use the population structure as a confounding variable. 

## 8. Format fastStructure file for EMMAX

## _GWAS_

## 9. EMMAX 
This is where it all comes together! You have to use your _original, unfiltered_ vcf for this, not the one you filtered - hence, genome-wide and not genome-slim.

Checklist:

- [X] PLINK files derived from original VCF `.bed`, `.bim`, `.fam`
- [X] PLINK kinship matrix `.rel`
- [X] fastStructure population clusters `.txt`
- [X] Phenotype file `.txt`

```
emmax -v -d 
```

## _Other stuff_

#### Using a dummy VCF
Don't have your own data and just want to practice?
