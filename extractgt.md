## Extracting genotypes from NYUAD Jubail HPC using gencore and bcftools modules

### Step 1. Log into NYUAD Jubail HPC 
```bash
ssh netid@jubail.abudhabi.nyu.edu
```
### Step 2. Go to desired .vcf.gz
```bash
cd $SCRATCH
```
### Step 3. Load modules
```bash
module purge
module load all gencore/2
module load bcftools/1.9 
```
### Step 4. View desired position (make sure it is aligned to grch38, and use position not rsid)
```bash
bcftools view -r chr__:_______ vcffilename.vcf.gz
```

### Step 5. Extract genotype information into table and save output to a .tsv file
```bash
bcftools query \
- r chr__:______ \
-f '%CHROM\t%POS\t%REF\t%ALT[\t%SAMPLE\t%GT\t%DP]\n' \
vcffilename.vcf.gz > desiredlocation/genotype_table.tsv
```

### Step 6. Download file onto local computer
```bash
# log out of HPC
exit
# download file
scp netid@jubail.abudhabi.nyu.edu:/desiredlocation/genotype_table.tsv /desiredlocation/on/localcomputer/
```
Now you can open it in excel/sheets/terminal whatever.

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
| `scp` | Securely copies files between computers |

