### Resources
Linux Privilege Escalation using Capabilities - https://www.hackingarticles.in/linux-privilege-escalation-using-capabilities/

SUID vs Capabilities - https://mn3m.info/posts/suid-vs-capabilities/

Linux Capabilities Privilege Escalation - https://medium.com/@int0x33/day-44-linux-capabilities-privilege-escalation-via-openssl-with-selinux-enabled-and-enforced-74d2bec02099

### Steps
- To find capabilties:
```
getcap -r / 2>/dev/null
```
![image](https://user-images.githubusercontent.com/64409788/208077399-8a62aad1-e318-41f7-b52f-0041df8a7179.png)
Note: ep is like permit everything.
