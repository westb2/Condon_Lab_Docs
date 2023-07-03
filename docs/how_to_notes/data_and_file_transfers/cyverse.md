# General Cyverse Information

**_Laura Condon, Jan 2019_**


## **Description:  **Running notes with links and tips for doing file transfers with Cyverse.  Feel free to update and add to this document as needed. 


---


## Creating and Account and Getting Access:

In order to upload data, you need to create a Cyverse account and be given permissions by Laura. Provide the username of your account and the e-mail you used to create the account (your UA e-mail) to Laura. 

**Logging in:**

[De.cyverse.org](http://de.cyverse.org/)

**Our Server:**



* You can find under Community Data/avra 
* From command line: /iplant/home/shared/avra

**GUI file transferring with cyberduck**

[https://cyberduck-quickstart.readthedocs.io/en/latest/#](https://cyberduck-quickstart.readthedocs.io/en/latest/#)


## Setting up irods so you can do command line transfers:



1. Sign up for a cyverse account: [https://user.cyverse.org/register](https://user.cyverse.org/register)
2. Install icommands so you can use irods
    * Instructions for setting up on Mac: [https://wiki.cyverse.org/wiki/display/DS/Setting+Up+iCommands#SettingUpiCommands-mac](https://wiki.cyverse.org/wiki/display/DS/Setting+Up+iCommands#SettingUpiCommands-mac)
    * For setting up on Powell I used the page above and followed the link for the cent6
    * To check OS: cat /etc/*release
    * Needed root access to install the irods commands so I logged in as root and then did:
    * sudo yum install[ https://files.renci.org/pub/irods/releases/4.1.10/centos6/irods-icommands-4.1.10-centos6-x86_64.rpm](https://files.renci.org/pub/irods/releases/4.1.10/centos6/irods-icommands-4.1.10-centos6-x86_64.rpm)
    * Then login as your username and setup the cyverse connection according to step 3
3. Initialize irods by typing iinit and then putting in your user information following part two of the link on step 2

**Command line transfers with irods: **

First initialize irods like this:



    * _iinit_
    * [data.cyverse.org](http://data.cyverse.org/)
    * Port: 1247
    * User: yourusername
    * Zone: iplant
    * Then enter your cyverse password

To upload: 



* cd to the local directory with the data you want to transfer
* Then ‘icd’ to the location on Cyverse where you want to put the data
    * icd /iplant/home/shared/avra  #for avra
    * icd /iplant/home/username #for your Cyverse home directory (note this is small and not where you should be storing things)
* iput -vP “filename"
* To batch upload: iput -K -P -b -r -T folder-where-you-want-files-on-cyverse/
    * If you icd to where you want to put your files on cyverse then you simply need to put a period after the iput command’
        1. iput -K -P -b -r -T .
    * -K “This causes iCommands to verify that transferred files weren't corrupted during the transfer by comparing the Checksums computed before and after the transfer.”
    * -P provides progress feedback
    * -b allows for bulk uploads
    * -r includes a folder or directory
    * -T renews socket connection every 10 min
* If its a tar file you can extract it on Cyverse like this:
    * ibun -x filename.tar new
    * [https://wiki.cyverse.org/wiki/display/DS/Dealing+with+Tar+and+other+Archive+Files](https://wiki.cyverse.org/wiki/display/DS/Dealing+with+Tar+and+other+Archive+Files)
    * **** You need to keep it to less than 2GB and several hundred files per Tar or the unzip ibun will fail. **

To Download:



* Basically the same thing but with iget instead of iput 

**Links with irods info:**

icommands help: [https://wiki.cyverse.org/wiki/display/DS/Using+iCommands](https://wiki.cyverse.org/wiki/display/DS/Using+iCommands)

**Setting up irods commands for R:**



* You should be able to run i commands in R using the system function. e.g. system(‘ils')
* If you get a warning saying command not found even though you have them installed you probably need to modify your R path so it can find them.  To check this echo $PATH on command line and compare with system(‘echo $PATH) within R (You can also check this with Sys.getevn() to see everything or Sys.getenv("PATH”) to just see the path in R)
* To fix you can set a new path appending the icommands to your current path like this: Sys.setenv(PATH='/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/Applications/icommands')
* Then you also need to add the plugin: Sys.setenv(IRODS_PLUGINS_HOME="/Applications/icommands/plugins/")
* Then check the path and check that it worked using getenv and testing an icommand. 
* NOTE: If you get an irods host error at this point its probably because you haven’t run iiint 
* PERMANENT Fix:  If you don’t want to have to add the Sys.setenv to every script then do the following:
    * vi ~/.Rprofile   (just make the file if you don’t have one)
    * Add the following lines:
        * Sys.setenv(PATH='/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/Applications/icommands')
        * Sys.setenv(IRODS_PLUGINS_HOME="/Applications/icommands/plugins/")
    * Note this will permanently set your environment variables

**Running an Analysis on Atmospheres**

**Steps to run on the Cyverse Rstudio App**



* Login: [de.cyverse.org](http://de.cyverse.org/) 
* Click on Apps - Search for ‘Rstudio’
* Select ‘rocker-geospatial-3.5.0’ # you can also add it to your favorites
* Give your analysis a name - you don’t have to change anything though if you don’t want
* Click ‘Launch Analysis’
* Then if you click on ‘Analyses’ on the left you should see the job you just launched it will say ‘running’ but its probably still initializing. 
* Click on the arrow next to its name to open the tab and you will see it launching your application.  
* You will need to login to Cyverse and then R studio the first time it launches.For Rstudio the username and password are both rstudio 
* Once you are in Rstudio - click on the terminal window
    1. iinit
        * [data.cyverse.org](http://data.cyverse.org/)
        * Port: 1247
        * User: lecondon
        * Zone: iplant
    2. Checkout the GitHub repo
        * git clone [https://github.com/lecondon/CONUS1_Warming.git \
](https://github.com/lecondon/CONUS1_Warming.git)
* Once you have done this you will see ‘CONUS1_Warming’ in your files tab
* Go to R scripts and click on the Rmd file you can run from here 