# 🐧 Akshat's Cybersecurity Notes — Day 3
## Linux Fundamentals — The Language of Security

> **Simple language. Real examples. Everything you need.**
> Based on: TryHackMe Linux Fundamentals + Professor Messer + OverTheWire Bandit
> Day 3 of your 60-Day Cybersecurity Roadmap 🔥

---

## 🎯 Why Linux for Cybersecurity?

Before we start — let me tell you WHY you're learning this.

**Every major security tool runs on Linux:**
- Kali Linux — the hacking OS
- Security Onion — the SIEM you'll build
- Wazuh — the threat detection system
- Metasploit, Nmap, Wireshark, Burp Suite — all run best on Linux

**Every server you'll investigate is Linux:**
- 96% of the world's top 1 million web servers run Linux
- All major cloud providers (AWS, Google, Azure) default to Linux
- When you investigate a breach, the victim server is almost certainly Linux

**Every SOC job requires Linux:**
- Reading logs → Linux
- Running scripts → Linux (Bash)
- Investigating files → Linux
- Connecting to remote servers → Linux (SSH)

**Real talk:** If you don't know Linux, you cannot work in cybersecurity. It's that simple.

---

## 🗺️ What You'll Learn Today

| Section | Topics |
|---------|--------|
| **1. Linux Basics** | What Linux is, distributions, terminal vs GUI |
| **2. The File System** | Directory structure, everything is a file |
| **3. Navigation** | Moving around, listing files, finding things |
| **4. Files & Directories** | Creating, copying, moving, deleting |
| **5. File Content** | Reading files, searching inside them |
| **6. Permissions** | Who can read/write/execute — critical for security |
| **7. Users & Groups** | Managing accounts, sudo, privilege escalation |
| **8. Processes** | Running programs, killing them, background jobs |
| **9. Networking Commands** | ifconfig, netstat, ping, curl — essential for SOC |
| **10. Text Editors** | nano and vim — editing files in terminal |
| **11. Bash Scripting** | Writing your first security scripts |
| **12. Package Management** | Installing tools |
| **13. Logs** | Where logs live — your bread and butter as SOC analyst |
| **14. Useful Tricks** | Pipes, redirection, grep — power user skills |

---

# 🏗️ SECTION 1 — What Is Linux?

## The Simple Explanation

Linux is an **operating system** — just like Windows or MacOS. But it's:
- **Free and open source** — anyone can see the code, modify it, use it
- **Extremely stable** — servers run for years without restarting
- **Highly customisable** — you can make it look and work exactly how you want
- **Command-line first** — most power comes from typing commands, not clicking

**Real Life Example 🌍**
> Think of Windows as a ready-made furnished apartment — everything is set up for you, looks nice, but you can't change the walls. Linux is like an empty flat with blueprints — you build it exactly how you want, put walls where YOU need them, and it's perfectly suited to your purpose.

## Linux Distributions (Distros)

Linux comes in many "flavours" called distributions. All use the same core (Linux kernel) but have different tools, looks, and purposes.

| Distro | Who Uses It | Why |
|--------|------------|-----|
| **Kali Linux** | Hackers & pentesters | Comes with 600+ security tools pre-installed |
| **Ubuntu** | Beginners, servers | Easy to use, huge community |
| **Debian** | Servers, stability-focused | Very stable, used in production |
| **CentOS/RHEL** | Enterprise servers | Preferred in corporate environments |
| **Parrot OS** | Security professionals | Lighter than Kali, privacy-focused |
| **Arch Linux** | Experts | Build everything yourself — maximum control |

**For your cybersecurity journey:** Use **Kali Linux** (already set up from your roadmap!)

## Terminal vs GUI

**GUI (Graphical User Interface):** Click around with mouse. Like Windows Desktop.

**Terminal (Command Line Interface):** Type commands. This is what you'll use 90% of the time in security work.

**Why terminal over GUI?**
```
With GUI: Click menu → Click folder → Click file → Right click → Click open
          (5 actions, 5 seconds)

With terminal: cat /var/log/auth.log
               (1 command, 0.5 seconds)

When investigating a breach with 50 log files to check:
GUI:      50 × 5 actions = 250 clicks
Terminal: grep "Failed password" /var/log/auth.log | head -100
          (1 command, instant)
```

Also — most servers have NO graphical interface. When you SSH into a remote server, you ONLY have the terminal.

---

# 📁 SECTION 2 — The Linux File System

## The Most Important Concept: Everything Is a File

In Linux, **everything is a file**. Not just documents and images — but also:
- Your hard drive → a file (`/dev/sda`)
- Your keyboard → a file (`/dev/input/keyboard`)
- Your network card → a file
- Running processes → files in `/proc/`
- System configuration → files in `/etc/`

This is powerful because the same tools for reading files work for reading devices, processes, network data, etc.

## The Directory Tree

Linux uses one single tree structure starting from `/` (called "root"). Everything hangs off this single point — no `C:\` or `D:\` like Windows.

```
/ (root — the very top)
├── bin/        Essential command binaries (ls, cp, mv, cat)
├── boot/       Bootloader files, Linux kernel
├── dev/        Device files (hard drives, USB, terminals)
├── etc/        System configuration files ← GOLD MINE for security
├── home/       User home directories
│   ├── akshat/ ← YOUR home directory
│   └── john/
├── lib/        Shared libraries (like .dll files in Windows)
├── media/      Mounted removable drives (USB sticks)
├── mnt/        Temporary mount point for filesystems
├── opt/        Optional/third-party software
├── proc/       Virtual filesystem — info about running processes
├── root/       Home directory of the ROOT (admin) user
├── run/        Runtime data (PIDs, sockets)
├── sbin/       System binaries (for admin use: reboot, fdisk)
├── srv/        Data for services (web server files)
├── sys/        Virtual filesystem — hardware/kernel info
├── tmp/        Temporary files (cleared on reboot)
├── usr/        User programs and data
│   ├── bin/    Most user commands (python, vim, git)
│   ├── lib/    Libraries for usr/bin programs
│   └── share/  Documentation, shared data
└── var/        Variable data — changes constantly
    ├── log/    ← LOG FILES — most important for SOC analysts!
    ├── www/    Web server files
    └── mail/   Email storage
```

## The Most Important Directories for Security Work

### `/etc/` — Where All Config Files Live

**Real Life Example 🌍**
> `/etc/` is like the settings/preferences drawer in your office. Everything about how the system is configured lives here — who can log in, how the network works, what services run.

```
/etc/passwd         — List of ALL user accounts on the system
/etc/shadow         — Hashed passwords (only root can read!)
/etc/group          — User groups
/etc/hosts          — Local DNS — maps hostnames to IPs
/etc/resolv.conf    — Which DNS server to use
/etc/network/       — Network interface configuration
/etc/crontab        — Scheduled tasks (malware loves hiding here!)
/etc/sudoers        — Who can run commands as root
/etc/ssh/           — SSH server configuration
/etc/apache2/       — Apache web server config
/etc/nginx/         — Nginx web server config
```

> 🔴 **Security note:** During an investigation, `/etc/passwd`, `/etc/shadow`, `/etc/crontab`, and `/etc/sudoers` are the FIRST places you check for signs of compromise. Attackers add fake users to `/etc/passwd` and backdoors to `/etc/crontab`.

### `/var/log/` — The SOC Analyst's Gold Mine

This is where **all log files** live. As a SOC analyst, you'll spend a LOT of time here.

```
/var/log/auth.log       — All login attempts (SSH, sudo, su) ← Most important!
/var/log/syslog         — General system messages
/var/log/kern.log       — Kernel messages
/var/log/apache2/       — Web server access and error logs
/var/log/nginx/         — Nginx web server logs
/var/log/ufw.log        — Firewall logs (UFW = Ubuntu firewall)
/var/log/dpkg.log       — Package installation history
/var/log/cron.log       — Scheduled task execution
/var/log/mysql/         — Database logs
/var/log/mail.log       — Email server logs
/var/log/faillog        — Failed login attempts
/var/log/wtmp           — Login/logout history (read with: last)
/var/log/btmp           — Failed login history (read with: lastb)
```

### `/proc/` — The Window Into Running System

`/proc/` is a virtual filesystem — the files don't actually exist on disk. They're created in real-time by the kernel and show you what's happening RIGHT NOW.

```
/proc/cpuinfo        — CPU information
/proc/meminfo        — RAM usage
/proc/net/tcp        — All TCP connections (raw format)
/proc/net/udp        — All UDP connections
/proc/1/             — Information about process with PID 1
/proc/1/cmdline      — The command that started process 1
/proc/1/maps         — Memory map of process 1
/proc/1/environ      — Environment variables of process 1
```

> 💡 **Security use:** Malware analysis! If you see a suspicious process, you can read `/proc/[PID]/cmdline` to see exactly what command started it, `/proc/[PID]/exe` to see what executable it is, even if the original file was deleted!

### `/home/` and `/root/` — User Directories

```
/home/akshat/           — YOUR home directory (like My Documents)
/home/akshat/.bashrc    — Your terminal configuration
/home/akshat/.ssh/      — Your SSH keys
/home/akshat/.bash_history — Every command you've typed! ← Security goldmine
/root/                  — Root user's home (only accessible by root)
```

> 🔴 **Security note:** `.bash_history` is HUGE for investigations. When you compromise a system in a pentest, or when investigating a breach, read the attacker's `.bash_history` — it tells you every command they ran!

---

# 🧭 SECTION 3 — Navigation (Moving Around)

## Your Starting Point — Where Am I?

```bash
pwd
```
**pwd = Print Working Directory.** Shows your current location.

```
$ pwd
/home/akshat
```

Think of it like asking "what street am I on?" in an unfamiliar city.

---

## Listing Files — `ls`

```bash
ls              # List files in current directory
ls -l           # Long format (permissions, size, date)
ls -a           # Show ALL files including hidden ones (start with .)
ls -la          # Long format + hidden files (most common!)
ls -lh          # Long format with human-readable file sizes (KB, MB)
ls -lt          # Sort by modification time (newest first)
ls -ltr         # Sort by time, REVERSED (oldest first — good for logs)
ls /etc/        # List files in /etc/ without going there
```

**Example output of `ls -la`:**
```
$ ls -la /home/akshat/
total 56
drwxr-xr-x  8 akshat akshat 4096 Jan 15 09:23 .
drwxr-xr-x  4 root   root   4096 Jan 10 18:45 ..
-rw-------  1 akshat akshat  890 Jan 15 09:20 .bash_history
-rw-r--r--  1 akshat akshat  220 Jan 10 18:45 .bash_logout
-rw-r--r--  1 akshat akshat 3771 Jan 10 18:45 .bashrc
drwx------  2 akshat akshat 4096 Jan 10 18:45 .ssh
-rw-r--r--  1 akshat akshat  807 Jan 10 18:45 .profile
drwxr-xr-x  3 akshat akshat 4096 Jan 14 16:30 Documents
drwxr-xr-x  2 akshat akshat 4096 Jan 12 11:15 Downloads
-rw-r--r--  1 akshat akshat 1024 Jan 15 09:00 notes.txt
```

Reading this output (we'll cover permissions fully in Section 6):
```
drwxr-xr-x  8 akshat akshat 4096 Jan 15 09:23 Documents
│           │ │      │      │    │              └── File/folder name
│           │ │      │      │    └─────────────── Last modified date/time
│           │ │      │      └──────────────────── File size in bytes
│           │ │      └─────────────────────────── Group owner
│           │ └────────────────────────────────── Owner (user)
│           └──────────────────────────────────── Number of hard links
└──────────────────────────────────────────────── Permissions
```

**Hidden files** start with a dot (`.`). The `-a` flag shows them.
- `.bashrc` = terminal config
- `.ssh/` = SSH keys
- `.bash_history` = command history

---

## Moving Around — `cd`

```bash
cd /etc/            # Go to /etc/ (absolute path — starts from root /)
cd Documents/       # Go into Documents (relative path — from where you are)
cd ..               # Go up one level (parent directory)
cd ../..            # Go up TWO levels
cd ~                # Go to YOUR home directory (shortcut!)
cd -                # Go back to previous directory (like browser back button)
cd /                # Go to the very root of the filesystem
```

**Absolute vs Relative paths:**
```
Absolute path: Starts with /  — always works from anywhere
  cd /home/akshat/Documents    (always goes to this exact location)

