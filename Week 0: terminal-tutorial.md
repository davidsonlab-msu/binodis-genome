# terminal-tutorial
Introduction to navigating a computer's system files, presented to the Moczek Lab at Indiana University. This tutorial will have two parts: 

1) <b>Phil on screen</b>: Review of common terminal commands, referencing this nice guide: https://gist.github.com/bradtraversy/cc180de0edee05075a6139e42d5f28ce#file-terminal-commands-md

2) <b>Together</b>: Working through a practice set of exercises, using a test set of genomic data from the Onthophagus sagittarius genome project (below).

## Exploring the <i>O. sagittarius</i> data

Before we begin, please make sure you have saved the folder conatining Osag data (from OneDrive) to your "Desktop" and rename the folder "osag1". 

Now, lets navigate to this folder via the terminal, using the "call directory" command:

```bash
cd ~/Desktop/osag1
```

And now let's check out what files are inside: 

```bash
ls -lh 
```
We can see that our "osag1" directory has 2 files (the genome sequence, and a masked version of the genome sequence) and three directories containing information about how the genome was annotated (genes, repeats, and annotations). 

We can also print what is inside other directories, without having to go into the directory first:

```bash
ls -lh ./genes
```

This directory contains three files.
1) A `gtf` file that describes the genomic locations of different gene features
2) A `fasta` file describing the nueclotide form of Osag genes
3) A `fasta` file describing the protein form of Osag genes

Lets see what these files have to offer:

```bash
cd genes
```

First, lets print the file contents of the `gtf` file using the `cat` command:

```bash
cat Osag1.gtf
```
WAIT!! This file is quite large (22Mb), so you like had thousands of lines of text fly across your screen, which is a bit much. Lets approach this a bit more elegantly with the `head` command, which will print the first 10 lines of the file (`tail` in contrast, prints the final 10 lines):

```bash
head Osag1.gtf
```
Lets do the same for the protein sequences, but this time print the first 100 lines:

```bash
head -100 Osag_proteins.fasta
```

Lets retrieve some additional information about this file, such as the number of lines and characters:

```bash
wc -l Osag_proteins.fasta
wc -c Osag_proteins.fasta
```

What if we want to just print the names of the genes? We can use `grep` to do this easily. Here the double quotes `""` tell grep a pattern to look for, the `^` symbol denotes the beginning of the line, and the `>` character is simply a character to ask grep to look for. In other works, we are asking `grep` to pull only lines that begin with a `>`.

```bash
grep "^>" Osag_proteins.fasta
```

This printed out many lines. How many exactly? We can "pipe" the results of one command to a second command using the `|` symbol. This is extremely useful when there are many steps in manipulating or analyzing data. Lets pipe the grepm results to the `wc` command above to count how many genes (in reality, transcripts, as isoforms are included here) have been identified in this genome!

```bash
grep "^>" Osag_proteins.fasta | wc -l
```

At the moment, the genes are labelled non-descriptly as "jgXXXXX.tX". Perhaps we want to change these labels to make them look a bit more distinct. Lets substitute "os_" for "jg" and create a new protein fasta file with these labels.

To do this, lets use `sed`, a powerful text manipulation programming language. A typical sed command for text substitution follows `sed 's/X/Y/g'`, where X is the original text and Y is the text you want to substitute in. So for our purposes, lets try to make the substitution and pipe the results to a `grep` and `head` command to preview our change. 

```bash
sed 's/jg/os_/g' Osag_proteins.fasta | grep "^>" | head
```

If we are happy with the change we are trying to make, we can save the updated file to a new one using the `>` character:

```bash
sed 's/jg/os_/g' Osag_proteins.fasta > Osag_proteins_clean.fasta
```

Lets check that the file was created and preview its contents to make sure the correct change worked: 

```bash
ls -lh
head Osag_proteins_clean.fasta
```

Great, so there are 20,765 genes identified in the genome. Lets see if this infomation matches what is in our GTF file. First, lets preview what the GTF file looks like again:

```bash
head -20 Osag1.gtf
```
We can see that a GTF describes the structure of a gene model within a genome. For example, the scaffold location is given, as well as the start and end coordinates of gene features like exons, start, and stop codons. 

If we want to count the number of transcripts in the file, we could try an (incorrect) approach similar to that above. For example:

```bash
grep "jg*" Osag1.gtf | wc -l
```
However, this command is inappropriate given the pattern "jg" occurs in multiple lines. Instead, we want to only count the lines in which column 3 has the value "transcript". For this task, lets utilize `awk`, another powerful text manipulating programming language.

First, lets print the 3rd column of the file to get an idea of what sorts of values are there:

```bash
awk -v FS="\t" '{print $3}' Osag1.gtf | head -20
```

Looks good. Now lets filter for lines that only have the value of "transcript". Note: many other conditions can be applied within awk (e.g. >, <, mathmatical formulas). 

```bash
awk -v FS="\t" '{if ($3 == "transcript")  print }' Osag1.gtf | head -20
```

Seems like its working! Lets count and see if the value matches that from the protein file. 

```bash
awk -v FS="\t" '{if ($3 == "transcript")  print }' Osag1.gtf | wc -l
```
Indeed it does. How about the number of genes? 

```bash
awk -v FS="\t" '{if ($3 == "gene")  print }' Osag1.gtf | wc -l
```

Why is this different than the number transcripts?

To finish today, lets try to now find out more about the function of these gene models. For this, lets dive into the `annotations` folder. But first, for the sake of the exercise, lets make a copy of our `*nucleotides.fasta` file and place it into our annotations folder. 

```bash
cp Osag_proteins.fasta ../annotations/
```

Remember, the `..` indicates to go "back" one directory. Lets check and make sure the file was copied successfully and then move our current directory to the annotations folder.

```bash
ls -lh ../annotations
cd ../annotations
```

Let's check out the `Osag_best_hits.txt` file, which tells us the best annotation "hit" for each gene model from several databases. 

```bash
head Osag_best_hits.txt
```

Now, lets do a *rough* search for genes potentiall related to insulin signalling using `grep`:

```bash
grep "insulin" Osag_best_hits.txt
```

It is a bit messy, so lets just print the 1st and 5th columns by piping the `grep` result into `awk`:

```bash
grep "insulin" Osag_best_hits.txt | awk -v FS="\t" -v OFS="\t" '{print $1,$5}'
```

To end, lets look at our work with the `history` command and the try a few exercises. 

```bash
history -100
```

## Exercises

1) Change the prefix name of the genome scaffolds to "chromosome" and save to a new file

*This one may require some sluething with the `man` command*

2) Calculate the size of the Osag genome (remember to not count the names of the scaffolds/chromosomes in your calculation! e.g. >Scaffold...)

3) Write a *single* command that prints the location (Scaffold, Start, and End) of the twisted gastrulation gene. *hint: check out grep -f and /dev/stdin*

















