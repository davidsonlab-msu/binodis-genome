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

The remaining files in the directory are `galba_mf.blast` and 











