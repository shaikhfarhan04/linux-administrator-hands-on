Absolutely. A good Linux Administrator project should be **practical rather than just a list of commands**. We can build one project that simulates managing a production Linux server and intentionally introduces failures so you practice troubleshooting.

# 🐧 Linux Administrator Hands-On Project

## Project: Production Linux Server Administration & Troubleshooting Lab

### Objective

Build and administer a Linux server environment covering:

* Linux installation and system basics
* Users and groups
* Files, directories and permissions
* ACLs
* Package management
* Processes and services
* Systemd
* Networking
* SSH
* Disk and filesystem management
* LVM
* Mounting and `/etc/fstab`
* File compression and archiving
* Searching and text processing
* Bash scripting
* Cron jobs
* Log management
* Firewall
* SELinux
* DNS
* Web server
* Database basics
* NFS
* Backup and restore
* Performance monitoring
* Troubleshooting
* Security hardening
* Automation

---

# 1. Project Architecture

We will use **Amazon Linux 2023 / RHEL-compatible Linux** because it is useful for both Linux Administrator and DevOps roles.

```text
                    Linux Administrator Lab
                             |
             +---------------+---------------+
             |                               |
       Linux Admin Server              Application Server
          admin01                         app01
             |                               |
       Administration                 Web Application
             |                               |
       +-----+-----+                 +-------+-------+
       |           |                 |               |
     Users       Storage           Nginx          Logs
       |           |                 |
     Groups      LVM              Port 80
       |
   Permissions
       |
   SSH / Sudo
```

### Recommended setup

| Server    | Purpose                     | Instance |
| --------- | --------------------------- | -------- |
| `admin01` | Administration / monitoring | t3.micro |
| `app01`   | Web server / application    | t3.micro |

For a local lab, you can alternatively use **VirtualBox + Ubuntu/Rocky/AlmaLinux**.

---

# 2. Project Folder Structure

Create this Git repository:

```text
linux-administrator-hands-on/
│
├── README.md
│
├── 01-linux-basics/
│   ├── commands.md
│   └── exercises.md
│
├── 02-users-groups/
│   ├── commands.md
│   └── exercises.md
│
├── 03-files-permissions/
│   ├── commands.md
│   └── exercises.md
│
├── 04-package-management/
│   ├── commands.md
│   └── exercises.md
│
├── 05-process-management/
│   ├── commands.md
│   └── exercises.md
│
├── 06-systemd-services/
│   ├── commands.md
│   └── troubleshooting.md
│
├── 07-networking/
│   ├── commands.md
│   └── troubleshooting.md
│
├── 08-ssh/
│   ├── commands.md
│   └── troubleshooting.md
│
├── 09-storage/
│   ├── commands.md
│   └── troubleshooting.md
│
├── 10-lvm/
│   ├── commands.md
│   └── troubleshooting.md
│
├── 11-filesystem/
│   ├── commands.md
│   └── troubleshooting.md
│
├── 12-archive-compression/
│   └── commands.md
│
├── 13-text-processing/
│   └── commands.md
│
├── 14-bash-scripting/
│   ├── scripts/
│   └── README.md
│
├── 15-cron/
│   └── commands.md
│
├── 16-logs/
│   └── troubleshooting.md
│
├── 17-firewall/
│   └── troubleshooting.md
│
├── 18-selinux/
│   └── troubleshooting.md
│
├── 19-nginx/
│   ├── installation.md
│   └── troubleshooting.md
│
├── 20-dns/
│   └── troubleshooting.md
│
├── 21-nfs/
│   └── troubleshooting.md
│
├── 22-backup-restore/
│   ├── backup.sh
│   └── restore.sh
│
├── 23-monitoring/
│   └── monitoring.md
│
├── 24-security/
│   └── hardening.md
│
└── 25-production-troubleshooting/
    ├── scenario-01.md
    ├── scenario-02.md
    ├── scenario-03.md
    └── ...
```

---

# 3. Phase-by-Phase Project

We should do this as a **25-phase hands-on project**.

## Phase 1 — Linux Fundamentals

Learn and practice:

```bash
pwd
ls
ls -l
ls -la
cd
cd ..
cd ~
clear
history
whoami
id
hostname
uname
uname -a
date
uptime
which
whereis
man
help
alias
```

### Practical task

Create:

```text
/opt/linux-admin/
├── applications/
├── backups/
├── logs/
├── scripts/
├── configuration/
└── users/
```

Practice:

```bash
mkdir
mkdir -p
touch
cp
mv
rm
rmdir
```

