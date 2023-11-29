# Running on Cheyenne (NCAR)

**Amanda Triplett **

**Original: **12/2019

**Update: **08/2020


## Description

** **This gives an overview with examples of how to remotely access HPC resources at NCAR (specifically Cheyenne), update your bash profile and modules, an example job_script with the commands Cheyenne needs to run, how to run and check your job as well as useful information to transfer and edit your files. 


## Software

No specific software is needed, you do need an account with NCAR and an HPC time allotment in order to access resources. Everything can be done through your terminal. 


## Accessing and logging in to Cheyenne 



    Go to your scratch folder

    4. Every user starts with a scratch folder under their username, once you navigate here, you can create file directories just as you do on your own computer
        4. cd /glade/scratch/”your_username”
        5. Mkdir “new_directory_name”


## Updating your bash profile and loading modules for Parflow


1. Make any necessary updates to your bash profile, directions below are required if you want to run ParFlow - you should only need to do this **once** and make sure you go to your scratch folder. The first time you do it you will have to either source your bash profile or log out then back in.
**first**
    5. `vim ~/.bash_profile`
    6. `export PARFLOW_DIR=/glade/p/univ/ucsm0002/parflow3.7_0826_2020/parflow`
    7. `export HYPRE_DIR=/glade/p/univ/ucsm0002/hypre/2.10.1`
    8. `export SILO_DIR=/glade/p/univ/ucsm0002/silo/4.10.2`
    9. `source ~/.bash_profile`

     2. Make sure correct modules are loaded



1. _Note:_ This may not always be necessary to do manually, but if you get errors such as mpi not loading correctly, load all the modules and see if that solves the problem. You **do **have to re-load the modules each time.
2. Type command: `module list` 
        a. You’ll probably see something like below if you haven’t made any changes:
        b. _Currently Loaded Modules:_
            1. _1) ncarenv/1.3   2) intel/18.0.5   3) ncarcompilers/0.5.0   4) mpt/2.19   5) netcdf/4.6.3_

3. Load the modules you need (example. below) 

        c. aktriplett@cheyenne4:/glade/scratch/aktriplett> module load cmake
        8. aktriplett@cheyenne4:/glade/scratch/aktriplett> module load nco
        e. aktriplett@cheyenne4:/glade/scratch/aktriplett> module load ncl
        f. aktriplett@cheyenne4:/glade/scratch/aktriplett> module load ncview
        g. aktriplett@cheyenne4:/glade/scratch/aktriplett> module load R
        h. aktriplett@cheyenne4:/glade/scratch/aktriplett> module list

4. After you load the modules your list will look like this: 


        Currently Loaded Modules:


        1) ncarenv/1.3    3) ncarcompilers/0.5.0   5) netcdf/4.6.3   7) nco/4.7.9   9) ncview/2.1.7


        2) intel/18.0.5   4) mpt/2.19              6) cmake/3.14.4   8) ncl/6.6.2  10) R/3.6.0


## Job Scripts

Cheyenne uses PBS for job scheduling. See general info

## Checking your Core Hour Usage

