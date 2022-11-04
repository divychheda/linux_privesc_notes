## Stored Passwords
- First run the `history` command or `cat ~/.bash_history` to look for typed passwords. (eg mysql login)
- Linpeas can also hunt passwords
- Not always clear cut, need to look in front of us sometimes like in cfg or ovpn file etc. These files can ref to other files that may have passwd.

## Weak file permissions
- check if you have read access to /etc/shadow (if you have modify access, can do a lot of things)
- Copy contents of /ect/passwd and /etc/shadow file into your machine
- run `unshadow` : combines passwd and shadow files
```
unshadow password-file shadow-file
```
- take only the lines with hashes (exclude ones with *)
- use hashcat to crach hashes
- go to hashcat and search for type of hash usingstarting chars (eg $6$), note the code
```
hashcat -m <code> <cred-file> <wordlist-file> -o
```
- use `su <username>` ro privesc

## Esc via SSH Keys
- Hunt ssh keys: `find / -name "authtorized_keys" 2> /dev/null` or `find / -name id_rsa 2>/dev/null`
- if found in backups or whatever, copy it to your machine, `chmod 600 id_rsa` and try too ssh to root from it.