Relative path: No starting /  — relative to WHERE YOU ARE NOW
  If you're in /home/akshat/
  cd Documents                 (goes to /home/akshat/Documents)
  cd ../john/Documents         (goes to /home/john/Documents)
```

**Real Life Example 🌍**
> Absolute path = giving someone a full address: "Flat 5, Building B, MG Road, Pune, Maharashtra"
> Relative path = "Turn right, then second building on left" — only works if you know where they're starting from!

---

## Finding Files — `find` and `locate`

### `find` — The Powerful One

```bash
find / -name "passwd"              # Find file named "passwd" anywhere on system
find /home -name "*.txt"           # Find all .txt files in /home
find /var/log -name "*.log"        # Find all log files
find / -type f -name "*.conf"      # Find files (-type f) ending in .conf
find / -type d -name "ssh"         # Find directories (-type d) named "ssh"
find / -size +10M                  # Find files larger than 10MB
find / -size -1k                   # Find files smaller than 1KB
find / -user akshat                # Find all files owned by user "akshat"
find / -perm 777                   # Find files with permission 777 (dangerous!)
find / -perm -4000                 # Find files with SUID bit set (security!)
find / -mtime -7                   # Modified in last 7 days
find / -newer /etc/passwd          # Modified more recently than /etc/passwd
find /tmp -name "*.sh" -type f     # Find shell scripts in /tmp (malware indicator!)
```

**Real security examples:**
```bash
# Find SUID files (files that run with owner's privileges — attackers love these)
find / -perm -u=s -type f 2>/dev/null

# Find world-writable files (any user can write — dangerous!)
find / -perm -o+w -type f 2>/dev/null

# Find recently modified files (possible indicator of compromise)
find /etc -mtime -1 2>/dev/null    # Modified in last 24 hours

# Find all shell scripts (attacker may have left backdoors)
find / -name "*.sh" -type f 2>/dev/null
```

> 💡 `2>/dev/null` redirects error messages to nowhere — keeps output clean by hiding "Permission denied" errors for files you can't read.

### `locate` — The Fast One

```bash
locate passwd           # Fast search — uses a pre-built database
locate -i readme        # Case-insensitive search
updatedb                # Update the locate database (run as root)
```

`locate` is much faster than `find` but only searches a database updated periodically. `find` searches in real-time — use `find` for security work.

---

## `which` and `whereis` — Finding Commands

```bash
which python3           # Where is the python3 command?
which nmap              # Where is nmap installed?
whereis nmap            # Where is nmap and its man page?
```

```
$ which nmap
/usr/bin/nmap

$ whereis nmap
nmap: /usr/bin/nmap /usr/share/man/man1/nmap.1.gz
```

---

# 📄 SECTION 4 — Files and Directories

## Creating Things

```bash
touch filename.txt              # Create empty file (or update timestamp if exists)
touch file1.txt file2.txt       # Create multiple files at once
mkdir myfolder                  # Create a directory
mkdir -p projects/web/html      # Create nested directories (makes all missing parents)
mkdir -p notes/{day1,day2,day3} # Create multiple subdirectories at once
```

**Example:**
```bash
$ mkdir -p /home/akshat/security/labs/week1
$ ls /home/akshat/security/labs/
week1
```

---

## Copying Files — `cp`

```bash
cp file.txt backup.txt              # Copy file.txt to backup.txt
cp file.txt /tmp/                   # Copy to /tmp/ directory
cp file.txt /tmp/newname.txt        # Copy with new name
cp -r folder/ /tmp/folder_backup/   # Copy entire directory (-r = recursive)
cp -p file.txt backup.txt           # Preserve permissions, timestamps
cp -v file.txt /tmp/                # Verbose — shows what it's copying
cp *.txt /tmp/                      # Copy ALL .txt files to /tmp/
```

---

## Moving / Renaming — `mv`

```bash
mv file.txt newname.txt             # Rename file
mv file.txt /tmp/                   # Move file to /tmp/
mv oldname.txt /tmp/newname.txt     # Move and rename at same time
mv -v file.txt /tmp/                # Verbose
mv folder/ /home/akshat/backup/     # Move entire directory
```

> 💡 `mv` is both move AND rename. If destination is in same folder = rename. If different folder = move.

---

## Deleting — `rm` (Be Careful!)

```bash
rm file.txt                 # Delete a file
rm -i file.txt              # Ask for confirmation before deleting
rm -f file.txt              # Force delete, no questions
rm -r folder/               # Delete entire directory and contents
rm -rf folder/              # Force delete directory (NO GOING BACK!)
rm *.log                    # Delete all .log files
```

> ⚠️ **WARNING:** Linux has NO recycle bin. `rm -rf` deletes permanently with NO confirmation. The infamous `rm -rf /` deletes your ENTIRE system. ALWAYS double-check before running `rm -rf`.

**Safe habit:** Use `-i` flag when learning → `rm -i file.txt` asks "remove file.txt?" before deleting.

---

## Creating Symbolic Links — `ln -s`

A symbolic link (symlink) is like a shortcut in Windows — it points to another file or directory.

```bash
ln -s /var/log/auth.log ~/auth_shortcut.log
# Now you can just type: cat ~/auth_shortcut.log
# Instead of: cat /var/log/auth.log
```

```
$ ls -la ~/auth_shortcut.log
lrwxrwxrwx 1 akshat akshat 20 Jan 15 10:00 auth_shortcut.log -> /var/log/auth.log
                                                                 ^ the -> shows it's a symlink
```

> 🔴 **Security note:** Attackers use symlinks to make malicious files look like legitimate system files. During investigations, check for unexpected symlinks with `find / -type l -ls`.

---

# 📖 SECTION 5 — Reading File Contents

## `cat` — Show Entire File

```bash
cat file.txt                    # Print entire file to screen
cat /etc/passwd                 # Read the passwd file
cat /var/log/auth.log           # Read auth log
cat -n file.txt                 # Show with line numbers
cat file1.txt file2.txt         # Show multiple files one after another
```

**`/etc/passwd` format:**
```
username:password:UID:GID:comment:home_directory:shell
akshat:x:1001:1001:Akshat,,,:/home/akshat:/bin/bash
       ^
       x = password stored in /etc/shadow (not here)
```

Fields explained:
```
akshat     = username
x          = password placeholder (actual hash in /etc/shadow)
1001       = User ID (UID) — unique number for this user
1001       = Group ID (GID) — primary group
Akshat,,,  = Comment/full name
/home/akshat = Home directory
/bin/bash  = Default shell (what runs when they log in)
```

---

## `less` and `more` — Reading Big Files

`cat` dumps everything at once — not useful for huge log files. Use `less` instead.

```bash
less /var/log/auth.log          # Opens file in scrollable viewer
```

**Controls inside `less`:**
```
Space or f     → Go forward one page
b              → Go back one page
Arrow keys     → Scroll line by line
/searchterm    → Search forward (press n for next match)
?searchterm    → Search backward
g              → Go to very beginning
G              → Go to very end (useful! Jump to latest log entries)
q              → Quit
```

> 💡 **SOC tip:** When reading a log file with `less`, press `G` immediately to jump to the END — the most recent events are usually what you want first.

---

## `head` and `tail` — First/Last Lines

```bash
head file.txt               # Show first 10 lines
head -n 20 file.txt         # Show first 20 lines
head -n 5 /etc/passwd       # Show first 5 users

tail file.txt               # Show last 10 lines
tail -n 50 file.txt         # Show last 50 lines
tail -n 100 /var/log/auth.log   # Show last 100 log entries

