# Identifying Gut Isolates Using 16S rRNA Sequencing

## Objective
You have isolated four bacterial organisms from a gut sample. Each isolate has paired-end 16S rRNA sequencing data.  
The goal of this exercise is to **assemble**, **analyze**, and **identify** these isolates using command-line tools on the **UB CCR HPC** environment and to construct a **phylogenetic dendrogram** showing their relatedness.

---

## Required Software (CCR Modules)
Load these modules on UB CCR as needed:

```bash
module load fastqc
module load trimmomatic
module load spades
module load blast
module load muscle
module load fasttree
````

---

## Step 1: Setup and Organization

Create a clean directory structure for the analysis:

```bash
mkdir -p ~/projects/gut_isolates/{raw,trimmed,fastqc,assemblies,blast,phylogeny,logs}
cd ~/projects/gut_isolates/raw
```

**Input files:**

```
isolate1_R1.fastq.gz  isolate1_R2.fastq.gz
isolate2_R1.fastq.gz  isolate2_R2.fastq.gz
isolate3_R1.fastq.gz  isolate3_R2.fastq.gz
isolate4_R1.fastq.gz  isolate4_R2.fastq.gz
```
---

## Step 2: Quality Control and Trimming

### Run FastQC

```bash
fastqc raw/*.fastq.gz -o fastqc/
```

### Trim adapters and low-quality bases

```bash
for s in {1..4}; do
  trimmomatic PE \
    raw/isolate${s}_R1.fastq.gz raw/isolate${s}_R2.fastq.gz \
    trimmed/isolate${s}_R1_paired.fq.gz trimmed/isolate${s}_R1_unpaired.fq.gz \
    trimmed/isolate${s}_R2_paired.fq.gz trimmed/isolate${s}_R2_unpaired.fq.gz \
    SLIDINGWINDOW:4:20 MINLEN:100
done
```

### Re-run FastQC

```bash
fastqc trimmed/*paired.fq.gz -o fastqc/
```

**Hint:** Check that per-base quality improves and adapters are gone in the new FastQC HTML reports.

---

## Step 3: Assembly of 16S rRNA Reads

Use **SPAdes** to assemble the paired reads for each isolate.

```bash
for s in {1..4}; do
  spades.py --only-assembler -k 21,33,55,77 \
    -1 trimmed/isolate${s}_R1_paired.fq.gz \
    -2 trimmed/isolate${s}_R2_paired.fq.gz \
    -o assemblies/isolate${s}_spades
done
```

**Output:**
`assemblies/isolateX_spades/contigs.fasta`

**Hint:** The longest contig often corresponds to the 16S gene, but you will confirm by BLAST in the next step.

---

## Step 4: Species Identification Using BLAST

### Prepare database

```bash
mkdir -p blast/db && cd blast/db
update_blastdb.pl 16S_ribosomal_RNA
```

### Run BLASTn

```bash
for s in {1..4}; do
  blastn -query ~/projects/gut_isolates/assemblies/isolate${s}_spades/contigs.fasta \
         -db 16S_ribosomal_RNA \
         -out ../isolate${s}_blast.txt \
         -outfmt "6 qseqid stitle pident length evalue bitscore" \
         -max_target_seqs 5
done
```

### Review BLAST results

Open the `.txt` files and note:

* Top hit description (species name)
* Percent identity (`pident`)
* Bitscore (higher = better match)

---

## Step 5: Multiple Sequence Alignment and Dendrogram

### 1. Combine sequences

Create a FASTA file `all_16S.fasta` containing:

* The top 16S contig sequence from each isolate
* A few reference 16S sequences downloaded from NCBI (in FASTA format)

### 2. Align sequences

```bash
muscle -in all_16S.fasta -out all_16S_aligned.fasta
```

### 3. Build dendrogram

```bash
FastTree -nt all_16S_aligned.fasta > all_16S_tree.nwk
```

### 4. Visualize tree

Upload the `.nwk` file to [iTOL](https://itol.embl.de/) or visualize it with a Python package such as `ete3`.

**Hint:** Your isolates should cluster near their closest known relatives (based on the BLAST step).

---

## Final Outputs 

| Step           | Output File                  | Description           |
| -------------- | ---------------------------- | --------------------- |
| QC             | `fastqc/*.html`              | Read quality reports  |
| Trimming       | `trimmed/*_paired.fq.gz`     | High-quality reads    |
| Assembly       | `assemblies/*/contigs.fasta` | Assembled 16S contigs |
| Identification | `blast/*.txt`                | Closest species match |
| Phylogeny      | `phylogeny/all_16S_tree.nwk` | Dendrogram file       |

---

## Example Write-Up for Methods Section

> Paired-end 16S rRNA sequencing reads from four gut isolates were analyzed using the UB CCR HPC environment.
> Quality control was performed with FastQC and Trimmomatic, followed by assembly using SPAdes.
> Assembled contigs were identified via BLASTn against NCBI’s 16S rRNA database.
> Phylogenetic relationships were inferred through MUSCLE alignment and FastTree dendrogram construction.

---

## Questions

* What parameters affect trimming quality (e.g., SLIDINGWINDOW, MINLEN)?
* How do assembly k-mer sizes influence contig length?
* Why would BLAST hits differ from phylogenetic placement?
* How would you verify if an isolate might represent a novel species?

---

## Optional

Try automating all steps into one **bash script** or **Snakemake workflow** to practice pipeline building!

---

