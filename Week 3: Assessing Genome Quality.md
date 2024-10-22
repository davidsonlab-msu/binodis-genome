# Assessing Genome Quality
In the past two weeks, we have assembled contigs for genome assemblies representing a female and male _O. binodis_ individuals. Now, our task is to examine the results and assess the quality of our first pass at genome assembly.

Like last week, let's login to the Quartz supercomputing cluster at IU:

```bash
ssh [IU_USERNAME]@quartz.uits.iu.edu
```

If things are working correctly, you will be prompted to enter your password (no characters will show up when doing this) and then hit `Enter`. If your IU password is correct, you will then be asked validate via DUO Push. Press `1` and hit `Enter`. Validate DUO on your phone. Then, you're in.

Now let's navigate to our individual working directories within our shared project page and check out what files are there from our assembly attempt. 

```bash
cd /N/project/moczek_cisreg/ob_genome/u_[YOUR_INITIALS]
ls -lh
```

If your run finsihed properly, you should have a list of files that looks something like this:

```
hifiasm.bash
obin_f.bp.hap1.p_ctg.gfa
obin_f.bp.hap1.p_ctg.lowQ.bed
obin_f.bp.hap1.p_ctg.noseq.gfa
obin_f.bp.hap2.p_ctg.gfa
obin_f.bp.hap2.p_ctg.lowQ.bed
obin_f.bp.hap2.p_ctg.noseq.gfa
obin_f.bp.p_ctg.gfa
obin_f.bp.p_ctg.lowQ.bed
obin_f.bp.p_ctg.noseq.gfa
obin_f.bp.p_utg.gfa
obin_f.bp.p_utg.lowQ.bed
obin_f.bp.p_utg.noseq.gfa
obin_f.bp.r_utg.gfa
obin_f.bp.r_utg.lowQ.bed
obin_f.bp.r_utg.noseq.gfa
obin_f.ec.bin
obin_f.ovlp.reverse.bin
obin_f.ovlp.source.bin
slurm-4912540.out
```

As a reminder, `hifiasm.bash` is our bash script that we used to submit the genome assembly job. The output of the job are the remaining files, including messages printed during the run that are found in the `slurm-4912540.out` file.
Lets check out this log file with `cat `, adjusting the script to your own file name:

