# Gene Modelling with BRAKER and GALBA

Two weeks ago we predicted gene moels in both the male and female _O. binodis_ genomes using two programs, BRAKER and GALBA. Today we are going to check out those results and explore some ways to assess the quality of the predicted gene sequences. 

As usual, to start, lets login to Quartz and navigate to our shared project folder:

```bash
ssh [IU_USERNAME]@quartz.uits.iu.edu

cd /N/project/moczek_cisreg/ob_genome/results
ls -lh
```

Which outputs:

```
BUSCO
gene_models
obf_summaryFiles_rep
obf_v1_2.fasta
obm_summaryFiles_rep
obm_v1_2.fasta
```

I've played the results of the BRAKER and GALBA analyses into the gene_models directory. I've also put the _O. taurus_ gene models in for comparison. Lets view those now:

```bash
ls -lh ./gene_models
```
```
braker_f.aa
braker_f.codingseq
braker_f.gtf
braker_f.log
braker_m.aa
braker_m.codingseq
braker_m.gtf
braker_m.log
galba_f.aa
galba_f.codingseq
galba_f.gtf
galba_m.aa
galba_m.codingseq
galba_m.gtf
Otau3.gtf
Otau.aa
Otau.codingseq
```

Here the `*aa` files are the protein coding sequences, the `*codingseq` files are the nucleotide sequences, and the `*gtf` files are the coordinates for various gene features. 

As mentioned in the PPT, we would now like to compute various quality metrics of the gene models to gain an idea of how successful our runs were. For starters, I've gone ahead and computed the BUSCO scores for the gene models as it takes a little bit of time to run. The outputs can be found in the `BUSCO` directory of our results folder. Lets view these results in the terminal now with this for loop. 

```bash
cd BUSCO
for i in *txt; do echo `basename $i .txt` | sed 's/short_summary.specific.insecta_odb10.//g'; cat $i; done
```
I've gone ahead a filled out our QC Table with these values:
<img width="741" alt="Screenshot 2024-11-13 at 11 01 01 PM" src="https://github.com/user-attachments/assets/74293a5c-3cd8-4c9b-86da-4f7e27a66b3b">

_Discuss implications of these results_
Which program worked better? Why is it important to compare BUSCO scores of gene models to BUSCO scores of the entire genome (we did this in Week 3)?

Now, lets assess the structure of the gene models. First, lets remind ourself what a `GTF` file looks like:

```bash
cd ../gene_models
head braker_f.gtf
```
And now count the number of genes and transcripts in each analysis using `awk`:

```bash
for i in *gtf; do echo $i; awk -v FS="\t" '{if ($3 == "gene") print}' $i | wc -l ; done

for i in *gtf; do echo $i; awk -v FS="\t" '{if ($3 == "transcript") print}' $i | wc -l ; done
```

These results are now filled out in the table:

<img width="748" alt="Screenshot 2024-11-13 at 11 10 36 PM" src="https://github.com/user-attachments/assets/003d12d5-9735-4a38-bd03-040039ed6401">

What do these results tell us about the performace of each program?

Now lets count introns and stop codons:

```bash
for i in *gtf; do echo $i; awk -v FS="\t" '{if ($3 == "intron") print}' $i | wc -l ; done

for i in *gtf; do echo $i; awk -v FS="\t" '{if ($3 == "stop_codon") print}' $i | wc -l ; done
```

And those results:

<img width="746" alt="Screenshot 2024-11-13 at 11 13 09 PM" src="https://github.com/user-attachments/assets/b6b2d9ab-fb17-4eac-b90c-fe5f6edd37f7">

Interesting... The BRAKER results produced more genes, more transcripts, but _fewer_ introns/gene relative to GALBA. What does this tell us about the potential strengths of each program?

To calculate the average and median length of each transcript model, lets mix it up and do some `python`. We will need to  navigate to these scripts, start an interactive node, load python, and run it.

```bash
cd /N/project/moczek_cisreg/programs/scripts

srun -p interactive --pty -c 1 --mem 2G -A r00262 --time=4:00:00 /bin/bash

module load python

for i in ../../ob_genome/results/gene_models/*codingseq; do echo $i; python ./len.py $i | cut -f2 | ./summary.sh; done
```
Beautiful. lets fill in those stats and discuss: 

<img width="739" alt="Screenshot 2024-11-13 at 11 18 21 PM" src="https://github.com/user-attachments/assets/70f7ad50-a541-4e5c-a5b1-950279b132b5">

# BLASTing and Synteny

To end today, submit some `batch` jobs that aligns our new _O. binodis_ gene models to the _O. taurus_ gene models. This will allows us to do a few things: 1) Assess how representative our new gene models are of a proteome (QC); 2) Provide putative IDs to each of of gene models; 3) prepare the neccessray results files for a synteny analysis (next week).

Lets navigate to our user directory, copy the necessary files, create a new batch script. 

```bash
cd ../u_[INITIALS]
cp ../results/gene_models/*aa .
cp ../scripts/sample.bash ./blast.bash
nano /blast.bash
```

Once in the text editor, lets edit to make the following script (substituting your email).:

```
#!/usr/bin/env bash
#SBATCH -J ob_blast
#SBATCH --mail-type=END
#SBATCH --mail-user=youremail@iu.edu
#SBATCH -c 8
#SBATCH --mem 8G
#SBATCH --time=24:00:00
#SBATCH -A r00262

module load blast

makeblastdb -in Otau.aa -dbtype prot -title ot -out ot

blastp -db ot -num_threads 8 -max_target_seqs 5 -evalue 1e-10 -query braker_f.aa -outfmt 6 > braker_f.blast
#runs in about 15 minutes

blastp -db ot -num_threads 8 -max_target_seqs 5 -evalue 1e-10 -query braker_m.aa -outfmt 6 > braker_m.blast
blastp -db ot -num_threads 8 -max_target_seqs 5 -evalue 1e-10 -query galba_f.aa -outfmt 6 > galba_f.blast
blastp -db ot -num_threads 8 -max_target_seqs 5 -evalue 1e-10 -query galba_m.aa -outfmt 6 > galba_m.blast


makeblastdb -in galba_m.aa -dbtype prot -title ob_galba_m -out ob_galba_m

blastp -db ob_galba_m -num_threads 8 -max_target_seqs 5 -evalue 1e-10 -query galba_f.aa -outfmt 6 > ob_mf.blast
```
The final two lines create a BLAST database for the male genome, and BLASTs the female gene models to it. This will ultimately allow us to get an idea of the structural differences between the male and female genome!

Submit!

```bash
sbatch blast.bash
```
