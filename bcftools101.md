# Bcftools basic tutorial 

## Tools you need
- Bcftools
- Tabix (if your VCFs are indexed `.vcf.gz`, mine are)

### Variant calling

- **Calling one single position**: 
```
Bcftools view -r chr1:123123123 yourvcf.vcf.gz
```

The `-r` argument is when you are specifying a genomic region (so you don't need to use it if you are inputting the rsid). 

- **Calling all variants in one chromosome**:

```
Bcftools view -r chr1 yourvcf.vcf.gz
```

- **Calling variants from two separate regions**: 
```
Bcftools view -r chr1:1-123,chr1:2000-4000 yourvcf.vcf.gz
```

- **Calling variants from a list of rsids/chromosomal positions you have:**
```
#example of .txt file


rs572000
rs2025
rs1312                   #you can combine rsid
chr21:10101383           #with chromosomal positions
chr7:50276666            #in the same txt file!
```
```
Bcftools view -t yourlist.txt yourvcf.vcf.gz
```

- **Calling a variant by genotype only**: 

```
Bcftools view -R positions.txt yourvcf.vcf.gz | bcftools query -f '%CHROM\t%POS\t%REF\t%ALT\t[%GT]\n'
```


## VCF file statistics 
```
Bcftools stats yourvcf.vcf.gz
```
