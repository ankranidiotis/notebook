# Rclone Bi-Directional Sync (bisync) Quick Guide

This guide ensures both your Local Machine and Google Drive are kept in sync using `rclone bisync` and cron.

## Part 1: Initial Sync Setup (Manual)

This step establishes the baseline state and must be run once.

1. **Run a Test (Dry Run):** Confirm the command works and shows the expected copies.
    
    ```
    rclone bisync \
	    --dry-run \
	    --resync \
	    /path/to/local GDRIVE_REMOTE:path/to/remote
    ```
    
2. **Perform Initial Sync:** Run the actual command. This creates the state files needed for future runs.
    
    ```
    rclone bisync \
	    --resync \
	    --progress \
	    --log-file ~/bisync_initial.log
	    /path/to/local GDRIVE_REMOTE:path/to/remote
    ```
    
    > **Note:** Remove the `--resync` flag for all subsequent automated runs. This flag generates initial state files stored in `.cache/rclone/bisync` which will be used later for comparing and updating. 
    

---

## Part 2: Automation Script

Create a script for the cron job, as cron needs the full path for the rclone executable.

1. **Create Script:** `nano ~/rclone_bisync_auto.sh`
    
2. **Add Content:**
    
    ```
    #!/bin/bash
    
    LOG_FILE=~/rclone_bisync_runs.log
    RCLONE_PATH=/usr/bin/rclone
    
    $RCLONE_PATH bisync \
        --progress \
        --log-file "$LOG_FILE" \
        --log-level INFO \
        /path/to/local/folder \
        GDRIVE_REMOTE:path/to/remote/folder
    ```
    
1. **Make Executable:**
    
    ```
    chmod +x ~/rclone_bisync_auto.sh
    ```
    

---

## Part 3: Schedule with Cron

Use `crontab` to schedule the script.

1. **Open Crontab:**
    
    ```
    crontab -e
    ```
    
2. **Add Entry (Example: Run Every 6 Hours):**
    
    ```
    0 */6 * * * /home/YOUR_USERNAME/rclone_bisync_auto.sh
    ```
    
    > **Remember:** Replace `YOUR_USERNAME` with your actual username.
    
3. **Save and Exit.**

---

## Conflict Handling

If a file is modified on **both** sides since the last sync, `bisync` will:

- **Rename** the remote file with a `_conflict_` suffix.
    
- **Copy** the local file over to the remote.
    
- Check the log file (`~/rclone_bisync_runs.log`) to review and manually resolve conflicts.
