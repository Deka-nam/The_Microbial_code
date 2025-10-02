# Genome Assembly with SPAdes

## Objectives
- Understand genome assembly concepts and challenges
- Learn to run SPAdes assembler on bacterial sequencing data
- Assess assembly quality using standard metrics
- Interpret assembly output files and results


### 1. Genome Assembly Basics

#### What is Genome Assembly?
- Process of reconstructing complete genome sequences from short sequencing reads- _de novo_
- **Contigs**: Continuous sequences from overlapping reads
- **Scaffolds**: Ordered and oriented contigs with gaps

#### Assembly Challenges
- **Repeats**: Regions that occur multiple times in the genome
- **Coverage variation**: Uneven sequencing depth across genome
- **Errors**: Sequencing errors and artifacts
- **Heterogeneity**: Mixed populations in sequencing sample

### 2. SPAdes Assembler [SPAdes Githib](https://github.com/ablab/spades) 

#### Why SPAdes for Microbial Genomes?
- Specifically designed for single-cell and bacterial genomes
- Handles uneven coverage common in microbial sequencing
- Multi-sized k-mer approach for better resolution of repeats
- Good performance on both isolate and metagenome data

#### Key SPAdes Features
- Multiple k-mer sizes for comprehensive assembly
- Error correction and read normalization
- Scaffolding and gap closure
- Plasmid assembly capability

## Hands-On Exercises

### Exercise 1: Setup and Data Preparation

#### Install SPAdes
```bash
# Using conda (recommended)
conda install -c bioconda spades

# Or check if already installed
spades.py --version

#if you are using UB CCR HPC servers
module spider spades
# module load as instructed
```

#### Download Practice Dataset
```bash
# Create assembly workspace
mkdir genome_assembly
cd genome_assembly

# Download a small bacterial dataset (E. coli paired-end)
# These are small files for quick practice
wget https://zenodo.org/record/3736457/files/SRR10963010_1.fastq.gz
wget https://zenodo.org/record/3736457/files/SRR10963010_2.fastq.gz

# Verify downloads
ls -lh *.fastq.gz
```

### Exercise 2: Quality Control (Optional but Recommended)

#### FastQC Quality Check
```bash
# Install FastQC if needed
# conda install -c bioconda fastqc

# Run FastQC on both files
fastqc SRR10963010_1.fastq.gz SRR10963010_2.fastq.gz

# View results (open in browser)
# firefox *.html

# UB CCR HPC served
module spider fastqc
```

#### Basic Read Statistics
```bash
echo "Forward reads: $(zcat SRR10963010_1.fastq.gz | awk 'END{print NR/4}')"
echo "Reverse reads: $(zcat SRR10963010_2.fastq.gz | awk 'END{print NR/4}')"
echo "Read length: $(zcat SRR10963010_1.fastq.gz | awk 'NR==2{print length}')"
```

### Exercise 3: Running SPAdes Assembly

#### Basic Assembly Command
```bash
# Run SPAdes with basic parameters
spades.py \
  -1 SRR10963010_1.fastq.gz \
  -2 SRR10963010_2.fastq.gz \
  -o spades_output \
  -t 4 \           # Use 4 CPU threads
  -m 16 \          # Use 16 GB memory
  --careful        # Reduce mismatches and short indels

# This will take 5-15 minutes, depending on your system
```

#### Monitor Progress
```bash
# Check if assembly is running
tail -f spades_output/spades.log

# Check output directory structure
ls -la spades_output/
```

### Exercise 4: Analyzing Assembly Results

#### Explore Output Files
```bash
cd spades_output

ls -la

ls -lh *.fasta
echo "Number of contigs: $(grep -c '>' contigs.fasta)"
echo "Assembly size: $(grep -v '>' contigs.fasta | tr -d '\n' | wc -c)"

ls -lh scaffolds.fasta
echo "Number of scaffolds: $(grep -c '>' scaffolds.fasta)"
```

#### Basic Assembly Statistics
```bash
# Calculate N50 and other metrics
python << EOF    # or nano (text editor) and write this python code and save the file as xyz.py
from Bio import SeqIO
import numpy as np

contigs = []
for record in SeqIO.parse("contigs.fasta", "fasta"):
    contigs.append(len(record.seq))

contigs.sort(reverse=True)
total_length = sum(contigs)
n50 = 0
current_sum = 0

for length in contigs:
    current_sum += length
    if current_sum >= total_length * 0.5:
        n50 = length
        break

print(f"Total contigs: {len(contigs)}")
print(f"Total length: {total_length:,} bp")
print(f"Largest contig: {max(contigs):,} bp")
print(f"N50: {n50:,} bp")
print(f"Average contig length: {np.mean(contigs):.0f} bp")
EOF
```

### Exercise 5: Quality Assessment

#### Check Assembly Completeness
```bash
# Install checkm if available, or use basic metrics
# conda install -c bioconda checkm-genome

echo "GC content: $(grep -v '>' contigs.fasta | tr -d '\n' | awk '{gc=gsub(/[GC]/,""); total=length(); print gc/total*100}')%"

# Count contigs by size
echo "=== Contig Size Distribution ==="
grep -v '>' contigs.fasta | awk '{print length}' | \
  awk '{if($1>=1000) large++; else if($1>=500) medium++; else small++} END{print "Large (>=1kb):", large, "\nMedium (500bp-1kb):", medium, "\nSmall (<500bp):", small}'
```

#### Visualize Assembly (Optional)
```bash
# Create simple assembly plot
# Install if needed: conda install -c bioconda bandage
Bandage image contigs.fasta assembly_graph.png
```

## Challenge: Assembly Optimization

Run SPAdes with different parameters and compare results:

1. **Default settings** (as above)
2. **Different k-mer sizes**: `-k 21,33,55,77`
3. **With error correction**: Add `--only-assembler` to skip error correction
4. **Compare results** using assembly statistics

### Solution Framework
```bash
# Run with different k-mers
spades.py -1 SRR10963010_1.fastq.gz -2 SRR10963010_2.fastq.gz \
  -o spades_kmer_test -k 21,33,55,77 -t 4

# Compare assemblies
for dir in spades_output spades_kmer_test; do
  grep -c ">" $dir/contigs.fasta
  grep -v ">" $dir/contigs.fasta | tr -d '\n' | wc -c
done
```

## Pro Tips for Assembly

1. **Start with quality control** - poor quality reads lead to poor assemblies
2. **Use appropriate k-mer sizes** - smaller k-mers for lower coverage, larger for higher coverage
3. **Monitor memory usage** - bacterial genomes typically need 8-32 GB RAM
4. **Use --careful** for final assemblies to reduce errors
5. **Always check multiple metrics** - not just N50, but also number of contigs and completeness

## Troubleshooting Common Issues

| Problem | Solution |
|---------|----------|
| Assembly too fragmented | Try different k-mer sizes, check read quality |
| Memory errors | Use `-m` to limit memory, filter reads |
| No output | Check input file paths and formats |
| Low N50 | Check coverage, consider read normalization |

- Complete [SPAdes documentation](https://github.com/ablab/spades/tree/main/docs)
- Complete [Spades user manual](https://ablab.github.io/spades/)

## Reference: 
Bankevich, A., et al., SPAdes: a new genome assembly algorithm and its applications to single-cell sequencing. J Comput Biol, 2012. 19(5): p. 455-77.
