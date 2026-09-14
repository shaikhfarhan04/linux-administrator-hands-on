# Phase 1 — Linux Administrator Lab Setup

Great. We’ll start this as a **real Linux Administrator project**, not just a command tutorial.

## 🎯 Phase 1 Objective

By the end of Phase 1, you will have:

```text
AWS
 │
 └── EC2
      │
      └── admin01
           ├── Amazon Linux 2023
           ├── Hostname configured
           ├── Admin user created
           ├── sudo configured
           ├── SSH working
           ├── Linux directory structure
           └── Basic system commands practiced
```

We'll keep the setup **Free-Tier/cost conscious** and use **one EC2 instance initially**.

---

# 1. Prerequisites

You need:

* AWS account
* AWS Console access
* Windows 11 laptop
* SSH client / PowerShell
* An EC2 key pair

We will use:

```text
Region:       us-east-1
OS:           Amazon Linux 2023
Instance:     t3.micro
Hostname:     admin01
Username:     adminuser
```

> **Cost note:** Check your current AWS Free Tier/credits before launching. AWS pricing and Free Tier eligibility can change.

---

# 2. Create the Project Directory

On your Windows machine, open PowerShell.

```powershell
mkdir "C:\Users\farhe\Desktop\AWS DevOps\Linux-Administrator"
cd "C:\Users\farhe\Desktop\AWS DevOps\Linux-Administrator"
```

Create our project structure:

```powershell
mkdir 01-linux-basics
mkdir 02-users-groups
mkdir 03-files-permissions
mkdir 04-package-management
mkdir 05-process-management
mkdir 06-systemd-services
mkdir 07-networking
mkdir 08-ssh
mkdir 09-storage
mkdir 10-lvm
mkdir 11-filesystem
mkdir 12-archive-compression
mkdir 13-text-processing
mkdir 14-bash-scripting
mkdir 15-cron
mkdir 16-logs
mkdir 17-firewall
mkdir 18-selinux
mkdir 19-nginx
mkdir 20-dns
mkdir 21-nfs
mkdir 22-backup-restore
mkdir 23-monitoring
mkdir 24-security
mkdir 25-production-troubleshooting
```

Check:

```powershell
tree
```

---

# 3. Launch EC2

Go to AWS Console → **EC2 → Instances → Launch instance**.

Use:

| Setting               | Value                                     |
| --------------------- | ----------------------------------------- |
| Name                  | `linux-admin01`                           |
| AMI                   | Amazon Linux 2023                         |
| Architecture          | 64-bit                                    |
| Instance type         | `t3.micro`                                |
| Key pair              | Your existing key or create `linux-admin` |
| VPC                   | Default VPC if available                  |
| Subnet                | Any public subnet                         |
| Auto-assign Public IP | Enable                                    |
| Storage               | 8 GiB gp3                                 |
| Security Group        | Create `linux-admin-sg`                   |

### Security Group

For now:

```text
Inbound
--------------------------------
SSH     TCP 22     My IP
```

Do **not** open SSH to:

```text
0.0.0.0/0
```

unless temporarily required for troubleshooting.

---

# 4. Connect to the Server

After the EC2 instance becomes:

```text
Running
```

copy its **Public IPv4 address**.

For example:

```text
54.x.x.x
```

From PowerShell:

```powershell
cd "C:\Users\farhe\Desktop\AWS DevOps\Linux-Administrator"
```

If your key is:

```text
linux-admin.pem
```

connect:

```powershell
ssh -i .\linux-admin.pem ec2-user@YOUR_PUBLIC_IP
```

Example:

```powershell
ssh -i .\linux-admin.pem ec2-user@54.123.45.67
```

You should see something similar to:

```text
       __|  __|_  )
       _|  (     /
      ___|\___|___|

Amazon Linux 2023
```

---

# 5. Verify the Linux Server

Run:

```bash
whoami
```

Expected:

```text
ec2-user
```

Check hostname:

```bash
hostname
```

Check OS:

```bash
cat /etc/os-release
```

Check kernel:

```bash
uname -r
```

Detailed kernel information:

```bash
uname -a
```

Check architecture:

```bash
uname -m
```

Expected:

```text
x86_64
```

---

# 6. First Linux Administrator Checklist

Run these commands one by one:

```bash
whoami
id
hostname
hostnamectl
uname -a
cat /etc/os-release
uptime
date
timedatectl
```

### Understand what they tell you

| Command               | Purpose                     |
| --------------------- | --------------------------- |
| `whoami`              | Current user                |
| `id`                  | UID, GID and groups         |
| `hostname`            | Server hostname             |
| `hostnamectl`         | Hostname/system information |
| `uname -a`            | Kernel information          |
| `cat /etc/os-release` | Linux distribution          |
| `uptime`              | Server uptime/load          |
| `date`                | Current date/time           |
| `timedatectl`         | Timezone/time configuration |

---

# 7. Update the Server

First check available updates:

```bash
sudo dnf check-update
```

Then:

```bash
sudo dnf update -y
```

Verify:

```bash
sudo dnf history
```

---

# 8. Configure Hostname

Our server will be called:

```text
admin01
```

Run:

```bash
sudo hostnamectl set-hostname admin01
```

Verify:

```bash
hostname
```

Expected:

```text
admin01
```

Also:

```bash
hostnamectl
```

You should see:

```text
Static hostname: admin01
```

Reconnect to SSH:

```bash
exit
```

Then:

```powershell
ssh -i .\linux-admin.pem ec2-user@YOUR_PUBLIC_IP
```

---

# 9. Create Linux Administrator User

We don't want to perform every administrative task directly using `ec2-user`.

Create:

```text
adminuser
```

Run:

```bash
sudo useradd -m -s /bin/bash adminuser
```

Verify:

```bash
id adminuser
```

You should see something similar to:

```text
uid=1001(adminuser) gid=1001(adminuser) groups=1001(adminuser)
```

---

# 10. Set Password

Run:

```bash
sudo passwd adminuser
```

You'll be prompted:

```text
New password:
Retype new password:
```

For this lab, create a strong temporary password.

---

# 11. Give Sudo Access

Amazon Linux/RHEL commonly uses the `wheel` group for administrative sudo access.

Check:

```bash
getent group wheel
```

Add the user:

```bash
sudo usermod -aG wheel adminuser
```

Verify:

```bash
id adminuser
```

You should see:

```text
wheel
```

Also:

```bash
groups adminuser
```

---

# 12. Test Sudo

Switch to the new user:

```bash
su - adminuser
```

Check:

```bash
whoami
```

Expected:

```text
adminuser
```

Now:

```bash
sudo whoami
```

Expected:

```text
root
```

This proves:

```text
adminuser
   |
   +---- sudo ----> root
```

Exit back:

```bash
exit
```

---

# 13. Understand Linux Users

Run:

```bash
cat /etc/passwd
```

Don't worry about understanding every line yet.

Look for:

```bash
grep adminuser /etc/passwd
```

You'll get something similar to:

```text
adminuser:x:1001:1001::/home/adminuser:/bin/bash
```

The fields represent:

```text
username
password placeholder
UID
GID
GECOS
home directory
login shell
```

Check the password database:

```bash
sudo cat /etc/shadow
```

Notice that normal users cannot read this file.

Try:

```bash
cat /etc/shadow
```

You should get:

```text
Permission denied
```

That's our first small **Linux security exercise**.

---

# 14. Create Linux Administrator Project Directories

Now create our working structure:

```bash
sudo mkdir -p /opt/linux-admin/{applications,backups,logs,scripts,configuration,users}
```

Check:

```bash
sudo ls -la /opt/linux-admin
```

Expected:

```text
applications
backups
logs
scripts
configuration
users
```

---

# 15. Change Ownership

Give `adminuser` ownership:

```bash
sudo chown -R adminuser:adminuser /opt/linux-admin
```

Check:

```bash
ls -ld /opt/linux-admin
```

And:

```bash
ls -l /opt/linux-admin
```

---

# 16. Basic File Operations

Switch to adminuser:

```bash
su - adminuser
```

Go to the project:

```bash
cd /opt/linux-admin
```

Create a file:

```bash
touch test.txt
```

Check:

```bash
ls -l
```

Create another:

```bash
touch server-info.txt
```

Write information:

```bash
hostname > server-info.txt
```

Check:

```bash
cat server-info.txt
```

Append:

```bash
date >> server-info.txt
```

Read:

```bash
cat server-info.txt
```

---

# 17. Practice `cp`, `mv`, `rm`

Copy:

```bash
cp server-info.txt backups/
```

Check:

```bash
ls -l backups/
```

Move:

```bash
mv test.txt users/
```

Check:

```bash
ls -l users/
```

Delete:

```bash
rm users/test.txt
```

Verify:

```bash
ls -l users/
```

---

# 18. Practice Directory Navigation

Run:

```bash
pwd
```

Then:

```bash
cd /var
pwd
```

Go into log:

```bash
cd log
pwd
```

Go one level up:

```bash
cd ..
```

Go home:

```bash
cd ~
```

Go to root:

```bash
cd /
```

List root:

```bash
ls -la
```

---

# 19. Important Linux Directories

Run:

```bash
ls -ld /*
```

You will see directories such as:

```text
/bin
/boot
/dev
/etc
/home
/lib
/media
/mnt
/opt
/proc
/root
/run
/sbin
/sys
/tmp
/usr
/var
```

For your administrator interviews, understand these:

| Directory | Purpose                       |
| --------- | ----------------------------- |
| `/etc`    | Configuration                 |
| `/var`    | Variable data/logs            |
| `/home`   | User home directories         |
| `/root`   | Root user's home              |
| `/tmp`    | Temporary files               |
| `/opt`    | Optional/application software |
| `/usr`    | User/system programs          |
| `/boot`   | Boot files                    |
| `/dev`    | Devices                       |
| `/proc`   | Process/kernel information    |
| `/sys`    | Kernel/device information     |
| `/run`    | Runtime information           |

---

# 20. System Resource Check

Run:

```bash
free -h
```

Disk:

```bash
df -h
```

CPU:

```bash
nproc
```

Block devices:

```bash
lsblk
```

Memory:

```bash
free -m
```

Load:

```bash
uptime
```

Processes:

```bash
ps
```

More detailed:

```bash
ps aux
```

---

# 21. Your First Administrator Report

Create:

```bash
nano /opt/linux-admin/server-report.txt
```

Add:

```text
Linux Administrator Server Report

Hostname:
Operating System:
Kernel:
Architecture:
Current User:
Uptime:
CPU Count:
Memory:
Disk:
IP Address:
```

Save and exit.

Then populate information manually using:

```bash
hostname
cat /etc/os-release
uname -r
uname -m
whoami
uptime
nproc
free -h
df -h
ip addr
```

---

# 22. Automated Server Report

Now let's make our first administrator script.

Create:

```bash
nano /opt/linux-admin/scripts/server-info.sh
```

Put:

```bash
#!/bin/bash

echo "======================================"
echo "       LINUX SERVER INFORMATION"
echo "======================================"

echo "Hostname       : $(hostname)"
echo "OS             : $(grep '^PRETTY_NAME=' /etc/os-release | cut -d= -f2- | tr -d '"')"
echo "Kernel         : $(uname -r)"
echo "Architecture   : $(uname -m)"
echo "Current User   : $(whoami)"
echo "Uptime         : $(uptime -p)"
echo "CPU Cores      : $(nproc)"

echo
echo "----- MEMORY -----"
free -h

echo
echo "----- DISK -----"
df -h /

echo
echo "----- IP ADDRESS -----"
hostname -I

echo
echo "======================================"
```

