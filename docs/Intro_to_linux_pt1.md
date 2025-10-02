# Introduction to Linux
## Philosophy & Goals

- **Target Audience**: Absolute beginners to Linux, but who have a specific goal (using HPC, remote servers, automation).
- **Goal**: Demystify the command line. Students should leave feeling capable of logging into a remote machine, navigating its file system, performing basic operations, and understanding how to run a simple job.
- **Motto**: Don't memorize commands; understand the concepts. You can always Google the syntax.

## 3 Modules (1-2 hours total)
## Module 1: The Why and The How - Foundations & First Steps 

### What is Linux? 
- **Briefly:** an operating system kernel, often referring to the whole OS (GNU/Linux). It's everywhere (Android, servers, 90% of cloud, all Top500 supercomputers).
- **The Technical Definition:** At its absolute core, Linux is just a kernel. Think of the kernel as the engine of a car. It's the fundamental software that talks directly to your computer's hardware.
- It manages resources, makes everything run, but it's not something you directly interact with.
- **The Common Usage:** When people say "Linux," they usually mean a full operating system built around that kernel. This includes all the tools, utilities, and programs you actually use.
- A more accurate name is GNU/Linux, acknowledging the vast array of software from the GNU project that combines with the Linux kernel.
- **It's Everywhere:** This is the most important thing to understand. You might not see it, but Linux is the invisible foundation of the modern digital world.

### Why Linux for HPC/Science? Stability, performance, free & open source, powerful command-line tools, no GUI overhead.
- You might be used to Windows or macOS on your laptop. Those are great for everyday tasks. But for heavy-duty scientific computing, Linux is the undisputed champion. Here’s why:
- *Stability & Reliability:* Imagine your analysis runs for 5 days straight. You can't have the operating system crashing or needing a reboot. Linux systems are famously stable and can run for years without interruption.
- This is non-negotiable for long-running experiments.
- *Performance:* Linux is very "lightweight." It doesn't waste precious computational resources on flashy graphical interfaces. Every bit of CPU power and memory can be dedicated to your simulation, your data analysis, your model.
- *Free & Open Source:* It is free to use, which is great for universities and research groups with limited budgets. More importantly, it's open source.
- Scientists can see exactly how it works, modify it for their specific needs, and aren't locked into a single company's products.
- *Powerful Command-Line Tools:* This is the big one. Linux provides an incredibly powerful set of text-based tools.
- You can combine them like Lego bricks to automate complex data processing, search through millions of files in seconds, and run analyses in a repeatable, documented way. This is far more powerful than clicking around in a GUI for scientific workflows.
- *No GUI Overhead:* This relates to performance. A graphical desktop (the icons, windows, animations) uses a lot of RAM and CPU cycles. On a massive HPC cluster with thousands of processors, installing a GUI on every node would be a huge waste.
- Scientists connect and run everything through the efficient, text-based command line."

### The Shell (Bash) vs. The Kernel: The shell (Bash) is a program that takes your commands and tells the kernel what to do.
- *The Kernel:* Remember our car engine? That's the kernel. It's the low-level core of the system, doing the actual work. You don't talk to it directly.
- *The Shell:* This is the command-line interface (CLI). If the kernel is the engine, the shell (in our case, called Bash) is the steering wheel, gear shifter, and pedals. It's how you, the driver, tell the engine what to do.
- You type commands into the shell.
- The shell takes those commands, interprets them, and tells the kernel to execute them.
- The kernel does the work and returns the result to the shell.
- The shell then shows the output to you.
- **Why we use Bash:** It's the most common and standard shell on Linux systems. It's powerful, scriptable, and what you will almost certainly encounter on any HPC cluster.
  
