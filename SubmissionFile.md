## Week 5 Homework Submission File: Archiving and Logging Data

Please edit this file by adding the solution commands on the line below the prompt.

Save and submit the completed file for your homework submission.

---

### Step 1: Create, Extract, Compress, and Manage tar Backup Archives

1. Command to **extract** the `TarDocs.tar` archive to the current directory:

- After logging in at home/sysadmin
- `mkdir Projects`

- Downloaded the file TarDocs.tar from the following website:

  - [TarDocs.tar](https://drive.google.com/a/2tor.com/file/d/1fRjFS1vOdS7yfKJgpJxR02_UxeT_qI_u/view?usp=sharing
  
- `mv /home/sysadmin/Downloads/TarDocs.tar /home/sysadmin/Projects` Moved this file to the Projects folder 

- Checked `ls -l /home/sysadmin/Projects` which confirmed `-rw-rw-r-- 1 sysadmin sysadmin 1099806720 Feb  8 00:40 TarDocs.tar`


- Then `cd ~/Projects` to navigate to Projects

- Inspecting the TarDocs.tar file. Listing the files inside the tar fileq

- `tar tvvf TarDocs.tar`

- Extracting the TarDocs.tar in the Projects folder

- `tar xvvf TarDocs.tar`

- Inspecting that Java folder is present

- `ls -lat TarDocs/Documents`


2. Command to **create** the `Javaless_Doc.tar` archive from the `TarDocs/` directory, while excluding the `TarDocs/Documents/Java` directory:

- `tar  cvvWf Javaless_Doc.tar --exclude "TarDocs/Documents/Java" TarDocs` - Tested- works

3. Command to ensure `Java/` is not in the new `Javaless_Docs.tar` archive:

- `tar tvvf Javaless_Doc.tar | grep Java`


**Bonus** 
- Command to create an incremental archive called `logs_backup_tar.gz` with only changed files to `snapshot.file` for the `/var/log` directory:

#### Critical Analysis Question

- Why wouldn't you use the options `-x` and `-c` at the same with `tar`?

- Thats because the -x flag is used for extraction while the -c flag instructs to create a tar file. Since its not possible extract and create a tar file at the same time, so naturally these flags cannot be together in a single command. 

### Step 2: Create, Manage, and Automate Cron Jobs

1. Cron job for backing up the `/var/log/auth.log` file:

- To add a cron job to a crontab
- `crontab -e`

- Automating the task to automate archiving of the file `/var/log/auth.log` to `/auth_backup.tgz` every Wednesday at 6 AM

- `cd /`
- `0 6 * * 3 sudo tar cvvf auth_backup.tgz var/log/auth.log` Note that here auth_backup.tgz is created in the home directory (which is the current directory that I am at and var/log/auth.log is the file I am archiving as a zipped backup in the homw directory)

- `tar tvf auth_backup.tgz` To check the file contents of the new tar.tgz file


---

### Step 3: Write Basic Bash Scripts

1. Brace expansion command to create the four subdirectories: I have created these folders while being inside the home/sysadmin folder

- `sudo mkdir -p backups/{freemen,diskuse,openlist,freedisk}`
- `nano system.sh`


2. Paste your `system.sh` script edits below:

    ```bash
    #!/bin/bash
    free -m > backups/freemem/free_mem.txt
    df -BM -h > backups/diskuse/disk_usage.txt
    lsof > backups/openlist/open_list.txt
    df -k -BM -h | awk '{print $1,$4}' > backups/free_disk.txt
    ```

3. Command to make the `system.sh` script executable:

- `chmod -x system.sh` 

**Optional**
- Commands to test the script and confirm its execution:

- `sudo ./system.sh`

**Bonus**
- Command to copy `system` to system-wide cron directory:

- `sudo cp system.sh /etc/cron.weekly`
---

### Step 4. Manage Log File Sizes
 
1. Run `sudo nano /etc/logrotate.conf` to edit the `logrotate` configuration file. 

    Configure a log rotation scheme that backs up authentication messages to the `/var/log/auth.log`.

    - Add your config file edits below:

    ```bash
    [Your logrotate scheme edits here]
    ```
---

### Bonus: Check for Policy and File Violations

1. Command to verify `auditd` is active:

2. Command to set number of retained logs and maximum log file size:

    - Add the edits made to the configuration file below:

    ```bash
    [Your solution edits here]
    ```

3. Command using `auditd` to set rules for `/etc/shadow`, `/etc/passwd` and `/var/log/auth.log`:


    - Add the edits made to the `rules` file below:

    ```bash
    [Your solution edits here]
    ```

4. Command to restart `auditd`:

5. Command to list all `auditd` rules:

6. Command to produce an audit report:

7. Create a user with `sudo useradd attacker` and produce an audit report that lists account modifications:

8. Command to use `auditd` to watch `/var/log/cron`:

9. Command to verify `auditd` rules:

---

### Bonus (Research Activity): Perform Various Log Filtering Techniques

1. Command to return `journalctl` messages with priorities from emergency to error:

1. Command to check the disk usage of the system journal unit since the most recent boot:

1. Comand to remove all archived journal files except the most recent two:


1. Command to filter all log messages with priority levels between zero and two, and save output to `/home/sysadmin/Priority_High.txt`:

1. Command to automate the last command in a daily cronjob. Add the edits made to the crontab file below:

    ```bash
    [Your solution cron edits here]
    ```

---
© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.