---

# 4. Phase 2 — Files and Directories

Commands:

```bash
find
locate
file
stat
du
df
tree
basename
dirname
```

Example:

```bash
find /var/log -type f
```

Find files larger than 100 MB:

```bash
find / -type f -size +100M 2>/dev/null
```

Find files modified within the last day:

```bash
find /var/log -type f -mtime -1
```

### Troubleshooting exercise

Find why `/opt/application` is consuming excessive disk space.

---

# 5. Phase 3 — Users and Groups

Create:

```text
devops
developers
database
```

Users:

```text
farhan
developer1
developer2
dbadmin
```

Commands:

```bash
useradd
usermod
userdel
passwd
groupadd
groupmod
groupdel
gpasswd
id
groups
who
w
last
lastlog
```

Example:

```bash
sudo groupadd developers
sudo useradd -m developer1
sudo usermod -aG developers developer1
```

Check:

```bash
id developer1
```

---

# 6. Phase 4 — File Permissions

Master:

```bash
chmod
chown
chgrp
umask
```

Understand:

```text
r = 4
w = 2
x = 1
```

Example:

```bash
chmod 755 script.sh
chmod 644 config.txt
chmod 700 private/
```

Ownership:

```bash
chown user file
chown user:group file
chgrp group file
```

### Troubleshooting

Application reports:

```text
Permission denied
```

You must determine:

```text
Who owns the file?
Which group owns it?
What permissions exist?
What is the user's group?
Is SELinux blocking access?
```

---

# 7. Phase 5 — ACL

Learn:

```bash
getfacl
setfacl
```

Example:

```bash
setfacl -m u:developer1:rwx /opt/application
```

Verify:

```bash
getfacl /opt/application
```

This is an important real-world Linux Administrator skill.

---

# 8. Phase 6 — Package Management

Amazon Linux/RHEL:

```bash
dnf
rpm
```

Commands:

```bash
dnf update
dnf install
dnf remove
dnf search
dnf info
dnf list installed
dnf history
rpm -qa
rpm -qi
rpm -ql
rpm -qf
```

Example:

```bash
sudo dnf install nginx -y
```

---

# 9. Phase 7 — Process Management

Learn:

```bash
ps
top
htop
pgrep
pidof
kill
killall
pkill
nice
renice
jobs
bg
fg
```

Examples:

```bash
ps aux
```

```bash
ps -ef
```

Find a process:

```bash
pgrep nginx
```

Terminate:

```bash
kill PID
```

Force:

```bash
kill -9 PID
```

### Troubleshooting scenario

CPU utilization reaches:

```text
95–100%
```

Determine:

1. Which process is consuming CPU?
2. Which user owns it?
3. How long has it been running?
4. Is it expected?
5. Can it safely be stopped?

---

# 10. Phase 8 — Systemd and Services

Master:

```bash
systemctl
systemd
journalctl
```

Examples:

```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
systemctl enable nginx
systemctl disable nginx
```

Check failed services:

```bash
systemctl --failed
```

Logs:

```bash
journalctl -u nginx
```

Recent logs:

```bash
journalctl -u nginx --since "1 hour ago"
```

### Troubleshooting scenario

Nginx won't start.

You must troubleshoot:

```bash
systemctl status nginx
journalctl -xeu nginx
nginx -t
ss -lntp
```

---

# 11. Phase 9 — Linux Networking

This will be one of the most important sections.

Learn:

```bash
ip
ss
ping
traceroute
tracepath
curl
wget
dig
nslookup
host
hostname
hostnamectl
nmcli
```

Examples:

```bash
ip addr
ip route
ip link
```

Ports:

```bash
ss -tulnp
```

Test HTTP:

```bash
curl http://localhost
```

Test DNS:

```bash
dig google.com
```

### Troubleshooting methodology

When a server cannot connect:

```text
1. Check interface
        ↓
2. Check IP address
        ↓
3. Check routing
        ↓
4. Check DNS
        ↓
5. Check firewall
        ↓
6. Check destination port
        ↓
7. Check service
```

---

# 12. Phase 10 — SSH Administration

Learn:

```bash
ssh
scp
sftp
ssh-keygen
ssh-copy-id
```

Example:

```bash
ssh ec2-user@SERVER_IP
```

Copy:

```bash
scp file.txt ec2-user@SERVER_IP:/tmp/
```

Generate key:

```bash
ssh-keygen -t ed25519
```

### Troubleshooting scenarios

Practice:

```text
Permission denied (publickey)
Connection refused
Connection timed out
No route to host
Host key verification failed
```

