## Welcome to the world of High-Performance Computing

# Hello World

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

This command wil tell you which storage allocations you have access to, how much storage exists, and how much has been used. For our genome project, we will be working within a shared working directory in a designated project allocation "moczek_cisreg". Lets go there now and preview what files are there: 

```bash
cd /N/project/moczek_cisreg/

ls -lh
```
You will see a number of files associated with various projects going on in the lab. I have created a subdirectory called "ob_genome" for us to carry out all of our analyses. Lets call that directory and preview its contents. 

```bash
cd ob_genome

ls -lh
```
# SLURM it up
<b>IMPORTANT: PLEASE NOTE- because this is a shared directory, please be very careful about deleting or moving files, especially in the parent directory "moczek_cisreg". It would be very unfun to lose files assoacted with other projects!</b>


