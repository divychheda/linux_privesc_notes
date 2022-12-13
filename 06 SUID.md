## Privesc using suid bit binaries
- Find all files that have suid bits
```
find / -perm -u=s -type f 2>/dev/null
```
or
```
find / -type f -perm -4000 -ls 2>/dev/null
```
Note: 4000 for suid, 2000 for sgid (6000 for both bits set)
- Cross check that list with, the list here and look for matches : https://gtfobins.github.io/#+suid
- Try to eploit using them.

## Via Shared Object Injection
- look for file with suid bit set
- strace it and look for access/open calls with 'no such file or directory' error message
```
strace <file> 2>&1 | grep -i -E "open|access|no such file"
```
- then check all the no such file errors and see where we have read + write permissions and put malicious files in that place with the same name so that they get executed.
- for eg, TCM saw a libcalc.so that didn't exist so he wrote following C code:
![image](https://user-images.githubusercontent.com/64409788/207356746-50834369-dfab-4e64-8d65-5a610354ef1c.png)
- compiled it using: `gcc -shared -fPIC -o <proper dir>/libcalc.so libcalc.c`
- execute the original binary with suid set and get elevated shell

## Escalation via Binary Symlinks
- based on this cve: https://legalhackers.com/advisories/Nginx-Exploit-Deb-Root-PrivEsc-CVE-2016-1247.html
- if nginx ver `1.6.2` or lesser and suid bit is set on `sudo`, you can execute the exploit.
- Install this: https://github.com/xl7dev/Exploit/blob/master/Nginx/nginxed-root.sh
- run and point to error log:
```
./nginxed-root.sh /var/log/nginx/error.log
```
- wait for nginx to be restarted / rotated
- and done.

## Environment variables
- if in all the suid bins, some bin is calling other binaries using relative path.
- check using: `strings bin-name`
- if relative path being used, one can make a malicious bin with the same name as the one with relative path in sany dir like `tmp`
- now, edit the PATH env var to have that dir with most priority:
```
export PATH=/tmp:$PATH
```
- now on running the org suid bin, we execute the malicious code.

<br><br>
- if on the other hand absolute path is used in some suid bin (strings output)
- one can make a shell function with that name
![image](https://user-images.githubusercontent.com/64409788/207397900-b619e1e2-10f6-4e86-a61f-1788ec45f5e9.png)
