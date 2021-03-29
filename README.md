Installation and running disBUSCO

(1) To install the original BUSCO follow the instruction from website: 

https://busco.ezlab.org/busco_userguide.html

conda create -n busco406 -c conda-forge busco=4.0.6

(2) The experiment version of BUSCO is 4.0.6. After installation, to use the anaconda to activate the environment:

conda activate busco406

cp ~/.conda/envs/busco406/config/config.ini ~/ .conda/envs/busco406/config/config.ini-bak

vi ~.conda/envs/busco406/config/config.ini

Then, modify the tblastn and augustus command to :

command = tblastndis

command = augustusdis

(3) To install the tbalstndis and augustusdis to the BIN directory:

cp tblastndis augustusdis .conda/envs/busco406/bin/

(4)To install the modified Toolset.py and BuscoTools.py to the busco lib directory:

cp BuscoTools.py Toolset.py ~/ .conda/envs/busco406/lib/python3.6/site-packages/busco/

(5) Preparing a hostfile named buscohosts.txt, to specify the hostname for program to run. example:

vi buscohosts.txt:

node01

node01

node01

node01

node02

node02

node02

node02

it means the job will run on node01 and node02, and each node with 4 processes. node01 and node02 must share the directory through nfs or lustre filesystem.

(6) Using the same operating method as BUSCO to run disBUSCO, for example:

busco -i na12878-rel6-flye-racon4.fasta -m geno --cpu 36  -l ~/busco/busco_downloads/lineages/primates_odb10  -o contigs.busco  --offline|tee out.log

(7) This is a slurm job script for a supercomputing environment:

conda activate busco406



vi disbusco.sh

#!/bin/bash

#SBATCH –o %j.out

#SBATCH -J disBUSCO

#SBATCH --nodes=4   #use 4 nodes

#SBATCH --ntasks-per-node=36   #use 36 cores(tasks) per node

srun hostname>./buscohosts.txt

busco -i na12878-rel6-flye-racon4.fasta -m geno --cpu 36  -l ~/busco/busco_downloads/lineages/primates_odb10  -o contigs.busco  --offline|tee out.log



sbatch disbusco.sh



PS: if you encounter errors like “OSError: [Errno 24] Too many open files”, you need to edit the file /etc/sysctl.conf for each node, to add the following configure:

fs.file-max = 100000

