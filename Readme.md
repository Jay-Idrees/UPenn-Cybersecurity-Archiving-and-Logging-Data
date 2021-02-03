# Linus operating system file structure assessment and user control
Date of publication: 2/2/2021

I am writing this readme to reinforce and document things I leant during the University of Pensylvania Cybersecurity Engineer certification training so I can circle back to it later


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
   Here the .txt file outputs the log file detailing file characteristics of all the files that are archived in the .tar file or the results of the command. Note that you must not be in the epscript folder

   `cd ~/Documents/epscript/` for partial address to path
   `ls -l doctors` This will list all the files in the folder along with their permissions but this folder must be a subdirectory of the epscript

   `grep -R "Mark,Lopez" epscript/`  Searching a particular patient in a given directory files



  ## Extracting files from the archive

   - `mkdir restored_emails`

    x is extraction -C restored_emails is where the extraction will be done , wildcards *.csv is extracting only the csv files
   `tar xvvf 10May2019-235536-0700.tar -C restored_emails --wildcards "*.csv"`