1. Follow this link to the Systems Accounting Manager (SAM) system 
    1. [Sam.ucar.edu](https://sam.ucar.edu/app/home)
    2. You can also look at this link for additional information about what is available on SAM 
        1. [https://www2.cisl.ucar.edu/user-support/systems-accounting-manager](https://www2.cisl.ucar.edu/user-support/systems-accounting-manager)
2. Log-in to the SAM system using your regular log-in info, you will have to authenticate with Duo push as well
3. One logged in, you can go to the reports tab to track your usage activity, it will look something like this:


![cheyenne usage hours](../../images/cheyenne_usage.png)


## Installing icommands on Cheyenne 

This is likely deprecated, use at your own risk (Ben West)

It is possible to install icommands on Cheyenne without any additional privilege in your own directory. _Icommands if NOT natively installed on Cheyenne unlike UAHPC. _Detailed can be found here, [https://wiki.cyverse.org/wiki/display/DS/Setting+Up+iCommands#SettingUpiCommands-co](https://wiki.cyverse.org/wiki/display/DS/Setting+Up+iCommands#SettingUpiCommands-co)



1. Download the installer to your local computer (links can be found in [https://wiki.cyverse.org/wiki/display/DS/Setting+Up+iCommands#SettingUpiCommands-co](https://wiki.cyverse.org/wiki/display/DS/Setting+Up+iCommands#SettingUpiCommands-co)) and upload to your directory in Cheyenne.
    1. Download the **ubuntu 14 version for unprivileged users**
2. In your Cheyenne directory with the installer, type or copy command:

    ```
    sh irods-icommands-4.1.10-ubuntu-14.installer
    ```



    Then you will see:

            Where would you like to install it? [/home/cyverse-user]
            Expanding contents under /home/cyverse-user.
            Updating .bashrc
            done!


3. To make the changes take effect in the current shell, you will need to source your .bashrc file (`vim ~/.bash_profile`). The following lines should be added:


```
# iRODS iCommands support
export  IRODS_PLUGINS_HOME=/glade/u/home/yourusername/icommands/plugins/
export PATH=/glade/u/home/yourusername/icommands:$PATH

```



4. Open a new terminal window, if necessary, and run `ienv` to check that the new version was installed.
5. initialise your account:

        _iinit_


        [data.cyverse.org](http://data.cyverse.org/)


        Port: 1247


        User: yourusername


        Zone: iplant


        Then enter your cyverse password

6. Then you can start use icommands.


## Running with Python on Cheyenne

* General instructions of how to get running with python can be found at: 
    * [https://www2.cisl.ucar.edu/resources/python-–-ncar-package-library](https://www2.cisl.ucar.edu/resources/python-–-ncar-package-library)

- In order to install your own packages, you will have to create your own virtual environment after following the steps in the above page and then activate as follows: 

	- NOTE: You do not have permission to write in the ncar virtual env, so to install packages not included there, you have to create your own

       `ncar_pylib -c 20201220 /glade/work/$USER/my_npl_clone`


```
    ncar_pylib my_npl_clone
    pip --no-cache-dir install PACKAGE_NAME

```



* If your virtualenv stops working, you may have to delete it and create a new one, just follow the above steps again and you should be good


## Building a New Version of ParFlow on Cheyenne (08/2020) from MM at CSM. 

Use this for reference on module loading at the very least, Parflow blog can be used to suplement if any of the links to dependencies are deprecated.

_Adding specific ParFlow Builds to your bash profile. This is an example for a specific version, you need to add the path to the version you want to use after export:_

`vim ~/.bash_profile`.


```
export PARFLOW_DIR=/glade/p/univ/ucsm0002/parflow_build/parflow #(this is the folder where you will put the new version of Parflow)
export HYPRE_DIR=/glade/p/univ/ucsm0002/hypre/2.10.1
export SILO_DIR=/glade/p/univ/ucsm0002/silo/4.10.2
```


_Build instructions_

_Please load the following modules:_


```
module load impi
module load intel/17.0.1
module load cmake
```


To see which modules are available, use command `module avail`. To check which modules you have loaded, you can use  `module list`. For me, this returns:


```
Currently Loaded Modules:
1) ncarenv/1.3    3) ncarcompilers/0.5.0   5) cmake/3.14.4       7) nco/4.7.9      9) netcdf/4.6.3
2) intel/17.0.1   4) impi/2017.1.132       6) ncl/6.6.2    (H)   8) ncview/2.1.7  10) R/3.6.0
```


I was able to follow the typical installing directions for Silo and Hypre (from the parflow blog) and set them in my bash profile. 

Then just follow the cmake workflow for installing parflow:

`cd ~/glade/p/univ/ucsm0002/parflow_build     `(or wherever)


```
git clone -b master http://github.com/parflow/parflow.git 
```


`export PARFLOW_DIR=~//glade/p/univ/ucsm0002/parflow_build/parflow  `(or wherever. Add to bash_profile.)


```bash
cd $PARFLOW_DIR
mkdir build
cd build

cmake .. -DCMAKE_INSTALL_PREFIX=$PARFLOW_DIR -DPARFLOW_AMPS_LAYER=mpi1 -DPARFLOW_ENABLE_TIMING=TRUE -DHYPRE_ROOT=$HYPRE_DIR -DSILO_ROOT=$SILO_DIR -DPARFLOW_HAVE_CLM=ON -DPARFLOW_AMPS_SEQUENTIAL_IO=TRUE

make
make install

```
