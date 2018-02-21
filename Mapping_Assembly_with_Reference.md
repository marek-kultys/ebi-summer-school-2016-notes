### MAPPING ASSEMBLY WITH REFERENCE

#### Mapping Raw Reads onto Reference with *BWA*

Using the [BWA mapping assembler](http://bio-bwa.sourceforge.net/), we will map raw reads from Illumina's pair-ended SRR341554_1.fastq and SRR341554_2.fastq obtained from [EBI ENA website](http://www.ebi.ac.uk/ena/data/view/SRS259598) files onto the TY-2482 E. coli reference sequence (file: [Escherichia_coli_TY-2482.chromosome.20110616.fa.gz](ftp://climb.genomics.cn/pub/10.5524/100001_101000/100001/Escherichia_coli_TY-2482.chromosome.20110616.fa.gz)) obtained from [GigaDB website](http://gigadb.org/dataset/100001) [Accessed: 15 June 2016].

Because mapping onto a reference de facto means aligning your reads to  the reference sequence, the output of our job will be an aligned assembly. Start by:

1. Create a directory where *BWA* will be working. We work in one named "Mapping".
   > *IMPORTANT NOTE:* If you have installed and made *BWA* from downloaded binaries in a specific directory on your machine (which is not the general directory with all Terminal programmes) you will need to always specify the path from which you want to run *BWA* relative to your current directory. So for example if you keep *BWA* in the `/user/Desktop/project/bwa` directory and you want to run *BWA* to run one level higher in `user/Desktop/project` directory, you will need to enter `bwa/bwa index` to run *BWA*.

2. Get the [TY-2482 reference sequence](ftp://climb.genomics.cn/pub/10.5524/100001_101000/100001/Escherichia_coli_TY-2482.chromosome.20110616.fa.gz) from [GigaDB website](http://gigadb.org/dataset/100001) and place it in our "Mapping" working directory.

   >Another way to get the reference genome via NCBI website:
   - Search for "TYÂ­2482" in [NCBI](http://www.ncbi.nlm.nih.gov/),
   - In the search result page you can click on "BioProject" (these are THE sequences that should be used, as they were curated), leading to this [BioProject page](http://www.ncbi.nlm.nih.gov/bioproject/?term=TYÂ­2482),
   - Click on the [best assembly available](http://www.ncbi.nlm.nih.gov/assembly/GCA_000221885.1) (in this case the "scaffolded" one with the 1/2 of pie chart full),
   - To download the FASTA file for this assembly, go to [Download the GenBank assembly](ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA_000221885.1_E.coli_0104_H4_Illumina_1.0) (in the right hand column),
   - Download the [file with fna.gz format](ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA_000221885.1_E.coli_0104_H4_Illumina_1.0/GCA_000221885.1_E.coli_0104_H4_Illumina_1.0_genomic.fna.gz) (which is a compressed FASTA file)
   - **NOTE:** this reference sequence has several groups of unknown positions (N), unlike the [one from GigaDB](http://gigadb.org/dataset/100001)

3. Go to working directory and `gunzip` the reference sequence. We ran `gunzip Escherichia_coli_TY-2482.chromosome.20110616.fa.gz` which produced a file in fasta (.fa) format.

4. Run `bwa index [reference sequence file]` - this will prepare the library of files needed to for your mapping. We ran `bwa index Escherichia_coli_TY-2482.chromosome.20110616.fa` which produced a set of files next to the original reference sequence in fasta format. The files were:

   - Escherichia_coli_TY-2482.chromosome.20110616.fa.amb
   - Escherichia_coli_TY-2482.chromosome.20110616.fa.ann
   - Escherichia_coli_TY-2482.chromosome.20110616.fa.bwt
   - Escherichia_coli_TY-2482.chromosome.20110616.fa.fai
   - Escherichia_coli_TY-2482.chromosome.20110616.fa.pac
   - Escherichia_coli_TY-2482.chromosome.20110616.fa.sa

5. Now, to our working directory we need to add the read files that we want to map onto the reference sequence. Move the [SRR341554_1](ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR341/SRR341554/SRR341554_1.fastq.gz) and [SRR341554_2](ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR341/SRR341554/SRR341554_2.fastq.gz) read files from the Illumina pair-ended sequencing experiment that we downloaded from the [Escherichia Coli 11-3677](http://www.ebi.ac.uk/ena/data/search?query=Escherichia+coli+11-3677) search query in the ENA EBI database that we have done previously.

6. `gunzip` the read files in the working directory.

7. Now, using the library prepared in step 4. we can run the *BWA* mapping. In our working directory run `bwa mem [reference sequence file] [reads file 1] [reads file 2] > [output file name]` To see more options for running *BWA* type `bwa mem` We ran `bwa mem Escherichia_coli_TY-2482.chromosome.20110616.fa SRR341554_1.fastq SRR341554_2.fastq > SRR341554.sam`

8. The output of our mapping will be a SAM file named SRR341554.sam

---

#### Preparing the binary BAM file with *SamTools*

We are still working in our working directory. We have now produced a SAM file as an output from mapping assembly with *BWA*. We will use [*samtools*](http://www.htslib.org/) to process the SAM files to make them suitable for variant calling.

1. Run `samtools [options] [source SAM file]` to convert your SAM file to a BAM file. We ran `samtools view -b SRR341554.sam > SRR341554.bam` where `view` ordered conversion between file formats, `-b` specified output format to be BAM, and `>` symbol ordered the output to be written to a new file named SRR341554.bam and not to the screen.

   > We now have our binary BAM file. However, before we can proceed we need to reorder the lines in the file so that they make sense for the tools we are going to use next. When we mapped the reads onto the reference sequence, BWA kept the random order of reads that came from sequencing and simply annotated the positions where individual reads should sit on the chromosome. But the lines in the file came ordered by read ID number which was assigned to them by the sequencer randomly. We now need to reorder the lines so that they are ordered by their location on the chromosome onto which they were mapped. We want the lines in the BAM file to start from the read that is in position 1 and then progress in the order defined by the location of reads in the chromosome.

2. To reorder the reads in our BAM file, run `samtools sort SRR341554.bam > SRR341554-sorted.bam` This process takes a while and literally means that *samtools* will simply reorder the lines of text in the file.

3. If you want to display your BAM file in [IGV genome browser](https://www.broadinstitute.org/igv/) (version at minimum 2.3.77), you will also need to index your BAM file and generate an indexing file compatible with BAI format required by IGV. You can do it with *samtools* bu running `samtools index -b [sorted BAM file] [indexed INDEX file]` If this does not work, try using IGV internal conversion tools.

4. We now have the binary BAM file with our reads assembled, mapped, aligned, sorted by location relative to our reference sequence, and indexed for IGV viewing. It should be in our working directory with all other files the inputted and outputted thus far.

---

#### Calling Variants with *FreeBayes*

We will now use our binary sorted BAM file and the reference sequence to call variants in the genome using [*freebayes*](https://github.com/ekg/freebayes). We will output a VCF file that will list all the variants in our SRR341554 genome.

> If you are using OSX El Captain (and potentially earlier versions) you will find it difficult or even impossible to install *freebayes* from the Git repository using instruction provided. This might be because newer versions of OSX do not support the `wget` command any more (they use `curl` instead). But there is a [solution](https://github.com/ekg/freebayes/issues/194). Try installing *freebayes* using the *[Conda](https://bioconda.github.io/)* bioinformatics software package manager. To install *Conda* first download [*Miniconda*](http://conda.pydata.org/miniconda.html) and [install it](http://conda.pydata.org/docs/install/quick.html) from Terminal by entering `bash Miniconda2-latest-MacOSX-x86_64.sh` Once you've accepted all license terms and set your preferred directory, go to that directory and run `conda install -c bioconda freebayes` to install *freebayes*.


1. Run `freebayes -f [reference sequence file] [BAM file] > [output file name]` in our working directory. We ran `freebayes -f Escherichia_coli_TY-2482.chromosome.20110616.fa SRR341554-sorted.bam > SRR341554.vcf`

2. After some time we got our VCF file in the working directory.

3. To view the VCF file enter `less SRR341554.vcf`

4. To find out how many variants are listed in the VCF, we will pipe together the `grep` command that lists of all lines that do not contain the "#" tags (in VCFs "#" is used to tag file metadata) with the `wc` command that does the word count. Run `grep -v "#" SRR341554.vcf | wc -l` where `|` pipes the output from one process as the input for another process.

5. You should 543 variants in your VCF.

6. Try filtering to improve the results. Suggested command to run freebayes to use quality filtering: `freebayes -f [reference] --ploidy 1 --theta 0.05 --genotype-qualities --standard-filters [BAM file] > [vcf])` Why?
   - `--ploidy 1` tells freebayes that we are working with a bacteria
   - `--theta 0.05` is the mutation rate, needed to establish a prior to the snp calling model
   - `--standard-filters` will filter out the noisier variants
   - `--genotype-qualities` will add some more fields to the vcf file
   - With this quality filter, we got the variant number down to 268.

   >Additional filtering after *freebayes*: `vcffilter f "DP > 40" g "GQ > 20" in.vcf > out.vcf`
   Why:
   - DP is the depth of the reads at the variant, and we want to see this base many times (>40) before calling it,
   - GQ is the genotype quality, expressed in PHRED (as the base qualities in the reads), and we want it to be at least 20.

#### Analysing VCF files using *bedtools*

Bedtools are very old tools that will help us learn a lot of metrics about the VCF files.
