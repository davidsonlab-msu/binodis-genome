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

This is a really nice assembly! Not quite chromosome resolved, but close. This is perhaps to be expected given the genome size of _O. binodis_ has turned out to be much larger than we initially anticipated (close to 1Gb versus 0.3Gb for the other species). 














