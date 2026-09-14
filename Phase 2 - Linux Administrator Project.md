# 🐧 Linux Administrator Project — Phase 2

## Linux Filesystem, File Management & Command Mastery

In Phase 1, we created `admin01`, configured `adminuser`, and built `/opt/linux-admin`.

In **Phase 2**, we'll work like a Linux Administrator who needs to manage thousands of files, investigate disk usage, find configuration files, work with symbolic links, and troubleshoot file-related problems.

---

# 1. Phase 2 Objectives

By the end of this phase, you will know how to use:

```text
pwd
ls
cd
mkdir
touch
cp
mv
rm
rmdir
file
stat
tree
find
locate
which
whereis
du
df
basename
dirname
readlink
ln
```

You'll also practice:

* Absolute vs relative paths
* Hidden files
* Wildcards
* File timestamps
* Symbolic links
* Hard links
* File searching
* Disk usage investigation
* Broken links
* Permission-related file problems
* Safe deletion
* Administrator troubleshooting

---

# 2. Connect to `admin01`

From Windows PowerShell:

```powershell
ssh -i .\linux-admin.pem adminuser@YOUR_PUBLIC_IP
```

If your key isn't in the current directory:

```powershell
ssh -i "C:\path\to\linux-admin.pem" adminuser@YOUR_PUBLIC_IP
```

Verify:

```bash
whoami
hostname
```

Expected:

```text
adminuser
admin01
```

---

# 3. Create Phase 2 Workspace

Run:

```bash
sudo mkdir -p /opt/linux-admin/phase2/{files,directories,links,search,backup,logs}
```

Give yourself ownership:

```bash
sudo chown -R adminuser:adminuser /opt/linux-admin/phase2
```

Enter it:

```bash
cd /opt/linux-admin/phase2
```

Check:

```bash
pwd
```

Expected:

```text
/opt/linux-admin/phase2
```

---

# 4. `pwd` — Print Working Directory

`pwd` tells you where you currently are.

```bash
pwd
```

Example:

```text
/opt/linux-admin/phase2
```

This is important because administrators frequently work with deeply nested directories.

---

# 5. Absolute vs Relative Path

## Absolute path

Starts from `/`.

```bash
cd /opt/linux-admin/phase2
```

## Relative path

Starts from your current directory.

```bash
cd files
```

Check:

```bash
pwd
```

Go back:

```bash
cd ..
```

Go back again:

```bash
cd ..
```

---

# 6. Important `cd` Commands

Practice all of these:

```bash
cd /
```

Root directory.

```bash
cd ~
```

Home directory.

```bash
cd ..
```

Parent directory.

```bash
cd -
```

Previous directory.

```bash
cd /var/log
```

Specific directory.

Check after each:

```bash
pwd
```

---

# 7. `ls` — List Files

Basic:

```bash
ls
```

Detailed:

```bash
ls -l
```

Hidden files:

```bash
ls -a
```

Detailed + hidden:

```bash
ls -la
```

Human-readable file sizes:

```bash
ls -lh
```

Reverse order:

```bash
ls -lr
```

Sort by modification time:

```bash
ls -lt
```

Oldest first:

```bash
ls -ltr
```

### Administrator trick

To find recently modified files:

```bash
ls -lt /var/log
```

---

# 8. Understanding `ls -l`

Create a file:

```bash
touch test.txt
```

Run:

```bash
ls -l test.txt
```

Example:

```text
-rw-r--r-- 1 adminuser adminuser 0 Sep 14 11:50 test.txt
```

Breakdown:

```text
- rw- r-- r--
│ │   │   │
│ │   │   └── Others
│ │   └────── Group
│ └────────── Owner
└──────────── File type
```

We'll cover permissions deeply in **Phase 4**.

---

# 9. Hidden Files

Create hidden files:

```bash
touch .config
touch .secret
```

Normal:

```bash
ls
```

They won't appear.

Now:

```bash
ls -la
```

You'll see:

```text
.config
.secret
```

Linux considers files beginning with `.` hidden.

---

