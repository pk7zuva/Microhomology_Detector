# Microhomology_Detector: Analysis of double strand break repair outcomes from paired end high throughput sequencing data

# Microhomology_Detector is a computational pipeline for the analysis of high throughput sequencing data that is generated for studying the double strand DNA break repair mechanism. 

The experimental approach is to create breaks at specific regions of the genome by targeted CRISPR-Cas9 in a specific cell line. After allowing the cells to grow and repair the breaks, specific primers are used to amplify the targeted regions. The PCR amplified products are used for paired end high throughput sequencing library preparation.  In an ideal situation breaks are assumed to be repaired perfectly (without any mutation compared to WT sequence at the ligation junction (double strand break point)). However, in reality this is not true. A significant fraction of the exposed ends (cut site) are chewed by DNA repair enzymes or accessory proteins before ligation and thus create a deletion compared to WT sequence. The length of the deletion varies from clone to clone depending on which repair pathway was used to repair the break. Most often a micro-homology of 2-15 bases has been observed at the ligation junction. The ligation happens in such a way that while one unit of the repeat sequence forms part of the ligation junction, the second repeat unit gets digested before ligation. 

Microhomology_Detector allows a user to quantitate if the break repaired perfectly (PCR amplified sequence identical to WT), while a repair with an error is quantitated as the extent of deletion (number of base pairs deleted at the ligation junction).  The program then identifies the presence of any micro homology at the ligation junction and reports the motif sequence. In other words, Microhomology_Detector collapses the identical reads, aligns sequencing reads to an expected amplicon, quantifies the length of deletion and finally identifies any micro homology at the ligation junction.

# Followings are the major steps of Microhomology_Detector.

1.	Joining overlapping tags (reads) of a paired end sequence: If the insert size of the sequencing library is shorter than the combined length of read1 and read2 then the actual sequence of insert and its length can be calculated as ((length of read1 + length of read2)-length of overlap). To merge the overlapping reads and identify the sequence of insert Microhomology_Detector pipeline internally uses FLASH (T. Magoc and S. Salzberg (Bioinformatics 27:21 (2011), 2957-63.)).   The FLASH program takes adaptor removed paired end fastq files as input, merges the overlapping reads and outputs a single fastq file with the merged sequence. 
2.	Filtering out non-target sequence: After a targeted double strand cut followed by a repair, the start and end of the amplified sequence is expected to have identical sequences from the start and end of the expected amplicon respectively (in this case 10bp was considered from each end). The pipeline filters out any sequence coming from the first step that does not match the above criteria. 
3.	Collapsing the identical reads: After filtering out non-specific amplified products, identical reads are collapsed to decrease the computation time during read alignment.
4.	Alignment of collapsed reads: After collapsing the identical reads, the pipeline performs a global alignment with the expected amplicon sequence. To do this Microhomology_Detector uses “needle” algorithm from EMBOSS package (Rice P., Longden I. and Bleasby A. EMBOSS: The European Molecular Biology Open Software Suite. Trends in Genetics. 2000 16(6):276-277). For aligning the read, default parameters are used except “gap opening penalty of 10.0 and gap extension penalty of 0.0”. 
5.	Parsing of alignment and identification of deletion and micro homology:  Alignment of each read with expected amplicon sequence is read by custom script. Length of deletion compared to expected sequence, ligation point and 2-15 bases long micro homology at ligation point (if any) is identified. Finally, Microhomology_Detector outputs an alignment of each unique read with the cloning frequency, length of deletion and micro homology information. The complete pipeline is available from at the GitHub repository (https://github.com/pk7zuva/Microhomology_Detector). 


# Following are the steps to download and run Microhomology_Detector

# Clone the repository

git clone https://github.com/pk7zuva/Microhomology_Detector.git

cd Microhomology_Detector

# Install the FLASH package

"FLASH-1.2.11.tar.gz" provided with this pipeline. More information about this package can be found here "https://ccb.jhu.edu/software/FLASH/".

# Install the EMBOSS package
"EMBOSS-6.5.7.tar.gz" provided with this pipeline. It can be also downloaded from "https://src.fedoraproject.org/lookaside/extras/EMBOSS/EMBOSS-6.5.7.tar.gz/6a2cb3f93d5e9415c74ab0f6b1ede5f0/".

After installing above program run the script with following argument:
pX330_A_R1_trimmed.fastq & pX330_A_R2_trimmed.fastq are adaptor trimmed fastq files.
pX330 = sample name
subject.onlyseq.fa = contains the expected amplicon sequence

# bash CRISPR-DNA-Repair.sh pX330_A_R1_trimmed.fastq pX330_A_R2_trimmed.fastq pX330 subject.onlyseq.fa 