tail -f /var/log/auth.log   # LIVE FOLLOW — shows new lines as they're added!
```

> 💡 **`tail -f` is your best friend as a SOC analyst!** It shows logs in real-time. Open a terminal, run `tail -f /var/log/auth.log`, then in another terminal try to SSH into the machine with wrong password. Watch the failed login appear in real-time!

---

## `grep` — Search Inside Files (Most Important Command!)

`grep` searches for patterns inside files. In security work, you'll use `grep` dozens of times per day.

```bash
grep "Failed" /var/log/auth.log          # Find lines with "Failed"
grep "akshat" /etc/passwd                # Find lines containing "akshat"
grep -i "error" file.txt                 # Case-insensitive search
grep -n "password" file.txt              # Show line numbers
grep -v "INFO" /var/log/syslog           # Show lines that DON'T contain "INFO"
grep -r "password" /etc/                 # Search recursively in directory
grep -l "password" /var/log/*            # Show only filenames that match
grep -c "Failed" /var/log/auth.log       # Count matching lines
grep -A 3 "Failed password" auth.log     # Show 3 lines AFTER match
grep -B 3 "Failed password" auth.log     # Show 3 lines BEFORE match
grep -A 3 -B 3 "Failed" auth.log         # 3 lines before AND after
grep "^root" /etc/passwd                 # Lines STARTING with "root"
grep "bash$" /etc/passwd                 # Lines ENDING with "bash"
```

**Regex basics in grep:**
```
^      = Start of line
$      = End of line
.      = Any single character
*      = Zero or more of previous
+      = One or more of previous
[abc]  = Any of a, b, or c
[0-9]  = Any digit
\d     = Digit (in grep -P for Perl regex)
```

**Real security examples:**
```bash
# Find failed SSH logins
grep "Failed password" /var/log/auth.log

# Find successful logins
grep "Accepted password" /var/log/auth.log
grep "Accepted publickey" /var/log/auth.log

# Find which IPs are brute-forcing your SSH
grep "Failed password" /var/log/auth.log | grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+'

# Find sudo usage (who ran commands as root?)
grep "sudo:" /var/log/auth.log

# Find new user creation (potential attacker adding backdoor account)
grep "useradd\|adduser" /var/log/auth.log

# Find commands run as root
grep "COMMAND" /var/log/auth.log
```

---

## `wc` — Count Lines, Words, Characters

```bash
wc file.txt                 # Lines, words, characters
wc -l file.txt              # Count lines only
wc -w file.txt              # Count words only
wc -c file.txt              # Count bytes/characters

# Security uses:
grep "Failed" /var/log/auth.log | wc -l    # How many failed logins total?
cat /etc/passwd | wc -l                     # How many users on this system?
```

---

## `strings` — Find Readable Text in Binary Files

```bash
strings suspicious_file.exe     # Extract readable strings from binary
strings /bin/ls | grep "/"      # Find paths used by ls command
```

> 🔴 **Malware analysis:** `strings` is one of the FIRST tools you run on a suspicious file. It can reveal:
> - URLs the malware connects to
> - File paths it accesses
> - Error messages that reveal its purpose
> - Hardcoded passwords or API keys

```bash
$ strings suspicious.exe | grep "http"
http://evil-c2-server.com/beacon
http://evil-c2-server.com/exfil

$ strings suspicious.exe | grep ".exe"
cmd.exe
powershell.exe
```

---

## `file` — What Type of File Is This?

```bash
file document.txt           # Shows: ASCII text
file image.jpg              # Shows: JPEG image data
file /bin/ls                # Shows: ELF 64-bit executable (Linux binary)
file suspicious_file        # Even without extension — tells you what it really is
```

> 🔴 **Security use:** Attackers rename malicious files to look innocent. A file named `invoice.pdf` might actually be an ELF executable. `file` ignores the extension and looks at the actual file content to identify it.

```bash
$ file invoice.pdf
invoice.pdf: ELF 64-bit LSB executable, x86-64, dynamically linked
                                         ^ This is a LINUX PROGRAM, not a PDF!
```

---

# 🔐 SECTION 6 — File Permissions (Critical for Security!)

## The Most Important Concept After Navigation

Permissions control WHO can do WHAT with every file. Getting permissions right is essential for both system administration AND security.

## Understanding Permission Notation

Every file has 10 characters of permission info:

```
-rwxr-xr--
│├──┤├──┤├──┤
│ │  │  └── Other (everyone else) permissions
│ │  └───── Group permissions
│ └──────── Owner (user) permissions
└────────── File type (- = file, d = directory, l = symlink)
```

**Each permission block has 3 characters:**
```
rwx = read, write, execute (all permissions)
r-- = read only
rw- = read and write
r-x = read and execute
--- = no permissions at all
```

## What Do Permissions Mean?

| Permission | On a File | On a Directory |
|-----------|-----------|----------------|
| **r (read)** | Can view file contents (`cat`, `less`) | Can list contents (`ls`) |
| **w (write)** | Can modify file contents | Can create/delete files inside |
| **x (execute)** | Can run file as program | Can enter directory (`cd`) |

**Real Life Example 🌍**
> Imagine a filing cabinet drawer (directory):
> - **r** = You can LOOK at what folders are inside the drawer
> - **w** = You can ADD or REMOVE folders from the drawer
> - **x** = You can actually OPEN the drawer and reach inside
>
> Without **x** on a directory, you can't enter it even if you can list it!

## The Three Permission Groups

```
-rwxr-xr--
  │││├──┤├──┤
  │││  │  └── Other: r-- (read only)
  │││  └───── Group: r-x (read + execute)
  │└┘──────── Owner: rwx (full control)
  └────────── File type: - (regular file)
```

**Who are these groups?**
- **Owner (User):** The person who created the file
- **Group:** A group of users (e.g., "developers", "admins")
- **Other:** Everyone else on the system

## Numeric (Octal) Permissions — The Fast Way

Each permission has a number:
```
r = 4
w = 2
x = 1
- = 0

Add them up for each group:
rwx = 4+2+1 = 7
rw- = 4+2+0 = 6
r-x = 4+0+1 = 5
r-- = 4+0+0 = 4
-wx = 0+2+1 = 3
-w- = 0+2+0 = 2
--x = 0+0+1 = 1
--- = 0+0+0 = 0
```

**Common permission numbers:**
| Number | Permissions | Meaning | Used for |
|--------|------------|---------|---------|
| **777** | rwxrwxrwx | Everyone can do everything | Dangerous! Never use. |
| **755** | rwxr-xr-x | Owner full, others read+execute | Programs, directories |
| **644** | rw-r--r-- | Owner read+write, others read | Regular files, configs |
| **600** | rw------- | Only owner can read+write | Private files, SSH keys |
| **400** | r-------- | Only owner can read | Very sensitive (private keys!) |
| **000** | --------- | No one can do anything | Lock a file |

**Security-critical permissions:**
```
/etc/shadow should be: 640 (root reads, shadow group reads, others NOTHING)
SSH private key:        600 (only you can read)
SSH public key:         644 (anyone can read — it's public!)
/etc/passwd:            644 (anyone reads, only root writes)
/sbin/su:               4755 (has SUID bit — runs as root!)
```

## Changing Permissions — `chmod`

```bash
chmod 755 script.sh             # Set to rwxr-xr-x (numeric)
chmod 644 config.txt            # Set to rw-r--r--
chmod 600 ~/.ssh/id_rsa         # Make SSH private key owner-only
chmod 777 file.txt              # Give everyone full permissions (dangerous!)

# Symbolic method:
chmod u+x script.sh             # Add execute for owner (u=user/owner)
chmod g-w file.txt              # Remove write from group
chmod o-r private.txt           # Remove read from others
chmod a+r file.txt              # Add read for ALL (a=all: u+g+o)
chmod u+x,g-w file.txt          # Multiple changes at once

# Recursive (whole directory):
chmod -R 755 /var/www/html/     # Set 755 on directory and all contents
```

**Remember:**
```
u = user (owner)
g = group
o = other
a = all (u+g+o)

+ = add permission
- = remove permission
= = set exactly these permissions
```

## Changing Ownership — `chown`

```bash
chown akshat file.txt               # Change owner to akshat
chown akshat:developers file.txt    # Change owner AND group
chown -R www-data:www-data /var/www/ # Change recursively (for web server)
chown :developers file.txt          # Change only group
chgrp developers file.txt           # Change group only (alternative)
```

## SUID, SGID, Sticky Bit — The Special Permissions

These are advanced permission bits that are very important for security.

### SUID — Set User ID

**What it does:** When a file with SUID is executed, it runs with the file OWNER's permissions (usually root), not the current user's permissions.

```
Permission display: -rwsr-xr-x  (note the 's' instead of 'x' for owner execute)
Numeric: 4755 (the 4 prefix = SUID)
```

**Real Life Example 🌍**
> The `passwd` command lets you change your own password. But passwords are stored in `/etc/shadow` which only root can write to. How can a normal user change their password?
>
> Answer: `/usr/bin/passwd` has SUID set! It temporarily runs as root to write your new password to `/etc/shadow`. After it finishes, root privileges are gone.

```bash
$ ls -la /usr/bin/passwd
-rwsr-xr-x 1 root root 59976 Nov 24 2022 /usr/bin/passwd
   ^ the 's' here = SUID
```

> 🔴 **Security critical:** Finding unexpected SUID files is a KEY part of privilege escalation in pentesting! Attackers look for misconfigured SUID files to gain root access.

```bash
# Find all SUID files (run during security audit or pentest)
find / -perm -u=s -type f 2>/dev/null

# Common legitimate SUID files:
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/su
/bin/ping
/bin/mount

# If you find something unusual here — BIG red flag!
```

### SGID — Set Group ID

```
Permission display: -rwxr-sr-x  (the 's' in group execute position)
Numeric: 2755 (the 2 prefix = SGID)
```

On directories: New files created inside inherit the directory's group (useful for shared project folders).

### Sticky Bit

```
Permission display: drwxrwxrwt  (the 't' at the end)
Numeric: 1777 (the 1 prefix = sticky)
```

On directories: Only the file OWNER (or root) can delete files, even if directory is world-writable.

```bash
$ ls -la /tmp/
drwxrwxrwt 12 root root 4096 Jan 15 10:00 /tmp/
         ^  sticky bit on /tmp/ — everyone can write, but only you delete YOUR files
```

---

# 👤 SECTION 7 — Users and Groups

## User Accounts in Linux

Every action in Linux is done by a USER. Understanding users is critical for both administration and security.

### Types of Users

| Type | UID Range | Description |
|------|-----------|-------------|
| **root** | 0 | Superuser — unlimited power. UID is ALWAYS 0. |
| **System users** | 1–999 | Services (www-data, mysql, ssh). No login shell. |
| **Regular users** | 1000+ | Actual human users. Have home directories. |

### The `/etc/passwd` File — Full Understanding

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
akshat:x:1001:1001:Akshat Sharma:/home/akshat:/bin/bash
```

Format: `username:password:UID:GID:comment:home:shell`

| Shell | Meaning |
|-------|---------|
| `/bin/bash` | Normal login shell — can log in |
| `/bin/sh` | Simple shell — can log in |
| `/usr/sbin/nologin` | Cannot log in — service account |
| `/bin/false` | Cannot log in — service account |

> 🔴 **Security:** During incident response, check `/etc/passwd` for accounts with UID 0 (besides root) — that's a MAJOR red flag (attacker created a root-level backdoor account).

```bash
# Check for any suspicious root-level accounts
awk -F: '($3 == 0) {print}' /etc/passwd

# Legitimate output:
root:x:0:0:root:/root:/bin/bash

# Suspicious output (attacker added this):
root:x:0:0:root:/root:/bin/bash
backdoor:x:0:0::/root:/bin/bash    ← Second UID 0 account = ATTACKER BACKDOOR!
```

### The `/etc/shadow` File — Password Hashes

```
akshat:$6$rounds=5000$salt$hashedpassword:19000:0:99999:7:::
       │                                   │     │ │     └── Days before must change
       │                                   │     │ └──────── Max days between changes
       │                                   │     └────────── Min days between changes
       │                                   └──────────────── Days since Jan 1, 1970 password was changed
       └──────────────────────────────────────────────────── Password hash
```

Password hash format: `$id$salt$hash`
```
$1$  = MD5 (weak — crackable quickly)
$2a$ = Blowfish (bcrypt)
$5$  = SHA-256
$6$  = SHA-512 (most secure — what modern Linux uses)
!    = Account locked (! at start of hash = can't log in)
*    = No password set (can't log in with password)
```

> 🔴 **Security:** `/etc/shadow` is readable ONLY by root. If you get access to it during a pentest, you can try to crack the hashes offline using hashcat or john the ripper.

## Managing Users — Commands

```bash
# Creating users
useradd akshat                          # Basic user creation
useradd -m -s /bin/bash akshat          # With home dir (-m) and shell (-s)
useradd -m -G sudo akshat               # Add to sudo group automatically
adduser akshat                          # Interactive version (Ubuntu) — easier

# Setting passwords
passwd akshat                           # Set password for akshat
passwd                                  # Change YOUR OWN password

# Modifying users
usermod -aG sudo akshat                 # Add akshat to sudo group (-a = append, -G = groups)
usermod -aG docker,developers akshat    # Add to multiple groups
usermod -s /bin/zsh akshat              # Change shell
usermod -l newname akshat               # Rename user (login name)
usermod -L akshat                       # LOCK account (can't log in)
usermod -U akshat                       # UNLOCK account

# Deleting users
userdel akshat                          # Delete user (keep home directory)
userdel -r akshat                       # Delete user AND home directory
```

## Groups — Managing Team Permissions

```bash
cat /etc/group              # See all groups and their members
groups                      # See what groups YOU belong to
groups akshat               # See groups akshat belongs to
id                          # See YOUR UID, GID, and all group memberships
id akshat                   # See akshat's IDs and groups

groupadd developers         # Create a new group
groupdel developers         # Delete a group
gpasswd -a akshat developers # Add akshat to developers group
gpasswd -d akshat developers # Remove akshat from developers group
```

**Example `id` output:**
```
$ id
uid=1001(akshat) gid=1001(akshat) groups=1001(akshat),27(sudo),1002(developers)
                                         ^ your groups: akshat, sudo, developers
```

## `sudo` — Running Commands as Root

`sudo` (Super User Do) lets authorised users run specific commands as root WITHOUT becoming root.

```bash
sudo command                    # Run command as root
sudo -u www-data command        # Run as specific user (www-data)
sudo -i                         # Become root (interactive root shell)
sudo su -                       # Alternative: switch to root
sudo su - akshat                # Switch to akshat user
sudo !!                         # Repeat last command with sudo (saves retyping!)

sudo cat /etc/shadow            # Read shadow file (only root can normally)
sudo systemctl restart nginx    # Restart web server (needs root)
sudo apt install nmap           # Install software (needs root)
```

**The `/etc/sudoers` file:**
Controls exactly who can run what with sudo.

```
# Format:
user    hosts=(run_as)  commands

# Examples:
root    ALL=(ALL:ALL) ALL          # Root can do everything
akshat  ALL=(ALL:ALL) ALL          # akshat can do everything with sudo
www-data ALL=(ALL) NOPASSWD:/bin/systemctl   # www-data can restart services without password
%sudo   ALL=(ALL:ALL) ALL          # Everyone in sudo GROUP can do everything
```

> ⚠️ **NEVER edit `/etc/sudoers` directly!** Use `visudo` — it validates syntax before saving. Wrong syntax in sudoers can lock you out of sudo!

```bash
sudo visudo                     # Safely edit sudoers file
```

> 🔴 **Privilege escalation:** During pentesting, the sudoers file is one of the first things you check. `sudo -l` shows what YOU can run with sudo without root password — sometimes misconfigured entries allow full root access!

```bash
sudo -l     # List what you can run with sudo (NO root password needed!)
```

---

---

# ⚙️ SECTION 8 — Processes (Running Programs)

## What Is a Process?

Every program running on your system is a **process**. When you open a browser, a process starts. When you run a command, a process starts. Linux assigns every process a unique **PID (Process ID)**.

**Real Life Example 🌍**
> Think of your college canteen taking orders. Each order gets a unique token number. That token number is the PID. The canteen (operating system) tracks every order (process) by its number. If an order is stuck, the manager (you) can cancel it using the token number.

---

## Viewing Processes

### `ps` — Snapshot of Processes

```bash
ps                          # Processes in YOUR current session only
ps aux                      # ALL processes on system (most common!)
ps aux | grep nginx         # Find nginx process
ps aux | grep akshat        # All processes run by akshat
ps -ef                      # Same as aux but different format
ps -ef --forest             # Show process tree (parent-child relationships)
```

**Understanding `ps aux` output:**
```
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 225804  9128 ?        Ss   Jan14   0:03 /sbin/init
root       623  0.0  0.2  72296 19456 ?        Ss   Jan14   0:00 /usr/sbin/sshd
www-data  1502  0.0  0.3 200512 24576 ?        S    09:00   0:01 /usr/sbin/apache2
akshat    2345  0.2  0.1  14256  8192 pts/0    Ss   10:30   0:00 -bash
akshat    2789  0.0  0.0  10944  3200 pts/0    R+   10:45   0:00 ps aux
```

Column meanings:
```
USER   = Who owns the process
PID    = Process ID (unique number)
%CPU   = CPU usage percentage
%MEM   = Memory usage percentage
VSZ    = Virtual memory used (KB)
RSS    = Physical RAM used (KB)
TTY    = Terminal (? = not attached to terminal)
STAT   = Process state (S=sleeping, R=running, Z=zombie, T=stopped)
START  = When process started
TIME   = Total CPU time used
COMMAND = The command that started it
```

**Process states:**
| State | Letter | Meaning |
|-------|--------|---------|
| Running | R | Currently executing on CPU |
| Sleeping | S | Waiting for something (I/O, event) |
| Stopped | T | Paused (Ctrl+Z) |
| Zombie | Z | Finished but parent hasn't cleaned it up |
| Disk sleep | D | Uninterruptible sleep (doing disk I/O) |

> 🔴 **Security use:** `ps aux` is one of the FIRST commands during incident response. Look for:
> - Processes running as root that shouldn't be
> - Processes with strange names (random characters)
> - Processes with no TTY (?) running from unusual locations
> - Multiple instances of the same process

---

### `top` — Live Process Monitor

```bash
top             # Live updating process list (like Task Manager!)
top -u akshat   # Show only akshat's processes
```

**Inside `top`:**
```
top - 10:45:01 up 5 days, 3:22,  2 users,  load average: 0.15, 0.10, 0.08
Tasks: 142 total,   1 running, 141 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.3 us,  0.5 sy,  0.0 ni, 96.8 id,  0.3 wa,  0.0 hi,  0.1 si
MiB Mem :   7982.1 total,   3421.2 free,   2876.5 used,   1684.4 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   4785.3 avail Mem
```

**Controls inside `top`:**
```
q     → Quit
k     → Kill a process (enter PID when prompted)
r     → Renice (change priority)
M     → Sort by memory usage
P     → Sort by CPU usage (default)
u     → Filter by username
1     → Show individual CPU cores
h     → Help
```

### `htop` — Better Version of top

```bash
sudo apt install htop       # Install it
htop                        # Run it
```

htop is colourful, easier to read, and supports mouse clicks. Use this for daily monitoring.

---

## Killing Processes

```bash
kill PID                    # Send SIGTERM (polite — please stop)
kill -9 PID                 # Send SIGKILL (force kill — stop NOW)
kill -15 PID                # Same as kill PID (SIGTERM)
kill -l                     # List all signal types

killall nginx               # Kill all processes named "nginx"
killall -9 nginx            # Force kill all nginx processes
pkill nginx                 # Same as killall (pattern match)
pkill -u akshat             # Kill all processes by user akshat
```

**Common signals:**
| Signal | Number | Meaning |
|--------|--------|---------|
| SIGTERM | 15 | Polite request to stop (can be ignored) |
| SIGKILL | 9 | Forced termination (cannot be ignored) |
| SIGSTOP | 19 | Pause the process |
| SIGCONT | 18 | Resume a paused process |
| SIGHUP | 1 | Reload config (restart without kill) |

**Real Life Example 🌍**
> SIGTERM is like politely saying "excuse me, please stop what you're doing." SIGKILL is like physically pulling the plug. Use SIGTERM first — it lets the program clean up. Use SIGKILL only if SIGTERM doesn't work.

---

## Background and Foreground Jobs

```bash
command &                   # Run command in BACKGROUND (& at end)
Ctrl + Z                    # Pause current foreground process
bg                          # Send paused process to background
fg                          # Bring background process to foreground
fg %2                       # Bring job #2 to foreground
jobs                        # List all background jobs in current session
nohup command &             # Run in background, survives terminal closing
```

**Example:**
```bash
$ ping google.com &
[1] 3456
PING google.com ... (running in background)

$ jobs
[1]+  Running     ping google.com &

$ fg %1          # Bring it back to foreground
ping google.com ...

Ctrl+Z           # Pause it
[1]+  Stopped    ping google.com

$ bg             # Resume in background
[1]+  ping google.com &

$ kill %1        # Kill job #1 by job number
```

---

## `systemctl` — Managing Services (Daemons)

Services (daemons) are programs that run continuously in the background — web server, SSH server, database, etc.

```bash
# Viewing services
systemctl status nginx              # Check if nginx is running
systemctl list-units --type=service # List ALL services
systemctl list-units --type=service --state=running  # Only running services

# Controlling services
sudo systemctl start nginx          # Start nginx
sudo systemctl stop nginx           # Stop nginx
sudo systemctl restart nginx        # Stop then start
sudo systemctl reload nginx         # Reload config without stopping

# Enable/disable on boot
sudo systemctl enable nginx         # Start nginx automatically on boot
sudo systemctl disable nginx        # Don't start on boot
sudo systemctl enable --now nginx   # Enable AND start right now

# Check logs for a service
sudo journalctl -u nginx            # Show nginx logs
sudo journalctl -u nginx -f         # Follow nginx logs live
sudo journalctl -u nginx --since "1 hour ago"
```

> 🔴 **Security:** Attackers create malicious services to maintain persistence. Check for unexpected services:
```bash
systemctl list-units --type=service --state=running
# Look for services with random names or unusual descriptions
```

---

## `cron` — Scheduled Tasks

Cron runs commands automatically at scheduled times. Like Windows Task Scheduler.

```bash
crontab -l          # List YOUR scheduled tasks
crontab -e          # Edit YOUR crontab
sudo crontab -l     # List ROOT's scheduled tasks (check for malware!)
cat /etc/crontab    # System-wide cron tasks
ls /etc/cron.d/     # Additional cron job files
ls /etc/cron.daily/ # Scripts that run daily
ls /etc/cron.hourly/# Scripts that run hourly
```

**Crontab format:**
```
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0-7, 0&7=Sunday)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)

* = "every" (any value)
```

**Examples:**
```bash
# Run every minute
* * * * * /path/to/script.sh

# Run every day at 2:30 AM
30 2 * * * /path/to/backup.sh

# Run every Monday at 9 AM
0 9 * * 1 /path/to/report.sh

# Run every 15 minutes
*/15 * * * * /path/to/check.sh