# 10. `mkdir`

Create directories:

```bash
mkdir application
mkdir database
mkdir backup
```

Check:

```bash
ls -l
```

Create nested directories:

```bash
mkdir -p application/config/nginx
```

Without `-p`, creating multiple nonexistent parent directories can fail.

Check:

```bash
find application -type d
```

---

# 11. `touch`

Create files:

```bash
touch app.conf
touch database.conf
touch nginx.conf
```

Create multiple files:

```bash
touch file1.txt file2.txt file3.txt
```

Check:

```bash
ls -l
```

---

# 12. `cp` — Copy

Create sample files:

```bash
echo "Linux Administrator Lab" > application/app.txt
```

Copy:

```bash
cp application/app.txt backup/
```

Check:

```bash
ls -l backup/
```

---

## Copy a directory

```bash
cp -r application backup/
```

Check:

```bash
find backup
```

### Important

`-r` means recursive.

You'll commonly use:

```bash
cp -r source destination
```

---

# 13. `mv` — Move/Rename

Move:

```bash
mv file1.txt application/
```

Rename:

```bash
mv file2.txt important.txt
```

Check:

```bash
ls -l
```

A useful point:

> Linux doesn't have a separate `rename` command for ordinary file renaming. `mv` is commonly used.

---

# 14. `rm` — Delete

Create:

```bash
touch delete-me.txt
```

Delete:

```bash
rm delete-me.txt
```

### Dangerous command

```bash
rm -rf directory
```

This recursively deletes files/directories without normal interactive confirmation.

**Never experiment with `rm -rf` against `/`, `/etc`, `/home`, `/var`, or another important system path.**

For safer deletion:

```bash
rm -i file.txt
```

You'll be asked for confirmation.

---

# 15. `rmdir`

`rmdir` removes **empty directories**.

Create:

```bash
mkdir emptydir
```

Remove:

```bash
rmdir emptydir
```

If directory contains files:

```bash
mkdir testdir
touch testdir/file.txt
rmdir testdir
```

You'll receive an error because the directory isn't empty.

---

# 16. Wildcards

Wildcards are extremely important for administrators.

## `*`

Matches many characters.

```bash
ls *.txt
```

Example:

```text
file1.txt
file2.txt
important.txt
```

---

## `?`

Matches one character.

```bash
ls file?.txt
```

Could match:

```text
file1.txt
file2.txt
```

but not:

```text
file10.txt
```

---

## Character ranges

```bash
ls file[1-3].txt
```

Matches:

```text
file1.txt
file2.txt
file3.txt
```

---

# 17. Practical Wildcard Exercise

Create:

```bash
touch app.log
touch app1.log
touch app2.log
touch database.log
touch nginx.log
touch server.txt
touch backup.txt
```

Find `.log` files:

```bash
ls *.log
```

Find `.txt`:

```bash
ls *.txt
```

Find files starting with `app`:

```bash
ls app*
```

Find `app1` or `app2`:

```bash
ls app?.log
```

---

# 18. `file`

`file` identifies what type of file something actually is.

Run:

```bash
file /etc/passwd
```

Try:

```bash
file /bin/bash
```

Try:

```bash
file /opt/linux-admin/phase2/app.conf
```

This is useful when a file has a misleading extension.

For example:

```text
application.txt
```

doesn't necessarily mean it's a text file.

---

# 19. `stat`

`stat` gives detailed file information.

```bash
stat application/app.txt
```

You'll see information including:

```text
File
Size
Blocks
IO Block
Device
Inode
Links
Access
Modify
Change
```

Three timestamps are particularly important:

```text
Access
Modify
Change
```

---

# 20. File Timestamps

Create:

```bash
touch timestamp.txt
```

Check:

```bash
stat timestamp.txt
```

Modify:

```bash
echo "Hello Linux" >> timestamp.txt
```

Check again:

```bash
stat timestamp.txt
```

You should notice the modification time changed.

---

# 21. `find` — One of the Most Important Commands

Find all files:

```bash
find /opt/linux-admin/phase2 -type f
```

