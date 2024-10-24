# Download the assemblies to your local computer
Now that we have goo starting assemblies for both male and female _O. binodis_ individual, our next task is to cleanup the assembly so we can annotate it. Today we will do 3 things:

1) Remove putative microbial contaminants from our genome assemblies
2) Rename our final set of contigs
3) Start the repeat annotation to run over the next few days

As usual let's login to the Quartz supercomputing cluster at IU:

```bash
ssh [IU_USERNAME]@quartz.uits.iu.edu
```
Now let's navigate to the data folder of our shared project directory and check out what is inside

```bash
cd /N/project/moczek_cisreg/ob_genome/data
ls -lh
```

We should see our original PacBio HiFi reads along with version 1 of both the male and female genome assembly:

```
m84066_240906_041456_s1.OB-F4.15_15.fasta.gz
m84066_240906_041456_s1.OB-M4.14_14.fasta.gz
obf_v1.fasta
obm_v1.fasta
```

Recall from last week that the female genome aseembly was great using Hifiasm's default paramters (high contiguity, low haplotype duplication), yet the male assembly has slightly elevated haplotype duplication issues (~6%). As a result, we ended up lowering the '-s' parameter from 0.55 to 0.45 to have a more stringent filtering of contigs potentially representing the same haplotype. This change lowered the duplication rate to < 1% which maintaining high contiguity. These final assemblies are now stored in the `data` directory with the names `*v1.fasta`. 

To remove contaminants, we will use a Galaxy Web tool to identify foreign (non-beetle) DNA and separate those contigs from the rest of the assembly. Thus, we will need to download these assemblies to our local computer and upload them via a web browser. Before switching to a new terminal window, lets print our working directory to aid in the `scp` command later:

```bash
pwd
```

Which will output:

```
/N/project/moczek_cisreg/ob_genome/data
```

Lets open a _separate_ terminal window on our laptops, navigate to our Desktop (or wherever you would like the files to be downloaded to, and download the genomes our local computer using this command:

```bash
scp [IU_USERNAME]@quartz.uits.iu.edu:/N/project/moczek_cisreg/ob_genome/data/obm_v1.fasta .
scp phidavid@quartz.uits.iu.edu:/N/project/moczek_cisreg/ob_genome/data/obf_v1.fasta .
```











