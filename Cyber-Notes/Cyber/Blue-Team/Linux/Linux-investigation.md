# Linux Forensics / Investigation
## Securing binaries
- Trusted binaries should be in `/bin`, `/sbin`, and `/usr/bin`
	- Anything else should be a red flag
- `export PATH=/mnt/usb/bin:/mnt/usb/sbin`
- `export LD_LIBRARY_PATH=/mnt/usb/lib:/mnt/usb/lib64`
- `debsums`
	- `-e --config` config files
	- `-c --changed` only output changed files
- `which` where is the command/binary located?
	- `file` Check the binaries and ensure they are not scripts
### Alias
- `alias` check if any commands have been aliased
-  `alias >> aliases.txt && cat aliases.txt`
> A bad actor could theoretically alias **ANYTHING** to `rm -rf /`. This should realistically be the first thing you run. It would also be a good idea to use `>>` as mentioned for future investigation possibilites
- `~/.bashrc` often contains aliases. **logging in will reset all aliases defined in .bashrc**
- `unalias --all` is a quick and dirty **temporary** solution to aliases.
## Forensics
- `ls -l` mtime (modify timestamp)
- `ls -lc` ctime (change timestamp) metadeta/filename/permissions
- `ls -la` atime (access timestamp)
## Volatility
#### Extractors
- LiME (Linux Memory Extractor) creates .dmp
- fmem
- avml
- memdump
- coredump
    - process specific
#### Steps
- `python3 vol.py -f [file] banner` gets the uname of Linux
- Create symbol table (vol3) using dwarf2json if versions match or use https://isf-server.techanarchy.net/
	- Move isf (.json) to `volatility3/symbols` folder
#### Symbols
- Linux.pslist process list
- linux.sockstat network list
- linux.bash bash history
- linux.pstree process tree
- linux.lsof
- linux.vmayarascan find malware signatures
## User config
### Config files
- `/etc/passwd`
	- `cat /etc/passwd | cut -d: -f1,3 | grep ':0$'`
- `/etc/group`
### Groups
- `/etc/group`
- `groups [USER]`
- `getent group adm`
- `getent group 27`
## Process snooping
- `lsof`
	- `-i` Network connections
	- `-P` display port numbers
	- `-n` show ips instead of resolving hostnames
	- `-p` PID
### Network
- `osqueryi`
	- `SELECT pid, fd, socket, local_address, remote_address FROM process_open_sockets WHERE pid = 267490;`
- `netstat -natup`
	- `-a, --all` : Show both listening and non-listening sockets.
	- `-l, --listening`: Show only listening sockets.
	- `-t, --tcp`: Display TCP connections.
	- `-u, --udp`: Display UDP connections.
	- `-4, --inet`: Show only IPv4 connections.
	- `-6, --inet6`: Show only IPv6 connections.
	- `-n, --numeric`: Display numerical addresses instead of resolving host, port, or user names, which speeds up output generation.
	- `-p, --program`: Show the PID and name of the program to which each socket belongs (PID/Program Name). Root privileges are typically required to view this information.
-  `iptable`, `ufw`
	- Check the firewall
## Processes
- `ps aux`
	- `a` all users
	- `u` user oriented (user and start time)
	- `x` Include processes not attached to a terminal
- `ps -eFH`
	- `-e` select all processes
	- `-F` extra full mode
	- `-H` process hierarchy (forest)
- `pstree [PID]`
	- `-p` list PID
	- `-s` list child processes parents
- `top`
	- `-d [seconds]` refresh rate
	- `-c` display full command paths
	- `-u [user]` only show a specific user's processes
- `who` logged in users
## Log Analysis
### `grep`
- `grep [SEARCH TERM] file.txt`
### `wc`
- -c --bytes
- -m --chars
- -l --lines (newlines)
- -w --words
## File Snooping
### Shells
- `echo $SHELL`
- `cat /etc/shells`
- `chsh -s /usr/bin/zsh`
- `history`
- `last`/`lastb` find the last logins or bad logins
### Find
- `find / -type f -executable 2> /dev/null`
### Good files to check
- `~/.bash_history`
	- run `history` in terminal
- `cat /var/log/auth.log | grep useradd`
- `/etc/systemd/system`
- `/var/log/syslog`

## System stuff
### Systemd `systemctl`
- `/etc/systemd/system`
- *sysd* is a very legacy version of systemd
### Cron
 - `/var/spool/cron/crontabs/[username]`
- `/etc/crontab` main cron file
	- `/etc/cron*/`  Contains system cron jobs
## Other persistance
- `~/.config/autostart/`
- `/etc/init.d`
## Disks
- `df -hT`
	- View usage and types of disks
- `dd if=<source> of=<destination> bs=<block_size> status=progress`
	- `sudo dd if=/dev/sdb1 of=/path/to/disk_image.img bs=4M status=progress`
- `mount -o ro [device] [destination]`
	- Read only mount
- 