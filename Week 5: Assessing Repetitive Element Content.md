# Check out our results!

**Question: why annotate reptitive elements in the first place?**

Over the past week, we ran a repeat identification and annotation pipeline (EarlGrey) to assess the repetitive element content of the male and female O. binodis genomes. Now, let check out our results, which I have placed in a new folder called `results` in our shared project directory.

```bash
ssh [IU_USERNAME]@quartz.uits.iu.edu

cd /N/project/moczek_cisreg/ob_genome/results
ls -lh
```

Presently, we have four files here:

```
obf_summaryFiles_rep
obf_v1_2.fasta
obm_summaryFiles_rep
obm_v1_2.fasta
```

The `*v1_2.fasta` files are our cleaned genomes, and the `*summaryFiles*` folders represent the output from our repeat analyses for each sex. Lets go into the "female" results and see what we have.

```bash
cd obf_summaryFiles_rep
ls -lh
```
which outputs:

```
obf_classification_landscape.pdf
obf_combined_library.fasta
obf_divergence_summary_table.tsv
obf-families.fa.strained
obf.familyLevelCount.txt
obf.filteredRepeats.bed
obf.filteredRepeats.gff
obf.highLevelCount.txt
obf.softmasked.fasta
obf_split_class_landscape.pdf
obf.summaryPie.pdf
obf_superfamily_div_plot.pdf
```

For the purposes of genome annotation, perhaps the two most important files are the `obf.softmasked.fasta` and `obf.filteredRepeats.gff`. The *softmasked* genome has the repetitive elements within the genome set to lowercase (a softmask), which can be recognized by gene annotation programs. The `gff` file describes the location of each reptitive element identified within the genome.

Lets quickly preview the contents of these files with the `head` command:

```bash
head obf.softmasked.fasta
head obf.filteredRepeats.gff
```

In addition, we have other fasta files (`obf_combined_library.fasta`, `obf-families.fa.strained`) that describe the sequence of each repetitive element identified. For e.g.:

```bash
head obf-families.fa.strained
```

Lastly, EarlyGrey creates a number of summary statistics and figures for us the check out. Lets analyze them now!

```
obf.summaryPie.pdf
```
<img width="904" alt="Screenshot 2024-10-30 at 10 21 15 PM" src="https://github.com/user-attachments/assets/bea22ebd-43b7-4c7d-be43-1d4b9146c81f">

This file describes the relative composition of each reptitive element class to the genome's content. As we can estimate, the O. binodis genome is > 75% repetitive! The underlying data file is `obf.highLevelCount.txt` .

```
obf_classification_landscape.pdf
```

<img width="1110" alt="Screenshot 2024-10-30 at 10 24 11 PM" src="https://github.com/user-attachments/assets/d6501cfd-9b5e-4b9d-8958-49964a605250">

This is a really neat plot, as it describes the relative age of repetitive elements within the genome! This can give us a proxy for understanding how transposable element activity has changed over evolutionary time. Here, for recent activity is found on the right side of the graph, and the metric for this is the *kimura distance*. This model measures the genetic distance between sequences, such that if the sequences are evolving at a steady neutral rate, then their relative age may be inferred. 

These next plots break down this result by TE class and further by family. Pretty neat!

<img width="1112" alt="Screenshot 2024-10-30 at 10 29 47 PM" src="https://github.com/user-attachments/assets/db692e50-9d56-45a0-8824-b366941c82b4">

<img width="1116" alt="Screenshot 2024-10-30 at 10 30 04 PM" src="https://github.com/user-attachments/assets/11006506-a7bb-4616-8ae5-af525c531085">





# Submitting our genome annotation jobs

In preparation for our next meeting