# Linux Command Cheat Sheet

A practical reference for day-to-day Linux use and home-server admin (written against Ubuntu 24.04, but almost everything works on any modern distro).

> **Tip:** Every command has a manual. `man <cmd>` for the full thing, `<cmd> --help` for a quick summary, `tldr <cmd>` (after `sudo apt install tldr`) for examples.

## Contents

1. [Navigation](#1-navigation)
2. [Files & Directories](#2-files--directories)
3. [Viewing & Editing Files](#3-viewing--editing-files)
4. [Searching](#4-searching)
5. [Permissions & Ownership](#5-permissions--ownership)
6. [Users & Groups](#6-users--groups)
7. [Processes](#7-processes)
8. [System Info & Monitoring](#8-system-info--monitoring)
9. [Disks & Storage](#9-disks--storage)
10. [Packages (apt)](#10-packages-apt)
11. [Services (systemd)](#11-services-systemd)
12. [Logs](#12-logs)
13. [Networking](#13-networking)
14. [SSH & Remote Copy](#14-ssh--remote-copy)
15. [Firewall (ufw)](#15-firewall-ufw)
16. [Archives & Compression](#16-archives--compression)
17. [Text Processing & Pipes](#17-text-processing--pipes)
18. [Redirection & Shell Tricks](#18-redirection--shell-tricks)
19. [Scheduling (cron)](#19-scheduling-cron)
20. [Keyboard Shortcuts](#20-keyboard-shortcuts)

---

## 1. Navigation

| Command | What it does |
|---|---|
| `pwd` | Print the current directory |
| `ls` | List files |
| `ls -la` | Long listing, including hidden (dot) files |
| `ls -lh` | Human-readable sizes (K, M, G) |
| `ls -lt` | Sort by modification time, newest first |
| `cd /path` | Change to an absolute path |
| `cd dir` | Change to a path relative to where you are |
| `cd ~` or `cd` | Go to your home directory |
| `cd -` | Go back to the previous directory |
| `cd ..` | Go up one level |
| `tree -L 2` | Show the directory tree two levels deep (`sudo apt install tree`) |

**Path shortcuts:** `~` = home, `.` = current dir, `..` = parent dir, `/` = filesystem root.

## 2. Files & Directories

| Command | What it does |
|---|---|
| `touch file.txt` | Create an empty file (or update its timestamp) |
| `mkdir dir` | Create a directory |
| `mkdir -p a/b/c` | Create nested directories, no error if they exist |
| `cp src dst` | Copy a file |
| `cp -r srcdir dstdir` | Copy a directory recursively |
| `cp -a src dst` | Copy and preserve permissions, owners, timestamps |
| `mv old new` | Move or rename |
| `rm file` | Delete a file (**no recycle bin**) |
| `rm -r dir` | Delete a directory and everything in it |
| `rm -i file` | Ask before each deletion |
| `rmdir dir` | Remove an *empty* directory |
| `ln -s target linkname` | Create a symbolic link |
| `file thing` | Tell you what type of file something is |
| `stat file` | Detailed metadata (size, perms, timestamps) |

> ⚠️ `rm -rf` never asks and can't be undone. Double-check the path, especially with `sudo` or variables.

## 3. Viewing & Editing Files

| Command | What it does |
|---|---|
| `cat file` | Print the whole file |
| `less file` | Scroll through a file (`q` quit, `/word` search, `n` next match) |
| `head -n 20 file` | First 20 lines |
| `tail -n 20 file` | Last 20 lines |
| `tail -f file` | Follow a file as it grows (great for logs) |
| `wc -l file` | Count lines |
| `diff a b` | Show differences between two files |
| `nano file` | Beginner-friendly editor (`Ctrl+O` save, `Ctrl+X` exit) |
| `vim file` | Powerful editor (`i` insert, `Esc` then `:wq` save & quit, `:q!` quit without saving) |
| `sudoedit /etc/file` | Safely edit a root-owned file with your editor |

## 4. Searching

| Command | What it does |
|---|---|
| `find . -name "*.log"` | Find files by name under the current dir |
| `find / -type d -name conf 2>/dev/null` | Find directories, hide permission errors |
| `find . -mtime -1` | Files modified in the last 24 hours |
| `find . -size +100M` | Files larger than 100 MB |
| `grep "text" file` | Lines containing "text" |
| `grep -i "text" file` | Case-insensitive |
| `grep -rn "text" dir/` | Recursive, with line numbers |
| `grep -v "text" file` | Lines that do **not** match |
| `which cmd` | Where a command's binary lives |
| `type cmd` | Whether something is an alias, builtin, or binary |
| `locate name` | Fast name search via index (`sudo apt install plocate`) |

## 5. Permissions & Ownership

```
-rwxr-xr--  1 alice devs  1234 Sep 30 file.sh
│└┬┘└┬┘└┬┘       │     │
│ │  │  └ others  │     └ group
│ │  └─── group   └────── owner (user)
│ └────── user (owner)
└──────── type: - file, d directory, l link
```

`r` = 4, `w` = 2, `x` = 1 → add them up per group: `755` = `rwxr-xr-x`, `644` = `rw-r--r--`, `600` = `rw-------`.

| Command | What it does |
|---|---|
| `chmod 755 script.sh` | Set permissions numerically |
| `chmod +x script.sh` | Make executable |
| `chmod u+w,g-w,o-r file` | Add/remove specific bits for user/group/other |
| `chmod -R 755 dir` | Apply recursively |
| `chown user file` | Change owner |
| `chown user:group file` | Change owner and group |
| `chown -R user:group dir` | Recursively |
| `sudo cmd` | Run a command as root |
| `sudo -i` | Open a root shell (use sparingly) |

## 6. Users & Groups

| Command | What it does |
|---|---|
| `whoami` | Your username |
| `id` | Your UID, GID, and groups |
| `groups user` | Groups a user belongs to |
| `sudo adduser name` | Create a user (interactive, Debian/Ubuntu) |
| `sudo usermod -aG group user` | Add a user to a group (**`-a` is important** — without it you replace their groups) |
| `sudo deluser name` | Delete a user |
| `passwd` | Change your password |
| `su - user` | Switch to another user |
| `who` / `w` | Who is logged in |
| `last` | Recent logins |

> Group changes apply at next login. Log out and back in, or run `newgrp group`.

## 7. Processes

| Command | What it does |
|---|---|
| `ps aux` | All running processes |
| `ps aux \| grep name` | Find a process |
| `pgrep -a name` | Find processes by name, show PIDs |
| `top` | Live process view (`q` quit, `M` sort by memory, `P` by CPU) |
| `htop` | Nicer `top` (`sudo apt install htop`) |
| `kill PID` | Politely ask a process to stop (SIGTERM) |
| `kill -9 PID` | Force-kill (SIGKILL) — last resort |
| `pkill name` | Kill by name |
| `cmd &` | Run in the background |
| `jobs` | List background jobs in this shell |
| `fg %1` / `bg %1` | Bring job 1 to foreground / resume it in background |
| `Ctrl+Z` | Suspend the foreground process |
| `nohup cmd &` | Keep running after you log out |
| `tmux` | Terminal multiplexer: sessions survive SSH disconnects (`Ctrl+B d` detach, `tmux attach` reattach) |

## 8. System Info & Monitoring

| Command | What it does |
|---|---|
| `uname -a` | Kernel and architecture |
| `cat /etc/os-release` | Distro name and version |
| `hostnamectl` | Hostname, OS, kernel, hardware |
| `uptime` | How long it's been up + load averages |
| `free -h` | RAM and swap usage |
| `nproc` | Number of CPU cores |
| `lscpu` | CPU details |
| `lsblk` | Block devices (disks and partitions) |
| `lsusb` / `lspci` | USB / PCI devices |
| `sensors` | Temperatures (`sudo apt install lm-sensors`) |
| `date` | Current date and time |
| `timedatectl` | Time zone and NTP sync status |
| `history` | Your command history (`!123` reruns entry 123) |

## 9. Disks & Storage

| Command | What it does |
|---|---|
| `df -h` | Free space per mounted filesystem |
| `du -sh dir` | Total size of a directory |
| `du -h --max-depth=1 \| sort -h` | What's using space here, sorted |
| `ncdu` | Interactive disk usage explorer (`sudo apt install ncdu`) |
| `lsblk -f` | Disks with filesystem type, label, UUID |
| `sudo blkid` | UUIDs of partitions (for `/etc/fstab`) |
| `sudo mount /dev/sdb1 /mnt` | Mount a partition |
| `sudo umount /mnt` | Unmount |
| `cat /etc/fstab` | Filesystems mounted at boot |
| `sudo mount -a` | Test fstab changes without rebooting |

> Always run `sudo mount -a` after editing `/etc/fstab`. A typo there can stop the machine from booting.

## 10. Packages (apt)

| Command | What it does |
|---|---|
| `sudo apt update` | Refresh the package list (doesn't install anything) |
| `sudo apt upgrade` | Install available updates |
| `sudo apt full-upgrade` | Upgrade, allowing package removals if needed |
| `sudo apt install pkg` | Install a package |
| `sudo apt remove pkg` | Uninstall (keep config) |
| `sudo apt purge pkg` | Uninstall and delete config |
| `sudo apt autoremove` | Remove unused dependencies |
| `apt search term` | Search packages |
| `apt show pkg` | Package details |
| `apt list --installed` | Installed packages |
| `apt list --upgradable` | Packages with updates available |
| `dpkg -L pkg` | Files installed by a package |
| `snap list` | Installed snaps (Ubuntu) |

## 11. Services (systemd)

| Command | What it does |
|---|---|
| `systemctl status nginx` | Is it running? Recent log lines |
| `sudo systemctl start nginx` | Start now |
| `sudo systemctl stop nginx` | Stop now |
| `sudo systemctl restart nginx` | Stop then start |
| `sudo systemctl reload nginx` | Reload config without full restart (if supported) |
| `sudo systemctl enable nginx` | Start at boot |
| `sudo systemctl enable --now nginx` | Start at boot **and** start now |
| `sudo systemctl disable nginx` | Don't start at boot |
| `systemctl list-units --type=service --state=running` | Running services |
| `systemctl --failed` | Services that failed |
| `sudo systemctl daemon-reload` | Re-read unit files after editing them |
| `sudo reboot` / `sudo poweroff` | Restart / shut down |

## 12. Logs

| Command | What it does |
|---|---|
| `journalctl -u ssh` | Logs for one service |
| `journalctl -u ssh -f` | Follow them live |
| `journalctl -b` | Logs since the last boot |
| `journalctl -b -1` | Logs from the previous boot (why did it crash?) |
| `journalctl -p err -b` | Only errors this boot |
| `journalctl --since "1 hour ago"` | Time-filtered |
| `sudo dmesg -T` | Kernel messages (hardware, drivers), readable timestamps |
| `sudo tail -f /var/log/syslog` | Classic system log |
| `sudo tail -f /var/log/auth.log` | Logins, sudo, SSH attempts |

## 13. Networking

| Command | What it does |
|---|---|
| `ip a` | Interfaces and IP addresses |
| `ip r` | Routing table (look for `default via` = your gateway) |
| `ping -c 4 1.1.1.1` | Test connectivity (4 packets) |
| `ping -c 4 google.com` | Test DNS + connectivity |
| `resolvectl status` | DNS servers in use |
| `ss -tulpn` | Listening ports and which process owns them (`sudo` for all names) |
| `curl -I https://example.com` | Fetch HTTP headers |
| `curl -O URL` / `wget URL` | Download a file |
| `traceroute host` / `mtr host` | Path to a host, hop by hop |
| `dig example.com` | DNS lookup (`sudo apt install dnsutils`) |
| `nmcli device wifi list` | Nearby Wi-Fi networks (NetworkManager) |
| `sudo netplan try` | Apply netplan config with auto-rollback if you lose connection |
| `sudo netplan apply` | Apply netplan config (`/etc/netplan/*.yaml`) |
| `hostname -I` | Just the IP addresses |

## 14. SSH & Remote Copy

| Command | What it does |
|---|---|
| `ssh user@host` | Log in to a remote machine |
| `ssh -p 2222 user@host` | Non-default port |
| `ssh-keygen -t ed25519` | Create a key pair |
| `ssh-copy-id user@host` | Install your public key on the server |
| `scp file user@host:/path/` | Copy a file to a remote machine |
| `scp user@host:/path/file .` | Copy from remote to here |
| `rsync -avh src/ user@host:/dst/` | Efficient sync (only changed bits; trailing `/` matters) |
| `rsync -avh --delete src/ dst/` | Mirror exactly (deletes extras in dst — careful) |
| `ssh -L 8080:localhost:80 user@host` | Local port forward: `localhost:8080` → host's port 80 |

Server config lives in `/etc/ssh/sshd_config` (and `/etc/ssh/sshd_config.d/*.conf`). After changes:

```bash
sudo sshd -t                    # test config for errors first
sudo systemctl restart ssh      # then apply
```

Keep your current session open while testing a new one, so a bad config can't lock you out.

`~/.ssh/config` saves typing:

```
Host myserver
    HostName 192.168.1.50
    User kvarma
    Port 22
```

→ now just `ssh myserver`.

## 15. Firewall (ufw)

| Command | What it does |
|---|---|
| `sudo ufw status verbose` | Current rules and defaults |
| `sudo ufw allow OpenSSH` | Allow SSH (**do this before enabling!**) |
| `sudo ufw allow 80/tcp` | Allow a port |
| `sudo ufw allow from 192.168.1.0/24 to any port 22` | Allow only from your LAN |
| `sudo ufw deny 23` | Block a port |
| `sudo ufw status numbered` | Rules with numbers |
| `sudo ufw delete 3` | Delete rule #3 |
| `sudo ufw enable` / `disable` | Turn firewall on / off |

## 16. Archives & Compression

| Command | What it does |
|---|---|
| `tar -czvf out.tar.gz dir/` | **C**reate g**z**ipped archive, **v**erbose, **f**ile name |
| `tar -xzvf out.tar.gz` | E**x**tract it |
| `tar -xzvf out.tar.gz -C /dest` | Extract to a specific directory |
| `tar -tzvf out.tar.gz` | Lis**t** contents without extracting |
| `zip -r out.zip dir/` | Create a zip |
| `unzip out.zip` | Extract a zip |
| `gzip file` / `gunzip file.gz` | Compress / decompress a single file |

## 17. Text Processing & Pipes

`|` sends one command's output into the next command's input.

| Command | What it does |
|---|---|
| `sort file` | Sort lines (`-n` numeric, `-r` reverse, `-h` human sizes) |
| `uniq -c` | Count repeated adjacent lines (usually after `sort`) |
| `cut -d: -f1 /etc/passwd` | Field 1 of colon-separated lines (all usernames) |
| `awk '{print $1}' file` | Print the first whitespace-separated column |
| `sed 's/old/new/g' file` | Replace text (prints result) |
| `sed -i 's/old/new/g' file` | Replace text in place |
| `tr 'a-z' 'A-Z'` | Translate characters (here: uppercase) |
| `xargs` | Turn input lines into arguments: `find . -name "*.tmp" \| xargs rm` |
| `tee file` | Write to a file **and** the screen |

Example — top 5 IPs hitting SSH:

```bash
sudo grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn | head -5
```

## 18. Redirection & Shell Tricks

| Syntax | What it does |
|---|---|
| `cmd > file` | Write output to file (overwrite) |
| `cmd >> file` | Append to file |
| `cmd 2> errors.txt` | Redirect errors only |
| `cmd > out.txt 2>&1` | Output and errors to the same file |
| `cmd 2>/dev/null` | Discard errors |
| `cmd1 && cmd2` | Run cmd2 only if cmd1 succeeded |
| `cmd1 \|\| cmd2` | Run cmd2 only if cmd1 failed |
| `cmd1 ; cmd2` | Run both regardless |
| `$(cmd)` | Insert a command's output: `echo "Today is $(date +%F)"` |
| `!!` | Repeat last command (`sudo !!` = last command with sudo) |
| `alias ll='ls -lah'` | Shortcut (put in `~/.bashrc` to keep it) |
| `source ~/.bashrc` | Reload shell config |
| `export VAR=value` | Set an environment variable |
| `echo $PATH` | Where the shell looks for commands |

## 19. Scheduling (cron)

| Command | What it does |
|---|---|
| `crontab -e` | Edit your scheduled jobs |
| `crontab -l` | List them |
| `sudo crontab -e` | Root's jobs |

```
┌──── minute (0-59)
│ ┌──── hour (0-23)
│ │ ┌──── day of month (1-31)
│ │ │ ┌──── month (1-12)
│ │ │ │ ┌──── day of week (0-7, Sun=0 or 7)
│ │ │ │ │
0 3 * * * /home/kvarma/backup.sh >> /home/kvarma/backup.log 2>&1
```

→ runs every day at 03:00. Use [crontab.guru](https://crontab.guru) to check expressions. `*/15 * * * *` = every 15 minutes.

## 20. Keyboard Shortcuts

| Keys | What it does |
|---|---|
| `Tab` | Autocomplete (press twice to see options) |
| `Ctrl+C` | Cancel the running command |
| `Ctrl+D` | End input / log out |
| `Ctrl+L` | Clear the screen |
| `Ctrl+R` | Search command history (keep pressing to go further back) |
| `Ctrl+A` / `Ctrl+E` | Jump to start / end of line |
| `Ctrl+U` / `Ctrl+K` | Delete to start / end of line |
| `Ctrl+W` | Delete the previous word |
| `↑` / `↓` | Previous / next command |
| `Alt+.` | Insert the last argument of the previous command |
