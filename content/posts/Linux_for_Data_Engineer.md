+++
title = 'Linux for Data Engineers: Essential Commands and Tools'
date = 2025-03-28T16:00:34+05:30
draft = false
author = 'Santosh Kumar Gouda'
+++


As a data engineer, mastering Linux is crucial for efficient data processing, pipeline management, and server administration. This guide covers essential Linux commands, tools, and concepts that every data engineer should know.

## Table of Contents
1. [File System Navigation](#file-system-navigation)
2. [File Operations](#file-operations)
3. [Text Processing](#text-processing)
4. [System Monitoring](#system-monitoring)
5. [Networking](#networking)
6. [Package Management](#package-management)
7. [Process Management](#process-management)
8. [Shell Scripting Basics](#shell-scripting-basics)
9. [Data-Specific Tools](#data-specific-tools)
10. [Useful Shortcuts](#useful-shortcuts)

## File System Navigation

```bash
# List files with details
ls -lah

# Show current directory
pwd

# Change directory
cd /path/to/directory

# Go to home directory
cd ~

# Find files (great for locating datasets)
find /data -name "*.csv" -type f

# Disk usage analysis (critical for managing large datasets)
du -sh /data/lake/
df -h
File Operations
bash
Copy
# View file contents
cat large_file.json | jq  # with jq for JSON prettifying
head -n 100 data.csv      # first 100 lines
tail -f logfile.log       # follow log in real-time

# Search within files (grep is your best friend)
grep "error" *.log
grep -r "pattern" /data/  # recursive search

# Compare files
diff file1.csv file2.csv

# File manipulation
cut -d',' -f1,3 data.csv      # extract columns
sort data.txt | uniq -c       # count unique values
awk -F',' '{print $1}' data.csv  # awk for column extraction

# Compression (essential for big data)
tar -czvf archive.tar.gz /data/
pigz -k large_file.csv        # parallel gzip
Text Processing
bash
Copy
# Count lines in a file (useful for data validation)
wc -l huge_dataset.csv

# Stream editor for transformations
sed 's/old/new/g' file.txt
sed -i '1d' data.csv  # remove header row

# Advanced text processing with awk
awk 'BEGIN {FS=","; OFS="|"} {print $1,$3,$5}' data.csv
awk -F',' '$3 > 100 {print $0}' sales.csv  # filter rows

# CSV specific tools
csvcut -c 1,3,5 data.csv      # from csvkit
csvsql --query "SELECT * FROM data WHERE amount > 100" data.csv
System Monitoring
bash
Copy
# Check system resources
top
htop        # more user-friendly
glances     # comprehensive monitoring

# Memory usage
free -h

# Check CPU info
lscpu

# Monitor disk I/O
iotop
iostat -x 1

# Check running processes
ps aux | grep python
Networking
bash
Copy
# Check network connections
netstat -tulnp
ss -tulnp      # modern alternative

# Test connectivity
ping google.com
curl -I https://api.example.com  # check HTTP headers

# Download files
wget https://example.com/data.zip
curl -O https://example.com/data.csv

# SSH (essential for remote servers)
ssh user@server
scp data.csv user@server:/path/to/destination/
rsync -avz /local/data/ user@remote:/backup/data/  # efficient sync
Package Management
bash
Copy
# Ubuntu/Debian
sudo apt update
sudo apt install python3-pip

# CentOS/RHEL
sudo yum install epel-release
sudo yum install python3

# Python packages
pip install pandas numpy
pip freeze > requirements.txt

# Conda environments
conda create -n data-env python=3.8
conda activate data-env
Process Management
bash
Copy
# Run process in background
python etl_script.py &

# List jobs
jobs

# Bring to foreground
fg %1

# Disown process to keep running after logout
disown -h %1

# Run process that survives terminal close
nohup python pipeline.py > output.log 2>&1 &

# Schedule jobs with cron
crontab -e
# Add line: 0 3 * * * /path/to/backup_script.sh
Shell Scripting Basics
bash
Copy
#!/bin/bash

# Simple ETL script example
INPUT_FILE=$1
OUTPUT_FILE=$2

if [ ! -f "$INPUT_FILE" ]; then
    echo "Error: Input file not found!"
    exit 1
fi

# Process data
echo "Processing $INPUT_FILE..."
awk -F',' '{print $1","$3","$5}' "$INPUT_FILE" | \
    grep -v "bad_data" > temp.csv

# Add timestamp
TIMESTAMP=$(date +"%Y-%m-%d %H:%M:%S")
sed -i "1i processed_at,$TIMESTAMP" temp.csv

mv temp.csv "$OUTPUT_FILE"
echo "Output saved to $OUTPUT_FILE"
Data-Specific Tools
bash
Copy
# Convert JSON to CSV
jq -r '. | [.field1, .field2] | @csv' data.json > output.csv

# CSV processing with Miller
mlr --csv cut -f date,value then sort -n value data.csv

# Parquet tools
parquet-tools head data.parquet
parquet-tools schema data.parquet

# Database clients
psql -h dbhost -U user -d database
mysql -u user -p database -e "SELECT * FROM table"

# Hadoop ecosystem
hdfs dfs -ls /data/
hdfs dfs -put localfile.csv /data/
Useful Shortcuts
bash
Copy
Ctrl+C        # Kill current process
Ctrl+Z        # Suspend process
Ctrl+D        # EOF (logout if empty line)
Ctrl+R        # Search command history
!!            # Repeat last command
!$            # Last argument of previous command
Conclusion
Mastering these Linux commands and tools will significantly boost your productivity as a data engineer. The command line is your most powerful tool for data manipulation, pipeline management, and server administration.

Remember to always:

Check manual pages with man command

Use --help for quick command reference

Test commands with small datasets before applying to production data

Chain commands with pipes (|) for complex data transformations

Happy data engineering!