# Run at boot
@reboot /path/to/startup.sh
```

> 🔴 **Security critical:** Attackers add entries to crontab for persistence (keep running even after reboot). ALWAYS check `crontab -l` AND `sudo crontab -l` AND `/etc/crontab` AND `/etc/cron.d/` during incident response. Even one suspicious entry = attacker has persistence!

---

# 🌐 SECTION 9 — Networking Commands

## Viewing Your Network Configuration

```bash
# Modern way (ip command):
ip addr                     # Show all network interfaces and IPs
ip addr show eth0           # Show specific interface
ip route                    # Show routing table
ip route show               # Same
ip neigh                    # Show ARP table (IP → MAC mappings)
ip link                     # Show interface status (up/down)

# Older way (still widely used):
ifconfig                    # Show all interfaces (install: apt install net-tools)
ifconfig eth0               # Show specific interface
route -n                    # Show routing table
arp -a                      # Show ARP table
```

**Understanding `ip addr` output:**
```
$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP>
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo        ← Loopback (localhost)

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    link/ether aa:bb:cc:dd:ee:ff          ← MAC address
    inet 192.168.1.50/24 brd 192.168.1.255 scope global eth0  ← Your IP
    inet6 fe80::a8bb:ccff:fedd:eeff/64    ← IPv6 link-local
