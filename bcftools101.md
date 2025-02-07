# Bcftools Hand-holding  

## Tools you need
- [Bcftools](https://samtools.github.io/bcftools/)
- [Tabix](http://www.htslib.org/doc/tabix.html) (if your VCFs are indexed with `.tbi` files, mine are! Otherwise bcftools alone should suffice)

## Variant calling

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

 **Calling missense variants**: 
```
Bcftools view -i 'INFO/ANN[*] ~ "missense_variant"' yourannotatedvcf.vcf # depending on bcftools version (before or after 1.19), view might be changed to `call`
```


## VCF file statistics 

```
Bcftools stats yourvcf.vcf.gz
```


## FAQs 

**Q:** _What's the difference between bcftools and vcftools?_

**A:** The b in bcftools stands for binary. This refers to the .bcf file which is an indexed version of a vcf. Vcfs can also be indexed without turning into a .bcf but instead having complimentary index files like the .tbi tabix file. Vcftools doesn't support indexed files and only takes unindexed vcfs. Thus, vcftools can be much slower.


**Q:** _Are all vcfs supposed to be indexed?_

**A:** Unless you own a supercomputer. You should index them especially if they are joint/project VCF with multiple samples.


**Q:** _What's the difference between a bcf and a vcf with index files?_

**A:** 


**Q:** _Is samtools a conglomerate?_

**A:** Yes but it's open-source, so...

