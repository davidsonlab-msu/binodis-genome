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

