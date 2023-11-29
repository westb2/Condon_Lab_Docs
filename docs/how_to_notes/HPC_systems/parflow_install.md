
# ParFlow Install

**_Laura Condon, 2/13/19_**

## We recommend you use the parflow blog if you can instead of this page
[The blog ](http://parflow.blogspot.com/) has install instructions for many systems. If you can use a resource there to help you it is updated more frequently. This page has been left up in case any of the dependency links are platform dependent (as in if you hypotheticall need to download and build a specific version of MPI on Ocelot for Parflow), but it is only recommended to use it as reference to debug why your install is not working.


## Description
  A running set of notes on ParFlow installs for various platforms. Please update this with your experiences either by adding comments or inserting text.  Please Add new installs to the top of this doc and use headings so that they can be included in the table of contents. 

**NOTE: If you are installing on a mac refer first to the ParFlow blog [http://parflow.blogspot.com/](http://parflow.blogspot.com/)  and look for the latest version in the compiling section which corresponds to your operating system. **

**Links: **You should also refer to the ParFlow blog for additional resources [http://parflow.blogspot.com/](http://parflow.blogspot.com/) 

## UA-HPC Ocelote 

**Notes from Laura Fall 2018**

## Contacts

Ric Anderson: ric@email.arizona.edu

Chris Reidy: chrisreidy@email.arizona.edu

## Software install request form

[https://it.arizona.edu/service-request-forms](https://it.arizona.edu/service-request-forms)

They built Hypre and silo and these are available as modules

## Module commands

**[https://docs.hpc.arizona.edu/display/UAHPC/Accessing+Software](https://docs.hpc.arizona.edu/display/UAHPC/Accessing+Software)**

    Module list  (gives loaded modules)

    Module avail (gives list of all modules)

    Module show NAME (gives info on a specific module


## Building ParFlow

`ssh_ _username@hpc.arizona.edu`

Download ParFlow

`git clone https://github.com/parflow/parflow`

Rename this directory to whatever you want

Setup environment

`vi ~/.bashrc`

Add the following lines to the user specified portion:

    module load gcc

    module load hypre

    module load silo

    export PARFLOW_DIR=/home/PATH_TO_YOUR_PARFLOW_DIR

    PATH=$PATH:$PARFLOW_DIR/bin

`source ~/.bashrc`

## Build ParFlow

    cd pfsimulator

    ./configure  --prefix=$PARFLOW_DIR --with-amps=mpi1 --with-clm --with-hypre=$HYPRE_BASE --with-silo=$SILO_BASE --with-amps-sequential-io

    make -j 14

    make install

    Build PFtools

    cd ../pftools

    ./configure  --prefix=$PARFLOW_DIR --with-amps=mpi1 --with-clm --with-hypre=$HYPRE_BASE --with-silo=$SILO_BASE --with-amps-sequential-io

    make -j 14

    make install

    Run tests

    cd ../test

    make check


## MacOS Mojave 

**These notes are from Nick Engdahl 2/12i/2019:**

 This post will describe a “typical” ParFlow installation, that is one that uses the most common options and configurations on macOS using the default bash shell. We’ll primarily use the command line for this so go ahead and open a terminal window (if you don’t know where terminal is use spotlight search).

 

### Preliminaries

For commands entered into the terminal window I’ll use the following convention where the command is preceded with “_>>_” for example:
 

_>> make                   	_{and here’s an example comment/note you <span style="text-decoration:underline;">don’t</span> type}

 

_Anything that follows in curly brackets _{ }_ is just a note or comment and is NOT to be entered._

 

OK, first thing’s first. We need to define a few environment variables for the installation. Navigate to your home folder and create (or edit) your bash_profile:

```
>> cd ~

>> vim .bash_profile
```

In vim, hit “i” to enter insert mode and add the following lines, preferably at the bottom:

```
export PARFLOW_DIR=~/ParF/parflow

export HYPRE_DIR=~/ParF/hypre

export SILO_DIR=~/ParF/silo

export CC=clang

export CXX=clang++

export FC=gfortran

export F77=gfortran
```
 

then press “escape” followed by “:” then “x” to save the changes and exit. To apply the changes:

`>> source .bash_profile`

 

This has now defined some environment variables and shortcuts we’ll take advantage of later on. Next, we’ll create a directory to hold all the various bits that comprise a typical installation. From your home directory, make a folder called “ParF” and enter it:

 
```
>> cd ~                                	{only necessary if you’ve navigated out of home}

>> mkdir ParF

>> cd ParF
```
 

There are 6 “parts” to these instructions and we’re going to handle each separately: 1) install gcc/gfortran, 2) install cmake, 3) install OpenMPI, 4) Install Silo, 5) Install Hypre, 6) install ParFlow. Everything except step 6 are supporting libraries or programs that ParFlow needs for our typical installation.

 