### The Terminal:
Your window into the shell.
### The Linux Filesystem: A Tree (Not a Drive Letter)
This is a fundamental concept that works differently than on Windows.
- **The Windows (Local Computer) Way:** Your hard drive is `C:\`. Your USB drive is `D:\`. Each device gets its own "tree" with its own root. They are separate silos.
- **The Linux (and HPC) Way:** There is only **one big tree**, and it starts from a single root, represented by a single forward slash `/`.
    - Everything is a file or a directory (folder) under this root.
    - Your personal home directory (where your files live) is usually at `/home/your_username/`.
    - When you log into a remote HPC system, you are dropped into your home directory on *their* big filesystem tree, e.g., `/home/your_username/`.

**Why this matters for HPC:** An HPC cluster has a huge, shared filesystem that all the compute nodes can access. Your `/home` directory might be on a fast, backed-up but smaller storage system. 
There might be a massive `/scratch` or `/work` directory for temporary, large-scale data from your active computations. The key is that from any node in the cluster, the path to your file is the same (e.g., `/scratch/your_username/my_big_data.dat`). 
This unified tree is essential for a cluster to function.

---

### Distinguishing: Local Computer vs. HPC (with Python/R)

| Aspect | Your Local Laptop (Windows/macOS) | An HPC Cluster (Linux) |
| :--- | :--- | :--- |
| **How You Work** | **Interactive.** You open RStudio or PyCharm, click "run," and see results immediately on your screen. | **Batch-Oriented.** You write a script (`analysis.R`, `simulation.py`), write a job script to run it, and submit it to a queue. It runs later, out of sight. |
| **The Interface** | **Graphical (GUI).** You point and click. | **Command Line (CLI).** You type commands. This is the **only** way to access the HPC's power. |
| **Resources** | Limited to what's inside your laptop (e.g., 8 CPU cores, 16GB RAM). | Vast shared resources (1000s of CPU cores, Terabytes of RAM, multiple GPUs). You request what you need. |
| **File System** | Your local hard drive (`C:\`, `Macintosh HD`). | A massive, unified network filesystem. Your data is accessible from any node in the cluster. |
| **Primary Goal** | Convenience, development, debugging, visualization. | **Raw computational throughput.** Getting the maximum number of calculations done in the shortest time. |

**The Analogy:** Think of your laptop as a **personal workshop**. You have your own tools, you can work on one project at a time, and you're always right there with it. 
An HPC cluster is a **massive industrial factory**. You don't work on the factory floor; you send detailed blueprints (job scripts) to the foreman (scheduler), who assigns it to an available assembly line (compute node) where it's built. 
You come back later to collect the finished product (output files).


# Hands-On Practice: First Steps with the Linux Terminal

## Prerequisites: Setting Up Your Environment

Before you begin, ensure you have a terminal environment ready:

- **Linux/macOS Users:** Open the built-in **Terminal** application.
- **Windows Users:**
  - **WSL (Windows Subsystem for Linux):** Provides a full Linux environment
  - **Git Bash:** Offers basic Linux command functionality

## Exercise 1: Opening and Understanding Your Terminal

### Step 1: Launch Your Terminal
Open your terminal application. You should see a window with text, ending with a `$` symbol (or sometimes `%` or `#`).

### Step 2: Understand the Prompt
Look at the text before the `$`. This is called the **command prompt**. It typically shows: 
`username@hostname:current_directory$`

- **username:** Your login name on the system
- **hostname:** The name of the computer you're using
- **current_directory:** Your current location in the filesystem
- `~` is shorthand for your **home directory**

Example: `student@linux-lab:~$` means you're user "student" on computer "linux-lab" in your home directory.

## Exercise 2: Your First Commands

### Command 1: `whoami` - Discover Your Identity
Type the following and press Enter:

- `whoami`- Who am I logged in as? 
- `pwd` - Print Working Directory. "Where am I?"
- `ls` - List contents of the current directory.
- `ls -l` - List with more detail (permissions, owner, size). Introduce the concept of flags (-l).
- `clear or Ctrl+L` - Clear the terminal.


# Module 2: Navigating the Jungle - Files & Directories

## Objectives
- Understand absolute vs. relative paths
- Master navigation and file manipulation commands
- Practice safe file operations in Linux

### Understanding Paths

In Linux, paths are addresses to files and directories.

#### Absolute Paths
- Start from the root directory (`/`)
- Always begin with a forward slash `/`
- Example: `/home/projects/documents/report.txt`
- Unambiguous - always points to the same location

