# Phoenix-cheatsheet
- setup
register for an account via https://www.adelaide.edu.au/technology/research/high-performance-computing/phoenix-on-premise-hpc

- there is a getting started guide for phoenix
https://wiki.adelaide.edu.au/hpc/Getting_Started_Guide

- logging in

```bash
ssh a1205810@phoenix-login1.adelaide.edu.au
```

- I set up an alias which I put in my ~/.bashrc

```bash
# shortcut to Phoenix HPC
alias phoenix='ssh a1205810@phoenix-login1.adelaide.edu.au'
```
# Slurm job submission

example slurm header 1
```bash
#!/bin/bash
#SBATCH -p batch        	                                # partition (this is the queue your job will be added to) 
#SBATCH -N 1               	                                # number of nodes (use a single node)
#SBATCH -n 1              	                                # number of cores (sequential job => uses 1 core)
#SBATCH --time=01:00:00    	                                # time allocation, which has the format (D-HH:MM:SS), here set to 1 hour
#SBATCH --mem=4GB         	                                # specify the memory required per node (here set to 4 GB)
#SBATCH --partition=icelake					# other options include a100cpu,saigenci_normal. These need to be granted access to	

# Execute the program (Here, the program is sequential)
./my_sequential_program  	                                # your software with any arguments

```

# partitions
- which slurm queue should I submit to?

```bash
sinfo
```
I currently have access to the following partitions
```
a100cpu, icelake
```
#SBATCH --partition=saigenci_normal

# I made an squeue alias 'sq' to change formatting
function sq() {
 squeue -u a1205810 --all --format='%.10i %.15N %.20P %.15u %.10T %.12M %.12l %.6D %.5C %.10c %.10m %.10r %.j' $* | column
}
- I put this in my ~/.bashrc
- this prints out progress of nextflow jobs nicely

## Screen, bug fix
- Screen was crashing, not writing to /var/run/screen/, I found a workaround which changed the environment variable to have screen directory elsewhere
- I put this in ~/.bashrc
```bash
mkdir ~/.screen && chmod 700 ~/.screen
export SCREENDIR=$HOME/.screen
```
to run screen

```bash
screen -S [newsession]
```
