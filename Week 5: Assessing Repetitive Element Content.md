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

The repetitive element/TE world is **HUGE** and a field in of itself. For now we will use this information to aid in annotating other genome features, but there is certainly a lot of cool biology to be found here!

# Submitting our genome annotation jobs

In preparation for our next meeting, lets go ahead and submit two gene annotation jobs using Singularity (software for running "containerized" images). Containerized images are exptremely useful for reproducible science, as entire programs and pipelines essentially come pre-packaged as a single file that can be run on any system. This makes installation a breeze relative to compiling many programs individually. 

We will be running two annotation programs, which I'll explain in detail next time we meet. For now, they are called *BRAKER* and *GALBA*. The idea will be to annotate each each using both of these methods and compare the results to see if one pipeline works better than the other. First lets locate our singularity `*sif` files:

```bash
ls -lh /N/project/moczek_cisreg/programs/singularity/
```

```
braker3.sif
galba.sif
Otau_proteins.fasta
testing_files
```

The `*sif` files are the singularity images, the testing folder is the folder I'm running tests in (please dont touch!), and the `Otau_proteins.fasta` are the *Onthophagus taurus* gene models that we will use the train the machine learning algorithms to predict genes within *O. binodis*. 

Now lets prepare to submit our jobs. First, copy the Otau proteins and soft masked genome file for the sex you are analyzing into your user directory. In this example, I am showing for the female assembly. 

```bash
cd /N/project/moczek_cisreg/ob_genome/u_{INITIALS}
cp ../../programs/singularity/Otau_proteins.fasta .
cp ../results/obf_summaryFiles_rep/obf.softmasked.fasta .
```

Now copy over two sample scripts into your user directory (one for BRAKER, one for GALBA)

```bash
cp ../scripts/sample.bash ./braker.bash
cp ../scripts/sample.bash ./galba.bash
```

Now edit your scripts to run each job (using `nano`). For BRAKER:

```bash
nano ./braker.bash
```

```


singularity exec -B ${PWD}:${PWD} ${BRAKER_SIF} braker.pl --species=obm2 --prot_seq=Otau_proteins.fasta --workingdir=./test_braker --threads 1 --genome=obm2.softmasked.fasta
```