Step 1) Installing gfortran and gcc

Truth be told we only really need gfortran but gcc is nice to have, and since they’re often bundled it makes sense to knock both out. You could try building them from source code, but the easier way is to use a binary that is pre-built for MacOS. Those can be downloaded here: [http://hpc.sourceforge.net](http://hpc.sourceforge.net)

 

The option you want is usually the first entry right below the word “Binaries” after the “big” paragraph. Today, for me that is “gcc-8.1-bin.tar.gz” and clicking on that link put it in my “Downloads” folder. You don’t need to bother with the “gfortran” link because it’s already included in this one. In your terminal window, we first need to enable compiling programs on your mac. This is necessary after every major MacOS upgrade before you can compile new programs (old one will be fine though).

 

`>> sudo xcode-select --install`

 

A confirmation window will likely open and after you OK through all of that, we’re done with that. You might also see a message saying command line tools are already installed, which is also fine.

 

Next, navigate to wherever you put that download and extract it to /usr/local with:

 
```
>> cd ~/Downloads              	{assuming your Download went here}

>> sudo tar -xvf gcc-8.1-bin.tar -C /
```

A lot of text will fly by then you’ll get a command prompt. To make sure this has copied correctly, at the command prompt type:

 

`>> gfortran -v`

And you should get something like this:

```
Using built-in specs.

COLLECT_GCC=gfortran

COLLECT_LTO_WRAPPER=/usr/local/libexec/gcc/x86_64-apple-darwin17.5.0/8.1.0/lto-wrapper

Target: x86_64-apple-darwin17.5.0

Configured with: ../gcc-8.1.0/configure --enable-languages=c++,fortran

Thread model: posix

gcc version 8.1.0 (GCC)
```

There is one last critically important step we need to take that was not necessary in the past. We need to install the package in MacOS that has some of the libraries we need in order to compile programs. These are located in “/Library/Developer/CommandLineTools/Packages/” and the easiest way to get there is to open a finder window, press command+shift+g, then paste in that path. There is only one package in this folder (macOS_SDK_headers_for_macOS_10.14.pkg), though its name might change, and to install it just double click on it and follow the instructions. I would also suggest you run “sudo --xcode-select --s /Library/Developer/CommandLineTools” to make sure the system is pointing to the right version. Apple may be depreciating the package in future macOS releases, but we’ll update you if/when that happens.


Step 2) Installing cmake

Past installation instructions have used the GNU autoconfig system. We’ll still use that for some of the supporting components, but ParFlow itself is now built using the CMake interface. This isn’t installed on your mac by default, so we’ll start by adding it.

 

