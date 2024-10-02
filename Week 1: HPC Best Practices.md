# Welcome to the world of High-Performance Computing

## Hello World

Let's login to the Quartz supercomputing cluster at IU using our shiny new accounts:

```bash
ssh [IU_USERNAME]@quartz.uits.iu.edu
```

If things are working correctly, you will be prompted to enter your password (no characters will show up when doing this) and then hit `Enter`. If your IU password is correct, you will then be asked validate via DUO Push. Press `1` and hit `Enter`. Validate DUO on your phone. Then, you're in.

First, lets assess where in the cluster we are by printing our working directory:

```bash
pwd
```
It appears we are in our Quartz hom directory. See also the `~` symbol on left. The home directory has some permanant storage, so it is good for keeping small files but not the best for running extensive analyses. 

You can see if you have any files in your home directory using the `ls` command as discussed last week:

```bash
ls -lh
```

Nows lets check out our data storage situation:

```bash
quota
```

This command wil tell you which storage allocations you have access to, how much storage exists, and how much has been used. For our genome project, we will be working within a shared working directory in a designated project allocation "moczek_cisreg". Note: Other storage options exist, including 800Gb of Slate personal space (lasts indefinitely) and near-infinite (effectively) scratch data (unmodified files deleted after 30 days). Lets go to our shared project directory now and preview what files are there: 

```bash
cd /N/project/moczek_cisreg/

ls -lh
```
You will see a number of files associated with various projects going on in the lab. I have created a subdirectory called "ob_genome" for us to carry out all of our analyses. Lets call that directory and preview its contents. 

```bash
cd ob_genome

ls -lh
```

You can see there are several directories: 1) a data directory, 2) a scripts directory, and 3) user directories for everyone participating -- this is where you should carry out your individual analyses. Also in the parent directory `../programs/` lies a set of pre-installed programs we can all use. More on this later.

<b>IMPORTANT: PLEASE NOTE- because this is a shared directory, please be very careful about deleting or moving files, especially in the parent directory "moczek_cisreg". It would be very unfun to lose files assoacted with other projects!</b>

## SLURM it up

Lets now turn to SLURM, the job scheduler for Quartz (and many HPC clusters accross the country, especially academic settings). To see whta jobs are running and in the queue, type:

```bash
squeue
```

There's a lot going on! You can specify a user `-u` to print only the jobs being run by a specific person. For example, lets look at what jobs I am running:

```bash
squeue -u phidavid
```
There are two primary ways to run a job on the cluster:

1) as a batch job (`sbatch`), in which a job is sent to the SLURM queue and is run when resources allow. This method is the most common, and typically preferred as it allows you to submit a job and leave the terminal without it cancelling or ending.
2) The second method is an `interactive` job, in which you are running the analyses live. This option is convenient for troubleshooting or running short tasks, as they are run instantaneously. However, it may take time to be allocated resources and has a major drawback in that the terminal and login session must be kept going.

For this project, we will primarily execute batch jobs. **IMPORTANT:** any sort of bioinformatic analyses beyond simple file manipulations, moving, copying, etc... should be conducted as a batch job or interactive job. By default, when you log into Quartz, you are logged into the "login node", in our case denoted by `hX` (see left side of terminal. Computational resources on the login node are limited and analyses should not be conducted here. 

Lets look into how to submit `batch` jobs now. First, lets navigate to our scripts folder where a couple of sample scripts are present. Lets preview the `sample.bash`, which you may use as a template for creating other bash scripts to submit your own jobs. 

```bash
cd ./scripts
cat sample.bash
```
Which should give you the following on your screen:

```bash
#!/usr/bin/env bash
#SBATCH -J job_name
#SBATCH --mail-type=END
#SBATCH --mail-user=youremail@iu.edu
#SBATCH -c x
#SBATCH --mem xG
#SBATCH --time=24:00:00
#SBATCH -A r00262

Your-commands-here...
...and here...
```

From top-to-bottom, these lines indicate: 
1) you are using `bash` scripting. Personally I've never had to change this, but must be included
2) name of your job, which will appear in the queue (recommended)
3) indicate whether to be emailed when the job finishes (optional)
4) your email
5) number of cores to use in the job (recommended)
6) amount of RAM to dedicate to job (recommended)
7) amount of time to dedicate to job (required, maximum of 4 days by default)
8) the RT project associated with the analysis, in our case never changes (required)