Find directories:

```bash
find /opt/linux-admin/phase2 -type d
```

Find `.log` files:

```bash
find /opt/linux-admin/phase2 -type f -name "*.log"
```

Find `.txt`:

```bash
find /opt/linux-admin/phase2 -type f -name "*.txt"
```

---

# 22. Find by Name

```bash
find /opt/linux-admin -name "server-info.txt"
```

Case insensitive:

```bash
find /opt/linux-admin -iname "SERVER-INFO.TXT"
```

---

# 23. Find Large Files

This is a very common administrator task.

Find files larger than 100 MB:

```bash
sudo find / -type f -size +100M 2>/dev/null
```

Find files larger than 1 GB:

```bash
sudo find / -type f -size +1G 2>/dev/null
```

### Why `2>/dev/null`?

Some directories produce permission errors.

```text
2
```

means standard error.

```text
/dev/null
```

discards it.

We'll cover redirection in greater depth later.

---

# 24. Find Recently Modified Files

Files modified within the last 24 hours:

```bash
find /opt/linux-admin -type f -mtime -1
```

Modified more than 7 days ago:

```bash
find /opt/linux-admin -type f -mtime +7
```

---

# 25. Find Empty Files

```bash
find /opt/linux-admin -type f -empty
```

Create one:

```bash
touch empty.txt
```

Then:

```bash
find . -type f -empty
```

---

# 26. Find Files by Permission

Example:

```bash
find /opt/linux-admin -type f -perm 600
```

We'll use this much more heavily when we reach permissions.

---

# 27. `locate`

`locate` searches a database of file names.

Check:

```bash
locate passwd
```

If unavailable:

```bash
sudo dnf install mlocate -y
```

Depending on the Amazon Linux version/package availability, the exact locate implementation may differ.

For current filesystem state, `find` is more reliable.

---

# 28. `which`

Find where a command is installed:

```bash
which bash
```

```bash
which python3
```

```bash
which systemctl
```

Example:

```text
/usr/bin/bash
```

---

# 29. `whereis`

```bash
whereis bash
```

It can show locations associated with the command, such as binaries and documentation.

Compare:

```bash
which bash
whereis bash
```

---

# 30. `du` — Directory Disk Usage

Check current directory:

```bash
du -sh .
```

Check directories:

```bash
du -sh *
```

Check `/var`:

```bash
sudo du -sh /var/*
```

Sort by size:

```bash
sudo du -sh /var/* 2>/dev/null | sort -h
```

This is a **very useful troubleshooting command**.

---

# 31. `df` — Filesystem Usage

Run:

```bash
df -h
```

Example:

```text
Filesystem      Size  Used Avail Use% Mounted on
/dev/...         8G  2.1G  5.9G  27% /
```

Important distinction:

### `df`

Tells you:

> How full is the filesystem?

### `du`

Tells you:

> Which files/directories are consuming the space?

Remember:

```text
df → filesystem level
du → directory/file level
```

---

# 32. Real Administrator Disk Investigation

Suppose you receive:

```text
ALERT: / filesystem is 92% full
```

Your troubleshooting starts:

```bash
df -h /
```

Then:

```bash
sudo du -xhd1 / | sort -h
```

If `/var` is large:

```bash
sudo du -xhd1 /var | sort -h
```

If `/var/log` is large:

```bash
sudo du -xhd1 /var/log | sort -h
```

Then:

```bash
sudo find /var/log -type f -size +100M -ls
```

This is the type of investigation expected from a Linux Administrator.

---

# 33. Symbolic Links

Create a target:

```bash
echo "Production configuration" > application/production.conf
```

Create symbolic link:

```bash
ln -s application/production.conf production.conf
```

Check:

```bash
ls -l production.conf
```

You'll see something similar to:

```text
production.conf -> application/production.conf
```

Read it:

```bash
cat production.conf
```

---

# 34. `readlink`

Check where the symbolic link points:

```bash
readlink production.conf
```

Expected:

```text
application/production.conf
```

Absolute path:

```bash
readlink -f production.conf
```

