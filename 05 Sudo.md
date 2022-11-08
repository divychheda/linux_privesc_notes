## Shell Escaping
- `sudo -l` to see what commands you can run without password as root.
- search for those commands in https://gtfobins.github.io/ and look under `sudo` subheading
- eg, for vim: sudo vim -c ':!/bin/sh'

## Intended functionality
- Sometimes if cant find on gtfobins then google for intended functionality
- eg, for apache2 there is no way to get shell but to view restricted files like /etc/shadow

## LD_PRELOAD
- Follow the steps here : https://www.hackingarticles.in/linux-privilege-escalation-using-ld_preload/

## CVE-2019-14287
- If a user is restricted from using root, i.e, `user ALL=(ALL,!root) /bin/bash` appearing in `sudo -l`
- Then it may be vulnerable to this cve (check sudo version with `sudo -V`): https://www.exploit-db.com/exploits/47502

## CVE-2019-18634 (sudo buffer overflow)
- Works on specific versions of sudo when password feedback is enabled
- How to check for pwfeedback? just type any sudo command and see if asterisks appear during typing the passwords.
- check sudo version and try to exploit: https://github.com/saleemrashid/sudo-cve-2019-18634