Make executable:

```bash
chmod +x /opt/linux-admin/scripts/server-info.sh
```

Run:

```bash
/opt/linux-admin/scripts/server-info.sh
```

---

# 23. Phase 1 Troubleshooting Exercises

Now we start thinking like a Linux Administrator.

## Exercise 1 — Permission Problem

Run:

```bash
chmod 600 /opt/linux-admin/server-report.txt
```

Switch to another user:

```bash
sudo -iu ec2-user
```

Try:

```bash
cat /opt/linux-admin/server-report.txt
```

You may receive:

```text
Permission denied
```

Investigate:

```bash
ls -l /opt/linux-admin/server-report.txt
```

Then:

```bash
id
```

Your task is to determine **why access is denied**.

---

## Exercise 2 — Find a File

Run:

```bash
find /opt/linux-admin -name "*.txt"
```

Find directories:

```bash
find /opt/linux-admin -type d
```

Find files:

```bash
find /opt/linux-admin -type f
```

---

## Exercise 3 — Disk Investigation

Run:

```bash
df -h
```

Then:

```bash
du -sh /var/*
```

Your task:

> Determine which `/var` directory is consuming the most space.

---

# 24. Phase 1 Interview Questions

You should now be able to answer:

### Q1. What is Linux?

Linux is an open-source operating system kernel used by distributions such as Amazon Linux, RHEL, Ubuntu, Debian, Rocky Linux and others.

### Q2. What is the difference between root and a normal user?

`root` has unrestricted administrative privileges, while normal users have limited permissions and can use `sudo` when authorized.

### Q3. What is sudo?

`sudo` allows an authorized user to execute commands with elevated privileges.

### Q4. What is `/etc`?

It contains system and application configuration files.

### Q5. What is `/var/log`?

It contains system and application log files.

### Q6. How do you check Linux version?

```bash
cat /etc/os-release
```

### Q7. How do you check kernel version?

```bash
uname -r
```

### Q8. How do you check memory?

```bash
free -h
```

### Q9. How do you check disk usage?

```bash
df -h
```

### Q10. How do you check CPU count?

```bash
nproc
```

---

# 25. Phase 1 Completion Checklist

Before moving to Phase 2, make sure these are working:

```text
[ ] EC2 Linux server created
[ ] SSH connection working
[ ] Amazon Linux 2023 verified
[ ] Hostname changed to admin01
[ ] adminuser created
[ ] adminuser has sudo
[ ] /opt/linux-admin created
[ ] Project directories created
[ ] Ownership configured
[ ] Basic file commands practiced
[ ] Linux filesystem directories explored
[ ] CPU/memory/disk checked
[ ] server-info.sh created
[ ] Script executed successfully
[ ] Permission troubleshooting practiced
[ ] File searching practiced
[ ] Disk investigation practiced
```

### Final verification

Run this:

```bash
echo "===== FINAL PHASE 1 CHECK ====="

echo "User:"
whoami

echo "Hostname:"
hostname

echo "OS:"
grep PRETTY_NAME /etc/os-release

echo "Kernel:"
uname -r

echo "CPU:"
nproc

echo "Memory:"
free -h | head -2

echo "Disk:"
df -h /

echo "Project:"
ls -ld /opt/linux-admin

echo "Admin User:"
id adminuser

echo "Sudo:"
sudo whoami
```

The last line should return:

```text
root
```

---

## 📌 What comes next

**Phase 2 will be "Linux Filesystem, File Management & Command Mastery."**

We'll go much deeper into:

```text
ls
cd
pwd
mkdir
touch
cp
mv
rm
find
locate
file
stat
tree
du
df
head
tail
less
more
cat
ln
readlink
```

and then build **real administrator exercises**, including finding large files, locating recently modified files, identifying broken symbolic links, recovering accidentally deleted/overwritten files, and troubleshooting filesystem-related problems.