We’re going to download the latest version of the CMake source. There is also a GUI for CMake but we’re not going to use it. Before proceeding, check here: [https://cmake.org/download/](https://cmake.org/download/) for the latest version. On the download page, go ahead and click on the link for the archive in the Unix/Linux source box and the download should complete shortly. Assuming the archive is in your ~/Downloads/ folder, we’ll copy it into ParF, the expand the archive:  As of this writing that file is “cmake-3.14.0-rc1.tar.gz”


To move that download directly into the ParF folder, in your terminal window, be sure you’re in the ParF directory, then type:

 
```
>> cd ~/ParF/

>> cp ~/Downloads/cmake-3.14.0-rc1.tar .

>> tar -xvf cmake-3.14.0-rc1.tar
```
 

Note the period at the end of the second line. You will need to update the file name as newer versions of CMake are released.

 

Now we’ll enter the new directory and start building using cmake’s helper:

 
```
>> cd cmake-3.14.0-rc1

>> ./bootstrap                      	{This will take a while}

>> make                               	{This will also take a while}

>> sudo make install             	_
```

At the end of the make process you’ll see percentage signs on the left edge of your screen and if it makes it to 100 without errors, you’re all set.

*From Jun:

When installing `cmake`, on the step of `./bootstrap`, sometimes it will pop up an error message that ‘Unable to find any JVMs matching version’, just follow the instruction to install the up-to-date JAVA’

 

Step 3) Installing OpenMPI

OpenMPI is the message passing interface we use for ParFlow (note that this is not the same as OpenMP). This is also what makes it parallel so we’ll do this step next. Navigate back to your ParF directory:


`>> cd ~/ParF`

Next we’re going to download openmpi. The simplest way to do that is from the command line using the “curl” command, which is like “wget” for a mac. However, before doing so you might want to check to make sure you’re downloading the most recent version at “https://www.open-mpi.org/software/ompi/”

You’ll need to modify the version numbers if using a more recent release but otherwise it’ll be just like this:

 

`>> curl “https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz” -o “openmpi-4.0.0.tar.gz”`

 

(Note: When copying and pasting, sometimes quotes get messed up. If you get an error from that command about libcurl, just delete then re-add the quotation marks manually once you’re in the terminal window.)

 

Next, we’ll decompress and extract the archive in one step:

 

`>> gunzip -c openmpi-4.0.0.tar.gz | tar xf -`

 

Next, we’ll configure the installation package for our system. Navigate into the newly created openmpi-4.0.0 folder and type:

 

`>> ./configure --prefix=/usr/local CC=/usr/bin/clang CXX=/usr/bin/clang++`

 

Those CC and CXX arguments force the computer to use a specific compiler, which is clang. On most systems this should install fine without those, but it doesn’t hurt to have them. After you hit return, lots of stuff will fly by your screen for a long time. Once that’s done, type:

 

`>> sudo make all install`

 

which will take forever and a decade before it completes, but then that’s it; OpenMPI is now installed. If you want to check, type “mpirun” at the command line, but not much will happen since it can’t find anything to do.

 

<span style="text-decoration:underline;">Important:</span> Remember that sudo is the “nuclear option” on unix-esque computers and should only be used when absolutely necessary; the install path for OpenMPI is inside a protected folder so we need to invoke sudo in this case, otherwise the installation can’t go to the default. You could specify an alternate location, but since most applications that might use OpenMPI will look here, we might as well.

 

 

Step 4) Installing Silo

Silo is a file format that is used as the default for viewing ParFlow outputs, but we also now have other options available. Many of the test cases use it so we always recommend installing it. The latest version of silo can be found at: [https://wci.llnl.gov/simulation/computer-codes/silo/downloads](https://wci.llnl.gov/simulation/computer-codes/silo/downloads)

 

You could download the archive in a web browser and move it but instead I’m going to use curl again. Navigate to the ParF folder and the commands are:

 

`>> cd ~/ParF`

`>> curl “https://wci.llnl.gov/content/assets/docs/simulation/computer-codes/silo/silo-4.10.2/silo-4.10.2.tar.gz” -o “silo-4.10.2.tar.gz”`

 

Mimicking the process we used for OpenMPI, to expand and extract the archive type:

 

`>> gunzip -c silo-4.10.2.tar.gz | tar xf -`

 

Now we’ll rename the newly expanded directory to match our environment variable SILO_DIR:

 

`>> mv silo-4.10.2 silo`

 

Now we’ll navigate into the new directory, configure, and install:

 
```
>> cd silo

>> ./configure --disable-silex

>> make install

>> cd ..
```
 

And that completes the installation.

 

Note that if your version of Silo differs, you’ll need to update the appropriate file/directory names environment variable we set to reflect your installation path.

 

Step 5) Installing the HYPRE library