#### Relative Paths
- Relative to your current working directory
- Do not start with `/`
- Use special symbols:
  - `.` - current directory
  - `..` - parent directory
- Examples:
  - `./script.sh` (file in current directory)
  - `../project/data.csv` (file in sibling directory)

### Special Directory Shortcuts
- `~` (tilde) - Your home directory
- `.` (dot) - Current directory
- `..` (dot dot) - Parent directory
- `-` (dash) - Previous directory

### File Operations Warning
**Critical:** Linux command line doesn't have a "Trash" or "Recycle Bin." When you delete something with commands like `rm`, it's gone permanently. Be certain before deleting!

## Hands-On Exercises

### Prerequisites
- Open terminal on Linux/macOS, WSL, or Git Bash
- Start in home directory: `cd ~`

---

### Exercise 1: Navigation Practice

```bash
# Go to home directory
cd ~

# Check current location
pwd

#make a directory
mkdir username

# Go to directory (absolute path)
cd /username
pwd

# Return home
cd ~

# Go up one level
cd ..
pwd

# Return home
cd
```
### Exercise 2: Creating files and directories
```bash
# Create practice directory
mkdir linux_practice

# Enter the directory
cd linux_practice
pwd

# Create empty files
touch file1.txt file2.txt

# Create file with content
echo "Hello Linux World!" > greeting.txt

# View file content
cat greeting.txt

# Create subdirectory
mkdir subfolder
```
`
### Exercise 3: File manipulation
```bash
# List files in detail
ls -l

# Copy a file
cp greeting.txt greeting_backup.txt
ls -l

# Rename a file
mv file1.txt renamed_file.txt
ls -l

# Move file to subdirectory
mv file2.txt subfolder/

# Check subdirectory contents
ls -l subfolder/
```

### Exercise 4: File deletion
```bash
# Remove a file (be careful!)
rm renamed_file.txt
ls -l

# Try to remove directory (will fail)
rm subfolder/

# Remove empty directory
rmdir subfolder/

# Create and remove directory with content
mkdir test_dir
touch test_dir/temp_file.txt
rm -r test_dir/  # -r = recursive deletion
```
### Exercise 5: Path Practice
```bash
# Navigate home using tilde
cd ~

# Use relative path
cd linux_practice

# List parent directory contents
ls ..

# View file using absolute path
cat /home/$(whoami)/linux_practice/greeting.txt
```
### Challenge: 
Test your skills: 
1. Create this directory structure from your home directory-
```bash
~/projects/
└── linux_course/
    ├── notes/
    └── code/
```
2. Create a file in notes/ with content
3. Copy that file to code/
4. Rename the copy in code/
5. List projects/ contents without changing directories

---

# Module 3: The Magic of SSH - Connecting to Remote Machines

## Objectives
- Understand SSH and its importance in HPC environments
- Learn how SSH encryption and authentication work
- Practice generating SSH key pairs and connecting to remote servers
- Learn basic file transfer using SCP

###  What is SSH?

**SSH (Secure Shell)** is a cryptographic network protocol for operating network services securely over an unsecured network. 

- **Purpose:** Provides a secure channel over an unsecured network by encrypting all communication  
- **Common Uses:**
  - Remote command-line login
  - Remote command execution
  - Secure file transfer  
- **Why It Matters for HPC:** All interaction with supercomputers and clusters happens through SSH connections  

### The Client-Server Model

SSH uses a client-server architecture:

- **SSH Client:** Software you run on your local computer (built into Linux/macOS, needs installation on Windows)  
- **SSH Server:** Software running on the remote machine (HPC cluster, cloud server, etc.)  
- **Connection Process:** Your client connects to the server, negotiates encryption, and authenticates you  

### Authentication Methods

#### Password Authentication
- You enter a password to connect  
- Simple but less secure (vulnerable to brute-force attacks)  
- Not typically used on HPC systems  

#### SSH Key Authentication (Recommended)
- Uses a pair of cryptographic keys:
  - **Private Key:** Stored securely on your local machine (never shared!)  
  - **Public Key:** Copied to the remote server(s) you want to access  
- More secure and convenient (no password typing)  
- Standard method for HPC access  

## Hands-On Exercises

### Prerequisites
- Linux/macOS: Open Terminal  
- Windows: Use WSL, Git Bash, or PowerShell with SSH client  
- For practice, we will use a test remote server or localhost  

---

### Exercise 1: Generate SSH Key Pair

```bash
# Check if you already have SSH keys
ls -la ~/.ssh/