```

---

## Testing Connectivity

```bash
ping google.com             # Test if host is reachable (sends ICMP packets)
ping -c 4 google.com        # Send exactly 4 pings then stop
ping -c 4 8.8.8.8           # Ping Google's DNS server
ping 127.0.0.1              # Ping yourself (test your own stack)
ping 192.168.1.1            # Ping your router/gateway

traceroute google.com       # Trace route to destination (every router hop)
traceroute -n google.com    # Don't resolve hostnames (faster)
mtr google.com              # Better traceroute — live updating (install: apt install mtr)

# Test if a specific port is open:
nc -zv 192.168.1.1 22       # Test if port 22 (SSH) is open on 192.168.1.1
nc -zv google.com 443       # Test if HTTPS port open on google.com
telnet google.com 80        # Old way to test ports (still useful)
```

---

## Viewing Network Connections — `netstat` and `ss`

```bash
# Modern way (ss — socket statistics):
ss -tlnp                    # TCP (-t), Listening (-l), Numeric (-n), Process (-p)
ss -ulnp                    # UDP listening ports
ss -tnp                     # All TCP connections (not just listening)
ss -anp                     # All connections (TCP+UDP)
ss -tnp state established   # Only established connections

# Older way (still everywhere):
netstat -tlnp               # TCP listening with process names
netstat -an                 # All connections, numeric
netstat -rn                 # Routing table
netstat -s                  # Statistics per protocol

# Find what's using a specific port:
ss -tlnp | grep :80         # What's using port 80?
netstat -tlnp | grep :443   # What's using port 443?
lsof -i :22                 # What process is using port 22?
lsof -i :8080               # What process is using port 8080?
```

> 🔴 **Security use:**
```bash
# Check for suspicious listening ports (backdoors!)
ss -tlnp
# Normal system has: 22 (SSH), maybe 80/443 (web)
# Suspicious: port 4444, 1234, 31337 — classic attacker/backdoor ports!

# Check for suspicious outbound connections (C2 communication!)
ss -tnp state established
# Look for connections to unusual IPs or ports you don't recognise
```

---

## DNS Tools

```bash
nslookup google.com                 # Basic DNS lookup
nslookup -type=MX gmail.com         # Mail server records
nslookup -type=TXT google.com       # TXT records (SPF, verification)
nslookup -type=NS google.com        # Nameservers

dig google.com                      # Better DNS tool (more detail)
dig google.com A                    # A record (IPv4)
dig google.com MX                   # Mail servers
dig google.com ANY                  # All record types
dig @8.8.8.8 google.com             # Query specific DNS server (8.8.8.8 = Google DNS)
dig -x 142.250.80.46                # Reverse lookup (IP → domain)

host google.com                     # Simple and clean DNS lookup
host -t MX gmail.com                # Mail records
```

---

## Downloading Files — `curl` and `wget`

```bash
# curl — transfer data from/to servers
curl https://google.com                     # Get webpage content
curl -o output.html https://google.com      # Save to file
curl -I https://google.com                  # Show only HTTP headers
curl -v https://google.com                  # Verbose (show everything)
curl -L https://shorturl.com/xyz            # Follow redirects (-L)
curl -u user:pass https://api.example.com   # With authentication
curl -X POST -d "key=value" https://api.com # POST request

# wget — download files
wget https://example.com/file.zip           # Download file
wget -O output.zip https://example.com/f.zip # Save with custom name
wget -r https://website.com                 # Download entire website
wget -c https://example.com/large.iso      # Resume interrupted download
wget -q https://example.com/file.txt       # Quiet (no output)
```

> 💡 **Security use:**
```bash
# Check what headers a web server sends (reveals software versions!)
curl -I https://targetsite.com
# Output might show: Server: Apache/2.4.29 → You know the version → Check for vulnerabilities!

# Download malware samples safely for analysis (in isolated VM)
wget https://malware-traffic-analysis.net/sample.exe

# Test if a site redirects properly (HTTP → HTTPS)
curl -I http://google.com
# Should show: HTTP/1.1 301 Moved Permanently → Location: https://google.com
```

---

## `ssh` — Connecting to Remote Servers

```bash
ssh akshat@192.168.1.100            # SSH to IP as user akshat
ssh akshat@server.company.com       # SSH to hostname
ssh -p 2222 akshat@server.com       # SSH on non-default port
ssh -i ~/.ssh/mykey akshat@server.com  # Use specific private key

# Copy files over SSH:
scp file.txt akshat@server.com:/home/akshat/    # Copy file TO remote
scp akshat@server.com:/var/log/auth.log ./      # Copy FROM remote
scp -r folder/ akshat@server.com:/home/akshat/  # Copy folder recursively

# SSH tunnelling (port forwarding):
ssh -L 8080:localhost:80 akshat@server.com      # Forward local port 8080 to server's port 80
ssh -R 9090:localhost:9090 akshat@server.com    # Reverse tunnel
```

**SSH config file — save typing:**
```bash
nano ~/.ssh/config

# Add this:
Host myserver
    HostName 192.168.1.100
    User akshat
    Port 22
    IdentityFile ~/.ssh/id_rsa

