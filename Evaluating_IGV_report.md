# Evaluating various automated IGV snapshot/report tools

Try 3 different tools:
- IGV batch script (https://github.com/igvteam/igv/wiki/Batch-commands/32fbbbf3c7b7c79a5e531f03346c4624716422b0)
- IGVer (https://github.com/shahcompbio/igver)
- IGV-report (https://github.com/igvteam/igv-reports)


## **1. IGV batch script**

This is done by creating a text file detailing the samples, track, region, etc to plot on IGV.

example of a script:
```
new  
genome /home/cenliau/00_nesi_projects/brins03581/Ref_Cen/SB2.1_diploid_plusorg.fasta 
snapshotDirectory /home/cenliau/00_nesi_projects/brins03581_nobackup/Cen/IGV
load /home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB037_clone_2025A/filteredModBam/C01208.1.1.1_sup_5mC_5hmC_6mA_SQK.nosecondary.sorted.bam
goto chr01_hap1:2357085-2357661
sort STRAND
collapse
colorBy BISULFITE
snapshot

load /home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB045_controls/filteredModBam/SB1031_PB045.1.1.1_sup_5mC_5hmC_6mA_SQK.noSecondary.sorted.bam
goto chr01_hap1:2357085-2357661
sort STRAND
collapse
colorBy BISULFITE
snapshot

load /home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB045_controls/filteredModBam/C06479.1.1.1_sup_5mC_5hmC_6mA_SQK.noSecondary.sorted.bam
goto chr01_hap1:2357085-2357661
sort STRAND
collapse
colorBy BISULFITE
snapshot
```

and the result:
<img width="1678" height="1178" alt="chr01_hap1_2,357,085_2,357,661" src="https://github.com/user-attachments/assets/39965ed5-8911-4f86-8097-f05a2af18591" />

I ran this on IGV on virtual desktop:
--> Tools --> Run Batch Script

There must be a way to run it on command line using igv.sh, but I haven't managed to make it work, and have moved on to other tools instead.

Limitation:
- Color options are more limited than those actually available on IGV
- snapshot is static, and need to create one snapshot for each region


## **2. IGVer**

installed and run using singularity

*Install:*

singularity pull docker://sahuno/igver:latest

*Run:*

singularity exec docker://sahuno/igver:latest igver --help


**Example 1:**

```
singularity exec docker://sahuno/igver:latest igver --input /home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB037_clone_2025A/filteredModBam/C01208.1.1.1_sup_5mC_5hmC_6mA_SQK.nosecondary.sorted.bam \
--regions chr01_hap1:2357085-2357661 \
--output /home/cenliau/00_nesi_projects/brins03581_nobackup/Cen/IGV \
--genome /home/cenliau/00_nesi_projects/brins03581/Ref_Cen/SB2.1_diploid_plusorg.fasta \
--no-singularity
```

Result:
<img width="1150" height="439" alt="chr01_hap1-2357085-2357661" src="https://github.com/user-attachments/assets/26a2b3a9-2398-4bcc-b7ba-cdb36c0cd1ad" />


**Example 2 (multiple regions):**

```
#multiple regions in one panel
ingularity exec docker://sahuno/igver:latest igver \
--input /home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB037_clone_2025A/filteredModBam/C01208.1.1.1_sup_5mC_5hmC_6mA_SQK.nosecondary.sorted.bam \
--regions "chr01_hap1:2357085-2357661 chr01_hap1:2743045-2743294" \
--output /home/cenliau/00_nesi_projects/brins03581_nobackup/Cen/IGV \
--genome /home/cenliau/00_nesi_projects/brins03581/Ref_Cen/SB2.1_diploid_plusorg.fasta \
--no-singularity
```

Result:
<img width="1150" height="439" alt="chr01_hap1-2357085-2357661 chr01_hap1-2743045-2743294" src="https://github.com/user-attachments/assets/a820462a-0fa6-4650-aff1-fc7eb37c29e3" />


**Example 3 (multiple regions):**

```
#multiple regions, with one snapshot each
singularity exec docker://sahuno/igver:latest igver \
--input /home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB037_clone_2025A/filteredModBam/C01208.1.1.1_sup_5mC_5hmC_6mA_SQK.nosecondary.sorted.bam \
--regions /home/cenliau/00_nesi_projects/brins03581_nobackup/Cen/IGV/igver_region2.bed \
--output /home/cenliau/00_nesi_projects/brins03581_nobackup/Cen/IGV \
--genome /home/cenliau/00_nesi_projects/brins03581/Ref_Cen/SB2.1_diploid_plusorg.fasta \
--no-singularity

#region2.bed file:
chr01_hap1	2357085	2357661	geneA	.	.
chr01_hap1	2743045	2743294	geneB	.	.
```

Result:
chr01_hap1-2357085-2357661.geneA.png
<img width="1150" height="439" alt="chr01_hap1-2357085-2357661 geneA" src="https://github.com/user-attachments/assets/cf1694bb-d245-4467-9a53-b6abf336f88b" />

chr01_hap1-2743045-2743294.geneB.png
<img width="1150" height="439" alt="chr01_hap1-2743045-2743294 geneB" src="https://github.com/user-attachments/assets/a900128f-aa82-47d1-97ff-1a9064fae1d1" />


**Example 4 (multiple samples and regions):**

```
 singularity exec docker://sahuno/igver:latest igver --input /home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB037_clone_2025A/filteredModBam/C01208.1.1.1_sup_5mC_5hmC_6mA_SQK.nosecondary.sorted.bam \
/home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB037_clone_2025A/filteredModBam/C00106.1.1.1_sup_5mC_5hmC_6mA_SQK.nosecondary.sorted.bam \
--regions "chr01_hap1:2357085-2357661 chr01_hap1:2743045-2743294" \
--output /home/cenliau/00_nesi_projects/brins03581_nobackup/Cen/IGV/igver/ \
--genome /home/cenliau/00_nesi_projects/brins03581/Ref_Cen/SB2.1_diploid_plusorg.fasta \
--no-singularity
```

Result:
<img width="1150" height="593" alt="chr01_hap1-2357085-2357661 chr01_hap1-2743045-2743294 (1)" src="https://github.com/user-attachments/assets/af129a5b-c10b-43cc-a7d9-bd0075896e8e" />


Limitations:
- singularity can't seem to run on Jupyter (has to be run on shell)
- can only define output dir, but not the file name
- static snapshot


## **3. IGV reports**

So far, my favourite tool, as it create an html report with embedded IGV snapshot (unlinked to input file, so is quite flexible, e.g.:
- to move the report file elsewhere, e.g. download to your computer
- move the BAM file to different directory, etc

The report also contains all the regions of interest in the input --sites (VCF, BED, MAF, BEDPE, or generic tab delimited file) as a list, that we can go through one by one.

Additionally, the IGV snapshot is not a static picture, but can be further customised within the html (e.g. change color_by, sort_by, etc).

IGV reports was installed locally using bioconda
run by activating the env:
```
source activate /nesi/project/brins03581/env/igv_reports
```

script can be run in command line:

**Example1 (single sample, multiple regions in a BED file):**

```
#single bam track, multiple regions, using bed to define sites
create_report /home/cenliau/00_nesi_projects/brins03581_nobackup/Cen/IGV/igver_region2.bed \
--fasta /home/cenliau/00_nesi_projects/brins03581/Ref_Cen/SB2.1_diploid_plusorg.fasta \
--tracks /home/cenliau/00_nesi_projects/brins03581_nobackup/Genotyping_2025/PB037_clone_2025A/filteredModBam/C01208.1.1.1_sup_5mC_5hmC_6mA_SQK.nosecondary.sorted.bam \
--output /home/cenliau/00_nesi_projects/brins03581_nobackup/Cen/IGV/igv_report_1.html
```

Result: 





