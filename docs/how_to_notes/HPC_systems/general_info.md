## How to interact with HPC
Once you have an account you can ssh into a terminal instance as such (example for Cheyenne)

    Open your terminal and type the following: 

    1. ssh -Y -l “your_username”@cheyenne.ucar.edu (other HPCs would have a different address after the @)
        1. _Note:_ Macs often need the -Y and -l commands
        2. If it asks if you want to continue connecting, say yes
    2. Enter your password and your 2FA token if necessary
        3. You probably already set up duo when you set up your account, look on NCARs website for Cheyenne or call the help desk
    3. You’re logged in!

For writing code it is recommended to use the ![remote explorer extension](https://code.visualstudio.com/docs/remote/ssh) in vscode. This will let you edit files on the machine via a vscode window on your machine. You can then run these files however your HPC requires.

## Where to do your runs
If possible, it is preferred to setup your runs in a folder owned by the group you belong to, not your personal scratch directory. This is to make collaboration and the eventuality of handing off your model easier. It WILL be harder than you think to move from your personal storage to the group folder if you want to make that change down the line.

Additionally, HPC systems often periodically wipe the data on them. It is advised to be familiar with what that policy is, and to run frequent backups using something like globus.

## Large file transfers
The recommended tool for moving data in and out of NERSC for large transfers
[http://www.globus.org/](http://www.globus.org/) or [http://globus.nersc.gov/](http://globus.nersc.gov/)
See also the group notes on [Globus](../data_and_file_transfers/globus.md)

User scratch directory: `/global/cscratch1/sd/yourusername`

Some HPCs do not have Globus available in which case you will need to determine what tool is best. I have found Globus to be the easiest option though when it is available (Ben West)

If you cannot use these tools SCP or potentially Icommands can be used. Icommands instructions will need to be googled, the ones in this doc are deprecated.

## Conda
It is recommended to use conda environments on HPC for both collaboration and to minimize the chance your build spontaneously breaks (no promises). You may have to check if your HPC requires a particular distribution or that you use a version it has already installed.

![anaconda instructions](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html)

Look at Conda Environments: `conda env list`

Create Conda Environments: `conda create --name -myenv`

Activate Conda Environments: `conda activate` and `conda deactivate`

Alternatively one may use venv or virtualenv for your environment needs.

## Creating and monitoring jobs 
HPCs track the compute hours people use out of a pre-allocated amount. This means any serious compute (running models or analyzing results) will need to be done via the submission of a job.

Most HPCs use a technology called slurm to manage any jobs (instances of compute you request). Below are some useful links, but also make sure to google "slurm + name of your hpc" because it may have minor differences between machines in terms of the settings available. 

[https://docs.nersc.gov/jobs/#submitting-jobs](https://docs.nersc.gov/jobs/#submitting-jobs)
[https://docs.nersc.gov/jobs/#monitoring-jobs](https://docs.nersc.gov/jobs/#monitoring-jobs)

- `sbatch`: submit a batch script
- `sacct`: display accounting data for jobs and job steps
- `salloc`: request nodes for an interactive batch session
- `srun`: launch parallel jobs
- `scancel`: delete a batch job
- `sqs`: NERSC custom queue display with job priority ranking info
- `squeue`: display info about jobs in the queue
- `sinfo`: view SLURM configuration about nodes and partitions
- `scontrol`: view and modify SLURM configuration and job state

Some HPCs we use use something called PBS instead. See

[Tutorial](https://emleddin.github.io/comp-chem-website/UNIXguide-PBS.html)

[Scribd article (with annoying ads but useful free content)](https://www.scribd.com/document/359446973/PBS-Queue-Commands)

and for an example job submission script:

```bash
# Your job will use 1 node, 8 cores, and 48gb of memory total.

#PBS -q standard

#PBS -l select=1:ncpus=16:mem=48gb:pcmem=6gb

### Specify a name for the job

#PBS -N test

### Specify the group name

#PBS -W group_list=lecondon

### Walltime is how long your job will run

#PBS -l walltime=00:50:00

### Joins standard error and standard out

#PBS -j oe
```


cd /home/u18/lecondon/Test/washita/tcl_scripts

tclsh Dist_Forcings.tcl

tclsh LW_Test.tcl


## Debug queue

The debug queue is a high-priority queue with a short runtime that is used to debug your runs before sending them to the standard or other queue. More information can be found [here](https://docs.hpc.arizona.edu/display/UAHPC/Allocation+and+Limits) for UA HPC. All HPCs should have a debug queue available.

## We recommend you use the parflow blog if you can instead of this page
[The Parflow blog](http://parflow.blogspot.com/) has install instructions for many systems. This is the recommended path for now as it is updated most often.