# Now just type:
ssh myserver          # Instead of: ssh -i ~/.ssh/id_rsa akshat@192.168.1.100
```

---

# ✏️ SECTION 10 — Text Editors

## `nano` — Easy Editor (Start Here!)

```bash
nano file.txt           # Open file in nano editor
nano /etc/hosts         # Edit hosts file (need sudo for system files)
sudo nano /etc/crontab  # Edit crontab as root
```

**Nano controls** (^ means Ctrl):
```
Ctrl + O        → Save file (WriteOut)
Ctrl + X        → Exit
Ctrl + W        → Search (Where is)
Ctrl + K        → Cut entire line
Ctrl + U        → Paste line
Ctrl + G        → Help
Ctrl + V        → Page down
Ctrl + Y        → Page up
Ctrl + \        → Find and replace
Alt  + U        → Undo
```

> 💡 **Tip:** When nano shows `^X Exit` at the bottom — the `^` means Ctrl. So `^X` = Ctrl+X.

---

## `vim` — Powerful Editor (Learn This Too!)

vim is on every Linux system by default. You WILL encounter it in the field.

```bash
vim file.txt            # Open file in vim
vi file.txt             # Same (vi is the older version)
```

**The KEY thing about vim — it has MODES:**

```
NORMAL mode   → Default. Navigate, copy, paste, delete. Press Esc to return here.
INSERT mode   → Type text. Press i to enter.
COMMAND mode  → Run commands. Press : to enter.
VISUAL mode   → Select text. Press v to enter.
```

**Essential vim commands:**

Starting out:
```
i           → Enter INSERT mode (before cursor) — start typing!
a           → Enter INSERT mode (after cursor)
o           → New line below and enter INSERT mode
O           → New line above and enter INSERT mode
Esc         → Return to NORMAL mode (press this when lost!)
```

In NORMAL mode (navigation):
```
h, j, k, l  → Left, Down, Up, Right (or arrow keys)
w           → Jump to next word
b           → Jump back one word
0           → Go to start of line
$           → Go to end of line
gg          → Go to very first line
G           → Go to very last line
:n          → Go to line number n (e.g. :42 goes to line 42)
Ctrl+d      → Page down half screen
Ctrl+u      → Page up half screen
```

Editing in NORMAL mode:
```
dd          → Delete (cut) current line
yy          → Copy (yank) current line
p           → Paste after cursor
P           → Paste before cursor
u           → Undo
Ctrl+r      → Redo
x           → Delete character under cursor
r           → Replace single character
cw          → Change (delete) word and enter insert mode
D           → Delete from cursor to end of line
```

Searching:
```
/searchterm → Search forward (press n for next, N for previous)
?searchterm → Search backward
:%s/old/new/g → Replace ALL occurrences of "old" with "new"
```

Saving and quitting (COMMAND mode — press : first):
```
:w          → Save (write)
:q          → Quit (fails if unsaved changes)
:wq         → Save and quit
:q!         → Quit WITHOUT saving (force)
:wq!        → Force save and quit
:x          → Save and quit (same as :wq)
```

> 💡 **If you're stuck in vim:** Press `Esc` multiple times, then type `:q!` and press Enter. This exits without saving.

**Real Life Example 🌍**
> vim is like driving a manual car — harder to learn than automatic (nano), but once you know it, you're faster and it works everywhere. Every server has vim. nano is not always installed. Learn both.

---

# 📝 SECTION 11 — Bash Scripting (Automate Everything!)

## What Is a Script?

A script is a file containing multiple commands that run one after another. Instead of typing 20 commands manually, put them in a script and run it once.

**Real Life Example 🌍**
> Every morning you check the weather, read news headlines, and make a to-do list. Instead of doing each step manually, imagine a robot that does all three for you in 2 seconds. That's a Bash script — your personal automation robot.

---

## Your First Script

```bash
# Create the script file
nano hello.sh

# Type this inside:
#!/bin/bash
echo "Hello, Akshat!"
echo "Today's date is: $(date)"
echo "You are logged in as: $(whoami)"

# Save (Ctrl+O, Enter, Ctrl+X)

# Make it executable
chmod +x hello.sh

# Run it!
./hello.sh
```

**Output:**
```
Hello, Akshat!
Today's date is: Mon Jan 15 10:45:00 IST 2024
You are logged in as: akshat
```

---

## The Shebang Line — `#!/bin/bash`

Every script starts with this line. It tells the system "use bash to run this file."

```bash
#!/bin/bash         # Use bash
#!/bin/sh           # Use sh (simpler, more compatible)
#!/usr/bin/python3  # Use Python 3
#!/usr/bin/perl     # Use Perl
```

---

## Variables

```bash
#!/bin/bash

# Assign a variable (NO spaces around =)
name="Akshat"
age=22
target_ip="192.168.1.1"

# Use a variable ($ prefix)
echo "Name: $name"
echo "Age: $age"
echo "Scanning: $target_ip"

# Command output as variable
current_user=$(whoami)
today=$(date +%Y-%m-%d)
echo "Running as: $current_user"
echo "Date: $today"

# Read input from user
echo "Enter your name: "
read username
echo "Hello, $username!"
```

---

## If Statements — Making Decisions

```bash
#!/bin/bash

# Basic if-else
age=18
if [ $age -ge 18 ]; then
    echo "You can vote!"
else
    echo "Too young to vote."
fi

# Check if file exists
file="/etc/passwd"
if [ -f "$file" ]; then
    echo "$file exists!"
else
    echo "$file does not exist."
fi

# Check if directory exists
if [ -d "/home/akshat" ]; then
    echo "Home directory found!"
fi

# String comparison
name="akshat"
if [ "$name" = "akshat" ]; then
    echo "Hello, Akshat!"
elif [ "$name" = "root" ]; then
    echo "Hello, Root!"
else
    echo "Unknown user."
fi
```

**Comparison operators:**
```bash
# Numbers
-eq     Equal to              [ $a -eq $b ]
-ne     Not equal to          [ $a -ne $b ]
-lt     Less than             [ $a -lt $b ]
-le     Less than or equal    [ $a -le $b ]
-gt     Greater than          [ $a -gt $b ]
-ge     Greater than or equal [ $a -ge $b ]

# Strings
=       Equal                 [ "$a" = "$b" ]
!=      Not equal             [ "$a" != "$b" ]
-z      Is empty              [ -z "$a" ]
-n      Is not empty          [ -n "$a" ]

# Files
-f      Is a file             [ -f "$path" ]
-d      Is a directory        [ -d "$path" ]
-e      Exists (file or dir)  [ -e "$path" ]
-r      Is readable           [ -r "$path" ]
-w      Is writable           [ -w "$path" ]
-x      Is executable         [ -x "$path" ]
-s      File is not empty     [ -s "$path" ]
```

---

## Loops

```bash
#!/bin/bash

# For loop — iterate over a list
for ip in 192.168.1.1 192.168.1.2 192.168.1.3; do
    echo "Pinging $ip..."
    ping -c 1 $ip > /dev/null && echo "$ip is UP" || echo "$ip is DOWN"
done

# For loop — iterate over a range
for i in {1..10}; do
    echo "Number: $i"
done

# For loop — files in a directory
for logfile in /var/log/*.log; do
    echo "Checking: $logfile"
    wc -l "$logfile"
done

# While loop
counter=1
while [ $counter -le 5 ]; do
    echo "Count: $counter"
    ((counter++))
done

# While loop — reading file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < /etc/passwd
```

---

## Functions

```bash
#!/bin/bash

# Define a function
check_port() {
    local host=$1
    local port=$2
    nc -zv $host $port 2>&1 && echo "Port $port OPEN" || echo "Port $port CLOSED"
}

# Call the function
check_port 192.168.1.1 22
check_port 192.168.1.1 80
check_port 192.168.1.1 443
```

---

## Real Security Scripts You'll Actually Use

### Script 1: Check Failed Logins

```bash
#!/bin/bash
# check_failed_logins.sh — Find brute force attempts

echo "=== Failed Login Attempts ==="
echo "Count: $(grep 'Failed password' /var/log/auth.log | wc -l)"
echo ""
echo "=== Top Attacking IPs ==="
grep "Failed password" /var/log/auth.log \
    | grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' \
    | sort | uniq -c | sort -rn | head -10
echo ""
echo "=== Targeted Usernames ==="
grep "Failed password" /var/log/auth.log \
    | grep -oP "for \K\S+" \
    | sort | uniq -c | sort -rn | head -10
```

### Script 2: Port Scanner (Simple)

```bash
#!/bin/bash
# simple_portscan.sh — Scan common ports on a host

target=$1

if [ -z "$target" ]; then
    echo "Usage: ./simple_portscan.sh <IP>"
    exit 1
fi

echo "Scanning $target..."
for port in 21 22 23 25 53 80 110 143 443 445 3306 3389 8080; do
    (echo >/dev/tcp/$target/$port) 2>/dev/null \
        && echo "  Port $port: OPEN" \
        || echo "  Port $port: closed"
done
```

### Script 3: System Info Report

```bash
#!/bin/bash
# sysinfo.sh — Quick system overview for investigation

echo "=============================="
echo "  SYSTEM INVESTIGATION REPORT"
echo "  Generated: $(date)"
echo "=============================="

echo -e "\n[*] CURRENT USERS LOGGED IN:"
who

echo -e "\n[*] RECENT LOGINS:"
last | head -10

echo -e "\n[*] LISTENING PORTS:"
ss -tlnp

echo -e "\n[*] ESTABLISHED CONNECTIONS:"
ss -tnp state established

echo -e "\n[*] RUNNING PROCESSES (suspicious check):"
ps aux | grep -v "^\[" | awk '{print $1, $11}' | sort -u

echo -e "\n[*] CRONTAB FOR ROOT:"
sudo crontab -l 2>/dev/null

echo -e "\n[*] SUID FILES:"
find / -perm -u=s -type f 2>/dev/null

echo -e "\n[*] RECENTLY MODIFIED FILES IN /etc:"
find /etc -mtime -1 2>/dev/null

echo -e "\n=============================="
echo "  END OF REPORT"
echo "=============================="
```

---

# 📦 SECTION 12 — Package Management (Installing Tools)

## APT — Debian/Ubuntu Package Manager

