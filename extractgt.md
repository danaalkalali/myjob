## Extracting genotypes from NYUAD Jubail HPC using gencore and bcftools modules

### Step 1. Log into NYUAD Jubail HPC 
```ssh netid@jubail.abudhabi.nyu.edu```
### Step 2. Go to desired .vcf.gz
``` cd $SCRATCH```
### Step 3. Load modules
``` 
module purge
module load all gencore/2
module load bcftools/1.9 
```
### Step 4. View desired position (make sure it is aligned to grch38, and use position not rsid)
```
bcftools view -r chr__:_______ vcffilename.vcf.gz
```

### Step 5. Extract genotype information into table and save output to a .tsv file
```
bcftools query \
- r chr__:______ \
-f '%CHROM\t%POS\t%REF\t%ALT[\t%SAMPLE\t%GT\t%DP]\n' \
vcffilename.vcf.gz > desiredlocation/genotype_table.tsv
```


### Dictionary

| Argument | Meaning |
|---|---|
| `view` | View position from VCF |
| `query` | Extracts fields from a VCF |
| `-f` | Specifies output format |
| `%CHROM` | Chromosome |
| `%POS` | Genomic position |
| `%REF` | Reference allele |
| `%ALT` | Alternate allele |
| `%SAMPLE` | Sample/participant name |
| `%GT` | Genotype |
| `%DP` | Read depth |
| `%GQ` | Genotype quality |
| `\t` | Tab separator |
| `\n` | New line |
