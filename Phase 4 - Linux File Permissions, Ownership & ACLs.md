Absolutely. Let’s continue from **Phase 3** and build Phase 4 directly on the existing `/company` environment.

# Phase 4 — Linux File Permissions, Ownership & ACLs

## 🎯 Phase 4 Objectives

By the end of this phase, you will be able to:

* Understand Linux `rwx` permissions
* Use `chmod`
* Use `chown`
* Use `chgrp`
* Understand numeric and symbolic permissions
* Understand permissions on **files vs directories**
* Configure department-level access
* Use `umask`
* Understand **SUID, SGID and Sticky Bit**
* Configure and troubleshoot **ACLs**
* Use `getfacl` and `setfacl`
* Use `namei -l` for permission troubleshooting
* Diagnose `Permission denied`
* Understand the Linux permission decision process
* Answer common Linux Administrator interview questions

---

# 4.1 — Connect to the Server

From your Windows PowerShell:

```powershell
ssh -i .\linux-admin.pem adminuser@YOUR_PUBLIC_IP
```

Then:

```bash
hostname
whoami
pwd
```

Expected:

```text
admin01
adminuser
/home/adminuser
```

Confirm sudo:

```bash
sudo -v
```

---

# 4.2 — Verify Our Phase 3 Environment

Check the users:

```bash
getent passwd developer1
getent passwd developer2
getent passwd operator1
getent passwd dbadmin
getent passwd securityadmin
getent passwd appsvc
```

Check groups:

```bash
getent group developers
getent group operations
getent group database
getent group security
```

Check company directories:

```bash
sudo ls -ld /company/*
```

You should have:

```text
/company/development
/company/operations
/company/database
/company/security
/company/application
```

---

# 4.3 — Linux Permission Model

Run:

```bash
ls -ld /company/development
```

Example:

```text
drwxr-x---  root developers  /company/development
```

Break this down:

```text
d rwx r-x ---
│ │   │   │
│ │   │   └── others
│ │   └────── group
│ └────────── owner
└──────────── directory
```

For a file:

```text
-rwxr-xr--
```

The first character represents the file type.

| Symbol | Meaning          |
| ------ | ---------------- |
| `-`    | Regular file     |
| `d`    | Directory        |
| `l`    | Symbolic link    |
| `c`    | Character device |
| `b`    | Block device     |

---

# 4.4 — Understanding `rwx`

For a **file**:

| Permission | Meaning      |
| ---------- | ------------ |
| `r`        | Read file    |
| `w`        | Modify file  |
| `x`        | Execute file |

For a **directory**, the meaning is different:

| Permission | Directory meaning            |
| ---------- | ---------------------------- |
| `r`        | List directory contents      |
| `w`        | Create/delete/rename entries |
| `x`        | Enter/traverse directory     |

This is extremely important for Linux administration.

For example:

```text
directory = r--
```

You may be able to see names, but you generally cannot access the files inside because you don't have directory `x`.

---

# 4.5 — Numeric Permissions

Linux converts permissions into numbers:

```text
r = 4
w = 2
x = 1
```

Therefore:

```text
rwx = 4 + 2 + 1 = 7
rw- = 4 + 2     = 6
r-x = 4 +     1 = 5
r-- = 4         = 4
-wx =     2 + 1 = 3
-w- =     2     = 2
--x =         1 = 1
--- = 0
```

So:

```text
755
```

means:

```text
Owner  = rwx = 7
Group  = r-x = 5
Other  = r-x = 5
```

And:

```text
640
```

means:

```text
Owner  = rw- = 6
Group  = r-- = 4
Other  = --- = 0
```

---

# 4.6 — `chmod`

`chmod` changes permissions.

Create a test directory:

```bash
mkdir -p /opt/linux-admin/phase4/permissions
cd /opt/linux-admin/phase4/permissions
```

Create a file:

```bash
echo "Linux permissions lab" > test.txt
```

Check:

```bash
ls -l test.txt
```

---

## Symbolic Mode

Remove write permission from group:

