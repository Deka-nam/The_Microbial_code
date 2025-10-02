# NCBI Databases & Genomic File Types

## Objectives
- Understand major NCBI databases and data types
- Learn to navigate and search NCBI for genomic data
- Identify different genomic file formats and their purposes
- Practice downloading and inspecting various biological data files

### 1. NCBI Database Overview

#### Major NCBI Databases
- **SRA (Sequence Read Archive)**: Raw sequencing data from high-throughput platforms
- **GenBank**: Annotated DNA and RNA sequences
- **RefSeq**: Curated, non-redundant reference sequences
- **Assembly**: Genome assembly data and metadata
- **BioProject**: Organized collection of biological data related to a single project
- **BioSample**: Descriptions of biological source materials

#### Data Flow Concept
```
BioProject → BioSample → SRA → Assembly → GenBank/RefSeq
```

### 2. Genomic File Formats

#### Raw Sequencing Data
- **FASTQ**: Raw sequencing reads with quality scores
  - Contains read sequences and per-base quality information
  - Typically very large files (GB to TB range)
- **SRA**: Compressed format used by NCBI for storage
  - Requires `sra-toolkit` to convert to FASTQ

#### Assembled Data
- **FASTA**: Sequence data without annotations
  - Assembly contigs/scaffolds
  - Protein or nucleotide sequences
- **GBFF (GenBank Flat File)**: Annotated sequences with features
  - Contains gene annotations, CDS, proteins, etc.
- **GFF/GTF**: Genome annotation files
  - Structured format for genomic features

#### Variant Data
- **VCF**: Variant Call Format
  - Stores genetic variations (SNPs, indels)
- **BAM/SAM**: Aligned sequencing reads
  - Binary/text format for alignments to reference

## Hands-On Exercises

### Exercise 1: Exploring NCBI Databases

#### Navigating NCBI Website
```bash
# We'll use command-line tools, but first understand the web interface:
echo "Visit these NCBI resources in your browser:"
echo "- https://www.ncbi.nlm.nih.gov/assembly"
echo "- https://www.ncbi.nlm.nih.gov/sra"
echo "- https://www.ncbi.nlm.nih.gov/bioproject"
```

#### Using E-utilities (Entrez Direct)
```bash
# Install E-utilities (if not available)
# conda install entrez-direct -c bioconda

# Search for E. coli assemblies
esearch -db assembly -query "Escherichia coli[organism]" | \
  efetch -format docsum

# Get specific assembly information
esearch -db assembly -query "GCF_000005845.2" | \
  efetch -format docsum

# Find SRA datasets for a specific BioProject
esearch -db sra -query "PRJNA257197" | \
  efetch -format runinfo
```

### Exercise 2: Downloading Genomic Data

#### Download Assembly Files
```bash
# Create a working directory
mkdir ncbi_practice
cd ncbi_practice

# Download E. coli reference genome
mkdir ecoli_genome
cd ecoli_genome

# Using datasets tool (recommended)
datasets download genome accession GCF_000005845.2 --include genome,gtf,cds,protein
unzip ncbi_dataset.zip

# Alternative: Using FTP
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/005/845/GCF_000005845.2_ASM584v2/GCF_000005845.2_ASM584v2_genomic.fna.gz
gunzip GCF_000005845.2_ASM584v2_genomic.fna.gz
```

#### Download SRA Data
```bash
# Install sra-toolkit first
# conda install sra-tools -c bioconda

# Create SRA practice directory
cd ..
mkdir sra_data
cd sra_data

# Download a small SRA dataset (example)
prefetch SRR12131466
fasterq-dump SRR12131466

# Check the downloaded files
ls -lh
file *.fastq
```

### Exercise 3: File Format Inspection

#### Inspect Different File Types
```bash
# Return to genome directory
cd ../ecoli_genome

# Examine FASTA file
echo "=== Genomic FASTA ==="
head -20 *.fna
echo "Number of sequences:"
grep -c ">" *.fna

# Examine GTF file
echo "=== GTF Annotation ==="
head -20 *.gtf
echo "Number of genes:"
grep -c "gene" *.gtf

# Examine Protein FASTA
echo "=== Protein FASTA ==="
head -20 *.faa
echo "Number of proteins:"
grep -c ">" *.faa

# Examine FASTQ files (from SRA)
cd ../sra_data
echo "=== FASTQ Format ==="
head -8 *.fastq
```

### Exercise 4: Data Quality Assessment

#### Basic QC of Downloaded Data
```bash
# For genomic FASTA
cd ../ecoli_genome
echo "Genome statistics:"
echo "Total sequences: $(grep -c '>' *.fna)"
echo "Total bases: $(grep -v '>' *.fna | tr -d '\n' | wc -c)"
echo "Sequence lengths:"
grep -v '>' *.fna | awk '{print length}' | sort -n | uniq -c

# For FASTQ files
cd ../sra_data
echo "FASTQ statistics:"
echo "Total reads: $(wc -l *.fastq | tail -1 | awk '{print $1/4}')"
echo "Read length distribution:"
awk 'NR%4==2{print length}' *.fastq | sort | uniq -c
```

## Challenge: Microbial Genome Investigation

### The Task
Choose a microbial species of interest and:
1. Find its latest assembly in NCBI
2. Download the genomic FASTA and GTF files
3. Extract basic statistics (genome size, GC content, gene count)
4. Identify what SRA data is available for this species

### Solution Framework
```bash
# 1. Search for your species
esearch -db assembly -query "Your_Species[organism]" | \
  efetch -format docsum

# 2. Download data (replace with actual accession)
datasets download genome accession YOUR_ACCESSION --include genome,gtf

# 3. Calculate statistics
echo "Genome size: $(grep -v '>' *.fna | tr -d '\n' | wc -c)"
echo "GC content: $(grep -v '>' *.fna | tr -d '\n' | awk '{gc=gsub(/[GC]/,""); at=gsub(/[AT]/,""); print gc/(gc+at)*100}')%"
echo "Gene count: $(grep -c '\tgene\t' *.gtf)"

# 4. Find SRA data
esearch -db sra -query "Your_Species[organism]" | \
  efetch -format runinfo | head -10
```

## Key Resources

### NCBI Tools
- **Entrez Direct**: Command-line access to NCBI databases
- **SRA Toolkit**: Tools for working with SRA files
- **datasets**: New NCBI tool for bulk data download

### File Format Documentation
- [NCBI File Formats](https://www.ncbi.nlm.nih.gov/genome/doc/ftpfaq/)
- [SRA Format](https://www.ncbi.nlm.nih.gov/sra/docs/sra-file-formats/)
- [GFF/GTF Specification](https://useast.ensembl.org/info/website/upload/gff.html)

## Pro Tips

1. **Always check file integrity** after download using MD5 checksums
2. **Use the datasets tool** for bulk downloads - it's more reliable than manual FTP
3. **Start small** - test with small datasets before downloading large files
4. **Document accession numbers** - keep track of what data you've downloaded
5. **Check for updates** - genomic data is frequently updated

___WHOOP!! WHOOP!!___ **Extra points if you can guess where this is from** 
