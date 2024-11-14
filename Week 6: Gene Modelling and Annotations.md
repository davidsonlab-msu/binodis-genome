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
<img width="737" alt="Screenshot 2024-11-13 at 11 09 34 PM" src="https://github.com/user-attachments/assets/2e66c9fd-3f59-4e5c-b69b-f87e91888dc0">