HYPRE is a library of numerical solvers for high-performance computing applications and ParFlow hooks into it for some of its functionality. 

 

As before the simplest way to get this is curl. There were some issues with hypre version 10 and ParFlow but the current release (2.11.2) or newer should work just fine.

 

Installing this is nothing more than a minor variation of what we’ve already done:

 

`>> cd ~/ParF`

`>> curl “https://computating.llnl.gov/projects/hypre-scalable-linear-solvers-multigrid-methods/download/hypre-2.11.2.tar.gz” -o “hypre-2.11.2.tar.gz”`

 ( from Jen the above is the correct url for the hypre installation)

`>> gunzip -c hypre-2.11.2.tar.gz | tar xf -`

>> mv hypre-2.11.2 hypre`

 

(That would vary if the version number changes, and your HYPRE_DIR variable would need to reflect your version/directory names). Now onto the setup.

 
```
>> cd ~/ParF/ hypre/src

>> ./configure --prefix=$HYPRE_DIR

>> make install

From Jen:

If make install is not working and you receive a Fatal error mpi.h is not found: go into your bash_profile and change the two CC and CXX lines to the normal mpi complier

export PARFLOW_DIR=~/ParF/parflow

export HYPRE_DIR=~/ParF/hypre

export SILO_DIR=~/ParF/silo

export CC=mpicc

export CXX=mpic++

export FC=gfortran

export F77=gfortran

Once done: 

Recompile via .configure --prefix=$HYPRE_DIR

Then make install

This should correct the previous error and allow you to install
```
 

This installation also takes a while to install, but it’s usually not as long as OpenMPI. You’re likely to see a lot of warnings about “could not find any symbols” but as long as there are _no errors_ you should be able to move on to ParFlow.

 

Step 6) Installing ParFlow

This step is where we depart significantly from the previous installation instructions, but in a good way. The ParFlow build process is now more streamlined because it uses CMake. Instead of separate builds for pfsimulator (the model itself) and pftools (the controlling interface), there is one unified build process. There are a lot of different setup options for ParFlow but for our “typical” setup we’re assuming you want to run in parallel, you might want to run CLM, you have Silo and Hypre, and a few other little things.

 

First, we need to download the code. ParFlow is hosted on GitHub, so to obtain the code:

 

`>> cd ~/ParF`

`>> git clone https://github.com/parflow/parflow.git --branch master --single-branch_`

 

which will create a folder called parflow in ParF and now you’ve got the source code. You may want to rename the folder (I like to use the date I downloaded it so /parflow.20190209 for example) but you’ll need to update your PARFLOW_DIR too if you change that. I’m going to proceed assuming you have not renamed it.

 

Next, we’ll create a directory for the incremental files of the build process called “build”

 

`>> mkdir build`

`>> cd build`

 

The next step will tell CMake how to configure ParFlow using the typical options (copy and paste will be your friend on this):

```
>> cmake ../parflow -DCMAKE_INSTALL_PREFIX=$PARFLOW_DIR -DHYPRE_ROOT=$HYPRE_DIR -DPARFLOW_AMPS_LAYER=mpi1 -DPARFLOW_AMPS_SEQUENTIAL_IO=true -DPARFLOW_ENABLE_TIMING=true -DSILO_ROOT=$SILO_DIR -DPARFLOW_HAVE_CLM=ON
```
 

