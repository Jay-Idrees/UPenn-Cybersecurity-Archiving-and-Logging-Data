# Linus operating system file structure assessment and user control
Date of publication: 2/2/2021

I am writing this readme to reinforce and document things I leant during the University of Pensylvania Cybersecurity Engineer certification training so I can circle back to it later


## Random Usefulthings

- `ls -l admit discharge` displays the files inside and the permissions of the folders


## `tar` for full and incremental backup

`tar [option(s)] [archive_name] [objects_to_archive]`

`sudo tar cvf hurricane-backup-10-11-2018.tar /var/log`


- `sudo tar cvvf 2018-10-12-hurricane-backup.tar /var/log`

    ```
    -rw-r--r--  root/root      5784  2019-07-03  15:38   /var/log/fontconfig.log
    -rw-r-----  syslog/adm   141571  2019-07-1-  11:16   /var/log/kern.log.2.gz
    -rw-rw-r--  root/utmp     20352  2019-07-15  15:33   /var/log/wtmp
    -rw-r--r--  root/root       695  2019-07-05  21:09   /var/log/vboxadd-install.log
    ```

    
`gunzip 2018-10-12-hurricane-backup.tar`

`ls -l 10May2019-235536-0700.tar` This will show the permissions of the tar file

- checking/listing files inside the .tar file

  - `tar tvvf 10May2019-235536-0700.tar | less`


  ## Creating a .tar file for archiving
   `tar cvvWf 20190505epscript.tar epscript/ > 20190505epscript.txt`
   Here the .txt file outputs the log file detailing file characteristics of all the files that are archived in the .tar file or the results of the command. Note that you must not be in the epscript folder if that is the folder you are taring. THe `W` is important to check for errors to make sure that the backup was not corrupt and should always be used

   `cd ~/Documents/epscript/` for partial address to path
   `ls -l doctors` This will list all the files in the folder, as well as the files in the sub-folders and are labled by the folders they are in, along with their permissions but this folder must be a subdirectory of the epscript

   `grep -R "Mark,Lopez" epscript/`  Searching a particular patient in a given directory files



  ## Extracting files from the archive

   - `mkdir restored_emails`

    x is extraction -C restored_emails is where the extraction will be done , wildcards *.csv is extracting only the csv files
   `tar xvvf 10May2019-235536-0700.tar -C restored_emails --wildcards "*.csv"`


## Incremental Backups

- After initial full backup you only perform subsequent backup of files that have changed- Saves memory, can be done hourly or daily. It is a special type of tar. Here emergency is the folder being archived

- `$ tar cvvWf emerg_back_sun.tar --listed-incremental=emerg_backup.snar --level=0 emergency`

- This creates 2 files one is emerg_back_sun.tar and the second is emerg_backup.snar-the later only stores the changes

- here level=0 tells that this is going to be the first in the series of backups.

## Restoring the backed up data


- Commands in sequence

- After you are in the right directory, typically the folder containing the folder to be archived

- `tar cvvWf emerg_back_sun.tar --listed-incremental=emerg_backup.snar --level=0 emergency`

- `tar tvvf emerg_back_sun.tar --incremental`

This is me changing the files to test backup
 - `echo "New file for CJones" > emergency/admit/file6`

 Monday backup

 - `tar cvvWf emerg_back_mon.tar --listed-incremental=emerg_backup.snar emergency`

 - `tar tvvf emerg_back_mon.tar --incremental`

  Tuesday backup- updatung the file and then testing that only the changed or new files are svaed in the new backup file for tuesday

     - `echo "Update MSmith" >> emergency/discharge/file2`

        - `tar cvvWf emerg_back_tues.tar --listed-incremental=emerg_backup.snar emergency`

        - `tar tvvf emerg_back_tues.tar --incremental`

 Listing all incremental files in the epscript folder       

- `ls -l *.tar`

- `rm -r emergency`  

 - `ls`

Extracting the data from archive - run this in the folder where you want the archive to unpack- typically its a folder for example running the command below will create
 ### - Extraction `tar xvvf emerg_back_sun.tar --incremental`

The testenvir folder contains the folder called patient. Then I have initially backed up this folder testenvir into a tar file called epscript_back_sun.tar. Then I manually go and delete the patient folder from the real testenvir folder to simulate an attack. Then I extract the patient folder from the escript_back_sun.tar- It will automatically replace the specified folder in the actual place. 

- Archiving
`tar cvvWf epscript_back_sun.tar --listed-incremental=epscript_backup.snar --level=0 testenvir`

- Simulating an attack by removing only one folder
`rm -r testenvir/patient/`

- Replacing that specific folder only
`tar xvvf epscript_back_sun.tar -R -C ~/Documents/epscript/ testenvir/patient/`
