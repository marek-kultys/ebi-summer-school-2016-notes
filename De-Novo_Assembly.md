### DE NOVO ASSEMBLY

#### De Novo Genome Sequence Assembly of the E. Coli 11-3677 Strain

We want to assemble the Ec11-3677 strain de novo (as opposed to another assembly method, which is done by mapping).

1. Go to [ENA](http://www.ebi.ac.uk/ena) and search for [Escherichia Coli 11-3677](http://www.ebi.ac.uk/ena/data/search?query=Escherichia+coli+11-3677).

2. Select [Sample SRS259598](http://www.ebi.ac.uk/ena/data/view/SRS259598).

3. Download [SRR341554 File 1](ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR341/SRR341554/SRR341554_1.fastq.gz) and [SRR341554 File 2](ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR341/SRR341554/SRR341554_2.fastq.gz) from Illumina HiSeq 2000 (Fastq files from ftp) - [Accessed on 14 June 2016 2pm, Study Accession PRJNA68215, Experiment Accession SRX095636, Run Accession SRR341554].

4. Use [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) (we used v0.11.5) tool to look at quality metrics of Illumina SRR341554 File 1 and File 2.

5. Our quality looks satisfactory.

6. Download [SRR353451 File 1](ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR353/SRR353451/SRR353451.fastq.gz) from PacBio RS (Fastq files from ftp) - [Accessed on 14 Jun 2016, 2:19pm, Study Accession SRS259598, Experiment Accession SRX101294, Run Accession SRR353451].

7. Use FastQC tool to look at quality metrics of File (SRS259598).

8. Initial comparison between Illumina and PacBio shows that Illumina reads are shorter (~101 base pairs), but there is more of them (about 0.5 million reads), whereas PacBio reads are fewer (633 reads), but longer (between 47 and 1160 base pairs).

---

#### Assembly with *Velvet* from single sequencing experiment

We now want to assemble the genome from raw reads from SRR341554 Illumina files 1 and 2 using *[Velvet](https://www.ebi.ac.uk/~zerbino/velvet/) (v1.2.10)*:

1. In Terminal, go to directory with saved raw reads (`wget` is alternative to downloading from browser).

2. Run `velveth` to do prep work for assembly using: `velveth [output directory] [kmer length] [read type options] [file format] [option for paired reads] [file 1 directory] [file 2 directory]` We ran: `velveth Assemlby 31 -shortPaired -fastq.gz -separate SRR341554_1.fastq.gz SRR341554_2.fastq.gz`.

3. Once *velveth* is done, in Terminal go to its output directory specified in point 2 (in our case it was Assembly).

4. Run: `velvetg [output directory]` We ran: `velvetg .` *velvetg* is the actual assembly tool.

5. *velvetg* failed because of not enough memory allocated.

---

#### Assembly with *SPAdes* from single sequencing experiment

For comparison we will assemble the genome from SRR341554 Illumina files 1 and 2 using *[SPAdes](http://bioinf.spbau.ru/spades) (v3.8.0)*:

1. In Terminal, run *spades* from anywhere by typing: `spades.py -1 [directory for File 1] -2 [directory for File 2] -o [output directory]` We ran: `spades.py -1 /home/training/Downloads/SRR341554_1.fastq.gz -2 /home/training/Downloads/SRR341554_2.fastq.gz -o /home/training/Downloads`

2. Spades was successful. It generated:
   - Folders:
      - corrected
      - K21
      - K33
      - K55
      - misc
      - tmp
   - Files:
      - assembly_graph.fastg
      - before_rr.fasta
      - contigs.fasta
      - contigs.paths
      - dataset.info
      - input_dataset.yaml
      - params.txt
      - scaffolds.fasta
      - scaffolds.paths   
      - spades.log

3. *Spades* has the benefit of running several Kmer settings at once and picking the best result automatically. The downside is that *Spades* takes time (~200% of assembly by *Abyss*).

---

#### Assembly with *Abyss* from single sequencing experiment

We will also assemble the genome from SRR341554 Illumina files 1 and 2 using *[Abyss](https://github.com/bcgsc/abyss) (v3.81)*. (To [install Abyss on Mac](https://github.com/bcgsc/abyss#install-abyss-on-mac-os-x), first install [Homebrew](http://brew.sh/) and run `brew install homebrew/science/abyss`).

1. Unzip all fastq.gz files using `gunzip`

2. Place unzipped input files to the directory in which you want *Abyss* to work and to which you want it to output assembly files. We did it in: `/home/training/Downloads/Abyss/illumina-only`

3. In Terminal, navigate to this directory and run *Abyss* by typing:
`abyss-pe [kmer length] [assembly name] [library used as input] [input libraries]` (NOTE: `abyss-pe`stands for "paired end"). We ran:
`abyss-pe k=31 name=ecoli lib="pe1" pe1="SRR341554_1.fastq SRR341554_2.fastq"`

4. The output is many files.

5. *Abyss* has the advantage of being approx. 50% faster than *Spades* (but runs only 1 Kmer setting per assembly).


#### Assembly with *Abyss* from multiple sequencing experiments

For comparison, we will made another assembly using *Abyss* and 3 raw files as input: SRR341554 Illumina files 1 and 2, and one SRR353451 PacBio. *Abyss* uses raw reads of different type from two separate platforms and separate experiments to create one assembly that can be done having best of both worlds - Illumina's high quality reads and PacBio's long reads:

1. Unzip all fastq.gz files using `gunzip` Place unzipped input files to the directory in which you want *Abyss* to work, and to which you want it to output assembly files. We did it in: `/home/training/Downloads/Abyss/illumina-plus-pacBio`

2. In Terminal, navigate to this directory and run Abyss by typing: `abyss-pe [kmer length] [assembly name] [library used as input] [input libraries]` We ran: `abyss-pe k=31 name=ecoli lib="pe1" pe1="SRR341554_1.fastq SRR341554_2.fastq" se="SRR353451.fastq"` (NONTE: `se` stands for "single ended".)

3. The output is many files.

   > [Here](http://evomics.org/learning/assembly-and-alignment/abyss/) is an interesting tutorial on de novo assembly of Illumina reads using *ABySS* and alignment using *BWA*.

---

#### Quality check of assembly with *Quast*

We now want to check the quality of our assembly made with *Spades* (one of the most common statistics used to describe the quality of genome assembly is [contig N50](http://www.nature.com/nrg/journal/v13/n5/box/nrg3174_BX1.html)):

1. Go to online tool [QUAST](http://quast.bioinf.spbau.ru).

2. Upload the "contigs.fasta" file outputted from spades to *Quast*.

3. Explore quality metrics.

---

#### Contig graphing with *Bandage*

We now want to see how the assembly was executed (for loops, dead ends, etc). It's a way to find out how messed up our assembly was.

1. Bandage will do it for us. In Terminal type `Bandage` to open the new *Bandage* window (you can also [download the app](https://rrwick.github.io/Bandage/)).

2. Import "assembly_graph.fastg" file produced by *Spades*.

3. Explore the graph to see how the contigs were put together. Some parts can be messed up, which might be because of difficulties in sequencing, high repetition areas, or - in case of bacteria - because you're looking at the CRISPR regions.

---

#### De Novo Assembler comparison

We have three sets of results from analysing the same data with three different softwares: *ABySS*, *Spades* and *Velvet*. In order to compare these results and determine the differences in parameters we will make table using the scaffold file and the contig files: `abyss-fac -t 1000 scaffolds.fasta` and `abyss-fac -t 1000 contigs.fasta` We specified `-t 1000` which overrides the default value of the n number (minimum contig length) - by default it is set to 500.


###### Table 1: Stats for assembly done with *Spades* from Illumina's SRR341554_1.fastq and SRR341554_2.fastq

n   |n:1000 |L50 |min  |N80   |N50    |N20    |E-size  |max    |sum     |name
--- |---    |--- |---  |---   |---    |---    |---     |---    |---     |---
469 |167    |15  |1026 |43526 |114039 |224901 |126413  |300784 |5205001 |scaffolds.fasta
471 |169    |15  |1026 |43526 |114039 |224901 |126287  |300784 |5205001 |contigs.fasta


###### Table 2: Stats for assembly done with *Abyss* from Illumina's SRR341554_1.fastq and SRR341554_2.fastq

n    |n:1000 |L50 |min  |N80   |N50   |N20    |E-size  |max    |sum     |name
---  |---    |--- |---  |---   |---   |---    |---     |---    |---     |---
1292 |209    |16  |1009 |35141 |98497 |197435 |116511  |287685 |5299825 |ecoli-scaffolds.fa
1345 |228    |23  |1009 |32871 |62817 |148017 |86599   |250344 |5286969 |ecoli-contigs.fa