Note that if your parflow source tree is NOT in a folder called parflow, you’ll need to change that right after “cmake” in the above command. The manual provides a few extra details on how you can customize an installation using the “ccmake” interface but we don’t need that now. Next type:

 

`>> make`

 

and lots of stuff will happen. If the last two lines resemble:

_[100%] Linking C shared library libpftools.dylib_

_[100%] Built target pftools_

 

Then you’re ready for the last command:

 

`>> make install`

 

The difference is that “make” builds everything in our temporary structure (the reason we created that build directory in the first), and “make install” copies the final build into our installation location.

 

 

On a few computers we’ve found that after the “make” command you might see something like this:

_collect2: error: ld returned 1 exit status_

_make[2]: *** [pftools/libpftools.dylib] Error 1_

_make[1]: *** [pftools/CMakeFiles/pftools.dir/all] Error 2_

_make: *** [all] Error 2_

 

If that’s the case, just back up and add “-DCMAKE_SHARED_LINKER_FLAGS=-ltcl8.5” to the end of that list for the “cmake” command, the “make” again and you should be all sorted out.

 

 

And that’s it for the installation. The last step is to test the installation. Navigate to:

`>> cd ~/ParF/parflow/test              	{Or wherever you installed to}`

 

Then type:

 

`>> make check`

 

A lot of things will fly by as ParFlow verifies that it is working correctly. Most should pass, but some will likely fail if you have fewer processors than are being tested; for example, some of the tests need nine processors and these will fail on a system with only 4. You’ll see a report at the end and as long as most pass, you’re all done and ParFlow has been installed successfully.

 

 

Step 7) Some troubleshooting tips

The single most common reason steps of the installation don’t go well is not being in the correct path. You can always verify your location in the directory tree using the “pwd” command and each step tells you where you should be before you begin entering commands.

 

You mac is also case-sensitive so pay attention to capitalization. That also tends to be a wrench in the gears of the setup process.

 

If you prefer to use the c-shell instead of the bash-shell most of the steps are the same but your environment variables are defined differently. Fortunately, the setup example in the ParFlow manual outlines the c-shell variations of all the steps we covered here.

 

Lastly, if you have MacPorts or any other “helper packages” your system paths may have been altered from the defaults. Defining the environment variables at the bottom of your .bash_profile should take care of this.

If you’ve installed mpi properly but got errors with mpi, one solution can be turn off WIFI and keep WIFI off when running ParFLow.

 

Step 8) What to do next

See that wasn’t nearly as bad as you thought it was going to be, so now what? Well, you could start getting to work with ParFlow (which is totally what you should do if you’re a grad student), but maybe go outside, enjoy some fresh air, and see how things are today first. Maybe take a break and have a tasty beverage of your choosing while you ponder the intricacies of the scientific awesomeness you’re going to unleash upon the world using ParFlow.


---


## MacOS High Sierra

**Notes from Laura:  9/11/2018**

Summary: Successful install: With GCC 8.1.0, MPI 3.1.2, Hypre 2.9.0b, Slio 4.10.2

Only modification from PF Blog is that I compiled MPI with clang per Nick’s instructions (see the configure line below).  Also it didn’t work when I ran tests with WiFI on when I was at school (MPI error) but it worked once I turned it off. 



