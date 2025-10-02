# Working with Files & Data on Linux
## Objectives 
- Master essential file inspection and manipulation commands
- Understand common bioinformatics file formats
- Learn about file compression and why it matters
- Understand Linux permissions and environment variables
- Apply skills to real bioinformatics data

## 1. File Inspection & Manipulation
### Why Text Processing Matters in Bioinformatics
- Most bioinformatics data is stored in text-based formats.
- Being able to quickly inspect, search, and manipulate these files from the command line is an essential skill for efficient research.
- The Power of Pipes and Redirection: Pipes (`|`): Connect commands together, passing output from one command as input to the next
- Redirection (`>, >>`): Control where command output goes: `>` overwrites a file, `>>` appends to a file
---
## 2. Common bioinformatics file formats
### FASTA
```bash
>sequence_id description
ATCGATCGATCGATCGATCGATCGATCG
ATCGATCGATCGATCGATCGATCG
```
- Used for DNA, RNA, or protein sequences
- Header line starts with `>`
- Sequence data follows on subsequent lines

### FASTQ
```bash
@sequence_id
ATCGATCGATCGATCGATCGATCG
+
IIIIIIIIIIIIIIIIIIIIIIII
```
- Used for sequencing reads with quality scores
- Four lines per sequence: identifier, sequence, +, quality scores

### Other Important Formats
- GFF/GTF: Genome annotation files (genes, features)
- VCF: Variant Call Format (genetic variations)
- SAM/BAM: Sequence Alignment Map (aligned sequencing reads)

## 3. Compression in Bioinformatics
#### Why Compress?
- Sequencing data files can be enormous (GBs to TBs)
- Saves storage space and transfer time
- Most bioinformatics tools can work with compressed files directly

#### Common Compression Tools
- `gzip` / `gunzip` - Standard compression
- `zcat` - View compressed files without decompressing
- `bgzip` - Block compression (used for indexed files)


## Hands-On Exercises
### Prerequisites: 
Access to a Linux terminal (local or via SSH)

- Basic familiarity with navigation commands

### Exercise 1: File Inspection Commands
#### Create Practice Files

```bash
# Create a practice directory
mkdir microbial_code_practice1
cd microbial_code_practice1

# Create a sample multi-sequence FASTA file
cat > mock1_sequence.fasta << 'EOF'
>seq1 chromosome:1 description:"hypothetical protein"
ATCGATCGATCGATCGATCGATCGATCGATCG
ATCGATCGATCGATCG
>seq2 chromosome:2 description:"ribosomal protein"
GGGATCGATCGATCGATCGATCGATCGGGGGG
CCCCCCCCCCCCCCCCCCCC
>seq3 chromosome:1 description:"membrane protein"
TTTTTTTTTTTTTTTTTTTTTTTTTTTTTTTT
ATCGATCGATCGTAGCTAGCTAGCTAGC
EOF

# Create a larger file for testing
for i in {1..100}; do echo ">xyz_seq_$i"; echo "ATCGATCGATCGATCGATCGATCG"; done > large_sequences.fasta

# Upload the document given to you

```
- The << `EOF` part is called a “here-document” delimiter. `EOF`: End of file. 
- cat > xyz.fasta means you’re writing to a file called xyz.fasta.
- << 'EOF' tells the shell: “Read everything that follows until a line containing only EOF, and send it to cat as input.”
- Here, the shell takes all the lines between << `EOF` and the terminating EOF and feeds them to cat, which writes them into xyz.fasta.
- You could replace EOF with any marker (like END or STOP), as long as the opening and closing match.

#### Lets inspect the files you created

```bash
# View entire file
cat sequences.fasta

# View first 10 lines
head sequences.fasta

# View first 5 lines
head -n 5 sequences.fasta

# View last 10 lines
tail sequences.fasta

# View last 5 lines
tail -n 5 sequences.fasta

# Interactive viewing (scroll with arrow keys, press 'q' to quit)
less sequences.fasta
```
### Exercise 2: Counting and Searching

```bash
# Count lines, words, and characters
wc sequences.fasta

# Count only lines
wc -l sequences.fasta

# Search for patterns with grep
grep ">" sequences.fasta                    # Find all sequence headers
grep -c ">" sequences.fasta                 # Count sequences
grep "chromosome:1" sequences.fasta         # Find sequences from chromosome 1
grep -i "ribosomal" sequences.fasta         # Case-insensitive search

# Search in sequence data (not headers)
grep -v ">" sequences.fasta | head          # Show only sequence lines
```

### Exercise 3:  Sorting and Filtering

