## Cronjobs
- Look for cronjobs running frequently as root
- There are numerous ways, one way:
```
cat /etc/crontab
```
- One could also look for systemd timers:
```
systemctl list-timers --all
```
![image](https://user-images.githubusercontent.com/64409788/208079387-9f9c8e15-7b78-42a6-82f1-11296c0e3680.png)

### Escalate using Cron Path
- If relative path mentioned in crontab (eg, overwrite.sh in the pic above), one can make a malicious file in place of highest priority dir in PATH.
- If the file already exists, or absolute path is used : If you have `write` access, overwrite that file to escalate.
- Eg
![image](https://user-images.githubusercontent.com/64409788/208079672-1a378992-e34c-4cbf-b2cc-463bd940f1f8.png)
- Now we need to wait for /tmp/bash to be updated, do `ls -la` to check for `last updated` time to change.
- Once updated, execute:
```
/tmp/bash -p
```
- And you are root!

### Escalation using Cron Wildcards
- when cron jobs use wildcards like *
- In the same example,
![image](https://user-images.githubusercontent.com/64409788/208081525-9bdf4ba0-c453-4a5a-8c07-c6d3a84bfacf.png)
- The `compress.sh` file is using tar with a wildcard.
- We only have read permissions to it, cant overwrite.
- Because /home/user is in the PATH, we can do:
![image](https://user-images.githubusercontent.com/64409788/208084938-38551ee0-c329-4090-99c2-2006dfc897c2.png)
TODO: read up on checkpoints and tar.


