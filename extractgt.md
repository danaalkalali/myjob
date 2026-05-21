## Extracting genotypes from NYUAD Jubail HPC using gencore and bcftools modules

### Step 1. Log into NYUAD Jubail HPC 
```ssh netid@jubail.abudhabi.nyu.edu```
### Step 2. Go to desired .vcf.gz
``` cd $SCRATCH```
### Step 3. Load modules
``` 
module purge
module load all gencore/2
module load all bcftools/1.9 
```
### Step 4. View desired position (make sure it is aligned to grch38, and use position not rsid).
```
bcftools view -r chr__:_______ vcffilename.vcf.gz
```
