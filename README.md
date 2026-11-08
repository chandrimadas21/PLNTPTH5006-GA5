# GA5 answers - Chandrima Das

[ DOI: 10.5072/zenodo.637421](https://handle.test.datacite.org/10.5072/zenodo.637421)

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
28624
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
results/salmon_quant/S63/logs/salmon_quant.log:mapping rate: 82.4560%
results/salmon_quant/S64/logs/salmon_quant.log:mapping rate: 81.9152%
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

MultiQC found:

-   Salmon: 22 meta reports and 22 library format counts reports. There were 22 samples, and Salmon produced one quantification output and associated metadata/library-format information for each sample.

-   Trim Galore: 44 reports. There were 22 paired-end samples, with one R1 and one R2 file per sample, so TrimGalore produced 44 read-level reports.

-   FastQC: 44 reports. FastQC was run by TrimGalore on both the R1 and R2 trimmed reads for each of the 22 samples, giving 44 FastQC reports in total.

### 14: The General Statistics table

*Answer*: The sample with the lowest Salmon mapping rate, and whether this matches your answer to question 11:

Sample S83 had the lowest Salmon mapping rate in the MultiQC General Statistics table, at approximately 76.2%. This matches my answer to question 11, where the Salmon log showed a mapping rate of 76.1504% for S83.

### 15: Adapter lengths

*Answer*: The most common adapter lengths, whether this surprised you, and why you think these lengths are the most common:

The most common adapter lengths were very short, approximately 1-3 bp, with 1 bp being the most common. This did not surprise me after reading the MultiQC explanation. Trim Galore uses a default stringency of 1, so even a single-base overlap with the adapter can be detected and trimmed. Many of these 1-bp matches can occur randomly rather than representing substantial adapter contamination. Short 2-3 bp overlaps were also common, while longer adapter matches occurred much less frequently.

### 16: Ask Claude about the MultiQC report

*Answer*: What you asked, and what you learned from Claude's answer (in your own words):

I asked Claude whether the high number of very short adapter matches in the MultiQC report represented a real library-quality problem, and how I could distinguish random short matches from genuine adapter contamination. I then asked it to examine the actual Trim Galore results behind my MultiQC report.

I learned that most of the apparent adapter matches were only 1-3 bp long. Because Trim Galore was run with a minimum adapter overlap of 1 bp, many of these very short matches can occur randomly and do not necessarily represent true adapter readthrough. On the other hand, longer adapter matches, where random matches are much less likely, were very rare. So, the high percentage of reads reported as having adapters in some samples does not by itself indicate poor library quality. The length distribution of the adapter matches is important for deciding whether the signal represents real contamination.

------------------------------------------------------------------------

## Part E: Improving the workflow with genAI

### 17: Claude's review of your protocol and scripts

*Answer*: At least three of Claude's suggestions, and whether you agree or disagree with each one, and why:

Claude reviewed my `protocol.md` and the scripts in my `scripts/` directory and suggested several ways to improve reproducibility.

**Suggestion 1: Better document the location of the input FASTQ files.**\
Claude pointed out that the protocol used `fastq_dir=../garrigos/fastq` without explaining that this path refers to the shared course dataset. I agree with this suggestion because another user would not necessarily know where these files came from or what to change if they were running the workflow somewhere else. The input data location is essential information for reproducing the analysis.

**Suggestion 2: Explain that the Slurm account is specific to this OSC project.**\
Claude noted that all of the Slurm scripts use `#SBATCH --account=PAS3493`. I agree that this should be documented. The account is correct for this assignment, but someone using a different OSC allocation would need to change it before submitting the jobs. Otherwise, the scripts might fail even though the analysis commands themselves are correct.

**Suggestion 3: Make it clearer how to confirm that one Slurm step is finished before starting the next.**\
Claude suggested documenting a way to monitor jobs, such as `squeue -u $USER`, and checking the logs and expected output files before continuing. I agree with this suggestion because later steps depend on the outputs of earlier steps. For example, Salmon quantification should not start until the Trim Galore jobs have completed successfully.

**Suggestion 4: Clean up the protocol header and repository information.**\
Claude noticed that the protocol header contained accidentally merged text and that the GitHub repository field had not yet been filled in. I agree that the header needed to be corrected because it should clearly describe the computing environment. I also agree that the final GitHub repository URL should be added once the repository is created in Part F.

### 18: Address a suggestion

*Answer*: Which suggestion(s) you addressed, and what you changed:

I addressed several of Claude's reproducibility suggestions in my protocol. I added an explanation that `../garrigos/fastq` is the input location used for this course and that `fastq_dir` should be changed when the workflow is run in a different environment.

I also documented that the Slurm scripts use the OSC project account `PAS3493` and that this value needs to be changed if a different OSC allocation is used. In addition, I added instructions to monitor submitted jobs with `squeue -u $USER`, check the Slurm logs for successful completion, and confirm that the expected output files were produced before proceeding to the next step.

Finally, I corrected the corrupted sentence in the protocol header so that it clearly states that the workflow was run on the Pitzer cluster at the Ohio Supercomputer Center. These changes were committed to Git to improve the reproducibility of the workflow.

------------------------------------------------------------------------

## Part F: Publish your repo on GitHub

### 19: Create a GitHub repository and push

<!-- No answer needed here -- we check your GitHub repo directly. -->

### 20: Get a (test) DOI with Zenodo

*Answer*: Your Zenodo Sandbox DOI: [ DOI: 10.5072/zenodo.637421](https://handle.test.datacite.org/10.5072/zenodo.637421)