```bash
# Update package list (do this FIRST before installing anything)
sudo apt update

# Upgrade all installed packages
sudo apt upgrade

# Update and upgrade together
sudo apt update && sudo apt upgrade -y

# Install a package
sudo apt install nmap
sudo apt install wireshark
sudo apt install git python3 curl wget

# Install multiple packages
sudo apt install nmap netcat curl wget git vim -y

# Remove a package
sudo apt remove nmap

# Remove package AND its config files
sudo apt purge nmap

# Remove unnecessary packages
sudo apt autoremove

# Search for a package
apt search nmap
apt search "network scanner"

# Get info about a package
apt show nmap

# List installed packages
dpkg -l
dpkg -l | grep nmap         # Is nmap installed?

# Find which package owns a file
dpkg -S /usr/bin/nmap
```

## Installing From Other Sources

```bash
# Install .deb file directly
sudo dpkg -i package.deb
sudo apt install -f            # Fix any dependency issues after dpkg

# Install from GitHub (common for security tools)
git clone https://github.com/author/tool.git
cd tool/
chmod +x install.sh
./install.sh

# Install Python tools with pip
pip3 install requests
pip3 install scapy

# Snap packages (universal Linux packages)
sudo snap install code         # VS Code
```

## The Most Important Security Tools to Install

```bash
sudo apt update
sudo apt install -y \
    nmap \           # Port scanner
    wireshark \      # Packet analyser
    netcat-openbsd \ # Network swiss army knife
    curl wget \      # Download files
    git \            # Version control
    python3 python3-pip \ # Python
    john \           # Password cracker
    hydra \          # Network login brute-forcer
    dirb \           # Web directory scanner
    nikto \          # Web vulnerability scanner
    sqlmap \         # SQL injection tool
    hashcat \        # GPU hash cracker
    binwalk \        # Firmware analysis
    foremost \       # File carving
    exiftool \       # Metadata viewer
    tcpdump          # Command-line packet capture
```

---

# 📋 SECTION 13 — Log Files (The SOC Analyst's Bible)

## Where Logs Live

```
/var/log/
├── auth.log          ← Login attempts, sudo, SSH — CHECK FIRST
├── syslog            ← General system messages
├── kern.log          ← Kernel messages (hardware, drivers)
├── dmesg             ← Boot messages, hardware detection
├── dpkg.log          ← Software installation history
├── apt/history.log   ← apt install/remove history
├── cron.log          ← Scheduled task execution
├── mail.log          ← Email server
├── ufw.log           ← Firewall (Ubuntu Firewall) logs
├── faillog           ← Failed logins
├── wtmp              ← Login/logout history (binary — read with 'last')
├── btmp              ← Failed login history (binary — read with 'lastb')
├── lastlog           ← Last login per user (read with 'lastlog')
├── apache2/
│   ├── access.log    ← Every HTTP request to web server
│   └── error.log     ← Web server errors
├── nginx/
│   ├── access.log    ← Nginx access log
│   └── error.log     ← Nginx errors
└── mysql/
    └── error.log     ← Database errors
```

---

## Reading the Auth Log — Your Most Used File

```bash
sudo cat /var/log/auth.log
sudo tail -f /var/log/auth.log          # Live follow
sudo grep "Failed" /var/log/auth.log    # Failed attempts only
sudo less /var/log/auth.log             # Scroll through
```

**Understanding auth.log entries:**

```
Jan 15 10:23:45 server sshd[1234]: Failed password for akshat from 192.168.1.50 port 54321 ssh2
│           │    │     │           │                  │       │                   │
│           │    │     │           │                  │       │                   └── Connection details
│           │    │     │           │                  │       └──── Source IP of the attacker
│           │    │     │           │                  └──── Which user they tried
│           │    │     │           └── What happened (Failed password)
│           │    │     └── Process ID of SSH daemon
│           │    └── Which service (sshd = SSH daemon)
│           └── Hostname of your server
└── Timestamp
```

**Important auth.log events to know:**

```bash
# Successful SSH login
grep "Accepted" /var/log/auth.log
# Jan 15 09:00:01 server sshd[1234]: Accepted password for akshat from 10.0.0.1 port 52341 ssh2

# Failed SSH login (brute force!)
grep "Failed password" /var/log/auth.log
# Jan 15 10:23:45 server sshd[1234]: Failed password for root from 1.2.3.4 port 54321 ssh2

# Invalid user (username doesn't exist)
grep "Invalid user" /var/log/auth.log
# Jan 15 10:23:50 server sshd[1234]: Invalid user admin from 1.2.3.4 port 54322

# Sudo usage (who ran root commands?)
grep "sudo:" /var/log/auth.log
# Jan 15 11:00:00 server sudo: akshat : TTY=pts/0 ; PWD=/home/akshat ; USER=root ; COMMAND=/bin/bash

# New user created (possible backdoor!)
grep "useradd\|new user" /var/log/auth.log

# Root login (should almost never happen!)
grep "session opened for user root" /var/log/auth.log
```

---

## Reading Web Server Logs — Apache/Nginx

```bash
sudo tail -f /var/log/apache2/access.log
sudo tail -f /var/log/nginx/access.log
```

**Apache access.log format:**
```
192.168.1.50 - akshat [15/Jan/2024:10:30:00 +0530] "GET /admin/login.php HTTP/1.1" 200 1024 "-" "Mozilla/5.0..."
│              │       │                            │   │   │                    │   │    │
│              │       │                            │   │   │                    │   │    └── User agent (browser)
│              │       │                            │   │   │                    │   └── Referrer
│              │       │                            │   │   │                    └── Response size in bytes
│              │       │                            │   │   └── HTTP status code
│              │       │                            │   └── HTTP method (GET/POST)
│              │       │                            └── Request
│              │       └── Timestamp
│              └── Authenticated user (- if none)
└── Client IP
```

**Security analysis of web logs:**

```bash
# Find SQL injection attempts
grep -E "union|select|insert|drop|delete|exec|script" /var/log/apache2/access.log -i

# Find directory traversal attempts
grep "../" /var/log/apache2/access.log

# Find 404 errors (scanner probing for files)
grep " 404 " /var/log/apache2/access.log | head -50

# Find 500 errors (server errors — possible exploit attempts)
grep " 500 " /var/log/apache2/access.log

# Top attacking IPs
awk '{print $1}' /var/log/apache2/access.log | sort | uniq -c | sort -rn | head -10

# Common vulnerability scanner user agents
grep -i "nikto\|sqlmap\|nmap\|masscan\|zgrab" /var/log/apache2/access.log
```

---

## `journalctl` — systemd Journal Logs

Modern Linux uses systemd which has its own log system. `journalctl` reads it.

```bash
journalctl                          # All logs (HUGE — use filters!)
journalctl -f                       # Live follow (like tail -f for everything)
journalctl -n 50                    # Last 50 lines
journalctl -u nginx                 # Logs for nginx service only
journalctl -u ssh                   # SSH service logs
journalctl --since "1 hour ago"     # Last hour
journalctl --since "2024-01-15"     # Since specific date
journalctl --since "09:00" --until "10:00"  # Time range
journalctl -p err                   # Only errors
journalctl -p warning               # Warnings and above
journalctl -k                       # Kernel messages only
journalctl _UID=1001                # Logs from specific user ID
journalctl -b                       # This boot only
journalctl -b -1                    # Previous boot (for crash analysis!)
```

---

# 🔧 SECTION 14 — Power User Tricks

## Pipes — Chain Commands Together

The pipe `|` takes output of one command and sends it as input to the next. This is one of Linux's MOST powerful features.

```bash
# Basic concept:
command1 | command2 | command3

# Examples:
cat /etc/passwd | grep "bash"       # Show only users with bash shell
ps aux | grep nginx                 # Find nginx process
ls -la | grep ".txt"                # List only .txt files
cat access.log | grep "404" | wc -l # Count 404 errors

# Real security examples:
grep "Failed password" /var/log/auth.log | \
    grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' | \
    sort | \
    uniq -c | \
    sort -rn | \
    head -10
# This: finds failed passwords → extracts IPs → sorts them → 
#       counts unique → sorts by count → shows top 10 attacking IPs
```

**Breaking it down:**
```
grep "Failed password" /var/log/auth.log    → Get all failed login lines
grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+'  → Extract just the IP addresses
sort                                         → Sort alphabetically
uniq -c                                      → Count each unique IP
sort -rn                                     → Sort by count (reverse/numeric)
head -10                                     → Show top 10
```

---

## Redirection — Sending Output to Files

```bash
# > redirect output TO a file (overwrites!)
echo "Hello" > file.txt        # Write "Hello" to file (overwrite)
ps aux > processes.txt         # Save process list to file
ls -la > listing.txt           # Save directory listing

# >> append to file (doesn't overwrite)
echo "Line 1" > file.txt       # Creates file with "Line 1"
echo "Line 2" >> file.txt      # Adds "Line 2" to file (keeps Line 1)
date >> logfile.txt            # Append current date to log

# < redirect input FROM a file
sort < file.txt                # Sort contents of file
wc -l < /etc/passwd            # Count lines from passwd

# 2> redirect error messages
find / -name "passwd" 2>/dev/null    # Hide error messages
command > output.txt 2>&1           # Save BOTH normal AND error output to file
command > output.txt 2> errors.txt  # Save output and errors to separate files

# Discard output entirely
command > /dev/null              # Throw away normal output
command > /dev/null 2>&1         # Throw away EVERYTHING (silence a command)
```

---

## `sort`, `uniq`, `cut`, `awk` — Data Processing

These four commands let you process text data like a pro.

### `sort` — Sort Lines

```bash
sort file.txt               # Sort alphabetically
sort -r file.txt            # Reverse sort
sort -n file.txt            # Sort numerically (1, 2, 10 not 1, 10, 2)
sort -k2 file.txt           # Sort by 2nd field (column)
sort -t: -k3 -n /etc/passwd # Sort passwd by UID (field 3, using : separator)
```

