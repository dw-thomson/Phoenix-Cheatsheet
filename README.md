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
- to run a slurm script, 
```bash
sbatch script.sh
```
example slurm header 1
- script.sh
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

# Screen, bug fix
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

# mounting HPC to mac using sshfs
- on mac
download macfuse and sshfs
https://macfuse.github.io/

```bash
mkdir /tmp/sshfs
cd /tmp/sshfs
# sshfs username@remote:/remote/directory /mount/point
sshfs a1205810@phoenix-login1.adelaide.edu.au:/home/a1205810/projects/SAGCQA1671_ATACseq_Geri/nfATACseq/outs_nfATACseq /tmp/sshfs
```
- I got a warning
```
Please open the Privacy & Security System Settings and allow loading system software from developer "Benjamin Fleischer". A restart might be required before the system extension can be loaded.

Then try mounting the volume again.
```
- needed to follow prompts to change system settings but it worked fine.

- need to unmount after use
```bash
sudo umount -f /tmp/sshfs
```

# Directory Structure
- useful resource <https://wiki.adelaide.edu.au/hpc/Phoenix_data_management>
- where to write jobs on phoenix?
```
1	/home/<userid> 10GB
2	/hpcfs/users/<userid> 1TB
3 /hpcfs/groups/<groupid> 60TB
4	/uofaresstor/<group> 100TB
```
- not meant to use /home for running jobs, this should be on /hpcfs/
- /uofaresstor/ is a mounted drive, can't run jobs from here

# disk quotas
 
- disk usage, 
- this command is more specific than using 'df' or 'du' 
```bash
(base) [a1205810@p2-log-1 ~]$ rcdu
========================================================================================================================
Disk usage for  Daniel Wyville Thomson (a1205810)
------------------------------------------------------------------------------------------------------------------------
  /home :                         4.28 TiB of   12.00 GiB ( 36550.5% )
  /hpcfs (group quota) 
    a1205810 :                   497.62 MiB of 1000.00 GiB (  0.0% )              208 of    1100000 files (  0.0% )
    phoenix-hpc-achinger :           35.64 TiB of   58.59 TiB ( 60.8% )           215411 of   67584000 files (  0.3% )


 /hpcfs quota are not tied to a specific folder but instead linked to the above-mentioned groups. If one of your group 
quota is over the limit but it shows discrepancy with specific folder, please follow this wiki link for 
instructions
 https://wiki.adelaide.edu.au/hpc/Disk_usage_discrepancy   
========================================================================================================================
```
Managing Disk quotas
- since 'disk quota's are calculated by 'group' permissions
- so changing the 'group' can move the quota away from an individuals quote (1TB) to the groups (60TB), eg
```bash
chmod -R phoenix-hpc-achinger [directory]
```
but nonetheless, files will need to be deleted to keep under disk space quota.
```bash
find ./ -size +10G | xargs rm
```
- I will routinely delete all temp files and often all large files I don't immediately need (e.g. bams).

# Data storage R drive
moving data from R drive, scp can be really slow, can run from screen but can't sent up a slurm script. Also it seems like soft links don't work
the best way I've found for moving large volumes from R drive is filesender
e.g.
```bash
python /home/a1205810/bin/filesender/filesender.py -v -u [user email] -a [apikey] -r [recipient email] [file]
```
file sender needs to be set up beforehand. (I have a git repor for that.)