You will investigate:

```bash
systemctl status sshd
ss -lntp | grep 22
firewall-cmd --list-all
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
```

---

# 13. Phase 11 — Disk Management

Commands:

```bash
lsblk
blkid
df
du
fdisk
parted
mount
umount
```

Example:

```bash
lsblk
```

Check disk usage:

```bash
df -h
```

Directory usage:

```bash
du -sh /var/*
```

---

# 14. Phase 12 — LVM

Build:

```text
Disk
 ↓
Partition
 ↓
Physical Volume
 ↓
Volume Group
 ↓
Logical Volume
 ↓
Filesystem
 ↓
Mount Point
```

Commands:

```bash
pvcreate
pvs
vgcreate
vgs
lvcreate
lvs
lvextend
lvreduce
```

Example:

```bash
pvcreate /dev/xvdf
vgcreate vg_app /dev/xvdf
lvcreate -L 5G -n lv_data vg_app
```

Then create filesystem:

```bash
mkfs.xfs /dev/vg_app/lv_data
```

Mount:

```bash
mkdir /data
mount /dev/vg_app/lv_data /data
```

---

# 15. Phase 13 — `/etc/fstab`

Configure persistent mounting.

Check:

```bash
cat /etc/fstab
```

Test before reboot:

```bash
mount -a
```

### Troubleshooting scenario

Server enters emergency mode after modifying `/etc/fstab`.

Learn how to identify and correct:

```text
wrong UUID
wrong filesystem type
wrong mount point
invalid options
missing disk
```

---

# 16. Phase 14 — Compression and Archives

Commands:

```bash
tar
gzip
gunzip
zip
unzip
bzip2
xz
```

Examples:

```bash
tar -cvf backup.tar /opt/application
```

```bash
tar -czvf backup.tar.gz /opt/application
```

Extract:

```bash
tar -xzvf backup.tar.gz
```

---

# 17. Phase 15 — Text Processing

This is essential for Linux administration.

Master:

```bash
cat
less
more
head
tail
grep
egrep
awk
sed
cut
sort
uniq
wc
tr
xargs
tee
```

Example:

```bash
grep "ERROR" /var/log/messages
```

Follow logs:

```bash
tail -f /var/log/messages
```

Count errors:

```bash
grep "ERROR" application.log | wc -l
```

Extract columns:

```bash
awk '{print $1,$5}' file.txt
```

Replace text:

```bash
sed -i 's/old/new/g' file.txt
```

---

# 18. Phase 16 — Bash Scripting

Build real administration scripts.

### Script 1 — Disk monitoring

```text
disk-monitor.sh
```

Should report:

```text
Filesystem
Size
Used
Available
Usage %
```

And produce:

```text
WARNING: Disk usage > 80%
CRITICAL: Disk usage > 90%
```

### Script 2 — Service monitor

```text
service-monitor.sh nginx
```

Expected:

```text
Nginx is running
```

or:

```text
Nginx is DOWN
Starting nginx...
```

### Script 3 — Backup

```text
backup.sh
```

Backup:

```text
/etc
/var/www
/opt/application
```

---

# 19. Phase 17 — Cron Jobs

Learn:

```bash
crontab
at
```

View:

```bash
crontab -l
```

Edit:

```bash
crontab -e
```

Example:

```cron
0 2 * * * /opt/scripts/backup.sh
```

Meaning:

```text
Every day
at 02:00
run backup
```

---

# 20. Phase 18 — Logs and Log Troubleshooting

Important locations:

```text
/var/log/
```

Commands:

```bash
journalctl
dmesg
tail
grep
less
```

Learn:

```bash
journalctl -b
journalctl -p err
journalctl -p warning
journalctl -u nginx
```

Troubleshooting flow:

```text
Problem
   ↓
Service status
   ↓
Application log
   ↓
System log
   ↓
Kernel log
   ↓
Network
   ↓
Storage
   ↓
Security
```

---

# 21. Phase 19 — Firewall

For RHEL/Amazon Linux environments, practice `firewalld` where available.

Commands:

```bash
firewall-cmd --state
firewall-cmd --list-all
firewall-cmd --list-ports
firewall-cmd --add-port=80/tcp
firewall-cmd --remove-port=80/tcp
firewall-cmd --reload
```

### Troubleshooting

Nginx works:

```bash
curl localhost
```

But another machine cannot access it.

Investigate:

```text
Nginx
↓
Listening port
↓
OS firewall
↓
Cloud Security Group
↓
Network ACL
↓
Routing
```

