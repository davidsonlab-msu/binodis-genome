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