# Generate a new ED25519 key pair (recommended)
ssh-keygen -t ed25519 -C "your_email@example.com"

# Or if your system does not support ED25519, use RSA:
# ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
````

**Follow the prompts:**

* Press Enter to accept default file location (`~/.ssh/id_ed25519`)
* Enter a secure passphrase (recommended for extra security)
* Confirm your passphrase

**Expected Output:**

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/username/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/username/.ssh/id_ed25519
Your public key has been saved in /home/username/.ssh/id_ed25519.pub
```

### Exercise 2: Examine Your SSH Keys

```bash
# List your SSH files
ls -l ~/.ssh/

# View your public key (this is what you share with servers)
cat ~/.ssh/id_ed25519.pub

# The private key should have strict permissions
ls -l ~/.ssh/id_ed25519
```

**Important:** Your private key should have permissions `-rw-------` (read/write for owner only). If not, fix them:

```bash
chmod 600 ~/.ssh/id_ed25519
```

### Exercise 3: Practice SSH Connection

#### Option A: Connect to a Practice Server

```bash
# Replace with your instructor's server details
ssh username@training-server.example.com

# If using a non-standard port (not 22):
# ssh -p 2222 username@training-server.example.com
```

#### Option B: Connect to Your Own Machine (Localhost)

```bash
# For Linux/macOS/WSL users - SSH into your own machine
ssh localhost

# If you get "connection refused," you may need to install SSH server:
# sudo apt update && sudo apt install openssh-server  # Ubuntu/Debian
```

### Exercise 4: SSH Configuration File (For Efficiency)

Create or edit `~/.ssh/config` to simplify connections:

```bash
# Create or edit the config file
nano ~/.ssh/config
```

Add these lines (customize for your servers):

```config
# Training server
Host training
    HostName training-server.example.com
    User your_username
    Port 22
    
# University HPC cluster
Host hpc
    HostName hpc.university.edu
    User your_campus_username
    IdentityFile ~/.ssh/id_ed25519
    
# Personal server
Host myserver
    HostName 123.45.67.89
    User admin
    Port 2222
```

Now you can connect using just the shortcut:

```bash
ssh training  # Instead of full command
```

### Exercise 5: Copy Files with SCP (Secure Copy)

```bash
# Copy local file to remote server
scp localfile.txt training:~/remote-folder/

# Copy remote file to local machine
scp training:~/remote-file.txt ./local-folder/

# Copy entire directory (recursively)
scp -r local-dir/ training:~/remote-dir/
```

### Exercise 6: Exit SSH Sessions

```bash
# Any of these will work:
exit
logout
Ctrl+D
```

## Challenge 

1. **Set up passwordless login** to your practice server by copying your public key:

   ```bash
   # Method 1: Using ssh-copy-id (easiest)
   ssh-copy-id training

   # Method 2: Manual (if ssh-copy-id is not available)
   cat ~/.ssh/id_ed25519.pub | ssh training "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
   ```
2. **Transfer a practice file** to the server, then SSH in and verify it arrived
3. **Create an SSH config entry** for your university's HPC system (if you have access)

## Troubleshooting Common SSH Issues

| Problem                            | Possible Solution                                                                |
| ---------------------------------- | -------------------------------------------------------------------------------- |
| "Permission denied (publickey)"    | Make sure your public key is in `~/.ssh/authorized_keys` on the server           |
| "Connection refused"               | Check if the server is running SSH on port 22, or specify correct port with `-p` |
| "Too many authentication failures" | Use `ssh -o IdentitiesOnly=yes server` to specify which key to use               |
| "Host key verification failed"     | Remove the offending key from `~/.ssh/known_hosts` or update it                  |




