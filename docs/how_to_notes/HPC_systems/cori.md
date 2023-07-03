# Cori
**Jun Zhang, 04/24/2019**

## Description
  Running notes with links and tips for running ParFlow on Cori. Feel free to update and add to this document as needed. 


## Useful links
Getting started: [http://www.nersc.gov/users/computational-systems/cori/](http://www.nersc.gov/users/computational-systems/cori/)
Slides for a short training course are available at Reference Docs/Cori_Training_course

## obtaining an account 
** Sep/2021 **
Apply an account according to the instructions [here](https://docs.nersc.gov/accounts). Obtain the project number before applying the account.

** Mar/2019 **
Ask Laura to create a username for you at the NERSC, you will get an email to submit an application. Once they approve it, a second email will be sent to active the account (have to be activated within 72 hours).

## Logging in 
1. Before you can login any host, you have to create a MFA token for your accounts, instructions are available in [https://docs.nersc.gov/connect/mfa/](https://docs.nersc.gov/connect/mfa/) 
2. Log onto cori with you NERSC username, password and OTP(one-time password) from your token. Our repo number is m2511 (this project is no longer activated).
`ssh username@cori.nersc.gov`
3. The CONUS project directory is (this project is no longer activated).
	`/project/projectdirs/m2511`
4. You should change your default shell to bash and you can do it by logging into  nim.nersc.gov. In the ‘Profile’ tab, scroll down to ‘Server Login’, click the ‘Edit’ option of ‘cori’, choose ‘/bin/bash’ in the ‘Login Shell’.
Transferring files
Use scp to transfer files between your local machine and cori for small transfer.
`scp your/local/file username@cori.nersc.gov:your/cori/directory`

## Globus
 the recommended tool for moving data in and out of NERSC for large transfers
[http://www.globus.org/](http://www.globus.org/) or [http://globus.nersc.gov/](http://globus.nersc.gov/)
See also the group notes on [Globus](../data_and_file_transfers/globus.md)

User scratch directory: `/global/cscratch1/sd/yourusername`

## Setting environments
[https://docs.nersc.gov/environment/](https://docs.nersc.gov/environment/) NOTE that you should not modify your bash_profile directly. Instead you make changes to .bash_profile.ext (mine is here: /global/homes/j/jzhang55). Also note that your home directory is the same across Cori and Hopper and you can put settings in this file that are specific to what machine you are running on. 
You should add PARFLOW_DIR in your .bash_profile.ext as following:

`export PARFLOW_DIR=/global/project/projectdirs/m2511/parflow/cori-v3.7.0-11-gf70ce58-2021-07-14`
Running scripts
[https://docs.nersc.gov/jobs/](https://docs.nersc.gov/jobs/)
You cannot run on your own directory, have to submit a job to the queue. Check out the queue policies because they have different ‘charge factors’ for different queues:
[https://docs.nersc.gov/jobs/policy/](https://docs.nersc.gov/jobs/policy/)
You need a job script to specify your job description, and use sbatch to submit the job. The script examples can be found [https://docs.nersc.gov/jobs/examples/](https://docs.nersc.gov/jobs/examples/) and also at the end of this file.
If you prepare your job script or input files in your local computer, it may result in error (Batch script contains DOS line breaks (\r\n). Use command `dos2unix yourfilename.`

Cori also provides an online job script generator, which is easy to use and can be found in MyNERSC -> Jobs -> Jobscript generator [https://my.nersc.gov/script_generator.php](https://my.nersc.gov/script_generator.php)

## To run parflow
Use tclsh on the command line before submitting the job to the queue. 

Job script example

```bash
#!/bin/bash -l
#SBATCH --qos=debug
#SBATCH --nodes=2
#SBATCH --constraint=haswell   
#SBATCH --time=00:10:00    {the maximum running time}
#SBATCH --job-name=test
#SBATCH --account=m3780

export PARFLOW_DIR=/global/project/projectdirs/m2511/parflow/cori-v3.7.0-11-gf70ce58-2021-07-14
source $PARFLOW_DIR/setenv.sh   {these two lines are required to run}

cd /global/cscratch1/sd/jzhang55/test/    {cd your directory}
srun -n 64 $PARFLOW_DIR/bin/parflow test    {srun -n 64 means you request 64/32=2 nodes to run, so #SBATCH --nodes=2}
```

## Monitoring your job
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
