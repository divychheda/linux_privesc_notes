## Note
- If in a command you write another command in between \`\`, it gets executed first and its o/p used bby the original command.
- eg,
- ![image](https://user-images.githubusercontent.com/64409788/209696359-9349e4a6-ab40-4161-8f60-1bb31b65716f.png)

## Escalation
- Any script like `linenum.sh` or `linpeas` could scan for this and tell about the vuln using docker.
- Look for : member of docker group.
- Now to attack follow: https://gtfobins.github.io/gtfobins/docker/
- We indeed have a shell and are a member of the docker group, so we execute:
```
docker run -v /:/mnt --rm -it bash chroot /mnt sh
```
- and we get root shell.