### `uniq` — Remove Duplicate Lines (Must be Sorted First!)

```bash
sort file.txt | uniq        # Remove duplicate lines
sort file.txt | uniq -c     # Count occurrences of each line
sort file.txt | uniq -d     # Show only DUPLICATE lines
sort file.txt | uniq -u     # Show only UNIQUE lines (no duplicates)
```

### `cut` — Extract Fields/Columns

```bash
cut -d: -f1 /etc/passwd          # Get field 1, delimiter is :  (just usernames!)
cut -d: -f1,3 /etc/passwd        # Get fields 1 and 3 (username and UID)
cut -d, -f2 data.csv             # Get 2nd field from CSV
cut -c1-10 file.txt              # Get first 10 characters of each line
```

### `awk` — Powerful Text Processing

```bash
awk '{print $1}' file.txt         # Print first column (space-separated)
awk '{print $1, $3}' file.txt     # Print columns 1 and 3
awk -F: '{print $1}' /etc/passwd  # Print column 1 (: delimited) — usernames
awk -F: '{print $1, $3}' /etc/passwd  # Usernames and UIDs
awk '{print NR, $0}' file.txt     # Add line numbers
awk 'NR>5' file.txt               # Skip first 5 lines
awk 'NR>=5 && NR<=10' file.txt    # Lines 5 to 10
awk '{sum+=$3} END {print sum}' file.txt  # Sum the 3rd column
awk -F: '($3==0){print $1}' /etc/passwd  # Find UID 0 accounts!
```

---

## `tee` — Output to Screen AND File

```bash
command | tee output.txt         # Show on screen AND save to file
command | tee -a output.txt      # Append to file
ps aux | tee processes.txt | grep nginx   # Save all, also filter for nginx
```

---

## `xargs` — Pass Output as Arguments

```bash
# Basic use
echo "file1.txt file2.txt" | xargs rm    # Delete multiple files
cat urls.txt | xargs curl -O             # Download each URL in file
find /tmp -name "*.log" | xargs rm       # Find and delete log files

# Security use
cat ips.txt | xargs -I{} ping -c 1 {}   # Ping each IP in file
```

---

## History and Shortcuts

```bash
history                     # Show all previous commands
history | grep ssh          # Search command history
!!                          # Repeat last command
!ssh                        # Repeat last command starting with "ssh"
!123                        # Repeat command number 123
Ctrl + R                    # Search history interactively (type to search)
Ctrl + C                    # Cancel current command
Ctrl + D                    # Logout / close terminal
Ctrl + L                    # Clear screen (same as 'clear')
Ctrl + A                    # Jump to start of command
Ctrl + E                    # Jump to end of command
Ctrl + W                    # Delete last word
Tab                         # Autocomplete (press twice for options)
```

**The `.bash_history` file:**
```bash
cat ~/.bash_history         # See your command history
cat ~/.bash_history | grep "ssh"     # Find SSH commands you've run
cat ~/.bash_history | grep "sudo"    # Find root commands you've run
```

> 🔴 **Security:** During incident response, ALWAYS read the attacker's `.bash_history` — it reveals every command they ran after compromising the system!

---

## `tmux` — Terminal Multiplexer (Work Faster!)

tmux lets you have multiple terminals in one window and keep sessions running even if you disconnect.

```bash
sudo apt install tmux       # Install
tmux                        # Start new session
tmux new -s mysession       # Start session with name

# Inside tmux (prefix key is Ctrl+B):
Ctrl+B then %       → Split vertically (side by side)
Ctrl+B then "       → Split horizontally (top and bottom)
Ctrl+B then arrow   → Switch between panes
Ctrl+B then z       → Zoom in/out on current pane
Ctrl+B then d       → Detach (session keeps running!)
Ctrl+B then c       → Create new window
Ctrl+B then n/p     → Next/previous window
Ctrl+B then [       → Scroll mode (use arrow keys, q to quit)

# After detaching, reattach:
tmux attach             # Reattach to session
tmux attach -t mysession # Reattach to named session
tmux ls                 # List sessions
```

> 💡 **SOC use:** Start a `tail -f` or long-running scan in tmux. Detach and go do something else. Come back and your scan is still running! Essential for long investigations.

---

# 🎯 SECTION 15 — Linux for Security — Quick Reference

## First 10 Commands in Any Investigation

```bash
# 1. Who is currently logged in?
who
w

# 2. Recent login history
last | head -20

# 3. What processes are running?
ps aux

# 4. What ports are listening?
ss -tlnp

# 5. What outbound connections exist? (C2 communication?)
ss -tnp state established

# 6. Check auth logs for recent activity
sudo tail -100 /var/log/auth.log

# 7. Check crontab for persistence
crontab -l
sudo crontab -l
cat /etc/crontab

# 8. Check for suspicious users (UID 0 besides root?)
awk -F: '($3==0){print}' /etc/passwd

# 9. Check recently modified files
find /etc -mtime -1 2>/dev/null
find /tmp -type f 2>/dev/null

# 10. Check SUID files (privilege escalation paths)
find / -perm -u=s -type f 2>/dev/null
```

---

## Privilege Escalation Checklist (For Your Pentest Labs)

```bash
# 1. What can you run with sudo?
sudo -l

# 2. What groups are you in?
id

# 3. SUID files (might be exploitable)
find / -perm -u=s -type f 2>/dev/null

# 4. World-writable files
find / -perm -o+w -type f 2>/dev/null

# 5. Writable crontab entries?
cat /etc/crontab
ls -la /etc/cron*

# 6. Weak file permissions on sensitive files?
ls -la /etc/passwd /etc/shadow /etc/sudoers

# 7. Running services as root?
ps aux | grep root

# 8. Kernel version (old = exploitable?)
uname -a
uname -r

# 9. Check for writable scripts called by root cron
cat /etc/crontab | grep -v "^#"
```

---

## Essential One-Liners for SOC Work

```bash
# Count failed SSH logins by IP
grep "Failed password" /var/log/auth.log | grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' | sort | uniq -c | sort -rn

# Find all users with bash shell (can log in)
grep "/bin/bash" /etc/passwd | cut -d: -f1

# Show disk usage, sorted by size
du -sh /* 2>/dev/null | sort -rh | head -20

# Find large files (over 100MB)
find / -size +100M -type f 2>/dev/null

# Monitor new processes in real-time
watch -n 1 'ps aux --sort=-%cpu | head -20'

# See all open files by a process (replace 1234 with PID)
lsof -p 1234

# Find which process is using a port
lsof -i :80
fuser 80/tcp

# Decode base64 (attackers use this to hide commands)
echo "SGVsbG8gQWtzaGF0IQ==" | base64 -d
# Output: Hello Akshat!

# Find recently created files (last 10 minutes)
find / -newer /tmp/timestamp -type f 2>/dev/null
# (first run: touch /tmp/timestamp, then run the find after 10 min)
```

---

# 📌 FINAL CHEAT SHEET — Stick This on Your Wall!

```
╔═══════════════════════════════════════════════════════════════╗
║              LINUX QUICK REFERENCE — AKSHAT                   ║
╠═══════════════════════════════════════════════════════════════╣
║  NAVIGATION                                                   ║
║  pwd=where am I  ls -la=list all  cd ..=go up  cd ~=home    ║
║  find / -name x  locate x  which cmd  grep x file           ║
╠═══════════════════════════════════════════════════════════════╣
║  FILES                                                        ║
║  cat=show  less=scroll  head/tail=first/last  nano=edit      ║
║  cp=copy  mv=move/rename  rm=delete  mkdir=make dir         ║
║  touch=create empty  chmod=permissions  chown=ownership      ║
╠═══════════════════════════════════════════════════════════════╣
║  PERMISSIONS                                                  ║
║  r=4 w=2 x=1  |  755=rwxr-xr-x  644=rw-r--r--  600=rw---- ║
║  SUID=4000  SGID=2000  Sticky=1000                          ║
╠═══════════════════════════════════════════════════════════════╣
║  PROCESSES                                                    ║
║  ps aux=all processes  top=live  kill PID  kill -9 PID      ║
║  systemctl start/stop/status service  crontab -l            ║
╠═══════════════════════════════════════════════════════════════╣
║  NETWORKING                                                   ║
║  ip addr  ss -tlnp  ping  traceroute  netstat -an           ║
║  ssh user@ip  scp file user@ip:/path  curl -I url           ║
╠═══════════════════════════════════════════════════════════════╣
║  GREP POWER                                                   ║
║  grep -i=case insensitive  -r=recursive  -v=invert          ║
║  -n=line numbers  -A/-B=lines after/before  -c=count        ║
╠═══════════════════════════════════════════════════════════════╣
║  PIPES & REDIRECTION                                          ║
║  cmd1 | cmd2=pipe  >=write  >>=append  2>/dev/null=hide err ║
║  sort | uniq -c | sort -rn = count unique occurrences        ║
╠═══════════════════════════════════════════════════════════════╣
║  KEY LOG FILES                                                ║
║  /var/log/auth.log = LOGIN ATTEMPTS (check first!)          ║
║  /var/log/syslog = system events                            ║
║  /var/log/apache2/access.log = web requests                 ║
╠═══════════════════════════════════════════════════════════════╣
║  INVESTIGATION QUICK CHECKS                                   ║
║  who | last | ps aux | ss -tlnp | sudo tail auth.log        ║
║  crontab -l | awk -F: '($3==0)' /etc/passwd                 ║
║  find / -perm -u=s -type f 2>/dev/null   (SUID check)       ║
╚═══════════════════════════════════════════════════════════════╝
```

---

> **Day 3 ✅ Done!**
>
> **Practice now:** Open your Kali Linux VM and run every command in this file. Don't just read — TYPE the commands. Your fingers need to learn this, not just your brain.
>
> **Then:** Go to OverTheWire Bandit (overthewire.org/wargames/bandit) — Levels 0 to 15 use exactly what you learned today. Each level is a puzzle solved using Linux commands.
>
> **Day 3/60 complete. You're building real skills now, Akshat.** 💪
