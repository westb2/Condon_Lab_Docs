# Remote Desktop / SSH / Basic Command line

**Amanda Triplett 10/2019**

**Description:  **Accessing your computer via SSH through your command line with a general example of how to create a TAR file remotely and move it to your home computer. It also outlines how to remotely manage your desktop from mac to mac computer.


## Method 1a: From Apple to Apple: SSH 



1. Open a command line and type ssh “your_username”@”your_static_IP”
2. If you’ve never logged in you will have to type “yes” in order to connect 
3. Enter the password for the computer you’re trying to access 
4. You are now accessing that other computer via the command line (anything you do while connected through this command line window will happen on the other computer) 


## Method 1b: Creating a TAR file on a remote computer and pulling it to your current computer 



1. After you followed the steps in 1a, you can create a file and pull it, or just pull an already created file to your local computer 
2. Navigate to the folder where the file of interest is stored using  a combination of ls (which tells you what is in the current folder and cd “file_dir” to navigate to the new folder. 
3. Once you are in the folder where your file of interest is, type pwd to get the current file path
4. If your file is small and does not need to be TARd skip the next step
5. Creating a TAR
    1. Type the following: 
    2. tar -czvf “file_name”.tar.gz “your file path from pwd” .
        1. C = create an archive 
        2. Z = compress a folder with gzip (remove the z and .gz to do a regular tar)
        3. V = display progress in terminal (AKA verbose mode, this is also optional)
        4. F =  (allows you to specify your filename)
        5. The “ .” will stop the entire absolute file pathway from being there when you extract
    3. You should have a new tar file if you ls 
6. Transferring
    4. Open a new command window, this one will be accessing the computer you’re on now
    5. Navigate (using cd and ls) to the folder where you want to put your file (you don’t have to do this but I think it makes things easier) 
    6. Once you’re there, type the following “scp “your_username”@”your_static_ip”:”your_file_path/filename.tar.gz” .
        6. The “ .” means put this file where I am right now (that’s why we navigated to the right folder, otherwise you type the filepath of where you want the file to go instead) 
        7. Change tar.gz to your appropriate file extension
7. Unzipping the file (if needed)
    7. gunzip -c foo.tar.gz | tar xopf 
        8. for tar.gz
        9. You don’t need to specify a file path if you are already in the folder where you want things extracted
    8. tar -xvf filename.tar -C path you want to copy it to


## Method 2: From Apple to Apple: Using VPN and Remote Management



1. Make sure your desktop has a static IP (all desktops have them at this time) 
2. Change your settings to allow remote management
    1. System Preferences -> sharing
    2. Check Screen sharing and remote management
        1. When you click remote management, select the capabilities you want when logging in remotely 
3. Get the VPN software
    3. Go to [Vpn.arizona.edu](http://vpn.arizona.edu/)
    4. NetID: your user name
    5. NetID Password:
    6. NetID Method: Push  (If you’re using Duo Mobile)
    7. After logging in click AnyConnect in the left toolbar
    8. Download the VPN software.
    9. Run the Cisco Anyconnect (you probably have to log in again)
    10. When the VPN runs the box may be blank. It should be: [vpn.arizona.edu](http://vpn.arizona.edu/)
4. Go to Finder
5. Select the Go menu from the top navigation bar (where the apple is) and go all the way to the bottom of the menu where it says connect to server
6. Type in vnc://”your_static_IP” and click connect 
7. You should now be able to control your desktop 