```bash
chmod g-w test.txt
```

Remove read permission from others:

```bash
chmod o-r test.txt
```

Add execute permission to owner:

```bash
chmod u+x test.txt
```

You can also combine:

```bash
chmod u+rwx,g+rx,o-rwx test.txt
```

---

## Numeric Mode

Set:

```bash
chmod 755 test.txt
```

Check:

```bash
ls -l test.txt
```

Then:

```bash
chmod 640 test.txt
```

Check again:

```bash
ls -l test.txt
```

---

# 4.7 — `chown`

`chown` changes ownership.

Check:

```bash
ls -l test.txt
```

Change owner:

```bash
sudo chown developer1 test.txt
```

Check:

```bash
ls -l test.txt
```

Change owner and group:

```bash
sudo chown developer1:developers test.txt
```

Verify:

```bash
ls -l test.txt
```

---

# 4.8 — `chgrp`

`chgrp` changes only the group.

```bash
sudo chgrp operations test.txt
```

Verify:

```bash
ls -l test.txt
```

Restore:

```bash
sudo chgrp developers test.txt
```

---

# 4.9 — Build Our Company Permission Model

Now we'll implement a realistic organization.

Our requirement:

| User          | Directory   | Access     |
| ------------- | ----------- | ---------- |
| developer1    | development | Read/Write |
| developer1    | database    | No access  |
| dbadmin       | database    | Read/Write |
| dbadmin       | development | Read Only  |
| operator1     | application | Read/Write |
| developer1    | application | Read Only  |
| securityadmin | security    | Read/Write |

We will use **groups for normal department access** and **ACLs for exceptions**.

---

# 4.10 — Set Base Ownership

First:

```bash
sudo chown root:developers /company/development
sudo chown root:operations /company/operations
sudo chown root:database /company/database
sudo chown root:security /company/security
sudo chown root:operations /company/application
```

Now:

```bash
sudo ls -ld /company/*
```

---

# 4.11 — Configure Development Directory

Requirement:

```text
developers → read/write
others → no access
```

Set:

```bash
sudo chmod 770 /company/development
```

Verify:

```bash
sudo ls -ld /company/development
```

Expected pattern:

```text
drwxrwx---
```

Test as developer:

```bash
sudo -u developer1 bash
```

Then:

```bash
cd /company/development
echo "Developer test" > developer1.txt
ls -l
exit
```

---

# 4.12 — Configure Database Directory

Database administrators need full access.

```bash
sudo chmod 770 /company/database
```

Test:

```bash
sudo -u dbadmin bash
```

Then:

```bash
cd /company/database
echo "Database configuration" > db.conf
ls -l
exit
```

---

# 4.13 — Give `dbadmin` Read-Only Access to Development

This is an **exception**.

The directory belongs to:

```text
root:developers
```

We don't want to put `dbadmin` into the developers group.

Instead, use an ACL.

First install ACL tools if necessary:

```bash
sudo dnf install -y acl
```

Verify:

```bash
getfacl --version
```

---

# 4.14 — Understanding ACL

ACL = **Access Control List**

Traditional Linux permissions provide:

```text
owner
group
others
```

ACL lets us add specific users/groups.

For example:

```text
root       → full
developers → full
dbadmin    → read/execute
others     → none
```

---

# 4.15 — View ACL

Run:

```bash
sudo getfacl /company/development
```

You'll see something similar to:

```text
# file: company/development
# owner: root
# group: developers
user::rwx
group::rwx
other::---
```

---

# 4.16 — Add Read-Only ACL for `dbadmin`

Run:

```bash
sudo setfacl -m u:dbadmin:rx /company/development
```

Check:

```bash
sudo getfacl /company/development
```

You should see:

```text
user:dbadmin:r-x
```

Now test:

```bash
sudo -u dbadmin bash
```

Run:

```bash
cd /company/development
ls
```

If a developer-created file exists:

```bash
cat /company/development/developer1.txt
```

Try writing:

```bash
echo "DB admin modification" >> /company/development/developer1.txt
```

Expected:

```text
Permission denied
```

That's intentional.

Exit:

```bash
exit
```

---

# 4.17 — Give Developer1 Read-Only Application Access

Application directory:

```bash
sudo ls -ld /company/application
```

Base owner/group:

```text
root:operations
```

Set:

```bash
sudo chmod 770 /company/application
```

`operator1` belongs to operations, so they have full access.

Now add developer1 read-only:

```bash
sudo setfacl -m u:developer1:rx /company/application
```

Check:

```bash
sudo getfacl /company/application
```

---

# 4.18 — Test Operator Access

```bash
sudo -u operator1 bash
```

Then:

```bash
cd /company/application
echo "Application deployment" > deployment.txt
cat deployment.txt
exit
```

---

# 4.19 — Test Developer Read-Only Access

```bash
sudo -u developer1 bash
```

Run:

```bash
cd /company/application
ls
```

Read:

```bash
cat deployment.txt
```

Try writing:

```bash
echo "Developer change" >> deployment.txt
```

Expected:

```text
Permission denied
```

Exit:

```bash
exit
```

---

# 4.20 — Security Directory

Security team gets full access:

```bash
sudo chmod 770 /company/security
```

Test:

```bash
sudo -u securityadmin bash
```

```bash
cd /company/security
echo "Security audit" > audit.txt
cat audit.txt
exit
```

---

# 4.21 — Important ACL Concept: Mask

Run:

```bash
sudo getfacl /company/development
```

You may see:

```text
user::rwx
user:dbadmin:r-x
group::rwx
mask::rwx
other::---
```

The **ACL mask** limits the effective permissions of:

* named users
* named groups
* owning group

For example:

```bash
sudo setfacl -m m:r-x /company/development
```

Then:

```bash
sudo getfacl /company/development
```

You may see:

```text
user:dbadmin:r-x
#effective:r-x
```

Restore:

```bash
sudo setfacl -m m:rwx /company/development
```

---

# 4.22 — Default ACL

Default ACLs are extremely useful for shared application directories.

Suppose developers create files inside:

```text
/company/development
```

We want new files/directories to inherit appropriate permissions.

First inspect:

```bash
sudo getfacl /company/development
```

Set a default ACL:

```bash
sudo setfacl -d -m g:developers:rwx /company/development
```

Check:

```bash
sudo getfacl /company/development
```

You should see:

```text
default:group:developers:rwx
```

Create a new directory:

```bash
sudo -u developer1 mkdir /company/development/project1
```

Then:

```bash
sudo getfacl /company/development/project1
```

The default ACL inheritance should be visible.

---

# 4.23 — Remove ACL

To remove `dbadmin` ACL:

```bash
sudo setfacl -x u:dbadmin /company/development
```

Verify:

```bash
sudo getfacl /company/development
```

Remove all extended ACL entries:

```bash
sudo setfacl -b /company/development
```

**Be careful:** `-b` removes extended ACL entries.

---

# 4.24 — `umask`

`umask` controls default permissions for newly created files/directories.

Check yours:

```bash
umask
```

Typical result:

```text
0022
```

For a new file, Linux normally starts with:

```text
666
```

Then applies the umask:

```text
666
-022
----
644
```

So:

```text
new file → 644
```

For directories:

```text
777
-022
----
755
```

So:

```text
new directory → 755
```

---

## Test umask

Create:

```bash
mkdir -p /opt/linux-admin/phase4/umask
cd /opt/linux-admin/phase4/umask
```

Check:

```bash
umask
```

Create:

```bash
touch file1
mkdir dir1
```

Check:

```bash
ls -ld file1 dir1
```

---

## Temporary umask

Try:

```bash
umask 027
```

Then:

```bash
touch file2
mkdir dir2
```

Check:

```bash
ls -ld file2 dir2
```

Restore:

```bash
umask 022
```

---

# 4.25 — Special Permissions

Linux has three important special permission bits:

```text
SUID
SGID
Sticky Bit
```

---

## SUID

SUID = Set User ID.

Numeric value:

```text
4000
```

