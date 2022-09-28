## System Enumeration
- To get kernel info:
`uname -a`, `cat /proc/version`, `cat /etc/issue` (for distro version)
- To get arch info: `lscpu`
- To list all running services: `ps aux`, `ps aux | grep root`

## User Enumeration
- To get username: `whoami`
- To get all ids: `id`
- To list stuff we can do as sudo: `sudo -l`
- Important files to check if we have access: `cat /etc/passwd` (list all users), `cat /etc/shadow` (their passwd hashes), `cat /etc/group` (list all groups)

## Network Enumeration
- Interface info: `ip a`
- Routing table: `route`, `ip route`
- Arp tables: `arp -a`, `ip neigh`
- Port and connections: `netstat -ano`

## Password Hunting
- To search for plaintext passwords: `grep --color=auto -rnw '/' -ie "PASSWORD"  --color=always 2>/dev/null | less`
- To search for password files: `locate password | less` (or anything similar)
- Hunt ssh keys: `find / -name "authtorized_keys"` or `find / -name id_rsa`
