
# (UNDER CONSTRUCTION)Automated Backup Script on Linux


---

![Image](https://learn.nextwork.org/joyful_turquoise_jolly_manatee/uploads/caf37fbf-1c43-4755-ae48-7edde46e07c1_j46w5a9l)

## Project Overview

### Goals and motivation

In this project, you will write a *Bash* script from scratch that compresses a directory into a timestamped archive, enforces a retention policy to delete old backups, logs every action, and runs automatically on a daily schedule.



---
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
Append the shebang line as the first line of the script

```bash
#!/bin/bash
```

Save and exit the nano editor.

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

### Declaring the script variables

Open the script by running the following in the terminal:

```bash
nano ~/backup_project/backup.sh
```

In the nano editor below the shebang line, write the following to declare the script variables

```bash
# ============================================
# Automated Backup Script with Rotation
# ============================================

# ___________Configuration_______________
SOURCE_DIR="/home/$USER/backup_project/sample_data"
BACKUP_DIR="/home/$USER/backup_project/backups"
LOG_FILE="/home/$USER/backup_project/backup.log"
RETENTION_DAYS=7
DATE=$(date +%Y-%m-%d_%H-%M-%S)
BACKUP_NAME="backup_${DATE}.tar.gz"
```

An actual change will not be seen yet. The next section will include the actual backup logic for the script.

*Note: Ensure the is no space between the variable name the = sign and the actual variable.*

### Writing the logging function and input validation

This includes the log function that prepends a timestamp to the terminal and also to the *backup.log* file. The validation block checks whether *SOURCE_DIR* actually exists before doing anything. If it doesn't exist, the script logs an error and exits with code 1 (meaning failure). Then *mkdir -p* ensures the backup destination directory exists, creating it (and any parent directories) if needed without erroring if it already exists.

```bash
# __________Functions___________

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# _________ Validation____________

if [ ! -d "$SOURCE_DIR" ]; then
    log "ERROR: Source directory does not exist: $SOURCE_DIR"
    exit 1
fi

mkdir -p "$BACKUP_DIR"
```
The *log()* function is used to hold the date variable for the script to indicate when a cetain event took place and it also houses another variable $1 that is a "placeholder" for the arguments that shall be the input for the log() function. The *tee -a* is useful because it appends the output of the log function into the referenced log file and to the terminal.



### Writing the tar compression logic

This section is about the actual compression of the files in the target source directory and their storage in the target backup directory.

```bash
# _________ Create Backup____________
log "Starting backup of $SOURCE_DIR"

tar -czf "$BACKUP_DIR/$BACKUP_NAME" -C "$(dirname "$SOURCE_DIR")" "$(basename "$SOURCE_DIR")"

if [ $? -eq 0 ]; then
    log "SUCCESS: Backup created -> $BACKUP_DIR/$BACKUP_NAME"
else
    log "ERROR: Backup failed for $SOURCE_DIR"
    exit 1
fi

```

Save the script and exit the nano editor

Run the script to test it by running this command:
```bash
./backup.sh
```

You should see a log message printed to the terminal confirming the backup was created, something like
*SUCCESS: Backup created -> /home/.../backups/backup_2026-06-05_14-30-22.tar.gz*

Confirm the archive exists in your backups folder by running:
```bash
ls ~/backup_project/backups/
```
You should see a file named backup_ followed by the day's date and time, ending in .tar.gz.


![Image](https://learn.nextwork.org/joyful_turquoise_jolly_manatee/uploads/caf37fbf-1c43-4755-ae48-7edde46e07c1_a1byrvlg)

---

## Add Rotation and logging

In this step, a retention policy will be added that automatically deletes backups older than 7 days. Completion logging will also be added so every run leaves a clear log trail.

### Add the rotation block with conditional logging
Re-open the script using nano by running:

```bash
nano ~/backup_project/backup.sh
```


After the last line add the following:

```bash

# __________ Rotation___________

log "Rotating backups older than $RETENTION_DAYS days"

DELETED=$(find "$BACKUP_DIR" -name "backup_*.tar.gz" -type f -mtime +$RETENTION_DAYS -print -delete)

if [ -n "$DELETED" ]; then
    log "Deleted old backups: $DELETED"
else
    log "No backups older than $RETENTION_DAYS days to remove"
fi

log "Backup complete. Current backups:"
ls -l "$BACKUP_DIR"/backup_*.tar.gz >> "$LOG_FILE" 2>/dev/null

log "________________"
```

*NOTE:* The find command searches your backup directory for .tar.gz files with a modification time older than 7 days. The *-print -delete* flags output the filenames and remove them in one sweep.

The output of find is captured into the DELETED variable. The if ([ -n "$DELETED" ]) check tests whether the variable is non-empty. If files were deleted, their names get logged. If nothing was old enough to remove, a "no backups to remove" message is logged instead.

The final ls -l writes a listing of all remaining backups directly into the log file. 
The log "____________" line adds a visual separator between runs for distinction between different run sessions.*

To confirm that the srcipt is running, ensure you are in the backup_project directory then run:
```bash

./backup.sh
```
The terminal output should show lines like: a "Starting backup" message, a "SUCCESS: Backup created" confirmation, a "Rotating backups" message, a "No backups older than 7 days to remove" message (since this is the first run this will show), and a "Backup complete" line.

Confirm that the backup archive was created by running:

```bash
ls -l backup/
```

At least one file named something like *backup_2026-06-05_19-30-02.tar.gz* will show



## Scheduling Automated Backups with Cron

### Automating the schedule

In this step, I'm scheduling the script to run periodically so that the identified files can be backed up automatically at the set time.

### Breaking down the cron expression

I used the expression 20 19 * * * /home/mwaks/backup_project/backup.sh which has five fields representing the minute, hour, day of the month, month, and day of the week with only the minute and hour fields being represent by 32 and 21 respectively.

![Image](https://learn.nextwork.org/joyful_turquoise_jolly_manatee/uploads/caf37fbf-1c43-4755-ae48-7edde46e07c1_affrnh40)

#
[View this project](https://learn.nextwork.org/projects/caf37fbf-1c43-4755-ae48-7edde46e07c1)*