---

# 35. Broken Symbolic Link — Troubleshooting Exercise

Delete the original:

```bash
rm application/production.conf
```

Now:

```bash
ls -l production.conf
```

The link still exists, but the target doesn't.

Try:

```bash
cat production.conf
```

You'll receive something like:

```text
No such file or directory
```

Find broken symbolic links:

```bash
find . -xtype l
```

This is a great real-world troubleshooting technique.

---

# 36. Hard Links

Create:

```bash
echo "Important data" > original.txt
```

Create hard link:

```bash
ln original.txt hardlink.txt
```

Check:

```bash
ls -li original.txt hardlink.txt
```

Notice that both have the **same inode number**.

Now modify:

```bash
echo "New data" >> original.txt
```

Read:

```bash
cat hardlink.txt
```

You'll see the change.

---

# 37. Symbolic vs Hard Link

Remember this:

```text
Symbolic link
     |
     +---- points to filename/path

Hard link
     |
     +---- points to same inode/data
```

Example:

```text
original.txt
     |
     +---- inode 12345
              |
              +---- hardlink.txt
```

Symbolic:

```text
production.conf
       |
       +----> application/production.conf
```

---

# 38. `basename`

Run:

```bash
basename /var/log/nginx/access.log
```

Output:

```text
access.log
```

Useful in shell scripts.

---

# 39. `dirname`

```bash
dirname /var/log/nginx/access.log
```

Output:

```text
/var/log/nginx
```

Together:

```bash
basename /var/log/nginx/access.log
dirname /var/log/nginx/access.log
```

---

# 40. Phase 2 Practical Project

Now let's simulate a real application.

Create:

```bash
cd /opt/linux-admin/phase2

mkdir -p application/{config,logs,data}
mkdir -p backup/{daily,weekly}
mkdir -p users/{developer1,developer2}
```

Create files:

```bash
touch application/config/application.conf
touch application/config/database.conf
touch application/logs/application.log
touch application/logs/error.log
touch application/data/users.db
```

Add data:

```bash
echo "Application started successfully" > application/logs/application.log
echo "Database connection successful" > application/logs/database.log
echo "ERROR: Connection timeout" > application/logs/error.log
```

---

# 41. Investigate the Application

List everything:

```bash
find application -type f
```

Find logs:

```bash
find application -type f -name "*.log"
```

Find configuration:

```bash
find application -type f -name "*.conf"
```

Find files containing `ERROR`:

```bash
grep -R "ERROR" application/
```

Check sizes:

```bash
du -sh application/*
```

---

# 42. Create a Backup

```bash
cp -r application backup/daily/
```

Check:

```bash
find backup
```

Now simulate a configuration change:

```bash
echo "database_host=production-db" >> application/config/database.conf
```

Check:

```bash
cat application/config/database.conf
```

Restore the configuration:

```bash
cp backup/daily/application/config/database.conf application/config/
```

Verify:

```bash
cat application/config/database.conf
```

---

# 43. Troubleshooting Scenario #1

## Problem

A developer says:

> "The `application.log` file has disappeared."

First:

```bash
find /opt/linux-admin/phase2 -name "application.log"
```

If found:

```bash
ls -l PATH_TO_FILE
```

Check:

```bash
stat PATH_TO_FILE
```

Then determine:

```text
Where is it?
Who owns it?
What are the permissions?
When was it modified?
Is it a regular file?
Is it a symbolic link?
```

---

# 44. Troubleshooting Scenario #2

## Problem

> "The application directory is using too much disk."

Start:

```bash
du -sh application
```

Then:

```bash
du -sh application/*
```

Then:

```bash
du -sh application/*/* 2>/dev/null
```

Find large files:

```bash
find application -type f -size +1M -ls
```

---

# 45. Troubleshooting Scenario #3

## Problem

> "A configuration file cannot be found."

Don't immediately assume it is missing.

Search:

```bash
find /etc /opt -type f -name "database.conf" 2>/dev/null
```

Check possible symlink:

```bash
find /opt -type l -ls
```