This is especially useful for AWS interviews.

---

# 22. Phase 20 — SELinux

Learn:

```bash
getenforce
sestatus
setenforce
ls -Z
chcon
restorecon
semanage
ausearch
```

Troubleshoot:

```text
Permission denied
```

even though:

```bash
ls -l
```

shows correct Linux permissions.

Investigate SELinux:

```bash
ausearch -m AVC -ts recent
```

---

# 23. Phase 21 — Nginx Web Server

Install:

```bash
dnf install nginx -y
```

Start:

```bash
systemctl enable --now nginx
```

Check:

```bash
systemctl status nginx
```

Test:

```bash
curl localhost
```

Configure:

```text
/etc/nginx/
├── nginx.conf
└── conf.d/
```

Logs:

```text
/var/log/nginx/
├── access.log
└── error.log
```

---

# 24. Phase 22 — DNS Troubleshooting

Learn:

```bash
dig
nslookup
host
resolvectl
cat /etc/resolv.conf
```

Troubleshooting:

```text
Application cannot connect to database hostname
```

Check:

```bash
cat /etc/resolv.conf
dig database.example.com
ping database.example.com
```

Determine whether it is:

```text
DNS
Network
Firewall
Service
Application
```

---

# 25. Phase 23 — NFS

Build:

```text
NFS Server
     |
     |
     +----> App Server
     |
     +----> Admin Server
```

Learn:

```bash
exportfs
showmount
mount
umount
/etc/exports
```

Troubleshooting:

```text
mount.nfs: Connection timed out
```

Investigate:

```text
NFS service
Network
Firewall
exports
permissions
SELinux
```

---

# 26. Phase 24 — Backup and Restore

Implement:

```text
/etc
/var/www
/opt/application
```

backup structure:

```text
/backups/
├── daily/
├── weekly/
└── monthly/
```

Create:

```bash
backup.sh
restore.sh
```

Example:

```bash
tar -czf /backups/etc-$(date +%F).tar.gz /etc
```

Practice disaster recovery:

> Accidentally delete `/var/www/html`. Restore the website from backup.

---

# 27. Phase 25 — Production Troubleshooting

This is the **most important phase for interviews**.

We intentionally break the server.

---

## Scenario 1 — Nginx Down

Problem:

```text
Website is unavailable.
```

Check:

```bash
systemctl status nginx
journalctl -u nginx
nginx -t
```

---

## Scenario 2 — Port 80 Not Accessible

Check:

```bash
ss -lntp
firewall-cmd --list-all
curl localhost
```

Then investigate cloud firewall/security group if using AWS.

---

## Scenario 3 — Disk 100% Full

Check:

```bash
df -h
du -sh /*
```

Find large files:

```bash
find / -type f -size +500M 2>/dev/null
```

Check deleted-but-open files:

```bash
lsof +L1
```

---

## Scenario 4 — High CPU

```bash
top
ps aux --sort=-%cpu | head
```

Find process:

```bash
ps -fp PID
```

---

## Scenario 5 — High Memory

```bash
free -h
top
ps aux --sort=-%mem | head
```

---

## Scenario 6 — SSH Not Working

Investigate:

```bash
systemctl status sshd
ss -lntp | grep 22
```

Then:

```bash
firewall-cmd --list-all
```

Check:

```bash
~/.ssh/
~/.ssh/authorized_keys
```

---

## Scenario 7 — Permission Denied

Check:

```bash
ls -l
id username
getfacl filename
ls -Z
```

Investigate:

```text
Owner
Group
Permissions
ACL
SELinux
```

---

## Scenario 8 — Service Starts but Immediately Stops

Check:

```bash
systemctl status service-name
journalctl -u service-name
```

Check configuration:

```bash
service-command --test
```

---

## Scenario 9 — DNS Failure

Check:

```bash
cat /etc/resolv.conf
dig google.com
nslookup google.com
```

---

## Scenario 10 — Application Cannot Connect to Database

Check in this order:

```text
DNS
 ↓
IP connectivity
 ↓
Port
 ↓
Firewall
 ↓
Database service
 ↓
Credentials
 ↓
Application configuration
```

Commands:

```bash
ping DB_IP
nc -zv DB_IP 3306
ss -lntp
```

---

# 28. Linux Administrator Troubleshooting Framework

You should memorize this:

