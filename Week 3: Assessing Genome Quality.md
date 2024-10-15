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