Example:

```bash
ls -l /usr/bin/passwd
```

You may see:

```text
-rwsr-xr-x
```

The `s` indicates SUID.

A program with SUID can execute with the privileges of its file owner.

**Security warning:** Don't randomly apply SUID to scripts or binaries.

---

# 4.26 — SGID

SGID numeric value:

```text
2000
```

For directories, SGID causes newly created files/directories to inherit the directory's group.

Excellent for shared team directories.

Set SGID on development:

```bash
sudo chmod 2770 /company/development
```

Check:

```bash
sudo ls -ld /company/development
```

Expected:

```text
drwxrws---
```

Notice:

```text
s
```

in the group permission position.

---

# 4.27 — Sticky Bit

Sticky bit:

```text
1000
```

Classic example:

```bash
ls -ld /tmp
```

Typically:

```text
drwxrwxrwt
```

The `t` indicates sticky bit.

It means users cannot normally delete another user's files from the directory, even though the directory is writable by everyone.

Test in a controlled directory:

```bash
sudo mkdir -p /opt/linux-admin/phase4/shared
sudo chmod 1777 /opt/linux-admin/phase4/shared
```

Check:

```bash
ls -ld /opt/linux-admin/phase4/shared
```

Expected:

```text
drwxrwxrwt
```

---

# 4.28 — Permission Troubleshooting Lab

Now we'll intentionally create a problem.

Create:

```bash
sudo mkdir -p /opt/linux-admin/phase4/troubleshooting
sudo touch /opt/linux-admin/phase4/troubleshooting/app.log
```

Set:

```bash
sudo chown root:root /opt/linux-admin/phase4/troubleshooting/app.log
sudo chmod 600 /opt/linux-admin/phase4/troubleshooting/app.log
```

Test:

```bash
sudo -u developer1 cat /opt/linux-admin/phase4/troubleshooting/app.log
```

Expected:

```text
Permission denied
```

---

# 4.29 — Troubleshooting Method

Don't immediately use:

```bash
chmod 777
```

Instead investigate.

### Step 1 — Check user

```bash
id developer1
```

### Step 2 — Check file

```bash
ls -l /opt/linux-admin/phase4/troubleshooting/app.log
```

### Step 3 — Check parent directory

```bash
ls -ld /opt/linux-admin/phase4/troubleshooting
```

### Step 4 — Check every path component

Use:

```bash
namei -l /opt/linux-admin/phase4/troubleshooting/app.log
```

This is an extremely useful Linux administrator command.

It shows permissions for each component:

```text
/
opt
linux-admin
phase4
troubleshooting
app.log
```

---

# 4.30 — Fix the Problem Correctly

Suppose the application team needs read access.

Instead of:

```bash
chmod 777 app.log
```

Use ACL:

```bash
sudo setfacl -m u:developer1:r /opt/linux-admin/phase4/troubleshooting/app.log
```

Verify:

```bash
sudo getfacl /opt/linux-admin/phase4/troubleshooting/app.log
```

Test:

```bash
sudo -u developer1 cat /opt/linux-admin/phase4/troubleshooting/app.log
```

Now it should work.

---

# 4.31 — Permission Troubleshooting Flow

Memorize this:

```text
Permission denied
       |
       v
Who is the user?
       |
       v
id username
       |
       v
What are the file permissions?
       |
       v
ls -l file
       |
       v
Who owns the file?
       |
       v
What group owns it?
       |
       v
Is the user in that group?
       |
       v
id username
       |
       v
Check parent directory permissions
       |
       v
namei -l /full/path
       |
       v
Check ACL
       |
       v
getfacl file
       |
       v
Check SELinux
       |
       v
getenforce
```

This troubleshooting approach is much better than blindly changing permissions.

---

# 4.32 — Final Access Matrix

Let's verify the intended design:

| User            | Development | Database | Application | Security |
| --------------- | ----------- | -------- | ----------- | -------- |
| `developer1`    | RW          | None     | R           | None     |
| `developer2`    | RW          | None     | None        | None     |
| `dbadmin`       | R           | RW       | None        | None     |
| `operator1`     | None        | None     | RW          | None     |
| `securityadmin` | None        | None     | None        | RW       |

