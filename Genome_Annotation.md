### GENOME ANNOTATION

It is possible to annotate fasta sequences or de novo genome assemblies for genes.

#### De Novo Genome Annotation with *Prokka (v1.11)*

We used [*Prokka*](https://github.com/tseemann/prokka) for genome annotation.

- Annotation **without proteins**

   Run `prokka --addgenes [scaffold file]` In our case it was `prokka --addgenes /home/training/Desktop/Penelope/Summer_School/assembly_group2/Assemblies/Spades/scaffolds.fasta` Scaffolds came from assembling in *Spades* (which we found to be the best assembler for our purpose).

- Annotation **with proteins**

   Run `prokka --addgenes [scaffold file] --proteins [annotation file]` In our case it was `prokka --addgenes /home/training/Desktop/Penelope/Summer_School/assembly_group2/Assemblies/Spades/scaffolds.fasta --proteins /home/training/Desktop/Penelope/Summer_School/assembly_group2/Annotations/ecoli-seq.fasta` Complete protein set of *E. coli*  came from NCBI proteins database - we searched for TY-2482, which was the name of our reference strain.

#### Annotation of a Reference Sequence with *Prokka (v1.11)*

Same annotation with *prokka* can be done onto a reference sequence used in mapping with *BWA*. In such case simply use the reference sequence in .fasta format instead of the scaffolds input to *prokka*.