# Final Week: Gene Descriptions and Synteny

Welcome to the final week of the binodis genome bootcamp. Lets begin today as usual by logging, navigating to the `BLAST` subdirectory of the `results` folder, and checking out its contents.

```bash
ssh [IU_USERNAME]@quartz.uits.iu.edu

cd /N/project/moczek_cisreg/ob_genome/results/BLAST
ls -lh
```

Which will output:

```
braker_f.blast
braker_m.blast
galba_f.blast
galba_m.blast
galba_mf.blast
obf_ot.blast
Otau_best_hits.txt
```

The `braker_f.blast`, `braker_m.blast`, `galba_f.blast`, and `braker_m.blast` are the outputs of our BLASTP hits from each _O binodis_ gene set to the newest _O.taurus_ gene models which we ran at the end of last week. Lets check out one of those now:

```bash
head -100 braker_f.blast
```
outputs:
```
g1.t1	jg17715.t1	82.362	1236	218	0	1	1236	1	1236	0.0	2169
g1.t1	jg9310.t1	82.453	1060	186	0	177	1236	1	1060	0.0	1868
g1.t1	jg14143.t1	61.439	1237	454	3	1	1236	1	1215	0.0	1648
g1.t1	jg18312.t1	79.911	677	136	0	1	677	1	677	0.0	1145
g1.t1	jg7526.t1	84.339	613	96	0	624	1236	1	613	0.0	1112
g2.t1	jg14142.t1	77.814	311	69	0	1	311	117	427	1.44e-173	486
g2.t1	jg4664.t1	70.096	311	93	0	1	311	1	311	2.75e-165	461
g2.t1	jg17716.t1	70.096	311	93	0	1	311	1	311	2.75e-165	461
g2.t1	jg18310.t1	70.096	311	93	0	1	311	1	311	4.92e-165	461
g2.t1	jg7525.t1	69.453	311	95	0	1	311	1	311	1.19e-163	457
...
```
Lets count how many gene models from binodis had a significant hit to the _O. taurus_ gene models, and compare that to the total number of gene models in that set. 
```bash
cut -f1 braker_f.blast | sort -u | wc -l

grep "^>" ../gene_models/braker_f.aa | wc -l
```

Interesting... 21423 of 23448 genes had significant hits to O. taurus. What may account the ~2000 genes with no hits?

The remaining files in the directory are `galba_mf.blast` and `obf_ot.blast`, which are all-to-all blast hits that will come into play with the synteny analsyes (more on this later). The last file `Otau_best_hits.txt` is a set of gene descriptions of the _O. taurus_ gene models, which we will use to describe the _O.binodis_ gene models based on significant blast hits. Lets preview this file:

```bash
Otau_ID	OT2_ID	OT2_pident	OT2_evalue	OT2_description	ecdy_ID	ecdy_pident	ecdy_evalue	ecdy_description
jg6653.t1	XP_022920069.1	100	2.74e-117	uncharacterized protein LOC111428662	UniRef90_N6UAK1	33.75	4.5e-23	SAM domain-containing protein (Fragment)
jg6009.t1	NA	NA	NA	NA	NA	NA	NA	NA
jg5736.t1	XP_022905354.1	98.235	6.61e-113	cuticle protein 7-like	UniRef90_A0A1I8PDP8	57.669	7.55e-45	Uncharacterized protein
jg5894.t1	XP_022909302.1	100	0	TFIIH basal transcription factor complex helicase XPD subunit	UniRef90_D6WI90	90.646	0	General transcription and DNA repair factor IIH helicase subunit XPD
jg6289.t1	XP_022902988.1	38.272	2.85e-08	paramyosin isoform X3	NA	NA	NA	NA
jg5450.t1	NA	NA	NA	NA	UniRef90_A0A5N4B0J9	32.192	5.94e-12	Uncharacterized protein
jg5706.t1	XP_022917520.1	100	0	tether containing UBX domain for GLUT4	UniRef90_A0A0T6B6A4	58.608	0	Ubiquitin
jg5449.t1	NA	NA	NA	NA	UniRef90_A0A5N4B0J9	30.556	1.34e-12	Uncharacterized protein
jg5650.t1	XP_022902067.1	100	0	BTB/POZ domain-containing protein kctd15, partial	UniRef90_A0A5N4A5I0	81.419	7.94e-176	BTB domain-containing protein
```

Neat. Lets open a separate `Terminal` window on you laptop, create directory on your desktop called ob_anno and mcscanx (for later). Then navigate into `ob_genome` and copy the gene models from Quartz into this directory, along with O. taurus best hits text file. Your commands may look slightly different diepending on your computer system. 

```
cd ~/Desktop
mkdir ob_anno mcscanx
cd ob_anno
scp -r phidavid@quartz.uits.iu.edu:/N/project/moczek_cisreg/ob_genome/results/BLAST/ .
```

Lets open R Studio, and follow along with the proceedng code to annotate our genes from the `braker` annotations. 