```bash
# Count lines, words, and characters
wc sequences.fasta

# Count only lines
wc -l sequences.fasta

# Search for patterns with grep
grep ">" sequences.fasta                    # Find all sequence headers
grep -c ">" sequences.fasta                 # Count sequences
grep "chromosome:1" sequences.fasta         # Find sequences from chromosome 1
grep -i "ribosomal" sequences.fasta         # Case-insensitive search

# Search in sequence data (not headers)
grep -v ">" sequences.fasta | head          # Show only sequence lines
```
### Exercise 4: Redirection and Pipes

```bash
# Redirect output to a new file
grep ">" sequences.fasta > headers.txt
cat headers.txt

# Append to existing file
echo ">seq4 extra sequence" >> headers.txt
cat headers.txt

# Use pipes to chain commands
grep ">" sequences.fasta | wc -l                    # Count sequences
grep -v ">" sequences.fasta | wc -c                 # Count sequence characters
grep "chromosome:1" sequences.fasta | head -n 1     # First chromosome 1 sequence

# Complex pipe example: find most common sequence length
grep -v ">" sequences.fasta | awk '{print length}' | sort | uniq -c | sort -nr
```

### Exercise 5: Working with compressed files 

```bash
# Check original file size
ls -lh sequences.fasta

# Compress the file
gzip sequences.fasta
ls -lh sequences.fasta.gz

# View compressed file without decompressing
zcat sequences.fasta.gz | head

# Decompress the file
gunzip sequences.fasta.gz

# Create a compressed copy while keeping original
gzip -c sequences.fasta > sequences.fasta.gz
```

### Exercise 6: File Permissions

```bash
# View detailed file information
ls -l sequences.fasta

# Understanding the permission string: -rw-r--r--
# - = regular file
# rw- = user can read/write
# r-- = group can read
# r-- = others can read

# Change file permissions
chmod 644 sequences.fasta                    # rw-r--r--
chmod +x my_script.sh                       # Add execute permission
chmod 600 private_file.txt                  # Only owner can read/write

# Check your username and groups
whoami
groups
```

### Mini Final Challange: 
You've been given a FASTA file containing protein sequences. Your supervisor wants you to:
- Check how large the file is
- Count how many sequences it contains
- Find all sequences from chromosome 1
- Extract just the headers for reporting
- Find if any sequences contain the motif "ATGSTART"

#### Setup:
```bash
# Create the file
cat > challenge.fasta << 'EOF'
>protein1 chromosome:1 length:256
MVLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDLSHGSAQVKGHG
KKVADALTNAVAHVDDMPNALSALSDLHAHKLRVDPVNFKLLSHCLLVTLAAHLPAEFTP
>protein2 chromosome:2 length:189
MARTKQTARKSTGGKAPRKQLATKAARKSAPATGGVKKPHRYRPGTVALREIRRYQKSTE
LLIRKLPFQRLVREIAQDFKTDLRFQSAAIGALQEASEAYLVGLFEDTNLCAIHAKRVTI
>protein3 chromosome:1 length:312
MGTGSTSYVAVTSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSS
ATGSTARTTARGETSEQUENCEHEREKKKGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGG
>protein4 chromosome:3 length:167
MAAAAAVAAAAAPAAAAVAAAAAAAPAAAAVAAAAAAAPAAAAVAAAAAAAPAAAAVAAA
AAAAAAAAAPAAAAVAAAAAAAAAPVAAAAAAAPAAAAVAAAAAAAPAAAAVAAAAAAAAA
EOF
```
<details> <summary>Click to view solution</summary>

### 1. Check file size
ls -lh challenge.fasta
### or
wc -c challenge.fasta

### 2. Count sequences
grep -c ">" challenge.fasta

### 3. Find sequences from chromosome 1
grep "chromosome:1" challenge.fasta

### 4. Extract just headers
grep ">" challenge.fasta > headers_report.txt
cat headers_report.txt

#### 5. Find sequences with ATGSTART motif
grep -B1 "ATGSTART" challenge.fasta

### or to see just the headers of sequences containing the motif
grep "ATGSTART" challenge.fasta | grep -o ">.*" 
</details>



## Pro Tips for Bioinformatics Workflows
1. Always check your data first with head, wc, ls -lh before running analyses
2. Use compression for large files to save disk space and transfer time
3. Chain commands with pipes to build powerful one-liners
4. Redirect output to files to save results and create reports
5. Use less for large files instead of cat to avoid terminal overload

___END___ YAY!! ___CELEBRATIONS!!!___