```text
                INCIDENT
                   |
                   v
             What changed?
                   |
                   v
            Check the status
                   |
                   v
             Check the logs
                   |
                   v
          Check CPU / Memory
                   |
                   v
             Check Disk
                   |
                   v
            Check Network
                   |
                   v
            Check Security
                   |
                   v
          Check Configuration
                   |
                   v
             Fix the issue
                   |
                   v
              Test again
                   |
                   v
             Document fix
```

---

# 29. Important Linux Commands Cheat Sheet

By the end of this project, you should be comfortable with:

### System

```bash
uname
hostname
hostnamectl
uptime
date
timedatectl
whoami
id
who
w
last
history
```

### Files

```bash
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
du
df
```

### Permissions

```bash
chmod
chown
chgrp
umask
getfacl
setfacl
```

### Users

```bash
useradd
usermod
userdel
passwd
groupadd
groupdel
groups
id
```

### Processes

```bash
ps
top
htop
pgrep
pidof
kill
pkill
killall
nice
renice
```

### Services

```bash
systemctl
journalctl
```

### Networking

```bash
ip
ss
ping
curl
wget
traceroute
tracepath
dig
nslookup
host
nmcli
```

### Storage

```bash
lsblk
blkid
fdisk
parted
mount
umount
df
du
```

### LVM

```bash
pvcreate
pvs
vgcreate
vgs
lvcreate
lvs
lvextend
lvreduce
```

### Text

```bash
cat
less
head
tail
grep
awk
sed
cut
sort
uniq
wc
tr
xargs
tee
```

### Archives

```bash
tar
gzip
gunzip
zip
unzip
```

### Security

```bash
sudo
firewall-cmd
getenforce
sestatus
setenforce
semanage
ausearch
```

### Scheduling

```bash
crontab
at
```

### Remote Administration

```bash
ssh
scp
sftp
ssh-keygen
```

---

# 30. Final Capstone

At the end, we will combine everything into one **production-like Linux environment**:

```text
                    Internet
                       |
                       |
                 AWS Security Group
                       |
                       v
                +-------------+
                |   admin01   |
                | Linux Admin |
                +-------------+
                  /    |    \
                 /     |     \
                v      v      v
           SSH Admin  Backup  Monitoring
                |
                v
          +-------------+
          |    app01    |
          | Amazon Linux|
          +-------------+
             |       |
             v       v
          Nginx     NFS
             |
             v
        Web Application
             |
          /var/log
             |
             v
        Log Analysis
```

You will perform a complete administrator workflow:

```text
Provision server
      ↓
Configure hostname
      ↓
Configure users/groups
      ↓
Configure SSH
      ↓
Configure sudo
      ↓
Configure storage
      ↓
Configure LVM
      ↓
Configure filesystem
      ↓
Configure Nginx
      ↓
Configure firewall
      ↓
Configure SELinux
      ↓
Configure logging
      ↓
Configure backup
      ↓
Configure cron
      ↓
Monitor server
      ↓
Break server intentionally
      ↓
Troubleshoot
      ↓
Recover
      ↓
Document
```

## How I recommend we build it

Since you prefer **hands-on, phased projects**, we should not dump 25 phases at once and leave you to figure them out.

We'll build it sequentially:

**Phase 1 → Linux Server Setup**
**Phase 2 → Linux Commands & Filesystem**
**Phase 3 → Users & Groups**
**Phase 4 → Permissions & ACL**
**Phase 5 → Packages**
**Phase 6 → Processes**
**Phase 7 → Systemd**
**Phase 8 → Networking**
**Phase 9 → SSH**
**Phase 10 → Disk/LVM**
**Phase 11 → Filesystem/fstab**
**Phase 12 → Bash scripting**
**Phase 13 → Cron**
**Phase 14 → Logs**
**Phase 15 → Firewall**
**Phase 16 → SELinux**
**Phase 17 → Nginx**
**Phase 18 → DNS**
**Phase 19 → NFS**
**Phase 20 → Backup/Restore**
**Phase 21 → Monitoring**
**Phase 22 → Security hardening**
**Phase 23 → Troubleshooting labs**
**Phase 24 → Production incidents**
**Phase 25 → Final Linux Administrator Capstone**

**For every phase**, I'll provide:

1. Architecture
2. Objective
3. Prerequisites
4. Manual commands
5. What each command does
6. Expected output
7. Hands-on task
8. Intentional failure/breaking exercise
9. Troubleshooting methodology
10. Interview questions
11. Real-world scenario
12. GitHub documentation

We can start with **Phase 1: Create the Linux Administrator Lab on AWS using Amazon Linux 2023**, including the EC2 setup, SSH connection, hostname, admin user, sudo configuration, and the initial Linux command practice.