* Edited the bash profile per the instructions in the Yosemite blog post: [http://parflow.blogspot.com/2014/10/installing-parflow-in-os-x-yosemite.html](http://parflow.blogspot.com/2014/10/installing-parflow-in-os-x-yosemite.html)
* Installed the latest GCC per blog instructions: [http://hpc.sourceforge.net/](http://hpc.sourceforge.net/)
* After this it still wasn’t pointing to the correct gcc but this was fixed by a restart 

        dhcp-10-134-192-214:~ laura$ gcc --version

        gcc (GCC) 8.1.0

        Copyright (C) 2018 Free Software Foundation, Inc.

        This is free software; see the source for copying conditions.  There is NO

        warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

        dhcp-10-134-192-214:~ laura$ gfortran --version

        GNU Fortran (GCC) 8.1.0

        Copyright (C) 2018 Free Software Foundation, Inc.

        This is free software; see the source for copying conditions.  There is NO

        warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.



* Next I got the latest MPI:[https://www.open-mpi.org/software/ompi/v3.1/](https://www.open-mpi.org/software/ompi/v3.1/) 

```
  tar -xvf openmpi-3.1.2.tar.gz

  cd openmpi-3.1.2

./configure --prefix=/usr/local/ CC=/usr/bin/clang CXX=/usr/bin/clang

  cd openmpi-3.1.2

sudo make all install

ompi_info 

mpiexec —version

mpirun —version
```


* Get Parflow:  git clone[ https://github.com/parflow/parflow](https://github.com/parflow/parflow)

   							 mv parflow/ parflow.git



* Get Silo: [https://wci.llnl.gov/simulation/computer-codes/silo/downloads](https://wci.llnl.gov/simulation/computer-codes/silo/downloads)

Follow instructions from ParFlow Blog to build:

```
> cd silo

> ./configure --disable-silex

> make install

> cd ..
```


* Get Hypre 2.9.0b:[https://computation.llnl.gov/projects/hypre-scalable-linear-solvers-multigrid-methods/software](https://computation.llnl.gov/projects/hypre-scalable-linear-solvers-multigrid-methods/software)

Follow instructions from ParFlow Blog to build:

```
> tar -xvf hypre-2.9.0b.tar.gz

> mv hypre-2.9.0b hypre

> cd hypre/src

> ./configure --prefix=$HYPRE_DIR

> make install
```


* Followed steps 7 and 8 from the blog to install par flow ([http://parflow.blogspot.com/2014/10/installing-parflow-in-os-x-yosemite.html](http://parflow.blogspot.com/2014/10/installing-parflow-in-os-x-yosemite.html)) 
* All tests were failing with an MPI error when I had wifi turned on but when I turned it off they all pass 

**Other People’s Notes on High Seirra:**

From Nick:

For MPI:

Version 3.0.0

- Two catches to this though. 1) I had to use clang, and 2) I specified the path and compilers manually. Here’s the exact line I used:./configure --prefix=/usr/local/ CC=/usr/bin/clang CXX=/usr/bin/clang(FYI you can verify your MPI version with “ompi_info” at the command line and the version will be at the top)

Next Hypre/Silo:

It’s Hypre 2.9.0b and Silo 2.9.1. I haven’t tried any of the other versions lately, BUT these were installed with gcc, looks like v4.9.0 according to the config log

And PF was built with the same GCC and gfortran version used for hypre. I had no luck getting PF to build with clang so, annoyingly, it’s exactly the opposite of openMPI.

From Reed:

/Users/reed/parflow/libraries> gcc —version

gcc (GCC) 5.0.0 20141005 (experimental)

Copyright (C) 2014 Free Software Foundation, Inc.

This is free software; see the source for copying conditions.  There is NO

warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

/Users/reed/parflow/libraries> gfortran —version

GNU Fortran (GCC) 5.0.0 20141005 (experimental)

Copyright (C) 2014 Free Software Foundation, Inc.

GNU Fortran comes with NO WARRANTY, to the extent permitted by law.

You may redistribute copies of GNU Fortran

under the terms of the GNU General Public License.

For more information about these matters, see the file named COPYING


---


## PFNetCDF on TACC Stampede2

Katie Markovich, March 28, 2019

Home directory = /home1/06175/khm293** [make this your home directory!]**

```bsh
.**bashrc**

module load git

module load gcc

  export CC=gcc

  export CXX=g++

  export FC=gfortran

  export F77=gfortran

  export NCDIR=/home1/06175/khm293/ParF/netcdf4

  export H5DIR=/home1/06175/khm293/ParF/hdf5

  export MPI_DIR=/home1/06175/khm293/ParF/OMP-4.0.0

  export MPI_RUN_DIR=/home1/06175/khm293/ParF/OMP-4.0.0/bin

  export HYPRE_DIR=/home1/06175/khm293/ParF/hypre

  export SILO_DIR=/home1/06175/khm293/ParF/silo

  export PARFLOW_DIR=/home1/06175/khm293/ParF/parflow

  PATH=$PATH:$PARFLOW_DIR/bin

**hdf5**
```
Download hdf5 here:[https://www.hdfgroup.org/downloads/](https://www.hdfgroup.org/downloads/)


```
>> cd ~                                    
>> mkdir ParF
>> cd ParF
>>mkdir hdf5
>>exportH5DIR=${HOME}/ParF/hdf5
>>gunzip hdf5-1.10.4.tar.gz
>>tar -xvf hdf5-1.10.4.tar
>>cd hdf5-1.10.4/
>>CC=mpicc ./configure --enable-parallel --prefix=${H5DIR}
>>make
>>make install
>>cd ..
```


**netCDF4**

Download netCDF4 here:[https://www.unidata.ucar.edu/downloads/netcdf/index.jsp](https://www.unidata.ucar.edu/downloads/netcdf/index.jsp)


```
>>mkdir ${HOME}/ParF/netcdf4
>>exportNCDIR=${HOME}/ParF/netcdf4
>>gunzip netcdf-4.6.2.tar.gz
>>tar -xvf netcdf-4.6.2.tar
>>cd netcdf-4.6.2/
>>CC=mpicc CPPFLAGS=-I${H5DIR}/include LDFLAGS=-L${H5DIR}/lib \
./configure --disable-shared--disable-dap--enable-parallel-tests --prefix=${NCDIR}
>>make
>>make install
```


From Nick’s post:

**openMPI**


```
>> cd ~                                    
>> cd ParF
>> mkdir OMPI-4.0.0
>> curl "https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz" -o "openmpi-4.0.0.tar.gz"
>> gunzip -c openmpi-4.0.0.tar.gz | tar xf -
>> cd openmpi-4.0.0
>> ./configure --prefix=$MPI_DIR 
>> make
>> make install
```


**Silo**


```
>> cd ~/ParF
>> curl "https://wci.llnl.gov/content/assets/docs/simulation/computer-codes/silo/silo-4.10.2/silo-4.10.2.tar.gz" -o "silo-4.10.2.tar.gz"
>> gunzip -c silo-4.10.2.tar.gz | tar xf -
>> mv silo-4.10.2 silo
>> cd silo
>> ./configure --disable-silex
>> make install
```


**Hypre**


```
>> cd ..
>> cd ~/ParF
>> curl "https://computation.llnl.gov/projects/hypre-scalable-linear-solvers-multigrid-methods/download/hypre-2.11.2.tar.gz" -o "hypre-2.11.2.tar.gz"
>> gunzip -c hypre-2.11.2.tar.gz | tar xf –
>> mv hypre-2.11.2 hypre
>> cd ~/ParF/ hypre/src
>> ./configure --prefix=$HYPRE_DIR 
>> make install
```


**Parflow-NetCDF**


```
>> cd ~/ParF
>> git clone https://github.com/parflow/parflow.git --branch master --single-branch
>> mkdir build
>> cd build
>>cmake ../parflow -DCMAKE_INSTALL_PREFIX=$PARFLOW_DIR -DHYPRE_ROOT=$HYPRE_DIR -DPARFLOW_AMPS_LAYER=mpi1 -DPARFLOW_AMPS_SEQUENTIAL_IO=true -DPARFLOW_ENABLE_TIMING=true -DSILO_ROOT=$SILO_DIR -DPARFLOW_HAVE_CLM=ON -DNETCDF_DIR=$NCDIR -DHDF5_ROOT=$H5DIR
```