```bash
cat slurm-4912540.out
```
Now for the remaining files (from the hifiasm documentation: https://hifiasm.readthedocs.io/en/latest/interpreting-output.html#interpreting-output)

```
`prefix.r_utg.gfa`: haplotype-resolved raw unitig graph. This graph keeps all haplotype information.
`prefix.p_utg.gfa`: haplotype-resolved processed unitig graph without small bubbles. Small bubbles might be caused by somatic mutations or noise in data, which are not the real haplotype information. Hifiasm automatically pops such small bubbles based on coverage. The option --hom-cov affects the result. See homozygous coverage setting for more details. In addition, the option -p forcedly pops bubbles.
`prefix.p_ctg.gfa`: assembly graph of primary contigs. This graph includes a complete assembly with long stretches of phased blocks.
`prefix.*hap*.p_ctg.gfa`: phased contig graph. This graph keeps the phased contigs.
```

Great! It looks like our run was a success. Let's discuss what these different file types mean.

The file of most interest to us is the `obin_f.bp.p_ctg.gfa` file, as this file contains the primary or main contigs created by the program. For the sake of this project we will not pay attention to the different haplotypes for now.

However, we can see that the results are in a the format "gfa", which is convenient for quickly examining contig names, but not ideal for bioinformatic analyses. Let's check it out:

```bash
head obin_f.bp.p_ctg.gfa

Ctrl+X
```

A conventional format is fasta. Lets convert the GFA to FASTA using the following `awk` code ad preview its contents: 

```bash
awk '/^S/{print ">"$2;print $3}' ./obin_f.bp.p_ctg.gfa > ./obin_f.fasta

head obin_f.fasta
```

## Basic Genome Structure

Wonderful, we now have a genome assembly for a male and female individual. Lets check out the structure of the genome to get an idea of how well it performed. 

To do this, we first need to open an interactive job. This may take a few minutes depending on available resources. Here we are only asking for 1 core, 1Gb of RAM, and 1hr of time:

```bash
srun -p interactive --pty -c 1 --mem 1G --time=1:00:00 -A r00262 /bin/bash
```

Once that completes, we would like to run the `stats.sh` script of the `bbmap` program. It can be found in our shared `programs` directory of the `moczek_cisreg` parent folder. 
First lets just see what the script can do. We also need to load java to run the program. 

```bash
module load java

../../programs/bbmap/stats.sh
```

At a minimum we need to supply the fasta file to the `in=` parameter. Lets also ask bbmap to print a GC histogram, scaffold length histogram, and the n90 statistics. Lastly, we need to specify the maximum amount of RAM for the program to run with the `-Xmx` parameter.

```bash
../../programs/bbmap/stats.sh -Xmx1g in=obin_f.fasta gchist=gc.hist shist=length.hist n90=t
```

Output for a female genome may look like this:
```
A	C	G	T	N	IUPAC	Other	GC	GC_stdev
0.3288	0.1720	0.1726	0.3267	0.0000	0.0000	0.0000	0.3446	0.0906

Main genome scaffold total:         	195
Main genome contig total:           	195
Main genome scaffold sequence total:	956.223 MB
Main genome contig sequence total:  	956.223 MB  	0.000% gap
Main genome scaffold N/L50:         	6/63.576 MB
Main genome contig N/L50:           	6/63.576 MB
Main genome scaffold N/L90:         	19/4.089 MB
Main genome contig N/L90:           	19/4.089 MB
Max scaffold length:                	120.335 MB
Max contig length:                  	120.335 MB
Number of scaffolds > 50 KB:        	154
% main genome in scaffolds > 50 KB: 	99.85%


Minimum 	Number        	Number        	Total         	Total         	Scaffold
Scaffold	of            	of            	Scaffold      	Contig        	Contig  
Length  	Scaffolds     	Contigs       	Length        	Length        	Coverage
--------	--------------	--------------	--------------	--------------	--------
    All 	           195	           195	   956,223,404	   956,223,404	 100.00%
  10 KB 	           195	           195	   956,223,404	   956,223,404	 100.00%
  25 KB 	           191	           191	   956,145,277	   956,145,277	 100.00%
  50 KB 	           154	           154	   954,746,802	   954,746,802	 100.00%
 100 KB 	           105	           105	   951,502,570	   951,502,570	 100.00%
 250 KB 	            84	            84	   948,213,287	   948,213,287	 100.00%
 500 KB 	            70	            70	   943,173,062	   943,173,062	 100.00%
   1 MB 	            54	            54	   931,368,074	   931,368,074	 100.00%
 2.5 MB 	            29	            29	   891,520,168	   891,520,168	 100.00%
   5 MB 	            16	            16	   848,252,150	   848,252,150	 100.00%
  10 MB 	            14	            14	   831,031,856	   831,031,856	 100.00%
  25 MB 	            12	            12	   796,827,761	   796,827,761	 100.00%
  50 MB 	             8	             8	   621,680,865	   621,680,865	 100.00%
 100 MB 	             1	             1	   120,335,419	   120,335,419	 100.00%
```

This is a really nice assembly! Not quite chromosome resolved, but close. This is perhaps to be expected given the genome size of _O. binodis_ has turned out to be much larger than we initially anticipated (close to 1Gb versus 0.3Gb for the other species). 

Let's now download the Scaffold Length histogram to our laptop and visualize the results in Excel. First, open a new Terminal Window, and navigate to your Desktop (or wherever you would like to download the file to):

```bash
cd ~/Desktop
```

Now lets download the `length.hist` file from Quartz and preview its contents:

```bash
scp [YOUR IU USERNAME]@quartz.uits.iu.edu:/N/project/moczek_cisreg/ob_genome/u_[INITIALS]/length.hist .

head length.hist
```

In a later week we can do an introduction to R if that would be helpful to folks, but for the sake of simplicity, lets open the file in Excel and plot. 

<img width="781" alt="Screenshot 2024-10-15 at 12 56 45 PM" src="https://github.com/user-attachments/assets/def93716-741c-4308-9c23-fa16af2f1200">

As we can see, most of the genome assembly is contained in the larget 20 contigs! That is good!

For reference, here is what the old Otau2.0 genome scaffold length distribution looks like (not so good! -- note the x-axis scaffold number): 

<img width="612" alt="Screenshot 2024-10-15 at 1 06 48 PM" src="https://github.com/user-attachments/assets/b37a4a0b-2d41-4573-ae4c-794fdfbafcaf">

## BUSCO

To end today, lets examine our the results of a BUSCO analysis to assess gene content completeness. Because I was unsure if conda envs would be up and running yet, I went ahead and ran BUSCO on male and female genome assemblies but I will review the commands I used here:

```bash
cat /N/project/moczek_cisreg/obin/scripts/busco_obf_insect.bash
```
```
#!/usr/bin/env bash
#SBATCH -J busco
#SBATCH --mail-type=END
#SBATCH --mail-user=phidavid@iu.edu
#SBATCH -c 8 
#SBATCH --mem-per-cpu=2g
#SBATCH --time=24:00:00
#SBATCH -A r00262

module load anaconda
source activate /N/slate/phidavid/conda_envs/busco/

busco -i ./obin_female.fasta -m genome  -c 8 --augustus --out ./busco_female -f -l insecta_odb10
```
Which gave the output for the female assembly:
```
2024-09-12 11:09:41 INFO:	

	-----------------------------------------------
	|Results from dataset insecta_odb10           |
	-----------------------------------------------
	|C:99.2%[S:98.8%,D:0.4%],F:0.5%,M:0.3%,n:1367 |
	|1355	Complete BUSCOs (C)                   |
	|1350	Complete and single-copy BUSCOs (S)   |
	|5	Complete and duplicated BUSCOs (D)        |
	|7	Fragmented BUSCOs (F)                     |
	|5	Missing BUSCOs (M)                        |
	|1367	Total BUSCO groups searched           |
	-----------------------------------------------
```

Very good! Even exceeds completion of the other 3 dung beetle genomes. Furthermore, duplication is very low (0.4%) indicating a single haplotype is represented within the assembly, which is standard for reference genomes. 

The male results are:
```
	-----------------------------------------------
	|Results from dataset insecta_odb10            |
	-----------------------------------------------
	|C:99.2%[S:93.1%,D:6.1%],F:0.4%,M:0.4%,n:1367 |
	|1356	Complete BUSCOs (C)                   |
	|1273	Complete and single-copy BUSCOs (S)   |
	|83	Complete and duplicated BUSCOs (D)        |
	|6	Fragmented BUSCOs (F)                     |
	|5	Missing BUSCOs (M)                        |
	|1367	Total BUSCO groups searched           |
	-----------------------------------------------
```
Ooooo... Overall completion is good but there is some significant duplication (6.1%). Any ideas how to fix this?

Lets check out the hifiasm program again and see if there are any paramters we can tweak. Examine it yourself with this command and let me know which parameter may work to solve this issue. 

```bash
/N/project/moczek_cisreg/programs/hifiasm/hifiasm
```

Phil will show you what parameters were tweaked and place the new and improved male assembly in the `data` directory of our project folder. [On board demonstration of duplication repair]

## Conda Test

With our interactive node open, can we all please try:

```bash
module load miniconda
source activate /N/project/moczek_cisreg/programs/conda_envs/bbmap/
stats.sh
```














