
# (UNDER CONSTRUCTION)Automated Backup Script on Linux

**Project Link:** [View Project](https://learn.nextwork.org/projects/caf37fbf-1c43-4755-ae48-7edde46e07c1)


---

![Image](https://learn.nextwork.org/joyful_turquoise_jolly_manatee/uploads/caf37fbf-1c43-4755-ae48-7edde46e07c1_j46w5a9l)

## Project Overview

### Goals and motivation

In this project, I am building an automated linux system that backups files, compresses and archives them using tar and gzip and uses cron to do the automation at a set time. The project also implements logging of events that involves the files being backed up.

## Setting Up the Project Structure

### Planning the setup

In this step, I'm setting up a directory for the project and sample data to backup. Then I shall create a backup destination directory and create a script file that will house the automation code. 

Firstly, I created the backup_project and sample_data directories that will be reference directories for this task.

```bash
mkdir -p ~/backup_project/sample-data
```
Next is to add sample files into the sample_data directories.

```bash
echo "Important Information for this project" > ~/backup-project/sample-data/file1.txt
echo "Notes written for the project" > ~/backup_project/sample_data/file2.txt
echo "Configuration settings" > ~/backup_project/sample_data/file3.txt
```
Next task is to create the backups directory as a nested directory within the backup_project directory

```bash
mkdir -p ~/backup_project/backups
```
Additionally, the bash script for the backups is created. This is the script that will perform the automated backup and logging retention.

```bash
nano ~/backup_project/backup.sh
```

The script is made executable by running:
```bash
chmod +x ~/backup_project/backup.sh
```
Verify the script has execute permission by running:
```bash
ls -l ~/backup_project/backup.sh
```


![Image](https://learn.nextwork.org/joyful_turquoise_jolly_manatee/uploads/caf37fbf-1c43-4755-ae48-7edde46e07c1_kyq7xaed)


## Writing the Backup Script Core

In this step, I'm writing the core of the backup.sh script to configure the script variables, write a log function that shall handle logging of events related to the files in the sample_data directory,  and write the tar compression command that shall create a timestamped archive.

![Image](https://learn.nextwork.org/joyful_turquoise_jolly_manatee/uploads/caf37fbf-1c43-4755-ae48-7edde46e07c1_a1byrvlg)

### Logging with tee -a

The log() function is used to hold the date variable for the script to indicate when a cetain event took place and it also houses another variable $1 that is a "placeholder" for the arguments that shall be the input for the log() function. The tee -a is useful because it appends the output of the log function into the referenced log file and to the terminal.

## Implementing Rotation and Logging

### Retention policy strategy

In this step, I'm adding a retention policy that finds and deletes old backups, add conditional logging that records what was cleaned up so that backups older than a set period (which in this case is 7 days) shall be deleted.

### How the find command selects old backups

The find command searches for files in the backups directory that end with .tar.gz and deletes files that have a modoification time older than 7 days. The -print and -delete tags are used to output the file names and delete them in one sweep.

## Scheduling Automated Backups with Cron

### Automating the schedule

In this step, I'm scheduling the script to run periodically so that the identified files can be backed up automatically at the set time.

### Breaking down the cron expression

I used the expression 20 19 * * * /home/mwaks/backup_project/backup.sh which has five fields representing the minute, hour, day of the month, month, and day of the week with only the minute and hour fields being represent by 32 and 21 respectively.

![Image](https://learn.nextwork.org/joyful_turquoise_jolly_manatee/uploads/caf37fbf-1c43-4755-ae48-7edde46e07c1_affrnh40)

#
[View this project](https://learn.nextwork.org/projects/caf37fbf-1c43-4755-ae48-7edde46e07c1)*