There are many, mnay other options that are possible (e.g. see https://slurm.schedmd.com/sbatch.html). However, these core parameters will be all you need a lot of the time. 

To submit a `batch` job, the command is simply:

```bash
sbatch your_job.bash
```

When it is submitted successfully, the job is assigned a job_ID (see `squeue`). To cancel the job:

```bash
scancel [job_id]
```

You can also use the sacct command to print useful information about a run (see https://slurm.schedmd.com/sacct.html for extensive options). For example, this command would print the maximum amount of RAM used in the job and the time elapsed during the run:

```bash
sacct -j [job_ID] --format="MaxRSS,Elapsed"
```
So, if I wanted to run a job in my own directory within our project, a sample command would be:

```bash
cp sample.bash ../u_pd/job1.bash
cd ../u_pd
nano job1.bash
```
_...edit your job via `nano`..._

Then submit:

```bash
sbatch job1.bash
```

For the vast majority of your analyses, the `sample.bash` template will fulfill our needs, as a single batch job is most effective for carrying our a single command or series of inter-dependent commands. However, there are cases in which you may have hundreds of independent jobs to run. While submitting hundreds of individual batch jobs is certainly possible, _arrays_ make this task much easier. The `sample_array.bash` script provides a template for submitting these kinds of jobs, wherein you provide a single text file of commands (in the case of the sample, `list_of_jobs.txt`) and ask SLURM to run X amount of jobs, Y jobs at a time. 

If you need to troubleshoot or want to run some analyses on the fly, `interactive` jobs are your best bet. To start an interactive session, a sample command would be:

```bash
srun -p interactive --time=hr:min:sec -A r00262 -c [num_cores] --mem [amount of RAM]G --pty /bin/bash
```

To end the interactive session, type:
```bash
exit
```

Lastly, Quartz comes pre-installed with a number of popular bioinformatic programs that makes running analyses very convenient (sometimes...). To view these programs available for loading, type:

```bash
module avail
```

To view which modules are currently loaded in your session, type:

```bash
module list
```

To load a module so that you may use that program, type:

```bash
module load name-of-program
#e.g.
module load bowtie
```

We will utilize modules extensively in the coming weeks but for now we just need to know they exist. 

## Our first job -- assemble genome contigs

For today, we will simply submit our job before concluding, let it run while away, and then analyze the results at our next meeting. At this stage, we have isolated tissue from a male and female _O. binodis_ individuals and sequenced the DNA using PacBio HiFi technology at UC Davis. These reads are located in the `data` directory of our shared `ob_genome` project directory. Lets go to this directory and make sure our sequencing reads are there.

```bash
cd /N/project/moczek_cisreg/ob_genome/data
ls -lh
```

We should see two gzipped files:

```
m84066_240906_041456_s1.OB-F4.15_15.fasta.gz
m84066_240906_041456_s1.OB-M4.14_14.fasta.gz
```
These are our coveted sequencing reads! The F4 indicates the female sample and the M4 are the male reads. Now lets navigate to our individual user directories and prepare to submit our genome assembly job by copying a template bash script into our directory.

```bash
cd ../u_pd
cp ../scripts/sample.bash ./hifiasm.bash
```

To perform the genome assembly, we will be using `hifiasm` (https://www.nature.com/articles/s41592-020-01056-5), an extremely popular method for de-novo genome assembly with PacBio HiFi reads. Next week I will explain PacBio sequencing and genome assembly, but for today, lets simply get the run going as it will take some time to complete. Now lets edit our `hifiasm.bash` script to fir our analysis using `nano`. 

First type:
```bash
nano hifiasm.bash
```

A new window will appear, and you can now edit the file. Edit the file so that it reads as one of the below. Lets have half of the group assemble the female genome and half assemble the male.

Female:
```
#!/usr/bin/env bash
#SBATCH -J hifiasm
#SBATCH --mail-type=END
#SBATCH --mail-user=your-email@iu.edu
#SBATCH -c 8
#SBATCH --mem 90G
#SBATCH --time=95:00:00
#SBATCH -A r00262

/N/slate/phidavid/programs/hifiasm/hifiasm -o obin_f -t8 ../data/m84066_240906_041456_s1.OB-F4.15_15.fasta

```

Male:
```
#!/usr/bin/env bash
#SBATCH -J hifiasm
#SBATCH --mail-type=END
#SBATCH --mail-user=your-email@iu.edu
#SBATCH -c 8
#SBATCH --mem 90G
#SBATCH --time=95:00:00
#SBATCH -A r00262

/N/slate/phidavid/programs/hifiasm/hifiasm -o obin_m -t8 ../data/m84066_240906_041456_s1.OB-M4.14_14.fasta
```

Once you have entered all of this information, press `Ctrl+O`, `Enter`, `Ctrl+X` which will ask the save, confirm the save, and exit your editing screen.

After you have finished your batch script, lets submit! 

```bash
sbatch hifiasm.bash
``` 

And finish by checking that the job is in the queue

```bash
squeue -u [iu-username]



