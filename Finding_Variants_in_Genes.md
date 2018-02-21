# 2. FINDING VARIATION IN GENES

At this point we know what are the variants in our genome and where are the genes. We can find out how many variants listed in each .vcf file that we produced fall into the annotated genes. That will tell us what proportion of variation happened in coding regions of the genome (based on the knowledge of those genes that we have).

For this we will use standard UNIX command:

1. To find out the **number of variants in a .vcf file**, run `grep -v "#" [VCF file] | wc -l` This counts the number of lines in the .vcf file (one variant is one line) excluding many lines of .vcf metadata which all are marked with the # symbol. So we ran `grep -v "#" SRR341554_11-3677.vcf | wc -l`

2. To find out what is the **number of variants in gene regions** that we have annotated (how many of variants intersect with the gene regions), we will use powerful [*bedtools*](http://bedtools.readthedocs.io/en/latest/). We ran `intersectBed -a [set A: file with annotation] -b [set B: file with variants] -wb | wc -l` In our case it was `intersectBed -a PROKKA_06162016_no_fasta.gff -b SRR341554_11-3677.vcf -wb | wc -l`

3. If you have more .vcf files, you can also find out how many variants in gene regions are shared across these .vcf files. Again, use *bedtools* by running `intersectBed -a [set A: file with annotation] -b [set B: multiple files with variants] -u | wc -l` In our case we ran `intersectBed -a PROKKA_06162016_no_fasta.gff -b SRR341554_11-3677.vcf SRR341563_11-4632.vcf SRR341561_11-4623.vcf -u | wc -l`