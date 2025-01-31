## Recruiting participants from HFS to ARI 
Written in python.

### Tools needed
- SNP lists: We use the [GWAS catalog](https://www.ebi.ac.uk/gwas/)
- [Python3](https://www.python.org/downloads/): or alter the code to your language of choice 

### 1. Setting up the SNP data frame 
First, we will set up a data frame with a concatenated list of all the SNPs related to our phenotype of choice. You can have as many catalogs/list of SNPs you want, from your database of choice. Here I am downloading the GWAS catalogs.

```
import panda as pandas

GWAScatalog1 = /path/name/here/ # put as many SNP lists as you want here
GWAScatalog2 = /path/name/here/
GWAScatalog3 = /path/name/here/
GWAScatalog4 = /path/name/here/
GWAScatalog5 = /path/name/here/

df1 = pd.read_csv(path1,sep='\t',encoding='latin-1') # read the data as a csv
df2 = pd.read_csv(path2,sep='\t',encoding='latin-1') 
df3 = pd.read_csv(path3,sep='\t',encoding='latin-1') 
df4 = pd.read_csv(path4,sep='\t',encoding='latin-1') 
df5 = pd.read_csv(path5,sep='\t',encoding='latin-1') 

dfall = pd.concat([df1,df2,df3,df4,df5], axis=0, ignore_index=True) #concatenate

```