```R
install.packages("tidyverse")
library(tidyverse)

#to load in the files, adjust your path accordingly or simply use the Import Dataset function within R
braker_f <- 
  read.delim("~/Desktop/Research/binodis_genome/ob_anno/BLAST/braker_f.blast",
             header=FALSE)
braker_m <- 
  read.delim("~/Desktop/Research/binodis_genome/ob_anno/BLAST/braker_m.blast", 
             header=FALSE)
Otau_best_hits <- 
  read.delim("~/Desktop/Research/binodis_genome/ob_anno/BLAST/Otau_best_hits.txt")

colnames(braker_f) <- c("obf_ID", "Otau_ID", "pident", "aln_len", "num_mismatch", 
                        "num_gaps", "ob_start", "ob_end", "ot_start", "ot_end",
                        "eval", "bit")

colnames(braker_m) <- c("obm_ID", "Otau_ID", "pident", "aln_len", "num_mismatch", 
                        "num_gaps", "ob_start", "ob_end", "ot_start", "ot_end",
                        "eval", "bit")
#female models
braker_f_anno <- left_join(braker_f,Otau_best_hits, by = "Otau_ID")
braker_f_anno_best <- braker_f_anno %>% 
  group_by(obf_ID) %>% 
  top_n(-1, eval)

braker_f_anno_best <- braker_f_anno %>% 
  group_by(obf_ID) %>% 
  top_n(-1, eval) %>% 
  slice_max(pident)

#male_models
braker_m_anno <- left_join(braker_m,Otau_best_hits, by = "Otau_ID")

braker_m_anno_best <- braker_m_anno %>% 
  group_by(obm_ID) %>% 
  top_n(-1, eval) %>% 
  slice_max(pident)

#save tables
write.table(braker_f_anno_best, file = "~/Desktop/ob_anno/braker_f_anno_best.txt", 
            sep = "\t", row.names = FALSE, quote = FALSE)

write.table(braker_m_anno_best, file = "~/Desktop/ob_anno/braker_m_anno_best.txt", 
            sep = "\t", row.names = FALSE, quote = FALSE)
```

Voila! We now have annotated gene models. 

Lets hop back into our Quartz session and prepare to run the whole genome synteny analyses. To run the synteny analsyes, we will use a program called MCScanX, which requires two files: 
1) All-to-all BLAST results from your species of interest (I did this ahead of time to save time, but this is simply a BLASTP search like we did last week, except using a combined amino acid file of all species, e.g. `cat galba_f.aa Otau.aa > obf_ot.blast`.
2) A simplified GFF that includes the chromosome, gene name, start, and end of each gene for each species of interest. We will create these below.

First, navigate to your user directory and copy all-to-all BLAST results 

```bash
cd /N/project/moczek_cisreg/ob_genome/u_[INITIALS]
cp ../results/BLAST/obf_ot.blast .
cp ../results/BLAST/galba_mf.blast .
```

The `obf_ot.blast` file is the all-to-all results of the `galba_f.aa` protein models with the `Otau.aa` protein models. AKA we are finding synteny between O binodis female genome (GALBA models) and the Otaurus genome. The `galba_mf.blast` file is the all-to-all results between the male and female protein models. AKA finding synteny between male and female genomes using the GALBA models. You will notice that I added an "m" to the male gene models to distinguish from the female models (neccessary for the synteny analysis).

```bash
head galba_mf.blast
```

Lets also copy over GTF files for the male and female binodis GALBA annotations and the Otaurus models to convert to the simplified GFFs for MCSCanX. 

```bash
cp ../results/gene_models/galba_f.gtf .
cp ../results/gene_models/galba_m.gtf .
cp ../results/gene_models/Otau3.gtf .
```

And now lets convert them to the format mentioned in Step 2 above: chr (small initials), gene name, start, end:

```bash
#galba_female
awk -v OFS="\t" -v FS="\t" '{if ($3 == "transcript") print $1,$4,$5,$9}' galba_f.gtf | sed 's/contig_/ob/g' | awk -v OFS="\t" -v FS="\t" '{print $1, $4, $2, $3}' > galba_f.gff

#galba_male
awk -v OFS="\t" -v FS="\t" '{if ($3 == "transcript") print $1,$4,$5,$9}' galba_m.gtf | sed 's/contig/mb/g' | awk -v OFS="\t" -v FS="\t" '{print $1, $4, $2, $3}' | sed 's/\tg/\tmg/g' > galba_m.gff

#Otaurus
awk -v OFS="\t" -v FS="\t" '{if ($3 == "transcript") print $1,$4,$5,$9}' Otau3.gtf | sed 's/transcript_id "//g' | sed 's/"\;.*//g' | sed 's/Schr/ot/g' | sed 's/chr/ot/g' | sed 's/ScKx7SY_/ot/g' | awk -v OFS="\t" -v FS="\t" '{print $1,$4,$2,$3}' > ot3.gff
```

and preview them to double check:

```bash
head *gff
```

And now combine to create the appropriate GFFs for each comparison.

```bash
cat galba_m.gff galba_f.gff > galba_mf.gff
cat galba_f.gff ot3.gff > obf_ot.gff
```

We now have both the `GFF` and `blast` files for each comparison we are interested in: "ob_f vs ot" and "ob_f vs ob_m". Finally, we are ready to run MCScanX for each comparison. 

```bash
../../programs/MCScanX/MCScanX ./obf_ot
../../programs/MCScanX/MCScanX ./galba_mf
```

Success! Lets view the files:

```bash
ls -lh obf_ot*
ls -lh galba_mf*
```

Lets now return to our **second terminal window on our laptop** and download these files to our mcscanx folder.

## LAPTOP TERMINAL
```bash
noglob scp -r phidavid@quartz.uits.iu.edu:/N/project/moczek_cisreg/ob_genome/u_[INITIALS]/galba_mf.* ./mcscanx
noglob scp -r phidavid@quartz.uits.iu.edu:/N/project/moczek_cisreg/ob_genome/u_pd/obf_ot.* ./mcscanx
```

Almost there. Lets go to https://synvisio.github.io/ and visualize our results together!