This is a realistic example of **least privilege**.

---

# 4.33 — Commands You Must Know

By the end of Phase 4, you should be comfortable with:

```bash
ls -l
ls -ld
chmod
chown
chgrp
stat
id
groups
umask
getfacl
setfacl
namei
find
```

And special permissions:

```bash
chmod 4000
chmod 2000
chmod 1000
```

Common combinations:

```bash
chmod 755 file
chmod 644 file
chmod 700 directory
chmod 770 directory
chmod 2770 directory
chmod 1777 directory
```

---

# 4.34 — Hands-On Challenge

Don't look at the solution first.

### Task 1

Create:

```text
/company/projects
```

Owner:

```text
root
```

Group:

```text
developers
```

Permissions:

```text
770
```

---

### Task 2

Give `dbadmin` read-only access:

```text
/company/projects
```

Use ACL.

---

### Task 3

Give `securityadmin` read-only access to:

```text
/company/projects
```

Use ACL.

---

### Task 4

Configure `/company/projects` with SGID so newly created content inherits the `developers` group.

---

### Task 5

Verify everything with:

```bash
ls -ld /company/projects
getfacl /company/projects
namei -l /company/projects
```

---

# 4.35 — Phase 4 Interview Questions

### Q1. What is the difference between `chmod 755` and `chmod 777`?

`755`:

```text
owner  → rwx
group  → r-x
others → r-x
```

`777` gives everyone full read/write/execute permissions and is generally inappropriate for production.

---

### Q2. What does `x` mean on a directory?

It means the ability to **traverse/access** the directory.

---

### Q3. What is ACL?

ACL provides more granular permissions than the traditional owner/group/other model.

Example:

```bash
setfacl -m u:dbadmin:rx directory
```

---

### Q4. What is the difference between `chown` and `chmod`?

```text
chown → changes ownership
chmod → changes permissions
```

---

### Q5. What is SGID on a directory?

It causes newly created files/directories to inherit the directory's group.

Useful for shared team directories.

---

### Q6. What is sticky bit?

It prevents users from deleting files owned by other users in a shared writable directory.

Classic example:

```text
/tmp
```

---

### Q7. What does `umask 022` do?

It generally results in:

```text
Files       → 644
Directories → 755
```

for normal creation defaults.

---

### Q8. How would you troubleshoot `Permission denied`?

A strong answer:

> I first identify the user, then check file ownership and permissions with `ls -l`, verify group membership using `id`, check parent-directory permissions with `namei -l`, inspect ACLs using `getfacl`, and finally check SELinux if necessary. I don't immediately use `chmod 777`.

---

# ✅ Phase 4 Completion Checklist

Before moving to Phase 5, make sure you can perform these without assistance:

* [ ] Explain `rwx`
* [ ] Explain file vs directory permissions
* [ ] Use `chmod`
* [ ] Use numeric permissions
* [ ] Use symbolic permissions
* [ ] Use `chown`
* [ ] Use `chgrp`
* [ ] Explain `umask`
* [ ] Configure SGID
* [ ] Explain sticky bit
* [ ] Explain SUID
* [ ] Install/use ACL tools
* [ ] Use `getfacl`
* [ ] Use `setfacl`
* [ ] Configure default ACL
* [ ] Remove ACL
* [ ] Use `namei -l`
* [ ] Troubleshoot `Permission denied`
* [ ] Implement least-privilege access

### Next phase

**Phase 5 — Linux Processes, Jobs & System Monitoring**

We'll move into:

```text
ps
top
htop
pgrep
pkill
kill
killall
nice
renice
jobs
bg
fg
nohup
systemd
systemctl
journalctl
uptime
free
vmstat
iostat
sar
lsof
ss
```

and build **real production-style process/service troubleshooting scenarios** such as CPU 100%, memory exhaustion, zombie processes, crashed services, and port conflicts.