Check:

```bash
readlink -f PATH
```

---

# 46. Troubleshooting Scenario #4

## Problem

> "Disk usage is 90%, but `du` doesn't explain where the space went."

This is an important real-world scenario.

Check:

```bash
df -h
```

Then:

```bash
sudo du -xhd1 / | sort -h
```

If the totals don't make sense, investigate deleted-but-open files:

```bash
sudo lsof +L1
```

Why?

A process can keep a deleted file open. The directory entry disappears, but the disk blocks remain allocated until the process closes the file.

This is a common Linux production issue.

---

# 47. Troubleshooting Scenario #5

## Broken Symlink

Find links:

```bash
find /opt/linux-admin -type l -ls
```

Find broken links:

```bash
find /opt/linux-admin -xtype l
```

Inspect:

```bash
readlink -f LINK_NAME
```

Repair by recreating the target or correcting the link.

---

# 48. Administrator Command Challenge

Without looking above, try to accomplish these.

### Task 1

Create:

```text
/opt/linux-admin/phase2/challenge/
```

with:

```text
configs/
logs/
backup/
data/
```

### Task 2

Create:

```text
web.conf
database.conf
application.log
error.log
```

### Task 3

Find all `.conf` files.

### Task 4

Find all `.log` files.

### Task 5

Find all empty files.

### Task 6

Create a symbolic link to `web.conf`.

### Task 7

Find the symbolic link.

### Task 8

Find the target of the symbolic link.

### Task 9

Delete the target.

### Task 10

Identify the broken symbolic link.

Try doing these **without looking at the command list**.

---

# 49. Phase 2 Interview Questions

### Q1. Difference between `df` and `du`?

```text
df → filesystem usage
du → file/directory usage
```

### Q2. How do you find files larger than 1 GB?

```bash
find / -type f -size +1G 2>/dev/null
```

### Q3. How do you find files modified in the last 24 hours?

```bash
find /path -type f -mtime -1
```

### Q4. How do you find broken symbolic links?

```bash
find /path -xtype l
```

### Q5. Difference between hard link and symbolic link?

A hard link points to the same inode, while a symbolic link points to another path/name.

### Q6. How do you check filesystem usage?

```bash
df -h
```

### Q7. How do you find the largest directories?

```bash
du -sh /path/* | sort -h
```

### Q8. How do you identify a file type?

```bash
file filename
```

### Q9. How do you check detailed file metadata?

```bash
stat filename
```

### Q10. How do you find where a command is installed?

```bash
which command
```

or:

```bash
whereis command
```

---

# 50. Phase 2 Final Checklist

Before moving to Phase 3:

```text
[ ] pwd
[ ] cd
[ ] ls
[ ] mkdir
[ ] touch
[ ] cp
[ ] mv
[ ] rm
[ ] rmdir
[ ] Wildcards
[ ] file
[ ] stat
[ ] find
[ ] locate
[ ] which
[ ] whereis
[ ] du
[ ] df
[ ] basename
[ ] dirname
[ ] readlink
[ ] Symbolic links
[ ] Hard links
[ ] Broken link troubleshooting
[ ] Disk usage troubleshooting
[ ] File search troubleshooting
```

## 🔥 Most important commands from Phase 2

If you're preparing for a Linux Administrator interview, make these commands second nature:

```bash
find
ls -lah
du -sh
df -h
stat
file
cp -r
mv
rm
ln
readlink
```

And especially remember this production troubleshooting sequence:

```text
Disk Alert
    ↓
df -h
    ↓
Which filesystem?
    ↓
du -xhd1 /
    ↓
Which directory?
    ↓
du -xhd1 /var
    ↓
Which files?
    ↓
find ... -size
    ↓
Still unexplained?
    ↓
lsof +L1
```

**Phase 3 will be Users, Groups & Account Administration** — where we'll create multiple users, groups, service accounts, configure passwords, account expiry, sudo access, `/etc/passwd`, `/etc/shadow`, `/etc/group`, `su`, `sudo`, and troubleshoot login/account problems.
