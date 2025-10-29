# Identifying Gut Isolates Using 16S rRNA Sequencing  
**Bioinformatics Upskilling Club – Practical Exercise 1**

---

## Overview
In this hands-on exercise, we will analyze sequencing data from four bacterial isolates obtained from a gut sample. Each isolate has **paired-end 16S rRNA sequencing reads**.  
Our goal is to use **bioinformatics tools on UB’s CCR HPC** to identify the organisms, evaluate their genetic similarity, and visualize their relationships through a **phylogenetic dendrogram**.

This workflow introduces the fundamental steps of microbial identification through **computational genomics**, mirroring how real-world microbiologists identify unknown bacterial strains from sequencing data.

---

## Learning Objectives
By the end of this exercise, participants will be able to:
- Understand the typical pipeline for 16S rRNA sequence analysis.
- Perform **read quality control**, **trimming**, and **assembly**.
- Use **BLAST** to identify bacterial species based on sequence similarity.
- Align sequences and construct a **phylogenetic tree**.
- Interpret the dendrogram to infer relationships between isolates and known bacteria.
- Navigate the **UB CCR HPC** environment efficiently using command-line tools.

---

## Tools and Software
All tools are available as modules on UB’s CCR HPC system:

| Tool | Purpose | CCR Module |
|------|----------|-------------|
| FastQC | Assess raw read quality | `fastqc/0.11.9` |
| Trimmomatic | Trim adapters and low-quality reads | `trimmomatic/0.39` |
| SPAdes | Assemble reads into contigs | `spades/3.15.5` |
| BLAST+ | Identify sequences by comparison to NCBI databases | `blast+/2.13.0` |
| MUSCLE | Multiple sequence alignment | `muscle/3.8.31` |
| FastTree | Build dendrograms from aligned sequences | `fasttree/2.1.11` |

Optional classification tool:  
`kraken2/2.1.2` – for taxonomic assignment using k-mer–based methods.

---

## Why This Exercise Matters
The **16S rRNA gene** is a universal genetic marker present in all bacteria, comprising both conserved and variable regions that make it an ideal marker for taxonomic identification.  
By analyzing 16S reads, you can:
- Determine the identity of unknown bacterial isolates.
- Compare genetic relatedness among isolates.
- Build phylogenetic trees to understand evolutionary relationships.

These are core skills for careers in **microbial genomics**, **clinical microbiology**, and **bioinformatics-driven diagnostics**.

---

## Folder Structure
After completing the exercise, your directory should look like this:

```

gut_isolates/
├── raw/
├── trimmed/
├── fastqc/
├── assemblies/
├── blast/
├── phylogeny/
└── logs/

````

---

## Quick Start Instructions

1. **Log in to CCR HPC**
   ```bash
   ssh yourusername@hpc.ccr.buffalo.edu
``

2. **Load required modules**

   ```bash
   module load fastqc/0.11.9 trimmomatic/0.39 spades/3.15.5 blast+/2.13.0 muscle/3.8.31 fasttree/2.1.11

# also load any dependencies the module prompts
   ````

3. **Clone or download this exercise**

   ```bash
   git clone https://github.com/<your-org>/gut_isolate_identification.git
   cd gut_isolate_identification
   ````

4. **Follow the main exercise instructions**
   See [`exercise_protocol.md`](exercise_protocol.md) for detailed step-by-step commands.

5. **Expected Outputs**

   * Quality reports (`.html`)
   * Assembled contigs (`contigs.fasta`)
   * BLAST hit tables
   * Final dendrogram (`.nwk` file)

---

## Deliverables

By the end of the session, each participant should be able to:

* Provide the **identified species** of each isolate.
* Submit a **phylogenetic tree figure** showing relationships between isolates and reference species.
* Write a short paragraph summarizing their findings and interpretation.

---

## Discussion Questions

1. What criteria did you use to decide the species identity from BLAST results?
2. How does the phylogenetic tree support or differ from the BLAST identification?
3. What are the potential sources of error in 16S-based identification?
4. How would the workflow differ for **metagenomic** or **whole-genome** sequencing?

---

This exercise was designed for **The Microbial Code – University at Buffalo** to build reproducible and hands-on experience with command-line microbial genomics.

Contributor: *Namrata Deka*, Department of Microbiology & Immunology, JSMBS.
Google Co-pilot to refine commands/markdown.

---

## References

* NCBI 16S ribosomal RNA Database
* Bushnell B. (2014). *Trimmomatic: A flexible trimmer for Illumina sequence data.*
* Bankevich A. et al. (2012). *SPAdes: A new genome assembly algorithm and its applications to single-cell sequencing.*
* Edgar RC. (2004). *MUSCLE: Multiple sequence alignment with high accuracy and high throughput.*


