```{=html}
<!--
GA5 README template.
- Add your answers to each question's section below.
- For each question where we want you to add your commands,
  put the command(s) you ran in the terminal in the corresponding ```bash code block.
- Where we want to see a command's output, there is a plain ``` code block
  right below the command block: paste the output there.
- The code to run each step of the workflow goes in `protocol.md`, not here:
  you don't need to repeat it in this file.
- You don't need to paste the contents of your scripts into this file:
  we will look at the scripts in your `scripts` dir directly
  (so make sure you commit them!).
- Delete instructional comments like this one as you go.
-->
```

# GA5 answers - Chandrima Das

------------------------------------------------------------------------

## Part A: Your project dir, Git repo, and protocol

### 1: Fill in the top of the protocol

<!-- No answer needed here -- we check `protocol.md` directly. -->

### 2: Markdown formatting

<!-- No answer needed here -- we check the formatting of both Markdown files. -->

### 3: Create subdirs, and copy the scripts

``` bash
# Your mkdir command:
mkdir -p scripts data/ref results

cp -v /fs/ess/PAS3493/share/GA5/scripts/* scripts/
```

### 4: Initialize a Git repository, and commit your first files

<!-- No answer needed here -- we check the repo directly. -->

### 5: Download the reference transcriptome

<!-- Put the download command in `protocol.md`, not here. -->

Code to count the number of transcripts:

``` bash
# Your command:

#count the number of transcript
zcat data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz | grep -c "^>"
```

The output of the command was:

```         
--2026-11-07 13:44:04--  https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/016/801/865/GCF_016801865.2_TS_CPP_V2/GCF_016801865.2_TS_CPP_V2_rna.fna.gz
Resolving ftp.ncbi.nlm.nih.gov (ftp.ncbi.nlm.nih.gov)... 130.14.250.11, 130.14.250.12, 2607:f220:41e:250::10, ...
Connecting to ftp.ncbi.nlm.nih.gov (ftp.ncbi.nlm.nih.gov)|130.14.250.11|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 19093150 (18M) [application/x-gzip]
Saving to: ‘data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz’

GCF_016801865.2_TS_CPP_V2_rna.fna.gz     100%[================================================================================>]  18.21M  96.4MB/s    in 0.2s    

2026-11-07 13:44:05 (96.4 MB/s) - ‘data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz’ saved [19093150/19093150]
```

*Answer*: The number of transcripts in the file:

28624

------------------------------------------------------------------------

## Part B: Read trimming

### 6: Run TrimGalore

<!-- Put the code to run TrimGalore in `protocol.md`, not here. -->

*Answer*: Whether all batch jobs succeeded, and how you checked that (and any problems you encountered, and how you solved them):

Yes, all 22 TrimGalore batch jobs succeeded. I checked the Slurm log files with `grep -c "Successfully finished script trimgalore.sh" slurm-trimgalore-*.out`, and every log returned a count of 1. I also confirmed that there were 22 trimmed R1 FASTQ files, 22 trimmed R2 FASTQ files, and 44 FastQC HTML reports. I did not encounter any failed jobs. After confirming that the jobs had completed successfully, I moved the 22 Slurm log files into `results/trimgalore/logs/`.

------------------------------------------------------------------------

## Part C: Transcript quantification with Salmon

### 7: Create the Salmon index

Code to check the size of the index dir:

``` bash
# Your command:
du -sh results/salmon_index
```

The output of the command was:

```         
1.1G    results/salmon_index
```

*Answer*: How long it took to create the index, and how you found out:

The Salmon index took 21 seconds to create. I determined this from the start and finish timestamps in the Slurm log file: the script started at 3:08:41 PM and successfully finished at 3:09:02 PM. The Salmon index directory was 1.1 GB.

The first attempt failed because the shell variables `ref_transcripts` and `outdir` were not set in my new terminal session. I removed the failed Slurm log, redefined the variables, verified the reference transcriptome path, and resubmitted the job successfully.

*Answer*: The size of the index dir: 1.1 GB.

### 8: Run Salmon for all samples

<!-- No answer needed here -- we check `protocol.md` directly. -->

### 9: The transcripts with the most reads

*Answer*: What the `NumReads` column contains:

`NumReads` is Salmon's estimated number of reads originating from each transcript. It is an estimate because reads that map to multiple possible transcripts are assigned probabilistically based on the mapping information and estimated transcript abundances.

``` bash
# Your command:
tail -n +2 results/salmon_quant/S63/quant.sf | sort -k5,5nr | head -n 3
```

The output of the command was:

```         
XM_039591534.2  6566    6315.086        14518.656821    24004.889
XM_039573735.1  6445    6194.086        5342.073324     8663.254
XM_039573733.1  6591    6340.086        2876.480244     4774.749
```

### 10: The gene of the top transcript

``` bash
# Your command:
zcat "$ref_transcripts" | grep "^>XM_039591534.2"
```

The output of the command was:

```         
>XM_039591534.2 PREDICTED: Culex pipiens pallens vitellogenin-A1-like (LOC120426760), mRNA
```

*Answer*: The gene that the top transcript belongs to:

The most abundant transcript, `XM_039591534.2`, belongs to the **vitellogenin-A1-like gene (LOC120426760)**.

### 11: Mapping rates

``` bash
# Your command:
grep -i "mapping rate" results/salmon_quant/*/logs/salmon_quant.log
```

The output of the command was:

```         
results/salmon_quant/S65/logs/salmon_quant.log:mapping rate: 79.1055%
results/salmon_quant/S66/logs/salmon_quant.log:mapping rate: 81.9350%
results/salmon_quant/S67/logs/salmon_quant.log:mapping rate: 83.0459%
results/salmon_quant/S68/logs/salmon_quant.log:mapping rate: 78.8070%
results/salmon_quant/S69/logs/salmon_quant.log:mapping rate: 82.1824%
results/salmon_quant/S70/logs/salmon_quant.log:mapping rate: 82.3060%
results/salmon_quant/S71/logs/salmon_quant.log:mapping rate: 79.9047%
results/salmon_quant/S74/logs/salmon_quant.log:mapping rate: 78.2753%
results/salmon_quant/S75/logs/salmon_quant.log:mapping rate: 80.3915%
results/salmon_quant/S76/logs/salmon_quant.log:mapping rate: 82.3396%
results/salmon_quant/S77/logs/salmon_quant.log:mapping rate: 82.3281%
results/salmon_quant/S78/logs/salmon_quant.log:mapping rate: 84.2490%
results/salmon_quant/S79/logs/salmon_quant.log:mapping rate: 80.4061%
results/salmon_quant/S80/logs/salmon_quant.log:mapping rate: 82.1721%
results/salmon_quant/S81/logs/salmon_quant.log:mapping rate: 83.6795%
results/salmon_quant/S82/logs/salmon_quant.log:mapping rate: 78.7291%
results/salmon_quant/S83/logs/salmon_quant.log:mapping rate: 76.1504%
results/salmon_quant/S84/logs/salmon_quant.log:mapping rate: 83.2207%
results/salmon_quant/S85/logs/salmon_quant.log:mapping rate: 76.9540%
results/salmon_quant/S86/logs/salmon_quant.log:mapping rate: 83.0532%
```

*Answer*: The sample with the lowest mapping rate:

Sample S83 had the lowest Salmon mapping rate, at 76.1504%.

------------------------------------------------------------------------

## Part D: Summarizing all steps with MultiQC

### 12: Update your MultiQC script

<!-- No need to paste the script: we'll look at scripts/multiqc.sh and its Git history directly. -->

### 13: Run MultiQC

*Answer*: For each program, the number of reports that MultiQC found, and why it found this number:

### 14: The General Statistics table

*Answer*: The sample with the lowest Salmon mapping rate, and whether this matches your answer to question 11:

### 15: Adapter lengths

*Answer*: The most common adapter lengths, whether this surprised you, and why you think these lengths are the most common:

### 16: Ask Claude about the MultiQC report

*Answer*: What you asked, and what you learned from Claude's answer (in your own words):

------------------------------------------------------------------------

## Part E: Improving the workflow with genAI

### 17: Claude's review of your protocol and scripts

*Answer*: At least three of Claude's suggestions, and whether you agree or disagree with each one, and why:

### 18: Address a suggestion

*Answer*: Which suggestion(s) you addressed, and what you changed:

------------------------------------------------------------------------

## Part F: Publish your repo on GitHub

### 19: Create a GitHub repository and push

<!-- No answer needed here -- we check your GitHub repo directly. -->

### 20: Get a (test) DOI with Zenodo

*Answer*: Your Zenodo Sandbox DOI: