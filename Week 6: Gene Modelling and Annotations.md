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

